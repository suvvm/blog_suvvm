---
title: Codeforces 450B Jzzhu and Sequences
date: 2018-11-26 21:53:16
categories: 
- 算法相关
tags:
- 矩阵快速幂
- Codeforces
---
<meta name="referrer" content="no-referrer"/>
Jzzhu has invented a kind of sequences, they meet the following property:

 ![img](http://espresso.codeforces.com/c84e959b7fd00c2ebce39e2b33db6bdb624ba8f3.png) 

　　You are given *x* and *y*, please calculate *f**n* modulo 1000000007 (109 + 7).

Input

　　The first line contains two integers *x* and *y*(|*x*|, |*y*| ≤ 109). The second line contains a single integer *n* (1 ≤ *n* ≤ 2·109).

Output

　　Output a single integer representing *f**n* modulo 1000000007 (109 + 7).

Examples

Input

```
2 33
```

Output

```
1
```

Input

```
0 -12
```

Output

```
1000000006
```

Note

　　In the first sample, *f*2 = *f*1 + *f*3, 3 = 2 + *f*3, *f*3 = 1.

　　In the second sample, *f*2 =  - 1; - 1 modulo (109 + 7)equals (109 + 6).

解题思路：
　　本题给出一个运算公式 f( i ) = f( i - 1) + f(i + 1)给出两个整数分别为前两项的值，之后给出一个整数n，要求求出第n项的值。

　　我们首先将公式变形一下

　　

```
     f( i ) = f( i - 1 ) + f( i + 1 ) 
 ⇔  f( i + 1 ) =  f( i ) - f( i - 1 )
 ⇔  f( i ) = f( i - 1 ) - f( i - 2 )
```

　　之后我们将相邻两项计入一个2 * 1的矩阵中

![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181122211452205-500098852.png)

　　n大于3时计算n - 2次方即可得出答案

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
const LL mod = 1e9+7;
const int maxn = 2;
struct matrix{
    LL mat[maxn][maxn]; //mat存放矩阵
    matrix operator *(const matrix &a)const{    //重载运算符*
        matrix ans;
        for(int i = 0; i < 2; i++){ //遍历行
            for(int j = 0; j < 2; j++){ //遍历列
                ans.mat[i][j] = 0;
                for(int k =0; k < 2; k++){  //固定k进行矩阵乘法
                    ans.mat[i][j] = (ans.mat[i][j] + mat[i][k] * a.mat[k][j] % mod) % mod;
                }
            }
        }
        return ans;
    }
};
matrix power(matrix a, LL b){   //矩阵快速幂，与普通快速幂几乎没有区别
    matrix ans; //答案矩阵
    memset(ans.mat, 0, sizeof(ans.mat));    
    for(int i = 0; i < 2; i++){
        ans.mat[i][i] = 1;
    }
    //以上为初始化单位矩阵
    while(b){
        if(b & 1){  //b按位与1匹配
            ans = ans * a;
        }
        b >>= 1;
        a = a * a;
    }
    return ans;
}
int main()
{
    LL x ,y, n;

    while(scanf("%lld%lld", &x, &y) != EOF){    //输入x与y
        matrix a;
        scanf("%lld", &n);  //输入n
        a.mat[0][0] = 1;
        a.mat[0][1] = -1;
        a.mat[1][0] = 1;
        a.mat[1][1] = 0;
        //初始化矩阵
        //n为1或2单独判断
        if(n == 1){
            printf("%lld\n", (x % mod + mod) % mod);
        }else if(n == 2){
            printf("%lld\n", (y % mod + mod) % mod);
        }else{  //n>=3
            a = power(a, n - 2);    //计算矩阵a的n - 2次方
            LL ans = (((a.mat[0][0] * y + a.mat[0][1] * x) % mod) + mod ) % mod;
            
            printf("%lld\n", ans);
        }
    }
    return 0;
}
```

