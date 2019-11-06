# myrandom

using 1/1000 us accuracy time now as srand's param to produce a better random int or strings.


# you may see some bad example for using random like this:

### the first bad example

```
srand(time(NULL));//通过时间函数设置随机数种子，使得每次运行结果随机。return in seconds,not good,disaster
```
and we using it to generate random strings,simple coding like below:

```
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

#define STR_LEN 10
#define CHAR_MIN 'a'
#define CHAR_MAX 'z' 


void randomstrings(char * str){
	int i;
	srand(time(NULL));//return in seconds,not good,disaster
	for (i = 0; i < STR_LEN; i++)
	{
		str[i] = rand() % (CHAR_MAX - CHAR_MIN + 1) + CHAR_MIN; //生成要求范围内的随机数。
	}
	return str;
}
int main()
{
	char sstr[STR_LEN + 1] = { 0 };
	for (int j = 0; j < 10; j++){
		randomstrings(&sstr);
		printf("%s\n", sstr);
	}
	return 0;
}

```
and you got following result :
```
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
xkfejldmfy
```
this is because time(NULL) will give you a time now accuracy to seconds.and every time the rand runs it will call time(NULL) again to get random seed,but it runs too fast it finished in 1s so the random seed always the same.

### the second bad example

```
srand(clock());//clock will return the current clock in ms;not good;
```

and we using it to generate random strings,simple coding like below:

```
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

#define STR_LEN 10
#define CHAR_MIN 'a'
#define CHAR_MAX 'z' 


void randomstrings(char * str){
	int i;
	srand(clock());//clock will return the current clock in ms;not good;
	for (i = 0; i < STR_LEN; i++)
	{
		str[i] = rand() % (CHAR_MAX - CHAR_MIN + 1) + CHAR_MIN; //生成要求范围内的随机数。
	}
	return str;
}
int main()
{
	char sstr[STR_LEN + 1] = { 0 };
	for (int j = 0; j < 10; j++){
		randomstrings(&sstr);
		printf("%s\n", sstr);
	}
	return 0;
}

```
and you got following result :
```
mxwtptttyk
mxwtptttyk
mxwtptttyk
mxwtptttyk
phqghumeay
phqghumeay
phqghumeay
phqghumeay
phqghumeay
phqghumeay
```
this is because clock() will give you a time now accuracy to ms.and the program runs too fast it finished in 2ms.

---

### So we need a better srand seed,which is what we do here by using time now in  1/1000 us accuracy

srand() actually takes the pointer to the param you provide.and it will affect the rand,if you using srand(&something) before,then every time you using rand() would just like you using rand(&something).no matter something is a basetype or a funtion.

we can proof by following code:

```
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

#define STR_LEN 10
#define CHAR_MIN 'a'
#define CHAR_MAX 'z'


void randomstrings(char * str){
	int i;
	srand(randseed());//return in seconds,not good,disaster
	for (i = 0; i < STR_LEN; i++)
	{
		str[i] = rand() % (CHAR_MAX - CHAR_MIN + 1) + CHAR_MIN; //生成要求范围内的随机数。

	}
	return str;
}
int randseed(){
printf("randseed called\n");
return 888;
}
int main()
{
	char sstr[STR_LEN + 1] = { 0 };
	for (int j = 0; j < 10; j++){
		randomstrings(&sstr);
		printf("%s\n", sstr);
	}
	return 0;
}
```
you will get following result:

```
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
randseed called
aucvdbcjsi
```
So the code in this repo we using QueryPerformanceCounter to got accuracy time now and got the effective part turn into 1/1000 us as our random seed.
which will got following result finished in 2ms:

```
szetrdbrib
skdqqcsqei
aowyulunev
zxfrrwdopl
rhndjmctag
gzetxabgfo
hiodzexwsj
reqpglyasz
yjoklzhatq
pmdckoucmc
rwjakgdotz
iipsjwqqmt
dsvpkfzdtr
zztxgnxgps
uncuxddtra
wxhsynufrx
zqnqzwdrqv
fmltwsmjsd
ubibaskuom
wmozajbhnj
ttlgwjzkrs
kwsqvzmmse
hlqyzgkxon
esnfvgibsw
vwcxuwvdsq
rzzgonyipe
kxilhmxlvp
axhnocqfqb
rnsmfhtohv
kxghtjegai
dtsqqtswdq
apnsnujzvp
luhgweytis
jjvtirshrj
fvzvldtmdx
eruypubbtd
utkiuxzskg
negmirejqs
qmwzhjpmyn
vacaxqmstm
yuqyoebups
zcekrfgfkg
asytbsbifp
ouhqyrbjxr
ahkiyqlyal
jchxizdhun
nsypbkarim
knwomlrxsn
cohucjhlar
azlrbbdzwy
```


But it should be still not enough to be a standard prng.
Remember to using it only in your home.