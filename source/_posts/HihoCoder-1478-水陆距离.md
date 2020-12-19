---
title: HihoCoder 1478 水陆距离
date: 2019-01-28 20:00:37
categories: 
- 算法相关
tags:
- HihoCoder
- BFS（广度优先搜索)
---

### 描述

　　给定一个N x M的01矩阵，其中1表示陆地，0表示水域。对于每一个位置，求出它距离最近的水域的距离是多少。  

　　矩阵中每个位置与它上下左右相邻的格子距离为1。

### 输入

　　第一行包含两个整数，N和M。

　　以下N行每行M个0或者1，代表地图。

　　数据保证至少有1块水域。

　　对于30%的数据，1 <= N, M <= 100  

　　对于100%的数据，1 <= N, M <= 800

### 输出

　　输出N行，每行M个空格分隔的整数。每个整数表示该位置距离最近的水域的距离。

样例输入

```
4 4  
0110  
1111  
1111  
0110
```

样例输出

```
0 1 1 0  
1 2 2 1  
1 2 2 1  
0 1 1 0
```

时间限制:10000ms

单点时限:1000ms

内存限制:256MB

题目解析
　　本题给出n m之后给出一个n * m的水陆分布图0代表水1代表路，要求输出每个点距离其最近的水的距离所组成的距离矩阵。

　　本题使用BFS获取每个点到0点的最近距离，但如果暴力所有点的话是要超时的，结合BFS的特性，BFS每次入队的都是上一个点可以一步抵达的点，那么我们初始将所有的0点入队，以这些0点像周围搜索，记录每个点的最短入队层次即可。

　　AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 810;
struct node{
    int x, y;
    int step;   //step记录入队的层次
}Node;
int G[MAX][MAX];    //记录给出的图
int n, m;
bool vis[MAX][MAX]; //bool判断每个点是否已经走过
int ans[MAX][MAX];  //记录每个点距离水的最近距离
//上下左右四个方向
int X[4] = {1, 0, -1, 0};
int Y[4] = {0, 1, 0, -1};
bool judge(int x, int y){   //判断某位置是否可以走
    if(x < 0 || y < 0 || x >= m || y >= n)  //超边界
        return false;
    if(vis[y][x] == true)   //已经走过
        return false;
    return true;
}
queue<node> Q;
void BFS(){
    while(!Q.empty()){
        node top = Q.front();
        ans[top.y][top.x] = top.step;   //每个点的入队层数便是其距离水的最近距离
        Q.pop();
        for(int i = 0; i < 4; i++){ //判断上下左右四个方向的点是否可以走
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            if(judge(newX, newY)){  //若可以走将新的点入队并标记为已走过，层数为前一个点加一
                vis[newY][newX] = true;
                Node.x = newX;
                Node.y = newY;
                Node.step = top.step + 1;
                Q.push(Node);
            }
        }
    }
}
int main()
{
    scanf("%d%d", &n, &m);  //输入nn
    for(int i = 0; i < n; i++){ //输入n * m的矩阵
        string str;
        cin >> str;
        for(int j = 0; j < m; j++){
            G[i][j] = str[j] - '0';
        }
    }
    for(int i = 0; i < n; i++){ //将所有的0预先入队
        for(int j = 0; j < m; j++){
            if(G[i][j] == 0){
                Node.x = j;
                Node.y = i;
                Node.step = 0;
                vis[i][j] = true;
                Q.push(Node);
            }
        }
    }
    BFS();  //搜索获得答案
    for(int i = 0; i < n; i++){ //输出最短距离矩阵
        for(int j = 0; j < m; j++){
            if(j != 0)
                printf(" ");
            printf("%d", ans[i][j]);
        }
        printf("\n");
    }
    return 0;
}
```

