---
title: HDU 1059 Dividing
date: 2018-11-12 12:01:40
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---

Marsha and Bill own a collection of marbles. They want to split the collection among themselves so that both receive an equal share of the marbles. This would be easy if all the marbles had the same value, because then they could just split the collection in half. But unfortunately, some of the marbles are larger, or more beautiful than others. So, Marsha and Bill start by assigning a value, a natural number between one and six, to each marble. Now they want to divide the marbles so that each of them gets the same total value. 
Unfortunately, they realize that it might be impossible to divide the marbles in this way (even if the total value of all marbles is even). For example, if there are one marble of value 1, one of value 3 and two of value 4, then they cannot be split into sets of equal value. So, they ask you to write a program that checks whether there is a fair partition of the marbles. 

Input

　　Each line in the input describes one collection of marbles to be divided. The lines consist of six non-negative integers n1, n2, ..., n6, where ni is the number of marbles of value i. So, the example from above would be described by the input-line ``1 0 1 2 0 0''. The maximum total number of marbles will be 20000. 

　　The last line of the input file will be ``0 0 0 0 0 0''; do not process this line. 
Output

　　For each colletcion, output ``Collection #k:'', where k is the number of the test case, and then either ``Can be divided.'' or ``Can't be divided.''. 

　　Output a blank line after each test case. 
Sample Input

```
1 0 1 2 0 0
1 0 0 0 1 1
0 0 0 0 0 0
```

Sample Output

```
Collection #1:
Can't be divided.

Collection #2:
Can be divided.
```

解题思路：
　　本题有价值为1~6的6种大理石各若干块，要求将所有的大理石对半分给Marsha和Bill。本题有多组数据，每组数据包括6个整数，分别为价值为1 ~ 6的大理石的个数，以6个0为输入结尾。

　　基本解题思想为动态规划01背包，背包容量为总价值的一半，背包内容物价值为大理石块的价值，如果容量为总价值一半的背包其最大内容物价值正好也为总价值的一半时可以平分。

　　本题不能将大理石拆分单块，因为每种大理石最多有20000块，最坏情况下大理石总量高度120000块背包最大容量为420000，01背包时间复杂度为O(V * N)，而题目给出的时间限制为1000ms，不进行优化一定会超时。所以我们在拆分大理石的时候进行二进制优化。

二进制优化：

　　假设某一价值的大理石有100块，我们并不需要将100块大理石全部加入运算数组，我们将100拆分为数个小于100的数字，使这些数字可以构成1 ~ 100中任意一个数字。这里用到了一个数论的小知识：1，2，4 ~ 2^n 可以组成1 ~ 2^(n + 1) - 1之间的任意数，100便可分解为1，2，4，8，16，32，（取2的n次幂（二进制数），不能取到64，因为拆分100的话所有分解的数加起来不能超过100） 37（前面取到的1 ~ 32已经可以表示63（2^(5 + 1) - 1）以内所有的数了，那么再补上100 - 63 = 37这个数后就可以取到所有1 ~ 100的数了）。继续分析，假设这100块大理石价值都为2，那我们就可用到我们分解出来的数字，根据这些数字我们把100块大理石分解1块，2块，4块，8块，16块，32块，37块这7组，这样我们便可以把100块价值为2的大理石看成价值为2，4，8，16，32，64，74的7块大理石。将这7块大理石加入运算数组在运算时比起100块大理石就要节约很多时间。

　　至于为什么1，2，4，8，16，32，37可以代替100，这很简单，之前已经写过了新的数组可以表示1 ~ 100所有数组，那么在运算中，我们如果需要拿5块价值1的大理石，在这里就和拿一块价值为1的大理石与一块价值为4的大理石有同样效果，拿其他所有1 ~ 100的数皆是这个道理。

　　dp记录容量（最大价值）为 j 的背包可以装载大理石的最大价值。marbles记录优化后每块大理石的的价值。

　　动态转移方程：dp[ j ] = max(dp[ j ], dp[ j - marbles[ i ] ] + marbles[ i ])

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e6+100;
int marbles[maxn];  //记录拆分后每块大理石的价值
int dp[maxn];
int n[7];
int tot = 0;    //tot记录所有大理石总价值
int main()
{
    int t = 1;
    while(scanf("%d%d%d%d%d%d", &n[1], &n[2], &n[3], &n[4], &n[5], &n[6]) != EOF && (n[1] + n[2] + n[3] + n[4] + n[5] + n[6])){
        //输入每种大理石的数量
        int cnt = 1,tot = 0;    
        for(int i = 1; i <= 6; i++){    //二进制拆分每一个价值的大理石
            int num = n[i];
            int j = 1;
            if(num != 0){
                while(num > j){
                //所有拆分后数字的和不超过num
                //我们可以每拆分一个数就用num减去它直到num小于想要拆分的下一个数
                    marbles[cnt++] = j * i; //将拆分后的价值计入marbles
                    tot += marbles[cnt - 1];    //记入总价值
                    num -= j;   //num减去当前拆分的数字
                    j *= 2;    //下一个要拆分的数字为当前数字的两倍
                }
                marbles[cnt++] = num * i;   //最后补上差的数字，并记录其组成的价值
                tot += marbles[cnt - 1];    //记入总价值
            }
        }
        cnt--;
        if(tot % 2 != 0){   //总价值不能被2整出则一定不能平分
            printf("Collection #%d:\nCan't be divided.\n\n", t);
            t++;
            continue;
        }
        tot /= 2;   //记录总价值的一半
        memset(dp, 0, sizeof(dp));  //初始化dp为0
        for(int i= 0; i <= cnt; i++){   //01背包遍历大理石优化后的块数
            for(int j = tot; j >= marbles[i]; j--){ //逆序遍历背包容量
                dp[j] = max(dp[j], dp[j - marbles[i]] + marbles[i]);    //动态转移方程
            }
        }
        if(dp[tot] == tot)  //正好平分
            printf("Collection #%d:\nCan be divided.\n\n", t);
        else
            printf("Collection #%d:\nCan't be divided.\n\n", t);
        t++;
    }

    return 0;
}
```

