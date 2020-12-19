---
title: CodeForces 1060 B Maximum Sum of Digits
date: 2018-12-02 19:43:58
categories: 
- 算法相关
tags:
- 大数运算
- CodeForces
---

　　You are given a positive integer n.

　　Let S(x)S(x) be sum of digits in base 10 representation of xx , for example, S(123)=1+2+3=6S(123)=1+2+3=6 , S(0)=0S(0)=0 .

　　Your task is to find two integers a,ba,b , such that 0≤a,b≤n0≤a,b≤n , a+b=na+b=n and S(a)+S(b)S(a)+S(b) is the largest possible among all such pairs.

Input

　　The only line of input contains an integer nn (1≤n≤1012)(1≤n≤1012) .

Output

　　Print largest S(a)+S(b)S(a)+S(b) among all pairs of integers a,ba,b , such that 0≤a,b≤n0≤a,b≤n and a+b=na+b=n .

Examples

Input

```
35
```

Output

```
17
```

Input

```
10000000000
```

Output

```
91
```

Note

　　In the first example, you can choose, for example, a=17a=17 and b=18b=18 , so that S(17)+S(18)=1+7+1+8=17S(17)+S(18)=1+7+1+8=17 . It can be shown that it is impossible to get a larger answer.

　　In the second test example, you can choose, for example, a=5000000001a=5000000001 and b=4999999999b=4999999999 , with S(5000000001)+S(4999999999)=91S(5000000001)+S(4999999999)=91 . It can be shown that it is impossible to get a larger answer.

解题思路：
　　给出一个数字n，将他拆分为2个数字，使拆分的两个数字每一位相加的和最大，输出相加后的和。

　　个人感觉不用管下面提示。我们本着贪心的思想，希望获得尽可能多的9，就是将35，拆分为9与26，将10000000000，拆分为9999999999与1，既将原始数字拆分为比其第一位的全由9组成的数字与另一个补偿数字，补偿数字为原始数字减去拆分的全9数字。之后将拆分的两个数字所有位都相加便可以得到答案。

```c++
#include<iostream>
#include<sstream>
#include<string>
#include<cstdio>
//CodeForces不支持万能头文件bits/stdc++.h
using namespace std;
typedef long long ll;
string n;
ll power(int a, int b){ //快速幂
    ll ans = 1;
    while(b){
        if(b & 1){
            ans = ans * a;
        }
        a = a * a;
        b >>= 1;
    }
    return ans;
}
int main()
{
    ll a, b;
    while(cin >> n)
    {
        int suma=0;
        int sumb=0;
        a = power(10, n.size() - 1);
        //若想拆分出小于且9最多的数字，只需要找到n的位数n.size() - 1
        //之后便可以用10的n.size() - 1次幂找到与n位数相等数字中最小数字
        //减一便可以得到我们所要拆分的数字
        istringstream cinn(n);
        cinn >> b;
        //先用istringstream读取n中的值输入到整形b中
        //b - a就是补偿的数字。
        a--;
        b = b - a;
        while(a)    //将a的每一位加起来
        {
            suma += a % 10;
            a/=10;
        }
        while(b)
        {
            sumb += b % 10; //将b的每一位加起来
            b/=10;
        }
        cout << suma + sumb << endl;    //所有位数加和
    }
    return 0;
}
```

