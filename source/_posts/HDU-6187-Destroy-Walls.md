---
title: HDU 6187 Destroy Walls
date: 2018-11-14 23:52:30
categories: 
- 算法相关
tags:
- 最小生成树
- 二叉搜索树
- 并查集
- HDU
---

Long times ago, there are beautiful historic walls in the city. These walls divide the city into many parts of area.
　　Since it was not convenient, the new king wants to destroy some of these walls, so he can arrive anywhere from his castle. We assume that his castle locates at (0.6∗2–√,0.6∗3–√).
　　There are n towers in the city, which numbered from 1 to n. The ith's location is (xi,yi). Also, there are m walls connecting the towers. Specifically, the ith wall connects the tower ui and the tower vi(including the endpoint). The cost of destroying the ith wall is wi.
　　Now the king asks you to help him to divide the city. Firstly, the king wants to destroy as less walls as possible, and in addition, he wants to make the cost least.
　　The walls only intersect at the endpoint. It is guaranteed that no walls connects the same tower and no 2 walls connects the same pair of towers. Thait is to say, the given graph formed by the walls and towers doesn't contain any multiple edges or self-loops.
　　Initially, you should tell the king how many walls he should destroy at least to achieve his goal, and the minimal cost under this condition.

 

Input
　　There are several test cases.
　　For each test case:
　　The first line contains 2 integer n, m.
　　Then next n lines describe the coordinates of the points.
　　Each line contains 2 integers xi,yi.
　　Then m lines follow, the ith line contains 3 integers ui,vi,wi
　　|xi|,|yi|≤105
　　3≤n≤100000,1≤m≤200000
　　1≤ui,vi≤n,ui≠vi,0≤wi≤10000

Output
　　For each test case outout one line with 2 integers sperate by a space, indicate how many walls the king should destroy at least to achieve his goal, and the minimal cost under this condition.

Sample Input

```
4 4
-1 -1
-1 1
1 1
1 -1
1 2 1
2 3 2
3 4 1
4 1 2
```

Sample Output

```
1 1
```

解题思路：
　　首先，很重要的一点，本题给出的所有坐标值都没用，都是吓人的。

　　本题的意思是有一个城堡，被数条城墙分隔为多个区域，城墙的端点只会是塔楼，并且城墙只在端点相交，一条城墙的两端不会连在同一个塔楼上（图没有自环），拆除每个城墙都会有一定消耗，国王希望通过最少的消耗，使城堡所有区域都连通。

　　本题有多组测试用例，每组测试用例包括，塔楼数量（端点数量）n， 城墙数量（边数）m，首先跟随n行，每行包括两个整数x y为塔楼的坐标（没用），之后m行跟随，每行包括三个整数，分别为城墙连接的两个塔楼u ， v，拆毁该城墙的消耗w。

　　要求输出使所有区域连通所需的最少的消耗。

　　仔细思考一下就会发现，本题的最终要求就是让我们把给定的图通过抹去边变成无环图，而且抹去边的权值要尽可能的小。而将平面图变成无环图叫什么？——生成树！！

　　提到生成树，我们想到两个算法Prim算法与Kruskal算法，因为本题可以通过将边由大到小排序求的最大生成树，在这里我们使用Kruskal算法。

kruskal算法核心思想：　　

　　既然已经给出了邻接表。初始视所有塔楼都为不连通（即拆除所有城墙），之后将城墙按消耗排序，从大到小枚举所有城墙，判断城墙两端的塔楼是否已经连通，若已经连通不做处理（即该墙需要拆除）拆除的墙数加一，若不连通则将该边记录入最大生成树（该墙无需拆除），并从拆毁所有城墙的总消耗里减去该城墙的消耗。

```c++
bool cmp(edge a, edge b){   //城墙排序为拆除消耗由大到小
    return a.w > b.w;
}
LL kruskal(int n, int m, LL sum, int &cnt){ //kruskal算法
    //由于需要改变cnt的值所以在这里cnt传引用
    LL ans = sum;   //传入的sum为拆除所有城墙所需的总消耗
    for(int i = 1; i <= n; i++){
        father[i] = i;  //初始化所有塔楼为不连通
    }
    sort(Edge + 1, Edge + 1 + m, cmp);  //城墙权值从大到小排序
    for(int i = 1; i <= m; i++){
        int faNode1 = getFather(Edge[i].u);
        int faNode2 = getFather(Edge[i].v);
        if(faNode1 != faNode2){ //判断城墙连接的两个塔楼是否连通
            father[faNode1] = faNode2;  //不连通则标记为连通
            ans -= Edge[i].w;   //该城墙不需要拆数
        }else{  //如果城墙两个端点塔楼已经连通则该城墙需要拆除
            cnt++;  //记录需要拆除的城墙数量
        }
    }
    return ans; //返回的ans为拆除的最小消耗
```

 判断是否连通使用并查集 

```c++
int father[maxn];
int getFather(int x)
{
    if(father[x] == x)
        return x;
    else
        return father[x] = getFather(father[x]);

}
```

 AC代码

```c++
#include<iostream>
#include<cstring>
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long LL;
const int maxn = 2e5+100;
struct edge{    //edge储存城墙
    int u, v;   //城墙连接的两个结点
    LL w;   //拆除的消耗
}Edge[maxn];
int father[maxn];
int getFather(int x)    //并查集部分
{
    if(father[x] == x)
        return x;
    else
        return father[x] = getFather(father[x]);

}
bool cmp(edge a, edge b){   //城墙排序为拆除消耗由大到小
    return a.w > b.w;
}
LL kruskal(int n, int m, LL sum, int &cnt){ //kruskal算法
    //由于需要改变cnt的值所以在这里cnt传引用
    LL ans = sum;   //传入的sum为拆除所有城墙所需的总消耗
    for(int i = 1; i <= n; i++){
        father[i] = i;  //初始化所有塔楼为不连通
    }
    sort(Edge + 1, Edge + 1 + m, cmp);  //城墙权值从大到小排序
    for(int i = 1; i <= m; i++){
        int faNode1 = getFather(Edge[i].u);
        int faNode2 = getFather(Edge[i].v);
        if(faNode1 != faNode2){ //判断城墙连接的两个塔楼是否连通
            father[faNode1] = faNode2;  //不连通则标记为连通
            ans -= Edge[i].w;   //该城墙不需要拆数
        }else{  //如果城墙两个端点塔楼已经连通则该城墙需要拆除
            cnt++;  //记录需要拆除的城墙数量
        }
    }
    return ans; //返回的ans为拆除的最小消耗
}
int main(){
    int n, m;
    while(scanf("%d%d", &n, &m) != EOF){    //输入塔楼数n与城墙数m
        for(int i = 1; i <= n; i++){
            int x, y;
            scanf("%d%d", &x, &y);  //吸收掉这些没用的坐标
        }
        LL sum = 0;
        for(int i = 1; i <= m; i++){    //输入邻接表
            scanf("%d%d%lld", &Edge[i].u, &Edge[i].v, &Edge[i].w);
            sum += Edge[i].w;   //记录总权值（拆除所有城墙的消耗）
        }
        int cnt = 0;    //cnt记录需要拆除的城墙
        LL ans = kruskal(n, m, sum, cnt);   //得到最小消耗
        printf("%d %lld\n",cnt, ans);
    }
	return 0;
}
```
