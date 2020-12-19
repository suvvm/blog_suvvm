---
title: POJ 2251 Dungeon Master
date: 2018-12-06 20:11:27
categories: 
- 算法相关
tags:
- BFS（广度优先搜索)
- POJ
---

　　You are trapped in a 3D dungeon and need to find the quickest way out! The dungeon is composed of unit cubes which may or may not be filled with rock. It takes one minute to move one unit north, south, east, west, up or down. You cannot move diagonally and the maze is surrounded by solid rock on all sides. 

　　Is an escape possible? If yes, how long will it take? 

Input

　　The input consists of a number of dungeons. Each dungeon description starts with a line containing three integers L, R and C (all limited to 30 in size). 
　　L is the number of levels making up the dungeon. 
　　R and C are the number of rows and columns making up the plan of each level. 
Then there will follow L blocks of R lines each containing C characters. Each character describes one cell of the dungeon. A cell full of rock is indicated by a '#' and empty cells are represented by a '.'. Your starting position is indicated by 'S' and the exit by the letter 'E'. There's a single blank line after each level. Input is terminated by three zeroes for L, R and C.

Output

　　Each maze generates one line of output. If it is possible to reach the exit, print a line of the form 

 Escaped in x minute(s). 

 where x is replaced by the shortest time it takes to escape. 
　　If it is not possible to escape, print the line  

 Trapped! 

Sample Input

```
3 4 5
S....
.###.
.##..
###.#

#####
#####
##.##
##...

#####
#####
#.###
####E

1 3 3
S##
#E#
###

0 0 0
```

Sample Output

```
Escaped in 11 minute(s).
Trapped!
```

解题思路：
　　本题有多组测数据，每组数据包括第一行输入3个整数，分别为 l 地牢层数、 r 每次地牢行数， c 每层地牢列数。

　　之后输入 l 个矩阵，分别为地牢每层的状态。

　　其中' # '表示该位置充满岩石；

　　' . '表示空地；

　　' S '表示起点；

　　' E '表示终点；

　　以 l r c 输入都为0为结束标准。

　　要求按格式输出从起点到终点的最短时间，若不能逃脱输出Trapped!

　　本题可以使用bfs基本思想为，从起点开始向周围东西南北上下6个方向搜索，使用一个队列记录当前可用起点，每次取出一个点并将其可以抵达且还没有抵达过的点加入队列，并将其步数记录为先前点的步数加一，当找到终点时，到终点的最短时间就是到终点所需的步数，若直到队列为空，即没有可用起点，则证明终点不可达，输出Trapped!

　　这里将 l 视为z、 将r 视为x，将c视为y，使用一个char型的三维数组mapn记录地牢，由于给出的每个矩阵代表每层的信息，所以输入矩阵时按层输入。用三维布尔数组vis记录地牢内该位置是否已经抵达过，用一个结构体node记录该点信息与抵达该点所需的步数。

```c++
int BFS(int x, int y, int z){   //传入起点位置
    Node.x = x;
    Node.y = y;
    Node.z = z;
    Node.step = 0;
    //记录抵达起点所需的步数为0
    vis[x][y][z] = true;
    //标记起点为已抵达
    queue<node> Q;
    Q.push(Node);
    //起点入队
    while(!Q.empty()){
        node top = Q.front();
        //取出队首点寻找其周围可达的位置
        if(top.x == ex && top.y == ey && top.z == ez)
            return top.step;    //若队首为终点证明可以逃脱，返回所需步数
        Q.pop();    //队首点出队
        for(int i = 0; i < 6; i++){ //遍历周围四个位置
            //newX newY newZ为新的位置
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            int newZ = top.z + Z[i];
            if(judge(newX, newY, newZ)){    //判断新的位置是否可以走
                Node.x = newX;
                Node.y = newY;
                Node.z = newZ;
                Node.step = top.step + 1;   //记录可以走的新的位置步数为top步数加一
                vis[newX][newY][newZ] = true;   //标记新位置为已抵达
                Q.push(Node);   //新位置入队
            }
        }
    }
    return -1;  //若不可达返回-1
}
```

​		判断位置可以走的要求时不超地牢边界不是岩石且之前没走过。

　　AC代码

```c++
#include <iostream>
#include <queue>
#include <cstdio>
#include <cstring>
using namespace std;
const int maxn = 35;
int l, r, c;    //记录层数列数与行数
int bx, by, bz, ex, ey, ez;
//记录起点与终点坐标
char mapn[maxn][maxn][maxn];    //记录地牢
bool vis[maxn][maxn][maxn] = {false};   //某点是否走过
//周围6个方向
const int X[6] = {0, 0, 0, 0, 1, -1};
const int Y[6] = {0, 0, 1, -1, 0, 0};
const int Z[6] = {1, -1, 0, 0, 0, 0};
struct node{
    int x, y, z;    //记录当前点位置
    int step;   //记录当前点步数
}Node;
bool judge(int x, int y, int z){
    if(x < 0 || x >= r || y < 0 || y >= c || z < 0 || z >= l)
        return false;   //超边界返回false
    if(mapn[x][y][z] == '#' || vis[x][y][z])
        return false;   //目标点为岩石或之前走过返回false
    return true;
}
int BFS(int x, int y, int z){   //传入起点位置
    Node.x = x;
    Node.y = y;
    Node.z = z;
    Node.step = 0;
    //记录抵达起点所需的步数为0
    vis[x][y][z] = true;
    //标记起点为已抵达
    queue<node> Q;
    Q.push(Node);
    //起点入队
    while(!Q.empty()){
        node top = Q.front();
        //取出队首点寻找其周围可达的位置
        if(top.x == ex && top.y == ey && top.z == ez)
            return top.step;    //若队首为终点证明可以逃脱，返回所需步数
        Q.pop();    //队首点出队
        for(int i = 0; i < 6; i++){ //遍历周围四个位置
            //newX newY newZ为新的位置
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            int newZ = top.z + Z[i];
            if(judge(newX, newY, newZ)){    //判断新的位置是否可以走
                Node.x = newX;
                Node.y = newY;
                Node.z = newZ;
                Node.step = top.step + 1;   //记录可以走的新的位置步数为top步数加一
                vis[newX][newY][newZ] = true;   //标记新位置为已抵达
                Q.push(Node);   //新位置入队
            }
        }
    }
    return -1;  //若不可达返回-1
}
int main()
{
    while(scanf("%d%d%d", &l, &r, &c) != EOF && l && r &&c){
        for(int z = 0; z < l; z++){ //按层输入
            for(int x = 0; x < r; x++){
                for(int y = 0; y < c; y++){
                    cin >> mapn[x][y][z];
                    if(mapn[x][y][z] == 'S'){   //获取起点终点位置
                        bx = x, by = y, bz = z;
                    }
                    if(mapn[x][y][z] == 'E'){
                        ex = x, ey = y, ez = z;
                    }
                }
            }
        }
        memset(vis, false, sizeof(vis));
        //初始化所有点为未抵达
        int ans = BFS(bx, by, bz);
        //获取步数
        if(ans == -1){  //-1为不逃脱
            printf("Trapped!\n");
        }else{
            printf("Escaped in %d minute(s).\n", ans);
        }
    }
    return 0;
}
```

