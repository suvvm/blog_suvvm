---
title: HDU 2680 Choose the best route
date: 2018-11-24 16:51:04
categories: 
- 算法相关
tags:
- 最短路
- HDU
---

　　One day , Kiki wants to visit one of her friends. As she is liable to carsickness , she wants to arrive at her friend’s home as soon as possible . Now give you a map of the city’s traffic route, and the stations which are near Kiki’s home so that she can take. You may suppose Kiki can change the bus at any station. Please find out the least time Kiki needs to spend. To make it easy, if the city have n bus stations ,the stations will been expressed as an integer 1,2,3…n.

Input

　　There are several test cases. 
　　Each case begins with three integers n, m and s,(n<1000,m<20000,1=<s<=n) n stands for the number of bus stations in this city and m stands for the number of directed ways between bus stations .(Maybe there are several ways between two bus stations .) s stands for the bus station that near Kiki’s friend’s home. 
　　Then follow m lines ,each line contains three integers p , q , t (0<t<=1000). means from station p to station q there is a way and it will costs t minutes . 
　　Then a line with an integer w(0<w<n), means the number of stations Kiki can take at the beginning. Then follows w integers stands for these stations. 
Output

　　The output contains one line for each data set : the least time Kiki needs to spend ,if it’s impossible to find such a route ,just output “-1”.Sample Input

```
5 8 5
1 2 2
1 5 3
1 3 4
2 4 7
2 5 6
2 3 5
3 5 1
4 5 1
2
2 3
4 3 4
1 2 3
1 3 4
2 3 2
1
1
```

Sample Output

```
1
-1
```

解题思路：
　　本题有多组测试数据，每组第一行给出三个整数，分别为车站数量n，公交线路数m，朋友家s，之后m行为公交线路信息，每行包含3个整数分别为起点站p，终点站q，需要时间t，输入完公交线路后下一行给出一个整数w代表可以使用的起点站数量，再下一行给出w个整数，为可用起点。要求输入到达朋友家少需要多长时间。

　　通过分析可以得知本题是最短路问题，我们使用dijkstra寻找最短时间

　　用一个数组minTime储存从起点到所有公交站的最短时间，一个布尔数组vis记录是否固定对应公交站的最短时间。初始状态将到所有公交站的最短时间都视为无穷大，从起点开始标记，记录起点到自己的最短时间为0，之后每次找到并固定到达起点用时最短的公交车站，固定一个公交站后查看以站为媒介是否能使还没有固定的车站到达起点的时间减短，如果能减短，将优化后的时间记入该车站对应的minTime中。

　　由于本题初始可用车站有多个点，若依次以这些点为起点进行dijkstra运算会消耗大量的时间，我们不妨设立一个“超级原点”即（0，0）点，之后让“超级原点”到达所有初始可用车站的时间都为0，以“超级原点”为起点进行dijkstra运算即可得到前往所有车站的最短时间。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int inf = 0x3fffffff; //无穷大
const int maxn = 1e4+100;
int G[maxn][maxn];  //G为车站邻接矩阵
int minTime[maxn];  //minTime记录起点到每个车站的最短时间
bool vis[maxn] = {false};   //vis记录抵达当前车站的最短时间是否已经固定
int n, m;   //n为车站数，m为线路数
bool dijkstra(int s){   //传入终点
    //由于可能出现无法抵达朋友家的情况（即起点终点不连通）
    //dijkstra设为bool类型若不连通返回false
    memset(vis, false, sizeof(vis));    //初始化所有车站为不固定
    fill(minTime, minTime + n + 1, inf);    //初始化起点到所有车站的最短时间为无穷大
    minTime[0] = 0; //起点到本身最短时间为0
    for(int i = 0; i <= n; i++){    //遍历所有车站
        int minStation = -1;    //minStation记录当前到起点用时最短的点
        int nowMinTime = inf;   //nowMinTime记录起点到当前用时最短的点所需时间
        for(int j = 0; j <= n; j++){    //寻找minStation与nowMinTime
            if(!vis[j] && minTime[j] < nowMinTime){
                minStation = j;
                nowMinTime = minTime[j];
            }
        }
        if(minStation == -1){   //如果找不到用时最短的点证明之后的点与起点不连通
            if(minTime[s] != inf)   //判断朋友家与起点是否连通
                return true;
            else
                return false;
        }
        vis[minStation] = true; //将当前到起点用时最短的车站到起点的时间固定
        for(int j = 0; j <= n; j++){    //以该站为媒介判断是否能优化
            if(!vis[j] && G[minStation][j] != inf && minTime[j] > G[minStation][j] + minTime[minStation]){
                //如果能找到
                //1、时间未固定
                //2、与该站之间有公交线路
                //3、以该点为媒介可以优化到起点的时间
                //就进行优化
                minTime[j] = G[minStation][j] + minTime[minStation];
            }
        }
    }
    return true;
}
int main()
{
    int s;
    while(scanf("%d%d%d", &n, &m, &s) != EOF){  //输入车站数，线路数，朋友家所在的公交站
        for(int i = 0; i <= n; i++){    //初始化每个公交站到除了自己之外的所有站的耗时都为无穷大
            for(int j = 0; j <= n; j++){
                i == j ? G[i][j] = 0 : G[i][j] = inf;
            }
        }
        for(int i = 0; i < m; i++){
            int p, q, t;
            scanf("%d%d%d", &p, &q, &t);    //输入公交线路线路信息
            if(G[p][q] < t) //可能出现两个站之间有多条线路的情况，只取耗时最短的一条
                continue;
            G[p][q] = t;
        }
        int w;  //输入初始可用车站数
        scanf("%d", &w);
        for(int i = 0; i < w; i++){
            int temp;
            scanf("%d", &temp); //输入初始可用车站
            G[0][temp] = 0; //超级原点到初始可用车站耗时为0
        }
        if(dijkstra(s)){    //如果朋友家可达
            printf("%d\n", minTime[s]); //输出最短耗时
        }else{
            printf("-1\n"); //不可达输出-1
        }
    }
    return 0;
}
```

