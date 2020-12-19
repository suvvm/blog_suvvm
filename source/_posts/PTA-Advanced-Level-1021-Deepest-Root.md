---
title: PTA (Advanced Level) 1021 Deepest Root
date: 2019-01-25 15:31:56
categories: 
- 算法相关
tags:
- PTA
- DFS（深度优先搜索）
- 并查集
---

　　A graph which is connected and acyclic can be considered a tree. The hight of the tree depends on the selected root. Now you are supposed to find the root that results in a highest tree. Such a root is called the deepest root.

### Input Specification:

　　Each input file contains one test case. For each case, the first line contains a positive integer N (≤10^4) which is the number of nodes, and hence the nodes are numbered from 1 to N. Then N−1 lines follow, each describes an edge by given the two adjacent nodes' numbers.

### Output Specification:

　　For each test case, print each of the deepest roots in a line. If such a root is not unique, print them in increasing order of their numbers. In case that the given graph is not a tree, print `Error: K components` where `K` is the number of connected components in the graph.

### Sample Input 1:

```in
5
1 2
1 3
1 4
2 5
```

### Sample Output 1:

```out
3
4
5
```

### Sample Input 2:

```in
5
1 3
1 4
2 5
3 4
```

### Sample Output 2:

```out
Error: 2 components
```

题目解析
　　本题给出一个无向图的信息，包括一个数字n，代表无向图有n个结点，之后跟随n-1行分别为n-1条边连接的两个结点。要求判断该无向图是否是一颗树，若是一棵树，由小到大输出以其为根结点时深度最大的点，若不是树则输出连通块数量。

　　在解题之前首先看一下题目的限制

　　时间限制: 2000 ms

　　内存限制: 64 MB



　　本题结点数量最多1e4个若用二维数组存储邻接矩阵，虽然数组没有超限但是当节点数取到最大1e4个结点时，需要的内存空间超过382MB，所以在这里使用邻接表来存储无向图。对于判断无向图是否为树，由于无向图有n个结点n-1条边，那么只要这个图是连通图，它便肯定是一颗树。我们只需要维护一个并查集便可以达到判断数与输出连通块数量，由于本题时间限制非常松，时间宽松使人暴力，所以在寻找深度最大的根结点时，这里以所有点为根结点暴力深搜获取深度，用set保存所有深度最大的点，之后将其输出即可。

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAX =1e4 + 10;
vector<int> G[MAX];
int f[MAX];
int n;
void init(){    //初始化并查集
    for(int i = 1; i <= n; i++)
        f[i] = i;
}
int getF(int x) //并查集找爹函数
{
    if(f[x] == x)
        return x;
    else
        return f[x] = getF(f[x]);
}
int maxH = 0;
void DFS_getH(int u, int height, int preNode){  //深搜获取深度
    maxH = max(maxH, height);
    for(int i = 0; i < G[u].size(); i++)
        if(G[u][i] != preNode)
            DFS_getH(G[u][i], height + 1, u);
}
int main(){
    scanf("%d", &n);    //输入结点数量
    init(); //初始化并查集
    memset(G, 0, sizeof(G));
    for(int i = 0; i < n - 1; i++){ //输入n-1条边
        int u, v;
        scanf("%d%d", &u, &v);
        G[u].push_back(v);
        G[v].push_back(u);
        int fu = getF(u);
        int fv = getF(v);
        if(fu != fv)    //合并一条的两个端点
            f[fu] = fv;
    }
    int cnt = 0;    //记录连通块个数
    for(int i = 1; i <= n; i++){    //获取连通块个数
        if(f[i] == i)
            cnt++;
    };
    if(cnt != 1)    //若连通块个数不等于1证明给出的图不是一颗树
        printf("Error: %d components\n", cnt);   //输出连通块个数
    else{
        set<int> ans;
        int ansH = 0;
        for(int i = 1; i <= n; i++){
            maxH = 0;
            DFS_getH(i, 1, 0);  //获取每一个点为根时对应的树高（用maxH记录）
            if(maxH > ansH){    //ansH记录当前最高深度
                ansH = maxH;    //若找到深度更大的点便清空集合重新记录
                ans.clear();
                ans.insert(i);
            }else if(maxH == ansH){ //找到深度与当前最大深度相同的点便加入集合
                ans.insert(i);
            }
        }
        for(auto i : ans){
            printf("%d\n", i);
        }
    }
    return 0;
}
```

