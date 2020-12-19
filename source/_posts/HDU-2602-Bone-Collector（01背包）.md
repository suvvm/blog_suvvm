---
title: HDU 2602 Bone Collector（01背包）
date: 2018-11-08 21:52:33
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---
<meta name="referrer" content="no-referrer"/>
Many years ago , in Teddy’s hometown there was a man who was called “Bone Collector”. This man like to collect varies of bones , such as dog’s , cow’s , also he went to the grave … 
The bone collector had a big bag with a volume of V ,and along his trip of collecting there are a lot of bones , obviously , different bone has different value and different volume, now given the each bone’s value along his trip , can you calculate out the maximum of the total value the bone collector can get ?

 ![img](http://acm.hdu.edu.cn/data/images/C154-1003-1.jpg)



Input

　　The first line contain a integer T , the number of cases. 
　　Followed by T cases , each case three lines , the first line contain two integer N , V, (N <= 1000 , V <= 1000 )representing the number of bones and the volume of his bag. And the second line contain N integers representing the value of each bone. The third line contain N integers representing the volume of each bone.

Output

　　One integer per line representing the maximum of the total value (this number will be less than 231).

Sample Input

```
1
5 10
1 2 3 4 5
5 4 3 2 1
```

Sample Output

```
14
```

解题思路：
　　本题给出测试数量，每组测试给出骨骼数量n与背包体积v，之后跟随两行，第一行为骨骼的价值，第二行为骨骼的体积。要求输出背包所能装下骨骼的最大价值。

　　本题是标准的0 1背包问题。基本思路是动态规划，令dp[ j ]表示背包容量为j时能装下骨骼的最大价值。

　　对于第i块骨骼，有拿或不拿两种方案。

　　1、拿第 i 块骨骼，问题转化为计算背包容量为j - volume[ i ] 在前i - 1块骨骼中取得最大价值问题dp[ j ]的值为前i - 1块骨骼中取得的最大价值 + 第i块骨骼的价值。

　　2、不拿第i块骨骼，问题转化为背包容量为 j 时在前i - 1块骨骼中取得最大价值问题，dp[ j ]的值为前i - 1块骨骼中取得的最大价值。

　　可以写出状态转移方程：dp[ j ] = max(dp[ j ], dp[ j - volume[ i ] ] + value[ i ])

　　边界为拿前0块骨骼，最大价值为0，枚举所有骨骼，每次从最大背包容量开始逆序枚举便可获得答案。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+100;
int value[maxn], volume[maxn];
//value记录骨骼价值volume记录骨骼体积
int dp[maxn];
int main()
{
    int t;  //测试数量
    while(scanf("%d", &t) != EOF){
        int n, v;   //n为骨骼数量 v背包容量
        while(t--){
            scanf("%d%d", &n, &v);
            for(int i = 1; i <= n; i++){
                scanf("%d", &value[i]); //输入骨骼价值
            }
            for(int i = 1; i <= n; i++){
                scanf("%d", &volume[i]);    //输入骨骼体积
            }
            memset(dp, 0, sizeof(dp));  //初始化边界
            for(int i = 0; i <= n; i++){    //枚举骨骼
                for(int j = v; j >= volume[i]; j--){  //逆序枚举体积  
                    dp[j] = max(dp[j], dp[j - volume[i]] + value[i]);
                }
            }
            int ans = 0;
            for(int i = 0; i <= v; i++){    //找到最大值
                ans = max(ans, dp[i]);
            }
            printf("%d\n", ans);
        }
    }
    return 0;
}
```

