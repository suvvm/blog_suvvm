---
title: HDU 3085 Nightmare Ⅱ
date: 2018-12-05 20:00:52
categories: 
- 算法相关
tags:
- BFS（广度优先搜索)
- HDU
---
<meta name="referrer" content="no-referrer"/>
　　Last night, little erriyue had a horrible nightmare. He dreamed that he and his girl friend were trapped in a big maze separately. More terribly, there are two ghosts in the maze. They will kill the people. Now little erriyue wants to know if he could find his girl friend before the ghosts find them. 
　　You may suppose that little erriyue and his girl friend can move in 4 directions. In each second, little erriyue can move 3 steps and his girl friend can move 1 step. The ghosts are evil, every second they will divide into several parts to occupy the grids within 2 steps to them until they occupy the whole maze. You can suppose that at every second the ghosts divide firstly then the little erriyue and his girl friend start to move, and if little erriyue or his girl friend arrive at a grid with a ghost, they will die. 
　　Note: the new ghosts also can devide as the original ghost. 

Input

　　The input starts with an integer T, means the number of test cases. 
　　Each test case starts with a line contains two integers n and m, means the size of the maze. (1<n, m<800) 
　　The next n lines describe the maze. Each line contains m characters. The characters may be: 
　　‘.’ denotes an empty place, all can walk on. 
　　‘X’ denotes a wall, only people can’t walk on.
　　‘M’ denotes little erriyue 
　　‘G’ denotes the girl friend. 
　　‘Z’ denotes the ghosts. 
　　It is guaranteed that will contain exactly one letter M, one letter G and two letters Z. 
Output

　　Output a single integer S in one line, denotes erriyue and his girlfriend will meet in the minimum time S if they can meet successfully, or output -1 denotes they failed to meet.

Sample Input

```
3
5 6
XXXXXX
XZ..ZX
XXXXXX
M.G...
......
5 6
XXXXXX
XZZ..X
XXXXXX
M.....
..G...

10 10
..........
..X.......
..M.X...X.
X.........
.X..X.X.X.
.........X
..XX....X.
X....G...X
...ZX.X...
...Z..X..X
```

Sample Output

```
1
1
-1
```

解题思路：
　　本题给出测试数量t，每组测试包括两个整数n、m，之后给出一个n行m列的矩阵代表地图的初始状态。

地图中 :

　　" . "代表空地；

　　"X "代表墙；

　　"M "代表 little erriyue的初始位置；

　　"G "代表 女友的初始位置；

　　"Z "代表鬼的初始位置

　　每个图中M与G都只会有唯一的一个，Z有两个。little erriyue每一回合可以移动3个格子，女友每回合只可以移动1个格子，而鬼每回合会产生出分身（分身可以继续变出分身），覆盖周围2格内所有空地（本题所有行动只能向上下左右4个方向行动，不能斜着前进），行动的顺序是鬼先分身，之后little erriyue与女友行动。如果两人终的任何一人到达有鬼魂的格子就会被宰杀。每一回合算一个小时，要求输出little erriyue最短的汇合时间，若不能汇合输出-1.

　　本题可以使用广域深度搜索，定义一个结构体node储存little erriyue、女友与鬼魂的位置。在输入后先确定little erriyue、女友与鬼魂的位置，用step记录回合数，每回合little erriyue、女友各行动一次，所搜所有可达位置，判断目标位置与鬼魂的曼哈顿距离。由于鬼每一次覆盖其周围所有距离为2的格子，即曼哈顿距离为2的所有位置，所以判断某位置是否已经被鬼的分身占据，只需判断当前的回合数*2与鬼位置的曼哈度距离大小即可。*
*

　　曼哈顿距离：

![img](https://img2018.cnblogs.com/blog/1447131/201812/1447131-20181205191058576-1094477089.jpg)

　　上图红线、蓝线与黄线都为两点间等价的曼哈顿距离，绿线则代表欧氏距离（直线距离）。

　　曼哈顿距离就是图中两点间水平距离加竖直距离，也就是只能上下左右四个方向移动时两点间的最短距离。

　　本题与鬼魂曼哈顿距离判断函数：

```c++
bool judge(int x, int y, int step){ //传入值为要判断点的坐标与当前回合数
    if(x < 0 || y < 0 || x >= n || y >= m)  //如果传入的点超过地图的边界，该点不能走，返回false
        return false;
    else if(mapn[x][y] == 'X')  //如果该点是墙，该点不能走，返回false
        return false;
    //如果该点与第一个鬼魂的曼哈顿距离小于回合数的两倍则第一个鬼魂已经覆盖了该点，该点不能走，返回false
    //鬼魂每回合可以覆盖两格所以是回合数的两倍
    else if( (abs(x - lghost1.x) + abs(y - lghost1.y)) <= step * 2)
        return false;
    //如果该点与第二个鬼魂的曼哈顿距离小于回合数的两倍则第而个鬼魂已经覆盖了该点，该点不能走，返回false
    else if( (abs(x - lghost2.x) + abs(y - lghost2.y)) <= step * 2)
        return false;
    //只有上述条件都不满足时返回true
    return true;
}
```

　　对于little erriyue和女友我们可以利用广搜每回合搜索他们可以抵达的点判断是否可以汇合。

　　分别用两个队列表示little erriyue与女友当前回合可用的起始位置（上一回合的可达位置）

　　对于little erriyue，他每回合可以行动3格，我们就让他每次行动一格搜索3次即可

　　little erriyue：

```c++
bool bfsLE(int step){   //传入的值为当前回合数
    int t = 3;  //行动3次
    while(t--){
        int sum = littleErriyue.size(); 
        //sum为当前队列中元素的数量，代表当前回合的起始位置数量（即上一回合可达位置数量）
        while(sum--){   //遍历所有起始位置
            node top = littleErriyue.front();   
            littleErriyue.pop();
            //记录当前首位并出队
            if(!judge(top.x, top.y, step))  
                //由于每回合开始都是鬼鬼魂先分身，所以如果这里被鬼魂覆盖这点就不能用了
                continue;
            for(int i = 0; i < 4; i++){ //遍历当前点可以到达的4个位置
                int NewX = top.x + X[i];
                int NewY = top.y + Y[i];
                if(!visLE[NewX][NewY] && judge(NewX, NewY, step)){ 
                    //判断四个位置是否可用，vis数组用来记录目标位置是否已经走过
                    //只有没有走过，且judge为true的位置才可以使用
                    if(visGF[NewX][NewY])   //如果目标点本回合或之前女友亦可以抵达证明汇合成功
                        return true;    //返回true
                    Node.x = NewX;
                    Node.y = NewY;
                    littleErriyue.push(Node);   //新位置入队
                    visLE[NewX][NewY] = true;   //新位置标记为已经走过
                }
            }
        }
    }
    return false;
}
```

 　　女友： 

```c++
//女友行动与little erriyue类似，唯一的区别就是只行动一次
bool bfsGF(int step){   //传入的值为当前回合数
    int sum = girlFriend.size();
    //sum为当前队列中元素的数量，代表当前回合的起始位置数量（即上一回合可达位置数量）
    while(sum--){//遍历所有起始位置
        node top = girlFriend.front();
        girlFriend.pop();
         //记录当前首位并出队
        if(!judge(top.x, top.y, step))
            //由于每回合开始都是鬼鬼魂先分身，所以如果这里被鬼魂覆盖这点就不能用了
            continue;
        for(int i = 0; i < 4; i++){//遍历当前点可以到达的4个位置
            int NewX = top.x + X[i];
            int NewY = top.y + Y[i];
            if(!visGF[NewX][NewY] && judge(NewX, NewY, step)){
                //判断四个位置是否可用，vis数组用来记录目标位置是否已经走过
                    //只有没有走过，且judge为true的位置才可以使用
                if(visLE[NewX][NewY])//如果目标点本回合或之前littel erriyue亦可以抵达证明汇合成功
                    return true;//返回true
                Node.x = NewX;
                Node.y = NewY;
                girlFriend.push(Node);//新位置入队
                visGF[NewX][NewY] = true; //新位置标记为已经走过
            }
        }
    }
    return false;
}
```

 AC代码 

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 810;
const int X[4] = {1, -1, 0, 0};
const int Y[4] = {0, 0, 1, -1};
char mapn[maxn][maxn];
bool visLE[maxn][maxn] = {false};
//little erriyue走过的位置
bool visGF[maxn][maxn] = {false};
//女友走过的位置
struct node{
    int x;
    int y;
}Node, lLE, lGF, lghost1, lghost2;
//lLE little erriyue起始位置 lGF 女友起始位置 两个lghost分别为两个鬼魂的起始位置
queue<node> littleErriyue;//little erriyue当前回合可用的起始位置（上一回合的可达位置）
queue<node> girlFriend;//女友当前回合可用的起始位置（上一回合的可达位置）
int n, m;

void getLocation(){
    bool g2 = false;    //鬼魂2位置未获取
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            if(mapn[i][j] == 'M'){  //获取little erriyue起始位置
                lLE.x = i;
                lLE.y = j;
            }else if(mapn[i][j] == 'G'){//获取女友起始位置
                lGF.x = i;
                lGF.y = j;
            }else if(mapn[i][j] == 'Z'){    //获取鬼魂起始位置
                if(!g2){
                    lghost2.x = i;
                    lghost2.y = j;
                    g2 = true;
                }else{
                    lghost1.x = i;
                    lghost1.y = j;
                }
            }
        }
    }
}
bool judge(int x, int y, int step){ //传入值为要判断点的坐标与当前回合数
    if(x < 0 || y < 0 || x >= n || y >= m)  //如果传入的点超过地图的边界，该点不能走，返回false
        return false;
    else if(mapn[x][y] == 'X')  //如果该点是墙，该点不能走，返回false
        return false;
    //如果该点与第一个鬼魂的曼哈顿距离小于回合数的两倍则第一个鬼魂已经覆盖了该点，该点不能走，返回false
    //鬼魂每回合可以覆盖两格所以是回合数的两倍
    else if( (abs(x - lghost1.x) + abs(y - lghost1.y)) <= step * 2)
        return false;
    //如果该点与第二个鬼魂的曼哈顿距离小于回合数的两倍则第而个鬼魂已经覆盖了该点，该点不能走，返回false
    else if( (abs(x - lghost2.x) + abs(y - lghost2.y)) <= step * 2)
        return false;
    //只有上述条件都不满足时返回true
    return true;
}
bool bfsLE(int step){   //传入的值为当前回合数
    int t = 3;  //行动3次
    while(t--){
        int sum = littleErriyue.size(); 
        //sum为当前队列中元素的数量，代表当前回合的起始位置数量（即上一回合可达位置数量）
        while(sum--){   //遍历所有起始位置
            node top = littleErriyue.front();   
            littleErriyue.pop();
            //记录当前首位并出队
            if(!judge(top.x, top.y, step))  
                //由于每回合开始都是鬼鬼魂先分身，所以如果这里被鬼魂覆盖这点就不能用了
                continue;
            for(int i = 0; i < 4; i++){ //遍历当前点可以到达的4个位置
                int NewX = top.x + X[i];
                int NewY = top.y + Y[i];
                if(!visLE[NewX][NewY] && judge(NewX, NewY, step)){ 
                    //判断四个位置是否可用，vis数组用来记录目标位置是否已经走过
                    //只有没有走过，且judge为true的位置才可以使用
                    if(visGF[NewX][NewY])   //如果目标点本回合或之前女友亦可以抵达证明汇合成功
                        return true;    //返回true
                    Node.x = NewX;
                    Node.y = NewY;
                    littleErriyue.push(Node);   //新位置入队
                    visLE[NewX][NewY] = true;   //新位置标记为已经走过
                }
            }
        }
    }
    return false;
}
//女友行动与little erriyue类似，唯一的区别就是只行动一次
bool bfsGF(int step){   //传入的值为当前回合数
    int sum = girlFriend.size();
    //sum为当前队列中元素的数量，代表当前回合的起始位置数量（即上一回合可达位置数量）
    while(sum--){//遍历所有起始位置
        node top = girlFriend.front();
        girlFriend.pop();
         //记录当前首位并出队
        if(!judge(top.x, top.y, step))
            //由于每回合开始都是鬼鬼魂先分身，所以如果这里被鬼魂覆盖这点就不能用了
            continue;
        for(int i = 0; i < 4; i++){//遍历当前点可以到达的4个位置
            int NewX = top.x + X[i];
            int NewY = top.y + Y[i];
            if(!visGF[NewX][NewY] && judge(NewX, NewY, step)){
                //判断四个位置是否可用，vis数组用来记录目标位置是否已经走过
                    //只有没有走过，且judge为true的位置才可以使用
                if(visLE[NewX][NewY])//如果目标点本回合或之前littel erriyue亦可以抵达证明汇合成功
                    return true;//返回true
                Node.x = NewX;
                Node.y = NewY;
                girlFriend.push(Node);//新位置入队
                visGF[NewX][NewY] = true; //新位置标记为已经走过
            }
        }
    }
    return false;
}

int main()
{
    int t;
    scanf("%d", &t);    //输入测试数量
    while(t--){
        scanf("%d%d", &n, &m);  //输入n与m
        memset(visLE, false, sizeof(visLE));
        memset(visGF, false, sizeof(visGF));
        //初始化两个vis数组为false
        for(int i = 0; i < n; i++){//输入地图
            scanf("%s", mapn[i]);
        }
        getLocation();  //获得各特殊元素位置
        //清空两个队列
        while(!littleErriyue.empty()){
            littleErriyue.pop();
        }
        while(!girlFriend.empty()){
            girlFriend.pop();
        }
        //little erriyue与女友的初始位置入队
        littleErriyue.push(lLE);
        girlFriend.push(lGF);
        visLE[lLE.x][lLE.y] = true;
        visGF[lGF.x][lGF.y] = true;
        int step = 0;   //step记录时间（回合数）
        bool flag = false;  //flag 记录是否可汇合
        while(!girlFriend.empty() || !littleErriyue.empty()){
            step++;
            if(bfsLE(step)){    //little erriyue行动
                printf("%d\n", step);
                flag = true;
                break;
            }
            if(bfsGF(step)){    //女友行动
                printf("%d\n", step);
                flag = true;
                break;
            }
        }
        if(!flag){  //不能汇合
            printf("-1\n");
        }
    }
    return 0;
}
```

