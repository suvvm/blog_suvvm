---
title: HDU 1102 Constructing Roads(kruskal)
date: 2018-11-12 23:05:09
categories: 
- 算法相关
tags:
- 最小生成树
- HDU
---

There are N villages, which are numbered from 1 to N, and you should build some roads such that every two villages can connect to each other. We say two village A and B are connected, if and only if there is a road between A and B, or there exists a village C such that there is a road between A and C, and C and B are connected. 

　　We know that there are already some roads between some villages and your job is the build some roads such that all the villages are connect and the length of all the roads built is minimum. 

Input

　　The first line is an integer N (3 <= N <= 100), which is the number of villages. Then come N lines, the i-th of which contains N integers, and the j-th of these N integers is the distance (the distance should be an integer within [1, 1000]) between village i and village j. 

Then there is an integer Q (0 <= Q <= N * (N + 1) / 2). Then come Q lines, each line contains two integers a and b (1 <= a < b <= N), which means the road between village a and village b has been built. 
Output

　　You should output a line contains an integer, which is the length of all the roads to be built such that all the villages are connected, and this value is minimum. 
Sample Input

```
3
0 990 692
990 0 179
692 179 0
1
1 2
```

Sample Output

```
179
```

解题思路：
　　本题有多组测试数据，每种数据第一行给出村子的数量n，跟随n行，每行为使该行对应的村子与其他村子联通所需要修筑的道路距离（其实就是所有村子的邻接矩阵），之后给出已经修好的道路数量q，之后q行跟随，每行包括两个整数分别为道路两端的两个村子。要求输出使所有村子联通还要修筑道路的最小长度。

　　若不看已经修好的道路，本题就是一个最小生成树问题。在这里使用kruskal算法。

　　kruskal算法核心思想：　　

　　既然已经给出了邻接矩阵，那我们可以将其拆分为邻接表，即将每一条可以修筑的道路都记录下来。初始视所有结点都为不连通，之后将道路按长度排序，从小到大枚举所有边，判断边的两个顶点是否已经连通，若已经连通不做处理，若不连通则将该边记录入最小生成树，并记录当前总权值，最小生成树也是树，符合边数等于顶点数减一，所以结束条件为边数等于定点数减一，如果边数不等于顶点数减一则说明图不连通（当然在这里不存在不连通的情况，不过写上一定不会错，还能节约时间）。

　　将邻接矩阵拆分为邻接表：用结构edge保存道路，其成员包括两个顶点村子node1，node2与道路长度len。

```c++
for(int i = 0; i < n; i++){
            for(int j = 0; j < n; j++){
                Edge[cnt].node1 = i;
                Edge[cnt].node2 = j;
                scanf("%d", &Edge[cnt].len);
                cnt++;
            }
}
```

 在判断是否连通使用并查集 

```c++
int father[maxn];   //记录父结点
int getFather(int x){
    int tempx = x;
    while(x != father[x]){  //寻找父结点
        x = father[x];
    }
    while(tempx != father[tempx]){  //将路径上所有的点的father值改为父结点
        int preTempx = tempx;
        tempx = father[tempx];
        father[preTempx] = tempx;
    }
    return x;
}
```

 kruskal算法 

```c++
int kruskal(int n, int m){  //传入顶点数与边数
    int ans = 0, edgeCnt = 0;
    //ans记录道路长度和，edgeCnt记录当前最小生成树中边的数量
    for(int i = 0; i < n; i++){
        father[i] = i;
    }
    sort(Edge, Edge + m, cmp);  //将边排序
    for(int i = 0; i < m; i++){ //从小到大枚举所有边
        int faNode1 = getFather(Edge[i].node1);
        int faNode2 = getFather(Edge[i].node2);
        if(faNode1 != faNode2){ //判断该边的两个顶点是否已经连通
            father[faNode1] = faNode2;  //不连通将其标记为连通
            ans += Edge[i].len;    //记录长度
            edgeCnt++;  //记录遍数
            if(edgeCnt == n - 1)    //边数等于顶点数减一
                break;
        }
    }
    if(edgeCnt == n - 1){   //连通
        return ans;
    }else{  //不连通
        return -1;
    }
}
```

　之后就要考虑已经建好的道路，这其实很简单，只需要将建好的道路长度标记为0即可。给定一个已经建好的道路数量q，之后传入q组数据，每组包含两个村子village1与village2，根据我们邻接矩阵的拆分方法，我们可以得知，在记录边的数组Edge中，village1与village2所对应边的下标为village1 * n + village2（道路是双向的，在Edge中会有两个顶点为village1 与 village2的道路，但因为我们计算时会排序，所以标记一个就好）。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e4+10;
struct edge{
    int node1, node2;
    int len;
}Edge[maxn];
bool cmp(edge e1, edge e2){
    return e1.len < e2.len;
}
int father[maxn];   //记录父结点
int getFather(int x){
    int tempx = x;
    while(x != father[x]){  //寻找父结点
        x = father[x];
    }
    while(tempx != father[tempx]){  //将路径上所有的点的father值改为父结点
        int preTempx = tempx;
        tempx = father[tempx];
        father[preTempx] = tempx;
    }
    return x;
}

int kruskal(int n, int m){  //传入顶点数与边数
    int ans = 0, edgeCnt = 0;
    //ans记录道路长度和，edgeCnt记录当前最小生成树中边的数量
    for(int i = 0; i < n; i++){
        father[i] = i;
    }
    sort(Edge, Edge + m, cmp);  //将边排序
    for(int i = 0; i < m; i++){ //从小到大枚举所有边
        int faNode1 = getFather(Edge[i].node1);
        int faNode2 = getFather(Edge[i].node2);
        if(faNode1 != faNode2){ //判断该边的两个顶点是否已经连通
            father[faNode1] = faNode2;  //不连通将其标记为连通
            ans += Edge[i].len;    //记录长度
            edgeCnt++;  //记录遍数
            if(edgeCnt == n - 1)    //边数等于顶点数减一
                break;
        }
    }
    if(edgeCnt == n - 1){   //连通
        return ans;
    }else{  //不连通
        return -1;
    }
}
int main()
{
    int n, cnt = 0;
    while(scanf("%d", &n) != EOF){
        cnt = 0;    //cnt记录边数
        int numNode = n, numEdge = 0;   //numNode记录村子数量，numEdge记录总道路数量
        for(int i = 0; i < n; i++){ //拆分邻接矩阵
            for(int j = 0; j < n; j++){
                Edge[cnt].node1 = i;
                Edge[cnt].node2 = j;
                scanf("%d", &Edge[cnt].len);
                cnt++;
            }
        }
        numEdge = cnt;
        int q;
        scanf("%d", &q);
        for(int i = 0; i < q; i++){
            int village1, village2; //输入已经存在道路的两个村子
            scanf("%d%d", &village1, &village2);
            village1--; //由于之前拆分时 i 与 j从0开始所以村子对应的值为输入的值减一
            village2--;
            Edge[village1 * n + village2].len = 0;
        }
        int ans = kruskal(numNode, numEdge);
        printf("%d\n", ans);
    }
    return 0;
}
```

