---
title: HDU 1087 Super Jumping! Jumping! Jumping
date: 2018-11-06 23:19:32
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---
<meta name="referrer" content="no-referrer"/>
Nowadays, a kind of chess game called “Super Jumping! Jumping! Jumping!” is very popular in HDU. Maybe you are a good boy, and know little about this game, so I introduce it to you now. 

![img](http://acm.hdu.edu.cn/data/images/1087-1.jpg) 

　　The game can be played by two or more than two players. It consists of a chessboard（棋盘）and some chessmen（棋子）, and all chessmen are marked by a positive integer or “start” or “end”. The player starts from start-point and must jumps into end-point finally. In the course of jumping, the player will visit the chessmen in the path, but everyone must jumps from one chessman to another absolutely bigger (you can assume start-point is a minimum and end-point is a maximum.). And all players cannot go backwards. One jumping can go from a chessman to next, also can go across many chessmen, and even you can straightly get to end-point from start-point. Of course you get zero point in this situation. A player is a winner if and only if he can get a bigger score according to his jumping solution. Note that your score comes from the sum of value on the chessmen in you jumping path. 
Your task is to output the maximum value according to the given chessmen list. 

Input

　　Input contains multiple test cases. Each test case is described in a line as follow: 
　　N value_1 value_2 …value_N 
　　It is guarantied that N is not more than 1000 and all value_i are in the range of 32-int. 
　　A test case starting with 0 terminates the input and this test case is not to be processed. 
Output

　　For each case, print the maximum according to rules, and one line one case. 
Sample Input

```
3 1 3 2
4 1 2 3 4
4 3 3 2 1
0
```

解题思路：
　　本题有多组数据，每组数据给出棋子数量，棋子权值，玩家从起始位置上跳到棋子上，每次只能向后跳，且只能跳到比当前棋子权值大的棋子上。要求输出玩家跳过棋子的权值之和的最大值。

　　这其实就是一个给定数组求最大升序子串权值和（最大升序子串在原数组中不一定连续）的问题。可以用数组dp保存以每个点为结尾的数组的最大升序子串权值和（dp[i]为以原数组中以第i个数为结尾的最大升序子串权值和）这样对每个i都会出现两种情况。

　　1、第 i 号元素之前所有元素的权值都比第 i 号元素大，那么以第 i 号元素为结尾的最大升序子串权值和就为第 i 号元素自己的权值。

　　2、第 i 号元素之前存在比其权值小的元素（设其为 j ）则第 i 号元素的最大升序子串权值和为第 j 号元素的最大升序子串权值和再加上第 i 号元素的权值。

知道这两种情况后便可以写出动态规划状态转移方程 dp[i] = max{1, dp[j] + 1}  (j : 1 ~ i - 1)

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e4+100;
int arrays[maxn];   //棋子数组
int dp[maxn];   //以每个棋子为结尾的最大升序子串权值和
int main()
{
    int n;  //输入棋子数量
    while(scanf("%d", &n) != EOF){
        if(n == 0)  //棋子数为0结束
            break;
        for(int i = 1; i <= n ; i++){
            scanf("%d", &arrays[i]);    //输入棋子权值
        }
        int ans = -1;   //ans记录整个数组最大升序子串权值和
        for(int i = 1; i <= n; i++){
            dp[i] = arrays[i];  
            //初始化第 i 号元素为结尾的最大升序子串权值和就为第 i 号元素自己的权值
            for(int j = 1;  j < i; j++){    //遍历起始位置到i
                if(arrays[i] > arrays[j] && dp[j] + arrays[i] > dp[i]){
                //如果找到j权值小于i且以j元素结尾的最大升序子串权值和 加上i的权值 大于当前记录的权值和
                    dp[i] = dp[j] + arrays[i];//更新当前记录
                }
            }
            ans = max(ans, dp[i]);  //始终保持ans为dp中最大值
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

