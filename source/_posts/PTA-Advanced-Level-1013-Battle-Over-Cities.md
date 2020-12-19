---
title: PTA (Advanced Level) 1013 Battle Over Cities
date: 2018-12-16 20:34:57
categories: 
- 算法相关
tags:
- 并查集
- STL
- PTA
---

　　It is vitally important to have all the cities connected by highways in a war. If a city is occupied by the enemy, all the highways from/toward that city are closed. We must know immediately if we need to repair any other highways to keep the rest of the cities connected. Given the map of cities which have all the remaining highways marked, you are supposed to tell the number of highways need to be repaired, quickly.

For example, if we have 3 cities and 2 highways connecting city1-city2 and city1-city3. Then if city1 is occupied by the enemy, we must have 1 highway repaired, that is the highway city2-city3.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 3 numbers N (<), Mand K, which are the total number of cities, the number of remaining highways, and the number of cities to be checked, respectively. Then M lines follow, each describes a highway by 2 integers, which are the numbers of the cities the highway connects. The cities are numbered from 1 to N. Finally there is a line containing K numbers, which represent the cities we concern.

### Output Specification:

For each of the K cities, output in a line the number of highways need to be repaired if that city is lost.

### Sample Input:

```in
3 2 3
1 2
1 3
1 2 3
```

### Sample Output:

```out
1
0
0
```

解题思路：

　　城市之间本来有一些道路，现在有某个城市被攻陷了，与该城市连通的道路就都不能走了，如果剩下还在我们手里的城市不连通，就要修筑新道路使他们连通，任意两个城市都可以修筑新道路。
　　本题每组数据首先给出城市数量n，道路数量m，查询数量k，每次查询只有一个城市被攻陷，之后给出m行数据为每条道路连接的两个城市，随后一行有k个数，表示当前次查询中被攻陷的城市，要求输出最少需要新修筑多少条道路才可以使我们手里剩下的城市都连通。

　　这里使用并查集解答本题。（本题需要路径压缩，否则会T）

```c++
int getFather(int x){
    if(x != father[x])
        father[x] = getFather(father[x]);
    return father[x];
}
```

　　首先使用邻接表记录所有道路信息，每次查询遍历所有道路，通过并查集合并所有不与被攻陷城市连接的道路两端的城市。

```c++
for(int i = 1; i <= n; i++){    //遍历所有道路
                for(auto it : G[i]){
                    if(i != closed && it != closed){    //道路不能与被攻陷的城市相连
                        int fu = getFather(i);
                        int fv = getFather(it);
                        if(fu != fv)    //如果两个城市不在同一个连通块中，合并这两个连通块
                            father[fu] = fv;
                    }
                }
            }
```

 之后对于还在我们手里的城市，获得合并后的连通块数量ans，ans - 1即可获得最少需要新修筑的道路数量。 

```c++
int ans = 0;
for(int i = 1; i <= n; i++){    //获得连通块数量
    if(getFather(i) == i && i != closed){   //只要某个城市所在连通块父节点为自己且这个城市不是被攻陷的城市
        ans++;  //连通块加一
    }
}
```

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+10;
int father[maxn];   //father[i]记录i所在连通块的父节点
vector<int> G[maxn];    //临界表记录边信息
int n, m, k;
int getFather(int x){   //并查集路径压缩找爹函数
    if(x != father[x])
        father[x] = getFather(father[x]);
    return father[x];
}
int main(){
    while(scanf("%d%d%d", &n, &m, &k) != EOF){  //输入城市数量 道路数量 查询数量
        while(m--){ //输入道路信息
            int u, v;
            scanf("%d%d", &u, &v);
            //G[i]记录与i城市连通的道路
            G[u].push_back(v);
            G[v].push_back(u);
            //道路是双向的，所以道路两端的城市都要记录这条道路
        }
        int closed; //closed记录当前被攻陷的城市
        while(k--){ //k条查询
            for(int i = 0; i <= n; i++)  //初始化每个城市自己单独在一个连通块
                father[i] = i;
            scanf("%d", &closed);   //输入被攻陷的城市
            for(int i = 1; i <= n; i++){    //遍历所有道路
                for(auto it : G[i]){
                    if(i != closed && it != closed){    //道路不能与被攻陷的城市相连
                        int fu = getFather(i);
                        int fv = getFather(it);
                        if(fu != fv)    //如果两个城市不在同一个连通块中，合并这两个连通块
                            father[fu] = fv;
                    }
                }
            }
            int ans = 0;
            for(int i = 1; i <= n; i++){    //获得连通块数量
                if(getFather(i) == i && i != closed){   //只要某个城市所在连通块父节点为自己且这个城市不是被攻陷的城市
                    ans++;  //连通块加一
                }
            }
            printf("%d\n", ans - 1);    //输出答案
        }
    }
    return 0;
}
```

