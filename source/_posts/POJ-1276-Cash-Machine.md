---
title: POJ 1276 Cash Machine
date: 2018-11-09 20:15:53
categories: 
- 算法相关
tags:
- 动态规划（DP）
- POJ
---

A Bank plans to install a machine for cash withdrawal. The machine is able to deliver appropriate @ bills for a requested cash amount. The machine uses exactly N distinct bill denominations, say Dk, k=1,N, and for each denomination Dk the machine has a supply of nk bills. For example, 

　　N=3, n1=10, D1=100, n2=4, D2=50, n3=5, D3=10 

　　means the machine has a supply of 10 bills of @100 each, 4 bills of @50 each, and 5 bills of @10 each. 

　　Call cash the requested amount of cash the machine should deliver and write a program that computes the maximum amount of cash less than or equal to cash that can be effectively delivered according to the available bill supply of the machine. 

Notes: 
　　@ is the symbol of the currency delivered by the machine. For instance, @ may stand for dollar, euro, pound etc. 

Input

　　The program input is from standard input. Each data set in the input stands for a particular transaction and has the format: 

　　cash N n1 D1 n2 D2 ... nN DN 

　　where 0 <= cash <= 100000 is the amount of cash requested, 0 <=N <= 10 is the number of bill denominations and 0 <= nk <= 1000 is the number of available bills for the Dk denomination, 1 <= Dk <= 1000, k=1,N. White spaces can occur freely between the numbers in the input. The input data are correct. 

Output

　　For each set of data the program prints the result to the standard output on a separate line as shown in the examples below. 

Sample Input

```
735 3  4 125  6 5  3 350
633 4  500 30  6 100  1 5  0 1
735 0
0 3  10 100  10 50  10 10
```

Sample Output

```
735
630
0
0
```

Hint

　　The first data set designates a transaction where the amount of cash requested is @735. The machine contains 3 bill denominations: 4 bills of @125, 6 bills of @5, and 3 bills of @350. The machine can deliver the exact amount of requested cash. 

　　In the second case the bill supply of the machine does not fit the exact amount of cash requested. The maximum cash that can be delivered is @630. Notice that there can be several possibilities to combine the bills in the machine for matching the delivered cash. 

　　In the third case the machine is empty and no cash is delivered. In the fourth case the amount of cash requested is @0 and, therefore, the machine delivers no cash.

解题思路：
　　本题有多组测试数据，每组数据占一行，包括现金cash, 票据种类n，之后跟随n组数据每组包括票据数量，票据金额。要求输出小于等于现金的票据最大金额。

　　可以将本题转化为0 1背包问题，背包容量为现金数cash，背包内容物价值与占空间数都为票据面值。

　　基本思路是将每个面值的票据拆分，由于票据数量较大，直接拆分运算时会超时，所以我们对拆分进行优化。

 

二进制优化：

　　假设某一面值的票据有100张，我们并不需要将100张票据全部加入运算数组，我们将100拆分为数个小于100的数字，使这些数字可以构成1 ~ 100中任意一个数字。这里用到了一个数论的小知识：1，2，4 ~ 2^n 可以组成1 ~ 2^(n + 1) - 1之间的任意数，100便可分解为1，2，4，8，16，32，（取2的n次幂（二进制数），不能取到64，因为拆分100的话所有分解的数加起来不能超过100） 37（前面取到的1 ~ 32已经可以表示63以内所有的数了，那么再补上100 - 63 = 37这个数后就可以取到所有1 ~ 100的数了）。继续分析，假设这100张票据面值都为2，那我们就可用到我们分解出来的数字，根据这些数字我们把100张票分解1张，2张，4张，8张，16张，32张，37张这7组，这样我们便可以把100张面值为2的票据看成面值为2，4，8，16，32，64，74的7张票据。将这7张票据加入运算数组在运算时比起100张票据就要节约很多时间。

　　至于为什么1，2，4，8，16，32，37可以代替100，这很简单，之前已经写过了新的数组可以表示1 ~ 100所有数组，那么在运算中，我们如果需要拿5张面值1，在这里就和拿一张面值1一张面值4有同样效果拿其他所有1 ~ 100的数皆是这个道理。

 

背包思路：

　　动态规划，令dp[ j ]表示chsh为 j 时能得到的票据的最大金额。

　　对于第 i 张票据，有用或不用两种方案。

　　1、用第 i 块票据，问题转化为计算背包容量为 j - value[ i ] 在前i - 1张票据中取得最大金额问题dp[ j ]的值为前i - 1张票据中取得的最大金额 + 第 i 张票据的金额。

　　2、不用第 i 张票据，问题转化为背包容量为 j 时在前i - 1张票据中取得最大金额问题，dp[ j ]的值为前i - 1张票据中取得的最大金额。

　　动态转移方程：dp[ j ] = max(dp[ j ], dp[ j - value[ i ] ] + value[ i ])

　　边界为用前0张票据，最大金额为0，枚举所有票据，每次从最大背包容量开始逆序枚举便可获得答案。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 11 * 1000;
int dp[maxn];
int value[maxn];
//value记录票据面值
int main()
{
    int cash;   //cash为现金金额（即背包容量）
    while(scanf("%d", &cash) != EOF){
        memset(value, 0, sizeof(value));
        //初始化票据面值数组都为0
        int n, cnt = 1;
        scanf("%d", &n);    //输入票据种类数
        for(int i = 0 ; i < n; i++){
            int num, denomination;
            scanf("%d%d", &num, &denomination);
            //输入每种票据的数量与面值
            //二进制拆分num
            int j = 1;  //拆分的第一个数字为1
            while(num >= j){    
            //所有拆分后数字的和不超过num
            //我们可以每拆分一个数就用num减去它直到num小于想要拆分的下一个数
                value[cnt++] = j * denomination;    //将拆分后的面值计入value
                num -= j;   //num减去当前拆分的数字
                j *= 2; //下一个要拆分的数字为当前数字的两倍
            }
            value[cnt++] = num * denomination;  
            //最后补上差的数字，并记录其组成的面值
        }
        cnt--;
        memset(dp, 0, sizeof(dp));
        //初始化边界
        for(int i = 1; i <= cnt; i++){  //枚举票据
            for(int j = cash; j >= value[i]; j--){  //逆序枚举背包容量
                dp[j] = max(dp[j], dp[j - value[i]] + value[i]);
            }
        }
        int ans = 0;
        for(int i = 0; i <= cash; i++){ //找到最大值
            ans = max(ans, dp[i]);
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

