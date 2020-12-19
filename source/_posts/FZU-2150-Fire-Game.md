---
title: FZU 2150 Fire Game
date: 2019-02-24 17:20:57
categories: 
- 算法相关
tags:
- FZU
- BFS（广度优先搜索)
---

　　Fat brother and Maze are playing a kind of special (hentai) game on an N*M board (N rows, M columns). At the beginning, each grid of this board is consisting of grass or just empty and then they start to fire all the grass. Firstly they choose two grids which are consisting of grass and set fire. As we all know, the fire can spread among the grass. If the grid (x, y) is firing at time t, the grid which is adjacent to this grid will fire at time t+1 which refers to the grid (x+1, y), (x-1, y), (x, y+1), (x, y-1). This process ends when no new grid get fire. If then all the grid which are consisting of grass is get fired, Fat brother and Maze will stand in the middle of the grid and playing a MORE special (hentai) game. (Maybe it’s the OOXX game which decrypted in the last problem, who knows.)

　　You can assume that the grass in the board would never burn out and the empty grid would never get fire.

　　Note that the two grids they choose can be the same.

Input

　　The first line of the date is an integer T, which is the number of the text cases.

　　Then T cases follow, each case contains two integers N and M indicate the size of the board. 　　Then goes N line, each line with M character shows the board. “#” Indicates the grass. You can assume that there is at least one grid which is consisting of grass in the board.

　　1 <= T <=100, 1 <= n <=10, 1 <= m <=10

Output

　　For each case, output the case number first, if they can play the MORE special (hentai) game (fire all the grass), output the minimal time they need to wait after they set fire, otherwise just output -1. See the sample input and output for more details.

Sample Input

```
4
3 3
.#.
###
.#.
3 3
.#.
#.#
.#.
3 3
...
#.#
...
3 3
###
..#
#.#
```

Sample Output

```
Case 1: 1
Case 2: -1
Case 3: 0
Case 4: 2
```

题目解析
　　本题给出T代表测试数据组数，每组给出两个整数n， m之后给出一个n*m的矩阵，矩阵由字符 ' . ' 与 ' # ' 组成，' . '表示空地，' # '表示草地，两个人每人可以选择一个草地点火（可以选择同一个草地），火势每一次可以向其上下左右四个格的草地蔓延（空地不行），若火势可以在数次蔓延后覆盖全部草地，则要求求出最少的蔓延次数.

　　对于每次选定草地后，可以以选择的两块草地为起点向周围广搜，记录火势蔓延之其所需的步数。默认所需步数为无穷大，则之后若还存在所需步数为无穷大的则选择这两个点不能使火势覆盖所有草地。若可以覆盖所有草地，便找到步数最大的草地，其步数便是以这两个点为起点覆盖所有草地所需的步数。

　　遍历所有取法找寻最小所需步数即可获得答案。

```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <iostream>
#include <queue>
//poj的题不能用万能头文件bits/stdc++.h
using namespace std;
const int MAX = 15;
const int X[4] = {0, 0, 1, -1};
const int Y[4] = {1, -1, 0, 0};
struct node{
    int x, y;
    int step;
}Node;
node bg[MAX * MAX]; //bg记录所有草地所在的位置
int step[MAX][MAX]; //step[x][y]记录点火后火势蔓延到(x,y)位置的草地所需的步数
int m, n;   //记录输入矩阵大小
bool vis[MAX][MAX] = {false};   //记录某一个位置是否已覆盖
string G[MAX];  //记录输入的矩阵
bool judge(int x,int y){    //判断某个位置是否可以进行覆盖
    if(x >= n || x < 0 || y >= m || y < 0)
        return false;
    if(G[x][y] != '#')
        return false;
    if(vis[x][y])
        return false;
    return true;
}
void BFS(node a, node b){   //广搜获取以a, b为起点的蔓延情况
    memset(vis, false, sizeof(vis));
    fill(step[0], step[0] + MAX * MAX, INT_MAX);    //初始化到蔓延到所有草地所需步数为无穷大
    queue<node> Q;
    Q.push(a);
    Q.push(b);
    //起点入队
    vis[a.x][a.y] = true;
    vis[b.x][b.y] = true;
    //记录a b两块草地为已覆盖
    while(!Q.empty()){
        node top = Q.front();
        step[top.x][top.y] = min(top.step, step[top.x][top.y]);
        //记录覆盖x y 位置的草地的最小步数
        Q.pop();
        for(int i = 0; i < 4; i++){ //判断上下左右四个点
            int newX = top.x + X[i];
            int newY = top.y + Y[i];
            if(judge(newX, newY)){  //若可以覆盖则将其入队
                Node.x = newX, Node.y = newY;
                Node.step = top.step + 1;
                vis[newX][newY] = true;
                Q.push(Node);
            }
        }
    }
}
int main()
{
    int t, Case = 1;
    scanf("%d", &t);
    //t为测试数量
    //Case为当前测试编号
    while(Case <= t){
        int cnt = 0;    //cnt记录当前矩阵中草地总数
        scanf("%d%d", &n, &m);  //输入矩阵大小
        for(int i = 0; i < n; i++){ //输入矩阵
            cin >> G[i];
            for(int j = 0; j < m; j++){
                if(G[i][j] == '#'){ //记录草地位置
                    node temp;
                    temp.x = i;
                    temp.y = j;
                    temp.step = 0;
                    bg[cnt] = temp;
                    cnt++;
                }
            }
        }
        printf("Case %d: ", Case);
        Case++;
        if(cnt <= 2){   //草地小于两块可以直接覆盖不需要蔓延，输出0
            printf("0\n");
            continue;
        }
        int tans = 0, ans = INT_MAX;
        //tans记录每次选择两点后覆盖所有草地所需的次数
        for(int i = 0; i < cnt; i++){   //遍历所有选择情况
            for(int j = i; j < cnt; j++){
                BFS(bg[i], bg[j]);
                tans = 0;
                for(int k = 0; k < cnt; k++){
                    tans = max(tans, step[bg[k].x][bg[k].y]);
                    //获取该选择下所需步数
                }
                
                ans = min(ans, tans);
                //记录最小步数
            }
        }
        if(ans == INT_MAX)  //无法覆盖所有草地
            printf("-1\n");
        else
            printf("%d\n", ans);
    }
    return 0;
}
```

