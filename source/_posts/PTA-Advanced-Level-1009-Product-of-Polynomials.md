---
title: PTA (Advanced Level) 1009 Product of Polynomials
date: 2018-12-12 19:22:10
categories: 
- 算法相关
tags:
- STL
- PTA
---

This time, you are supposed to find A×B where A and B are two polynomials.

### Input Specification:

Each input file contains one test case. Each case occupies 2 lines, and each line contains the information of a polynomial:

K N1 aN1 N2 aN2 ... NK aNK

where K is the number of nonzero terms in the polynomial, Ni and aNi (i=1,2,⋯,K) are the exponents and coefficients, respectively. It is given that 1≤K≤10, 0≤NK<⋯<N2<N1≤1000.

### Output Specification:

For each test case you should output the product of A and B in one line, with the same format as the input. Notice that there must be NO extra space at the end of each line. Please be accurate up to 1 decimal place.

### Sample Input:

```in
2 1 2.4 0 3.2
2 2 1.5 1 0.5
```

### Sample Output:

```out
3 3 3.6 2 6.0 1 1.6
```

解题思路：
　　本题考查多项式乘法，给出两行数组，分别为第一个多项式与第二个多项式的信息，每行第一个数n为当前多项式项数，之后给出n组数据每组包括一个整数为对应项的指数，一个浮点数为对应项的系数，要求输出两多项式相乘后的项数，以及以指数从大到小输出每项的指数与系数。

　　这里用了三个double型的数组ans、pol1、pol2以指数为下标记录多项式的系数，pol1、pol2记录给出多项式的系数，ans记录答案多项式的系数。遍历两个多项式的指数，将第一个多项式的每一项都与第二个多项式相乘将结果记录在ans数组中。

　　由小到大遍历ans数组获得系数非0项的数量，利用栈后进先出的特性，用两个栈记录答案数组所有系数非0项的指数与系数，之后输出获得的数量，与栈中的数据即可。

```c++
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e4;
double pol1[maxn];  //记录第一个多项式
double pol2[maxn];  //记录第二个多项式
double ans[maxn];   //记录答案
int n1, n2; //给出的两个多项式的项数
int main(){
    while(scanf("%d", &n1) != EOF){ //输入第一个多项式项数
        fill(pol1, pol1 + maxn, 0.0);
        fill(pol2, pol2 + maxn, 0.0);
        fill(ans, ans + maxn, 0.0);
        //将三个数组都初始化为0
        int temp;   
        double num;
        int max1 = 0, max2 = 0;
        //记录第一个多项式与第二个多项式的最大指数
        while(n1--){
            scanf("%d%lf", &temp, &num);    //输入第一个多项式每一项的指数与系数
            pol1[temp] = num;   //以指数为下标记录系数
            max1 = max(temp, max1); //记录最大指数
        }
        scanf("%d", &n2);   //输入第二个多项式项数
        while(n2--){
            scanf("%d%lf", &temp, &num);    //输入第二个多项式每一项的指数与系数
            pol2[temp] = num;   //以指数为下标记录系数
            max2 = max(max2, temp); //记录最大指数
        }
        for(int i = 0; i <= max1; i++){ //遍历第一个多项式的所有指数
            for(int j = 0; j <= max2; j++){ //遍历第二个多项式的所有指数
                if(pol1[i] != 0 && pol2[j] != 0){   //当前相乘的项系数都不为0执行乘法运算
                    ans[i + j] += pol1[i] * pol2[j];    //指数相加系数相乘，结束后加入ans对应项
                }
            }
        }
        int cnt = 0;    //cnt记录答案项数
        stack<int> p;   //p记录指数
        stack<double> d;    //d记录系数
        for(int i = 0; i <= max1 + max2; i++){  //答案中指数最大为给出的两多项式最大指数相乘
            if(ans[i] != 0.0){    //对应项系数不为0
                cnt++;  //项数加一
                p.push(i);  //指数入栈
                d.push(ans[i]); //系数入栈
            }
        }
        printf("%d", cnt);  //输出答案项数
        while(!p.empty()){  //指数由大到小输出每一项指数与系数
            printf(" %d %.1f", p.top(), d.top());
            p.pop();
            d.pop();
        }
        printf("\n");
    }
    return 0;
}
```

