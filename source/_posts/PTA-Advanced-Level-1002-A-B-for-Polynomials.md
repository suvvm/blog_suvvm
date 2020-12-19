---
title: PTA (Advanced Level) 1002 A+B for Polynomials
date: 2019-03-29 19:12:54
categories: 
- 算法相关
tags:
- 模拟
- PTA
---

　　This time, you are supposed to find A+B where A and B are two polynomials.

**Input Specification:**

　　Each input file contains one test case. Each case occupies 2 lines, and each line contains the information of a polynomial:

　　K N1 aN1 N2 aN2 ... NK aNK

　　where K is the number of nonzero terms in the polynomial, Ni and aNi (,) are the exponents and coefficients, respectively. It is given that 1，0.

**Output Specification:**

　　For each test case you should output the sum of A and B in one line, with the same format as the input. Notice that there must be NO extra space at the end of each line. Please be accurate to 1 decimal place.

**Sample Input:**

2 1 2.4 0 3.2

2 2 1.5 1 0.5

**Sample Output:**

3 2 1.5 1 2.9 0 3.2

题目解析

  本题给出两个多项式，每个多项式按照 项数K 指数N1 系数aN1 指数N2 系数aN2 …… 指数NK 系数aNK 的方式给出，要求计算两个多项式的和。

​    用一个double型ans来记录两个多项式的和 （下标为指数，下标对应的和为系数），每输入一组指数与系数，就将系数加入ans中对应项中去。在输入数据时应记录输入的最大指数与最小指数。

​    遍历ans由最小指数到最大指数，获取系数不为0的项数记为cnt，cnt便是两个多项式的和的项数。

​    出数cnt，由最大指数到最小指数遍历并输出每一个系数不为0的项即可。

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX= 1e5+10;
double ans[MAX];
int main()
{
    int n;
    scanf("%d", &n);    //输入多项式A的项数
    int maxe = INT_MIN, mine = INT_MAX;
    //maxe保存最大指数    mine保存最小指数
    for(int i = 0; i < n; i++){ //输入多项式a
        int ea; //指数
        double temp;    //系数
        scanf("%d", &ea);   
        scanf("%lf", &temp);
        ans[ea] += temp;    //系数加入ans中对应项
        maxe = max(maxe, ea);   //记录最大指数
        mine = min(mine, ea);   //记录最小指数
    }
    scanf("%d", &n);    //输入多项式b
    for(int i = 0; i < n; i++){
        int eb; //指数
        double temp;    //系数
        scanf("%d", &eb);
        scanf("%lf", &temp);
        ans[eb] += temp;    //系数加入ans中对应项
        maxe = max(maxe, eb);   //记录最大指数
        mine = min(mine, eb);   //记录最小指数
    }
    int cnt = 0;
    //cnt记录A+B的项数
    for(int i = mine; i <= maxe; i++){  //由最小指数到最大指数遍历ans
        if(ans[i] != 0.0)   //若某项系数不为0则项数加1
            cnt++;
    }
    printf("%d", cnt);  //输出cnt
    for(int i = maxe; i >= mine; i--){  //由最大指数到最小指数遍历并输出每一个系数不为0的项
        if(ans[i] != 0.0)
            printf(" %d %.1f", i, ans[i]);
    }
    printf("\n");
    return 0;
}
```

