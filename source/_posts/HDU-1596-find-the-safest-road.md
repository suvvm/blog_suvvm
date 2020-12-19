---
title: HDU 1596 find the safest road
date: 2018-11-13 22:56:22
categories: 
- 算法相关
tags:
- 最短路
- HDU
---

XX星球有很多城市，每个城市之间有一条或多条飞行通道，但是并不是所有的路都是很安全的，每一条路有一个安全系数s,s是在 0 和 1 间的实数(包括0，1)，一条从u 到 v 的通道P 的安全度为Safe(P) = s(e1)*s(e2)…*s(ek) e1,e2,ek是P 上的边 ，现在8600 想出去旅游，面对这这么多的路，他想找一条最安全的路。但是8600 的数学不好，想请你帮忙 ^_^。

Input

　　输入包括多个测试实例，每个实例包括： 
　　第一行：n。n表示城市的个数n<=1000; 
　　接着是一个n*n的矩阵表示两个城市之间的安全系数，(0可以理解为那两个城市之间没有直接的通道) 接着是Q个8600要旅游的路线,每行有两个数字，表示8600所在的城市和要去的城市。

Output

　　如果86无法达到他的目的地，输出"What a pity!", 其他的输出这两个城市之间的最安全道路的安全系数,保留三位小数。

Sample Input

```
3
1 0.5 0.5
0.5 1 0.4
0.5 0.4 1
3
1 2
2 3
1 3
```

Sample Output

```
0.500
0.400
0.500
```

解题思路：
　　本题有多组测试数据，每组给出城市的数量与其安全系数邻接矩阵，之后给出旅游线路数量与每条线路的起点与终点。

　　本题几乎是dijkstra模板题，本题是使用dijkstra寻找最大安全系数，注意对安全系数是进行乘法运算。

　　用一个double数组safety储存从起点到所有城市的最大安全系数，一个布尔数组记录是否固定对应城市的最大安全系数。初始状态将到所有城市的安全系数视为0，从起点开始标记，记录起点到自己的安全系数为1并将其固定，之后每次找到并固定到达起点安全系数最大的城市，固定一个城市后查看以该城市为媒介是否能使还没有固定的城市到达起点的安全系数增高，如果能优化，将优化后的记入该城市对应的safety中。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+10;
int n, q, s;
double G[maxn][maxn];   //G记录邻接矩阵
double safety[maxn];    //safety记录起点到各城市的最大安全系数
bool vis[maxn] = {false};   //vis记录城市最大安全系数是否已经固定

void dijkstra(int s){   //传入起点
    memset(safety, 0, sizeof(safety));  //初始化起点到所有城市的最大安全系数为0
    memset(vis, false, sizeof(vis));    //所有城市都为未固定状态
    safety[s] = 1.0;    //起点到自己的最大安全系数为1
    for(int i = 0; i < n; i++){
        //开始计算并固定城市最大安全系数，需要将每个城市都固定所以循环n次
        int maxSafetyCity = -1;
        double maxSafetyNum = -1;
        for(int j = 0; j < n; j++){ //找到还没有固定的城市中到起点安全系数最大的城市
            if(!vis[j] && safety[j] > maxSafetyNum){
                maxSafetyCity = j;  //maxSafeCity记录还没有固定的城市中到起点安全系数最大的城市
                maxSafetyNum = safety[j];   //maxSafetyNum记录当前最大安全系数
            }
        }
        if(maxSafetyCity == -1){    //如果找不到符合条件的城市，证明其余城市与起点不连通
            return;
        }
        vis[maxSafetyCity] = true;  //固定安全系数最大的城市
        for(int j = 0; j < n; j++){ //寻找以该城市为媒介可以优化的城市
            if(!vis[j] && G[maxSafetyCity][j] != 0 && G[maxSafetyCity][j] * safety[maxSafetyCity] > safety[j]){
                //可以优化的条件为，该城市没有固定
                //该城市与媒介城市连通
                //从媒介城市到该城市的安全系数乘以媒介城市到起点的安全系数比不优化前该城市到达起点的安全系数高
                safety[j] = safety[maxSafetyCity] * G[maxSafetyCity][j];
                //优化
            }
        }
    }
}
int main()
{
    while(scanf("%d", &n) != EOF){  //输入城市数量
        for(int i = 0; i < n; i++){
            for(int j = 0; j < n; j++){
                scanf("%lf", &G[i][j]); //输入邻接矩阵
            }
        }
        scanf("%d", &q);    //输入旅行路线
        int ed;
        for(int i = 0; i < q; i++){
            scanf("%d%d", &s, &ed); //输入起点与终点
            dijkstra(s - 1);    //计算起点到所有城市的最大安全系数
            if(safety[ed - 1] != 0){
                printf("%.3f\n", safety[ed - 1]);   //格式化输出最大安全系数
            }else{
                printf("What a pity!\n");   //不连通
            }
        }
    }
    return 0;
}
```

