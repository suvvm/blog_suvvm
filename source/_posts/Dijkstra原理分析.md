---
title: Dijkstra原理分析
date: 2019-11-05 22:01:55
categories: 
- 算法相关
tags:
- 最短路
mathjax: true
---
## Dijkstra（迪杰斯特拉）



![img](dijkstra1-1.svg)

假设给定的源点是A，现在要求的便是源点A到其他所有点的最短路长度

我们初始将所有点看作不连通

![img](dijkstra1-2.svg)

当前A点到所有点的最短路长度表

| A    | B    | C    | D    | E    | F    | G    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| ∞    | ∞    | ∞    | ∞    | ∞    | ∞    | ∞    |



### 第一次优化

我们所规定的源点为A，A点到其本身的距离为0。这样我们就找到了第一个距离源点A最近的点，就是A本身，将其标记为红色。

![img](dijkstra1-3.svg)

当前A点到所有点的最短路长度表

| A       | B    | C    | D    | E    | F    | G    |
| ------- | ---- | ---- | ---- | ---- | ---- | ---- |
| **`0`** | ∞    | ∞    | ∞    | ∞    | ∞    | ∞    |



### 第二次优化

之后恢复以A为起点的所有边，更新所有可达点的最短路长度。如下图，可以发现以A为起点的两条边分别通向B与D，根据边权我们可以把B与D到A的最短路长度进行更新，可以发现

```
A点到B点的最短路长度由无穷大优化为了2
A点到D点的最短路长度由无穷大优化为了1
```

这时观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的点便是我们刚刚更新的D。我们便可以确定，A点到D点的最短路径1，将D标记为红色。

![img](dijkstra1-4.svg)

当前A点到所有点的最短路长度表

| A     | B    | C    | D       | E    | F    | G    |
| ----- | ---- | ---- | ------- | ---- | ---- | ---- |
| **0** | 2    | ∞    | **`1`** | ∞    | ∞    | ∞    |

### 第三次优化

重复之前操作，恢复以D为起点的所有边，以D为跳板更新所有未被标红的可达点的最短路长度，更新后可以发现

```
A点到C点的最短路长度由无穷大被优化为了3
A点到E点的最短路长度由无穷大被优化为了3
A点到F点的最短路长度由无穷大被优化为了9
A点到G点的最短路长度由无穷大被优化为了5
```

这时观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的点是最短路长度为2的点B。我们便可以确定，A点到B点的最短路径2，将B标记为红色。

![img](dijkstra1-5.svg)

当前A点到所有点的最短路长度表

| A     | B       | C    | D     | E    | F    | G    |
| ----- | ------- | ---- | ----- | ---- | ---- | ---- |
| **0** | **`2`** | 3    | **1** | 3    | 9    | 5    |

### 第四次优化

重复之前操作，恢复以B为起点的所有边，以B为跳板更新所有未被标红的可达点的最短路长度，更新后可以发现没有点可以被更新。

这时观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的点有两个，分别为最短路长度为3的点C与最短路长度为3的点E。我们可以选任意一个将其标为红色（以已经固定长度的点去优化另一个点，被优化的点的长度一定不会小于优化它的点，所以在这里另一个点一定会在下次优化中被标红）这里我选择点C。

![img](dijkstra1-6.svg)

当前A点到所有点的最短路长度表

| A     | B     | C       | D     | E    | F    | G    |
| ----- | ----- | ------- | ----- | ---- | ---- | ---- |
| **0** | **2** | **`3`** | **1** | 3    | 9    | 5    |



### 第五次优化

重复之前操作，恢复以C为起点的所有边，以C为跳板更新所有未被标红的可达点的最短路长度，更新后可以发现

```
A点到F点的最短路长度由无穷大被优化为了8
```

这时观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的是上一次没有选择的最短路长度为3的点E。我们便可以确定，A点到E点的最短路径3，将E标记为红色。

![img](dijkstra1-7.svg)

当前A点到所有点的最短路长度表

| A     | B     | C     | D     | E       | F    | G    |
| ----- | ----- | ----- | ----- | ------- | ---- | ---- |
| **0** | **2** | **3** | **1** | **`3`** | 8    | 5    |

### 第六次优化

依旧是重复之前操作，恢复以E为起点的所有边，以E为跳板更新所有未被标红的可达点的最短路长度，更新后可以发现没有点可以被更新。

依旧是观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的是最短路长度为5的点G。我们便可以确定，A点到G点的最短路径5，将G标记为红色。

![img](dijkstra1-8.svg)

当前A点到所有点的最短路长度表

| A     | B     | C     | D     | E     | F    | G       |
| ----- | ----- | ----- | ----- | ----- | ---- | ------- |
| **0** | **2** | **3** | **1** | **3** | 8    | **`5`** |



### 第七次优化

重复之前操作，恢复以G为起点的所有边，以G为跳板更新所有未被标红的可达点的最短路长度，更新后可以发现

```
A点到F点的最短路长度由无穷大被优化为了6
```

依旧是观察所有的点，我们可以发现当前距离A点最近且还没有被标为红色的是最短路长度为5的最后一个点F。我们便可以确定，A点到F点的最短路径6，将F标记为红色，至此所有的点都优化完毕。

![img](dijkstra1-9.svg)

当前A点到所有点的最短路长度表

| A     | B     | C     | D     | E     | F       | G     |
| ----- | ----- | ----- | ----- | ----- | ------- | ----- |
| **0** | **2** | **3** | **1** | **3** | **`6`** | **5** |

### 最短路长度表汇总

| A       | B       | C       | D       | E       | F       | G       |
| ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| ∞       | ∞       | ∞       | ∞       | ∞       | ∞       | ∞       |
| **`0`** | ∞       | ∞       | ∞       | ∞       | ∞       | ∞       |
| **0**   | 2       | ∞       | **`1`** | ∞       | ∞       | ∞       |
| **0**   | **`2`** | 3       | **1**   | 3       | 9       | 5       |
| **0**   | **2**   | **`3`** | **1**   | 3       | 9       | 5       |
| **0**   | **2**   | **3**   | **1**   | **`3`** | 8       | 5       |
| **0**   | **2**   | **3**   | **1**   | **3**   | 8       | **`5`** |
| **0**   | **2**   | **3**   | **1**   | **3**   | **`6`** | **5**   |

### 不适用情况

只要图中有任何负权边，dijkstra边不能正确得到预期答案，即使没有负环也不行。

至于为什么，可以从如下图的例子中看出

![img](dijkstra2-1.svg)

按照之前的方法进行分析

我们初始将所有点看作不连通

![img](dijkstra2-2.svg)

当前A点到所有点的最短路长度表

| A    | B    | C    |
| ---- | ---- | ---- |
| ∞    | ∞    | ∞    |



#### 第一次优化

A点到其本身的距离为0。找到了第一个距离源点A最近的点，为A本身，将其标记为红色。

![img](dijkstra2-3.svg)

当前A点到所有点的最短路长度表

| A       | B    | C    |
| ------- | ---- | ---- |
| **`0`** | ∞    | ∞    |

#### 第二次优化

恢复以A为起点的所有边，更新所有可达点的最短路长度。

如下图

![img](dijkstra2-4.svg)

```
A点到B点的最短路长度由无穷大优化为了3
A点到C点的最短路长度由无穷大优化为了2
```

当前距离A点最近且还没有被标为红色的点是C，A点到C点的最短路径2，将C标记为红色。

当前A点到所有点的最短路长度表

| A     | B    | C       |
| ----- | ---- | ------- |
| **0** | 3    | **`2`** |

#### 第三次优化

恢复以C为起点的所有边，更新所有可达点的最短路长度。

如下图

![img](dijkstra2-5.svg)

没有点可以被更新

当前距离A点最近且还没有被标为红色的点是B，A点到B点的最短路径3，将B标记为红色。

当前A点到所有点的最短路长度表

| A     | B       | C     |
| ----- | ------- | ----- |
| **0** | **`3`** | **2** |

#### 第四次优化

恢复以B为起点的所有边，更新所有可达点的最短路长度。

如下图

![img](dijkstra2-6.svg)

**由于C已经被固定，所以没有点可以被更新！！**

当前A点到所有点的最短路长度表

| A     | B     | C     |
| ----- | ----- | ----- |
| **0** | **3** | **2** |

所以上表便是dijkstra得出的最终结果，而并不是我们认为的

| A     | B     | C     |
| ----- | ----- | ----- |
| **0** | **3** | **1** |

### 算法实现

#### 一般实现

以上方正确示例为例，为了方便处理，我们将A~G这七个字母在算法中以0~6七个点所代替。

我们可以得出下图的输入数据

![img](dijkstra3-1.svg)

```
7 12
0 1 2
0 3 1
1 3 3
1 4 10
2 0 4
2 5 5
3 2 2
3 4 2
3 5 8
3 6 4
4 6 6
6 5 1
```

代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;
const int MAXV = 1e3;
const int inf = 1e6;
struct Node{
    int v, dis;
    Node(int tv, int tdis){
        v = tv, dis = tdis;
    }
};
int n;  //n为顶点数量
int d[MAXV]; //存放起点到各点的最短路径长度
vector<Node> Adj[MAXV];
bool vis[MAXV]; //标记数组
void dijkstra(int s){
    fill(d, d + MAXV, inf); //初始化起点到所有点的最短路长度为无穷大
    d[s] = 0;   //起点至本身的距离为0
    for(int i = 0; i < n; i ++){
        int u = -1, mind = inf;
        for(int j = 0; j < n; j++){ //寻找距离顶点最近的点
            if(!vis[j] && d[j] < mind){
                u = j;
                mind = d[j];
            }
        }
        if(u == -1) //若找不到u证明剩下的点与顶点不连通
            return;
        vis[u] = true;
        for(auto j : Adj[u]){   //遍历所有与u连通的点
            int v = j.v;
            if(!vis[v] && d[u] + j.dis < d[v]){ //以u为中转优化起点到与u连通的点的最短距离
                d[v] = d[u] + j.dis;
            }
        }
    }
}
int main()
{
    ios::sync_with_stdio(false);
    int s;  //为起点
    cout <<"输入顶点数:";
    cin >> n;
    int r;//路径数
    cout << "输入路径数:";
    cin >> r;

    for(int i = 0; i < r; i++){
        cout << "输入路径（格式:端点1 端点2 权值）:";
        int u, v, dis;
        cin >> u >> v >> dis;
        Adj[u].push_back(Node(v, dis));
        //Adj[v].push_back(Node(u, dis));
    }
    cout << "输入起点:";
    cin >> s;
    dijkstra(s);
    int e;  //终点
    cout << "输入终点:";
    cin >> e;
    cout << "最短路长度" << d[e] << endl;
    for(int i = 0; i < n; i ++){
        cout << i << ":" << d[i] << endl;
    }
    system("pause");
    return 0;
}

```

#### 堆优化dijkstra

分析一下上方dijkstra的时间复杂度，设顶点个数n，外层循环为 $ O(n) $，内层循环有寻找距离起点最近的点需要 $ O(n) $，每次枚举被优化点v需要$O(Adj[u].size) $总共需要 $ O(\sum {n-1 \atop u=0} Adj[u].size)=E $，总时间复杂度 $ O(N\times N + E)=O(N^2+E) $

如何去优化它呢？压榨外层循环，根据dijkstra的原理显然是不太现实的，但是根据前的设计dijkstra在每次进行优化时都需要寻找距离起点最近的点，这需要每次都遍历所有的点，这里便可以使用堆优化来降低时间复杂度，可以直接使用STL里的优先队列priority_queue，使距离起点进的点优先出队，这样就可以将内层循环中寻找距离起点最近的点消耗的 $ O(N) $优化为维护优先队列的 $ O(logN) $，这样总时间复杂度就降为了 $ O(NlogN+E) $

代码实现

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e3;
const int INF = 1e6;
int n;
int d[MAXN];
bool vis[MAXN];
struct node{
    int v, dis;
    bool operator < (const node &tNode) const{  //优先队列默认优先级高的先出队 这里就要通过重载运算符 < 使距离越小优先级越高
        return dis > tNode.dis;
    }
};
vector<node> Adj[MAXN];

void dijkstra(int s){
    fill(d, d + MAXN, INF); //初始化起点至所有点距离为无穷大
    priority_queue<node> Q;
    d[s] = 0;   //起点至其本身距离为0
    Q.push(node{s, 0}); //这里node中v代表的是当前顶点，dis代表其与起点的距离
    while(!Q.empty()){
        node f = Q.top();
        Q.pop();
        int u = f.v;
        if(vis[u])  //如果u已经标记则继续出队
            continue;
        vis[u] = true;  //将u标记
        for(auto i : Adj[u]){   //遍历所有与u相邻的顶点
            int v = i.v;
            int dis = i.dis;
            if(!vis[v] && d[u] + dis < d[v]){   //如果可以以u为中转优化起点到对应点的距离
                d[v] = d[u] + dis;      //执行松弛操作
                Q.push(node{v, d[v]});   //将松弛后的点与距离入队
            }
        }
    }

}

int main()
{
    ios::sync_with_stdio(false);
    cout << "输入顶点数:";
    cin >> n;
    int m;
    cout << "输入边数";
    cin >> m;
    for(int i = 0; i < m; i ++){
        cout << "输入边 (端点1 端点2 权值):" << endl;
        int u, v, dis;
        cin >> u >> v >> dis;
        Adj[u].push_back(node{v, dis});
        //Adj[v].push_back(node{u, dis});
    }
    int s;
    cout << "输入起点:";
    cin >> s;
    int e;
    cout << "输入终点:";
    cin >> e;
    dijkstra(s);
    cout << "最短路长度:" << d[e] << endl;
    for(int i = 0; i < n; i++){
        cout << i << " : " << d[i] << endl;
    }
    system("pause");
    return 0;
}

```

#### Dijkstra记录最短路路径

记录最短路径的方法非常简单，这里只需要开一个长度为n的数组pre，在每次进行优化的时候，记录每个被优化点的前驱点，最后由终点开始寻找前驱直到找到起点即可获得最短路径。

代码实现

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e3;
const int INF = 1e7;
struct Node{
    int v, dis;
    bool operator < (const Node &tNode) const{  // 优先队列默认优先级高的先出队 这里就要通过重载运算符 < 使距离越小优先级越高
        return dis > tNode.dis;
    }
};
vector<Node> Adj[MAXN];
int n;
bool vis[MAXN];
int d[MAXN], pre[MAXN];
void dijkstra(int s){
    fill(d, d +MAXN, INF);  // 初始化起点至所有点距离为无穷大
    d[s] = 0;    // 起点至其本身距离为0
    priority_queue<Node> Q; // 这里node中v代表的是当前顶点，dis代表其与起点的距离
    Q.push(Node{s, 0});
    while(!Q.empty()){
        Node f = Q.top();
        Q.pop();
        int u = f.v;
        if(vis[u])  // 如果u已经标记则继续出队
            continue;
        vis[u] = true;  // 将u标记
        for(auto i : Adj[u]){   // 遍历所有与u相邻的顶点
            if(d[i.v] > d[u] + i.dis){  // 如果可以以u为中转优化起点到对应点的距离
                d[i.v] = d[u] + i.dis;  // 执行松弛操作
                pre[i.v] = u;   // 记录本次优化的前驱点
                Q.push(Node{i.v, d[i.v]});  // 将松弛后的点与距离入队
            }
        }
    }
}
int main()
{
    ios::sync_with_stdio(false);
    cout << "输入顶点数:";
    cin >> n;
    int m;
    cout << "输入边数:";
    cin >> m;
    for(int i = 0; i < m; i++){
        int u, v, dis;
        cout << "输入边（顶点1 顶点2 权值）:";
        cin >>  u >> v >> dis;
        Adj[u].push_back(Node{v, dis});
        Adj[v].push_back(Node{u,dis});
    }
    int s, e;
    cout << "输入起点:";
    cin >> s;
    cout << "输入终点:";
    cin >> e;
    dijkstra(s);
    cout << "最短路长度:" << d[e] << endl;
    int tmp = e;
    while(tmp != s){    // 由重点开始寻找前驱便可以获得最短路径
        cout << tmp << " <- ";
        tmp = pre[tmp];
    }
    cout << s << endl;
    return 0;
}
```

#### Dijkstra+DFS

在考查dijkstra算法时又是可能会出现最短路不只存在一条的情况，这是通常就会给你一个其他的判断条件，比如要求找出点权和最大的最短路。

![img](dijkstra3-2.svg)

观察上图，以红色为起点，蓝色为终点，求一条途经点的点权和最高的最短路。

先不考虑点权，先来看对多条最短路的记录，之前在只记录一条最短路时采取了pre前驱数组的方式，单如果有多条最短路，便要使用其他方式。使用前驱数组时，每次只有在某个点被优化时才记录其前驱结点，对到起点距离相等的情况并没有进行处理，也就是说当存在多条最短路径时，即使某个点在被优化时产生了到起点距离相等的情况也要对其进行记录，每个点的前驱就可能不止一个。

这里便可以将int型数组pre更换为容器型（vector）数组，pre\[i\]中记录的是i点所有的前驱结点。

这样便成功记录了所有的最短路径。若想从这些最短路径中计算出一条点权和最大的来，就可以用dfs去搜索每一条最短路并记录点权和，最终选出点权和最大的最短路。

比如pat [PAT (Advanced Level) Practice  **1003** **Emergency** ]( https://pintia.cn/problem-sets/994805342720868352/problems/994805523835109376 )就是一道典型的这种类型的题目。

送上代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 510;
const int INF = 500000;
int d[MAXN];
struct Node{
    int v, dis;
    bool operator < (const Node &tNode) const    // 优先队列默认优先级高的先出队 这里就要通过重载运算符 < 使距离越小优先级越高
        return dis > tNode.dis;
    }
};
int n, m, s, e;
int val[MAXN];
bool vis[MAXN];
vector<Node> Adj[MAXN];
vector<int> pre[MAXN];  // 存储每个点的所有前驱点
void dijkstra(int s){
    fill(d, d + MAXN, INF); // 初始化起点至所有点距离为无穷大
    d[s] = 0;   // 起点至其本身距离为0
    priority_queue<Node> Q;  // 这里node中v代表的是当前顶点，dis代表其与起点的距离
    Q.push(Node{s, 0});
    while(!Q.empty()){
        Node f = Q.top();
        Q.pop();
        int u = f.v;
        if(!vis[u]){    // 如果未经标记
            vis[u] = true;
            for(auto i : Adj[u]){   // 遍历所有与u相邻的顶点
                if(d[i.v] > d[u] + i.dis){  // 如果可以以u为中转优化起点到对应点的距离
                    d[i.v] = d[u] + i.dis;  // 执行松弛操作
                    Q.push(Node{i.v, d[i.v]});
                    pre[i.v].clear();   // 清除之前记录的前驱
                    pre[i.v].push_back(u);  // u记录为v的前驱
                }else if(d[i.v] == d[u] + i.dis){
                    pre[i.v].push_back(u);  // u添加为v的前驱
                }
            }
        }
    }
}
vector<int> road;   // 记录最终点权和最大的最短路
vector<int> troad;  // 记录遍历中的每一条最短路
int minRoadCnt;
int saveCnt;
void dfs(int ed){
    if(ed == s){
        minRoadCnt++;
        int tempSaveCnt = 0;
        for(auto i : troad){
            tempSaveCnt += val[i];
        }
        if(saveCnt < tempSaveCnt){
            road = troad;
            saveCnt = tempSaveCnt;
        }
        return;
    }else{
        for(auto i : pre[ed]){
            troad.push_back(i);
            dfs(i);
            troad.pop_back();
        }
    }
}
int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> m >> s >> e;
    for(int i = 0; i < n ; i ++){
        cin >> val[i];
    }
    for(int i = 0; i < m; i ++){
        int u, v, dis;
        cin >> u >> v >> dis;
        Adj[u].push_back(Node{v, dis});
        Adj[v].push_back(Node{u, dis});
    }
    dijkstra(s);
    troad.push_back(e);
    dfs(e);
    cout << minRoadCnt << " " << saveCnt << endl;
    // 打印路径
    /*for(int i = road.size() - 1; i >= 0; i--){
        if(i == road.size() - 1)
            cout << road[i];
        else
            cout << " " << road[i];
    }*/

    return 0;
}

```

