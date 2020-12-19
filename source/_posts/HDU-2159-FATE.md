---
title: HDU 2159 FATE
date: 2018-11-12 08:59:57
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---

最近xhd正在玩一款叫做FATE的游戏，为了得到极品装备，xhd在不停的杀怪做任务。久而久之xhd开始对杀怪产生的厌恶感，但又不得不通过杀怪来升完这最后一级。现在的问题是，xhd升掉最后一级还需n的经验值，xhd还留有m的忍耐度，每杀一个怪xhd会得到相应的经验，并减掉相应的忍耐度。当忍耐度降到0或者0以下时，xhd就不会玩这游戏。xhd还说了他最多只杀s只怪。请问他能升掉这最后一级吗？

Input

　　输入数据有多组，对于每组数据第一行输入n，m，k，s(0 < n,m,k,s < 100)四个正整数。分别表示还需的经验值，保留的忍耐度，怪的种数和最多的杀怪数。接下来输入k行数据。每行数据输入两个正整数a，b(0 < a,b < 20)；分别表示杀掉一只这种怪xhd会得到的经验值和会减掉的忍耐度。(每种怪都有无数个)

Output

　　输出升完这级还能保留的最大忍耐度，如果无法升完这级输出-1。Sample Input

```
10 10 1 10
1 1
10 10 1 9
1 1
9 10 2 10
1 1
2 2
```

Sample Output

```
0
-1
1
```

解题思路：
　　本题要求处理杀怪升级问题，有多组数据，每组第一行给出4个整数，分别为升级所需的经验n，忍耐度（体力）m，怪物种类k，最多杀怪数s，之后k行跟随，每行输入两个整数,分别为杀掉这种怪会得到的经验值，杀掉这种怪消耗的体力。要求输出升完级后能保留的最大体力。

　　基本思路为完全背包，用数组dp记录消耗每个数量的体力所能获得的最大经验值，dp2记录该种情况下的杀怪数。根据dp与dp2寻找答案。

　　根据描述可以写出动态转移方程：

　　　　dp[ j ] = max(dp[ j ], dp[ j - engn[ i ] ] + expn[ i ] )

　　　　if(dp[ j ] == dp[ j - engn[ i ] ] + expn[ i ] )

　　　　　　dp2[ j ] = dp2[ j - eng[ i ] ] + 1

　　之后遍历最大体力值找到获得经验值可以达到升级要求且杀怪数低于限制的体力消耗值最小值，并用最大体力值减去它便可以得到答案，若没有符合要求的情况输出-1。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e2+10;
int engn[maxn];
int expn[maxn];
int dp[maxn], dp2[maxn];
int main()
{
    int n, m, k, s;
    while(scanf("%d%d%d%d", &n, &m, &k, &s) !=  EOF){
        //输入升级经验，忍耐度，怪物种类，最多杀怪数
        for(int i = 1; i <= k; i++){
            scanf("%d%d", &expn[i], &engn[i]);
            //输入每种怪物经验值与消耗忍耐度
        }
        for(int i = 0; i <= m; i++){
            //初始dp与dp2为0
            dp[i] = dp2[i] = 0;
        }
        for(int i = 1; i <= k; i++){    //完全背包
            for(int j = engn[i]; j <= m; j++){  //正向遍历背包容量
                dp[j] = max(dp[j], dp[j - engn[i]] + expn[i]);  //动态转移方程
                if(dp[j] == dp[j - engn[i]] + expn[i]){
                    dp2[j] = dp2[j - engn[i]] + 1;
                }
            }
        }
        int ans = -1;   //初始化答案为-1
        for(int i = 1; i <= m; i++){
            if(dp[i] >= n && dp2[i] <= s){  //找到符合升级条件与杀怪条件的消耗值
                ans = m - i;    //记录最大剩余忍耐度
                break;
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

