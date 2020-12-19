---
title: PTA (Advanced Level) 1003 Emergency
date: 2019-04-01 12:29:36
categories: 
- 算法相关
tags:
- DFS（深度优先搜索）
- 最短路
- PTA
---

　　As an emergency rescue team leader of a city, you are given a special map of your country. The map shows several scattered cities connected by some roads. Amount of rescue teams in each city and the length of each road between any pair of cities are marked on the map. When there is an emergency call to you from some other city, your job is to lead your men to the place as quickly as possible, and at the mean time, call up as many hands on the way as possible.

### Input Specification:

　　Each input file contains one test case. For each test case, the first line contains 4 positive integers: N (≤) - the number of cities (and the cities are numbered from 0 to N−1), M- the number of roads, C1 and C2 - the cities that you are currently in and that you must save, respectively. The next line contains N integers, where the i-th integer is the number of rescue teams in the i-th city. Then M lines follow, each describes a road with three integers c1, c2 and L, which are the pair of cities connected by a road and the length of that road, respectively. It is guaranteed that there exists at least one path from C1 to C2.

### Output Specification:

　　For each test case, print in one line two numbers: the number of different shortest paths between C1 and C2, and the maximum amount of rescue teams you can possibly gather. All the numbers in a line must be separated by exactly one space, and there is no extra space allowed at the end of a line.

### Sample Input:

```
5 6 0 2
1 2 1 5 3
0 1 1
0 2 2
0 3 1
1 2 1
2 4 1
3 4 1
```

### Sample Output:

```
2 4
```

题目解析

  本题有若干个城市与若干条连接城市的道路，每个城市中会有一定数量的救援队，要求解初给出两城市之间的最短路条数，并求出在最短路的情况下所能聚集的救援队的最大数量。

​    第一行给出4个正整数，分别为N(<=500)-城市数量（城市编号由0 ~ N-1，M-道路的条数，C1、C2 – 给出的两个城市。第二行给出N个整数，分别为第0 ~ N-1号城市所拥有的救援队数量。之后给随M行表示道路信息，每行包括道路两端的城市c1、 c2 与道路长度L。

​    由于城市最高数量只有500，所以可以之间用一个二维数组来储存道路信息，用一维数组teamCnt储存每个城市所拥有的救援队数量，vector<int>pre[i] 存储i城市的前驱城市，之后dijkstra获取每个城市的前驱，这样便可以构建出一张由最短路径组成的邻接表。之后dfs这张邻接表获取C1到C2的最短路径数量与最多课聚集救援队数量即可。

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 550;
int n, m, c1, c2;
int G[MAX][MAX];
int teamCnt[MAX];
int d[MAX];
bool vis[MAX] = {false};
vector<int> pre[MAX];
void dijkstra(int bg){
    fill(d, d + n, INT_MAX);    //初始化起点到任何点的距离都为正无穷
    d[bg] = 0;  //起点到本身的距离为0
    for(int i = 0; i < n ; i++){
        int minCity = -1, minDis = INT_MAX;
        //minCity保存最近的城市    minDis保存最近的距离
        for(int j = 0; j < n; j++){ //找到当前还未访问过的距离起点最近的城市
            if(d[j] < minDis && !vis[j]){
                minDis = d[j];
                minCity = j;
            }
        }
        if(minCity == -1)   //若minCity为-1证明其他城市都不与起点连通
            return;
        vis[minCity] = true;    //将找出的最近城市标记为已访问
        for(int next = 0; next < n; next++){    //遍历所有点
            if(!vis[next] && d[next] > d[minCity] + G[minCity][next] && G[minCity][next] != 0){
                //若存在未访问的点next 与起点之间的距离可被minCity优化 优化该点
                d[next] = d[minCity] + G[minCity][next];
                pre[next].clear();
                pre[next].push_back(minCity);
                //将next的前驱清空并将minCity计入next的前驱
            }else if(!vis[next] && d[next] == d[minCity] + G[minCity][next] && G[minCity][next] != 0){
                //若存在未访问的点next 与起点之间的距离和以minCity为中转的距离相等
                pre[next].push_back(minCity);
                //将minCity计入next的前驱
            }
        }
    }
}
int maxTeamCnt = INT_MIN;
//记录最大聚集救援队数量
int roadCnt = 0;
//记录最短路数量
void dfs(int ed, int nowTeamCnt){   //由终点向起点搜索 nowTeamCnt表示当前聚集的救援队数量
    if(ed == c1){   //若搜索到起点
        maxTeamCnt = max(maxTeamCnt, nowTeamCnt);   //获取最大聚集数量
        roadCnt++;  //最短路加一
    }
    for(auto i : pre[ed]){  //遍历ed的前驱
        if(!vis[i]){    //若前驱还每有被计入道路
            vis[i] = true;
            dfs(i, nowTeamCnt + teamCnt[i]);
            vis[i] = false;
        }
    }
}
int main()
{
    scanf("%d%d%d%d", &n, &m, &c1, &c2);
    //输入城市数量n 道路数量m 要计算最短路的城市c1与c2
    for(int i = 0; i < n; i++)  //输入每个城市的救援队数量
        scanf("%d", &teamCnt[i]);
    memset(G, 0, sizeof(G));    //初始化任意两个城市之间距离为0
    for(int i = 0; i < m; i++){ //输入道路信息
        int u, v;
        scanf("%d%d", &u, &v);
        scanf("%d", &G[u][v]);
        G[v][u] = G[u][v];
    }
    dijkstra(c1);   //最短路以c1为起点获取前驱
    memset(vis, false, sizeof(vis));
    dfs(c2, teamCnt[c2]);
    printf("%d %d\n", roadCnt, maxTeamCnt);
    return 0;
}
```

