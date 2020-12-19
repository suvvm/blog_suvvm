---
title: PTA (Advanced Level) 1015 Reversible Primes
date: 2018-12-18 16:47:54
categories: 
- 算法相关
tags:
- 模拟
- PTA
---

　　A reversible prime in any number system is a prime whose "reverse" in that number system is also a prime. For example in the decimal system 73 is a reversible prime because its reverse 37 is also a prime.

　　Now given any two positive integers N (<) and D (1), you are supposed to tell if N is a reversible prime with radix D.

### Input Specification:

　　The input file consists of several test cases. Each case occupies a line which contains two integers N and D. The input is finished by a negative N.

### Output Specification:

　　For each test case, print in one line `Yes` if N is a reversible prime with radix D, or `No` if not.

### Sample Input:

```in
73 10
23 2
23 10
-2
```

### Sample Output:

```out
Yes
Yes
No
```

解题思路：
　　本题给出一个十进制数字n，之后给出其进制d，要求判断其是不是可逆素数，若是可逆素数输出Yes否则输出No。

　　本题的可逆素数要求给出数字本身是素数且，将其转化为给出的进制，反转后重新转化为十进制还是素数。

　　如：23的2进制为10111反转后为11101 = 29，29与23都是素数，所以23是可逆素数。

　　由于本题有多组输入，所以可以用素数筛（埃氏筛法）先将素数打表。

　　埃氏筛法:

```c++
int prime[maxn];
bool vis[maxn] = {false};
int cnt = 0;
void findPrime(){   //埃氏筛法
    //每找到一个一个素数将其倍数都标记为不是素数
    //时间复杂度O(n loglogn)
    for(int i = 2; i < maxn; i++){
        if(vis[i] == false){    //i是素数
            prime[cnt++] = i;
            for(int j = 2 * i; j < maxn; j += i){
                vis[j] = true;  //标记所以i的倍数
            }
        }
    }
}
```

之后根据我们得到的素数判断输入的数是否为素数，若不是素数直接输出No，若是素数则将其转化为对应进制的数字后反转，将反转后得到的数字重新转化为10进制，在判断其是不是素数，是的话输出Yes否则输出No。

　　这样我们就需要两个函数，一个用来将其他进制数转化为10进制。另一个用来将10进制转化为其他进制并反转。

转化为d进制：

```c++
string decimalToOther(int num, int radix){  //将十进制数转化为其他进制数
    string ans;
    //ans从最低位开始记录，结束后得到的直接就是转化后数字的反转
    while(num){
        ans += (num % radix) + '0';
        num /= radix;
    }
    return ans;
}
```

 转化为10进制 

```c++
LL toDecimal(string num, int radix){    //将某进制数转化为10进制
    LL ans = 0;
    for(int i = 0; i < num.size(); i++){
        ans = ans * radix + (num[i] - '0');
        if(ans < 0)
            return -1;
    }
    return ans;
}
```

 AC代码 

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
const int maxn = 1e6+10;
int prime[maxn];
bool vis[maxn] = {false};
int cnt = 0;
void findPrime(){   //埃氏筛法
    //每找到一个一个素数将其倍数都标记为不是素数
    //时间复杂度O(n loglogn)
    for(int i = 2; i < maxn; i++){
        if(vis[i] == false){    //i是素数
            prime[cnt++] = i;
            for(int j = 2 * i; j < maxn; j += i){
                vis[j] = true;  //标记所以i的倍数
            }
        }
    }
}
LL toDecimal(string num, int radix){    //将某进制数转化为10进制
    LL ans = 0;
    for(int i = 0; i < num.size(); i++){
        ans = ans * radix + (num[i] - '0');
        if(ans < 0)
            return -1;
    }
    return ans;
}
string decimalToOther(int num, int radix){  //将十进制数转化为其他进制数
    string ans;
    //ans从最低位开始记录，结束后得到的直接就是转化后数字的反转
    while(num){
        ans += (num % radix) + '0';
        num /= radix;
    }
    return ans;
}
int n, d;
int main()
{
    findPrime();    //素数打表
    while(scanf("%d", &n) != EOF && n > 0){
        //输入n，n < 0时直接结束运算
        scanf("%d", &d);
        //输入进制d
        if(vis[n] == true || n <= 1){   //若n不是素数直接输出No进行下一次运算
            printf("No\n");
            continue;
        }
        string toRadix = decimalToOther(n, d);
        //将n转化为d进制并反转
        int ans = toDecimal(toRadix, d);
        //将反转的数重新转化为10进制
        if(vis[ans] == true || ans <= 1){//若反转的数不是素数输出No
            printf("No\n");
            continue;
        }
        printf("Yes\n");    //否则输出Yes
    }
    return 0;
}
```

