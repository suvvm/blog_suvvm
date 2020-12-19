---
title: HDU 2955 Robberies
date: 2018-11-11 23:55:21
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---
<meta name="referrer" content="no-referrer"/>
　　The aspiring Roy the Robber has seen a lot of American movies, and knows that the bad guys usually gets caught in the end, often because they become too greedy. He has decided to work in the lucrative business of bank robbery only for a short while, before retiring to a comfortable job at a university. 

 ![img](http://acm.hdu.edu.cn/data/images/con211-1010-1.jpg) 
　　For a few months now, Roy has been assessing the security of various banks and the amount of cash they hold. He wants to make a calculated risk, and grab as much money as possible. 


　　His mother, Ola, has decided upon a tolerable probability of getting caught. She feels that he is safe enough if the banks he robs together give a probability less than this.

Input

　　The first line of input gives T, the number of cases. For each scenario, the first line of input gives a floating point number P, the probability Roy needs to be below, and an integer N, the number of banks he has plans for. Then follow N lines, where line j gives an integer Mj and a floating point number Pj . 
Bank j contains Mj millions, and the probability of getting caught from robbing it is Pj .

Output

　　For each test case, output a line with the maximum number of millions he can expect to get while the probability of getting caught is less than the limit set. 

Notes and Constraints 
0 < T <= 100 
0.0 <= P <= 1.0 
0 < N <= 100 
0 < Mj <= 100 
0.0 <= Pj <= 1.0 



　　A bank goes bankrupt if it is robbed, and you may assume that all probabilities are independent as the police have very low funds.

Sample Input

```
3
0.04 3
1 0.02
2 0.03
3 0.05
0.06 3
2 0.03
2 0.03
3 0.05
0.10 3
1 0.03
2 0.02
3 0.05
```

Sample Output

```
2
4
6
```

解题思路：
　　本题是讲抢劫犯母亲帮助抢劫犯计算抢银行，题目给出一个数字T为测试数量，之后给出一行包括两个数字P与N，P为抢劫犯母亲认为安全的最大被抓概率，N为银行数量，之后N行跟随，每行包括两个数字，Mj抢劫这家银行可以获得的钱财，Pj抢劫这家银行被抓的概率，每家银行只能抢一次。要求输出低于抢劫犯母亲预计被抓几率所能获得的最大收益。

　　我们可以计算出抢劫银行的所有获利情况所对应的最大逃脱率，1 - 最大逃脱率便是当前获利对应最低被抓率。与抢劫犯母亲的预期进行比较便可以获得最大收益。

　　基本思路是动态规划01背包问题背包最大容量为抢劫所有银行可以获得的收益，背包内容物价值为逃脱率。

　　用dp[ j ]记录收益为 j 时的最大逃脱率，这样就可以写出动态转移方程：

　　　　dp[ j ] = max(dp[ j ], dp[ j - p[ i ] ] * (1 - m[ i ])

　　之后遍历dp找到被抓率低于抢劫犯母亲预计的最大收益即可。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 100  * 100;    
//整个程序中最大的数组为dp其元素个数最多为最多银行个数乘以最大获益金额
int money[maxn];    //money保存抢劫每个银行的收益
double probability[maxn];   //probability保存抢劫每个银行被抓概率
double dp[maxn];    //dp保存每个获益金额对应的最高逃脱率
int main()
{
    int t;
    while(scanf("%d", &t) != EOF){  //输入测试数量
        double p;
        int n, maxMoney = 0;
        scanf("%lf%d", &p, &n); //输入母亲预计值与银行数量
        for(int i = 1; i <= n; i++){
            scanf("%d%lf", &money[i], &probability[i]);
            maxMoney += money[i];   //记录最大获益金额
        }
        for(int i = 0; i <= maxMoney; i++){ //初始化dp为0
            dp[i] = 0;
        }
        dp[0] = 1;  //不抢银行不会被警察叔叔抓
        for(int i = 0; i <= n; i++){    //遍历银行
            for(int j = maxMoney; j >= money[i]; j--){  //01背包逆序遍历背包容量
                dp[j] = max(dp[j], dp[j - money[i]] *(1 - probability[i]));
                //动态转移方程
            }
        }
        int ans = 0;
        for(int i = 0; i <= maxMoney; i++){ 
            //遍历dp找到低于母亲预计值的最大收益
            if(dp[i] > 1 - p)
                ans = max(ans, i);
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

