---
title: Bellman Ford原理分析
date: 2019-11-12 19:11:56
categories: 
- 算法相关
tags:
- 最短路
mathjax: true
---

##  Bellman Ford（贝尔曼 福特算法） 

Dijkstra算法可以很好的解决无负权图的最短路问题，但是一旦遇到负权，这个负权就像在迷宫游戏中，走每一条路都会消耗体力，但是某些道路上有宝箱，宝箱里可以开出美利坚大香蕉食用可以恢复体力，dijkstra会使你只顾着眼前消耗体力最少的道路而忽视了之后的美利坚大香蕉（[具体分析](https://www.suvvm.work/2019/11/05/Dijkstra原理分析/)），这时便需要设计另一个算法去代替它，如Bellman Ford。

如果一个有N个顶点的连通图中有最短路存在，那么这条最短路径上的顶点个数肯定不会超过N个，当源点被确定时，我们可以根据源点建立一条最短路径树，具体以下图为例，假设A为源点。

![img](bf1-1.svg)

最短路径树

![img](bf1-2.svg)

最短路径树的层数一定不会超过N。

Bellman Ford算法与dijkstra类似，也需要设置一个数组d用来存放源点到各个顶点的最短距离。

每次遍历图中所有边，根据边的权值进行松弛操作（对于每条边$u \rightarrow v $ 如果以u为中介点可以使v到源点的距离更短，就用u到源点的距离与当前边权去优化v到源点的距离）。

初始状态下源点已知且到其本身的距离为0，即最短路径树的第一层结点已被确定，由第二层开始之后每次执行上述的松弛操作都可以确定一层的结点，由于最短路径树的层数不会超过N，所以确定所有点到源点的最短路长度所需要进行松弛操作的次数不会超过N-1。

同dijkstra一样，初始将起点到所有点的最短路长度视为无穷大。

记录一下图中的所有边

![img](bf2-1.svg)

### 确定源点

这里以A为源点，源点到其本身的距离为0。

![img](bf2-2.svg)

 当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | ∞    | ∞    | ∞    | ∞    | ∞    |

一共6个点，之后进行6-1=5次松弛操作。

### 第一次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(∞)	将B点到源点的最短距离由无穷大优化为0+2=2
边A(0)	3	C(∞)	将C点到源点的最短距离由无穷大优化为0+2=3
边B(2)	6	E(∞)	将E点到源点的最短距离由无穷大优化为2+6=8
边C(3)	-2	B(2)	将B点到源点的最短距离由2优化为3-2=1
边C(3)	5	E(8)	并不能进行优化
边D(∞)	4	B(1)	并不能进行优化
边E(8)	-5	D(∞)	将D点到源点的最短距离由无穷大优化为8-5=3
边E(8)	8	F(∞)	将F点到源点的最短距离由无穷大优化为8+8=16
边F(16)	3	C(3)	并不能进行优化
```

![img](bf2-3.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 3    | 8    | ∞    |

### 第二次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(1)	6	E(8)	将E点到源点的最短距离由8优化为1+6=7
边C(3)	-2	B(1)	并不能进行优化
边C(3)	5	E(7)	并不能进行优化
边D(3)	4	B(1)	并不能进行优化
边E(7)	-5	D(3)	将D点到源点的最短距离由3优化为7-5=2
边E(7)	8	F(16)	将F点到源点的最短距离由16优化为7+8=15
边F(15)	3	C(3)	并不能进行优化
```

![img](bf2-4.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 2    | 7    | 15   |

### 第三次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(1)	6	E(7)	并不能进行优化
边C(3)	-2	B(1)	并不能进行优化
边C(3)	5	E(7)	并不能进行优化
边D(2)	4	B(1)	并不能进行优化
边E(7)	-5	D(2)	并不能进行优化
边E(7)	8	F(15)	并不能进行优化
边F(15)	3	C(3)	并不能进行优化
```

![img](bf2-4.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 2    | 7    | 15   |

### 第四次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(1)	6	E(7)	并不能进行优化
边C(3)	-2	B(1)	并不能进行优化
边C(3)	5	E(7)	并不能进行优化
边D(2)	4	B(1)	并不能进行优化
边E(7)	-5	D(2)	并不能进行优化
边E(7)	8	F(15)	并不能进行优化
边F(15)	3	C(3)	并不能进行优化
```

![img](bf2-4.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 2    | 7    | 15   |

### 第五次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(1)	6	E(7)	并不能进行优化
边C(3)	-2	B(1)	并不能进行优化
边C(3)	5	E(7)	并不能进行优化
边D(2)	4	B(1)	并不能进行优化
边E(7)	-5	D(2)	并不能进行优化
边E(7)	8	F(15)	并不能进行优化
边F(15)	3	C(3)	并不能进行优化
```

![img](bf2-4.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 2    | 7    | 15   |

到此为止五次松弛操作都已经结束，在对比一下我们之前肉眼观察出来的最短路径树。

![img](bf1-2.svg)

仿佛没有什么问题，我们成功的获得了最短路径，可喜可贺，Bellman Ford结束了，诸位洗洗睡把。
```
6 9
0 1 2
0 2 3
1 4 6
2 1 -2
2 4 5
3 1 4
4 3 -5
4 5 8
5 2 3
0
```

# 呸！！！

### 负环

负环这个东西很神奇，拿之前迷宫的例子来描述它，就是你可以发现有一条道路与之前的某个点形成了一个环，这个环上有一个或者多个大宝箱，里面有很多美利坚大香蕉，使得走完一次循环后吃美利坚大香蕉所恢复的体力比走路消耗的还多，这样就可以一直走这个环，刷更多的美利坚大香蕉恢复更多的体力。

我们修改一下上面的图，将D到B的边的边权改为-4，这样就形成了一个B到E到D回到B的一个权值为6-5-4=-3的负环。

![img](bf3-1.svg)

我们依照之前的算法再次推理一遍。

### 确定源点

以A为源点，源点到其本身的距离为0。

![img](bf3-2.svg)

 当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | ∞    | ∞    | ∞    | ∞    | ∞    |

一共6个点，之后进行6-1=5次松弛操作。

### 第一次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(∞)	将B点到源点的最短距离由无穷大优化为0+2=2
边A(0)	3	C(∞)	将C点到源点的最短距离由无穷大优化为0+2=3
边B(2)	6	E(∞)	将E点到源点的最短距离由无穷大优化为2+6=8
边C(3)	-2	B(2)	将B点到源点的最短距离由2优化为3-2=1
边C(3)	5	E(8)	并不能进行优化
边D(∞)	-4	B(1)	并不能进行优化
边E(8)	-5	D(∞)	将D点到源点的最短距离由无穷大优化为8-5=3
边E(8)	8	F(∞)	将F点到源点的最短距离由无穷大优化为8+8=16
边F(16)	3	C(3)	并不能进行优化
```

![img](bf3-3.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | 1    | 3    | 3    | 8    | 16   |

### 第二次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(1)	6	E(8)	将E点到源点的最短距离由8优化为1+6=7
边C(3)	-2	B(1)	并不能进行优化
边C(3)	5	E(7)	并不能进行优化
边D(3)	-4	B(1)	将B点到源点的最短距离由1优化为3-4=-1
边E(7)	-5	D(3)	将D点到源点的最短距离由3优化为7-5=2
边E(7)	8	F(16)	将F点到源点的最短距离由16优化为7+8=15
边F(15)	3	C(3)	并不能进行优化
```

![img](bf3-4.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | -1   | 3    | 2    | 7    | 15   |

### 第三次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(-1)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(-1)	6	E(7)	将E点到源点的最短距离由7优化为-1+6=5
边C(3)	-2	B(-1)	并不能进行优化
边C(3)	5	E(5)	并不能进行优化
边D(2)	-4	B(-1)	将B点到源点的最短距离由-1优化为2-4=-2
边E(5)	-5	D(2)	将D点到源点的最短距离由2优化为-5+5=0
边E(5)	8	F(15)	将F点到源点的最短距离由15优化为5+8=13
边F(13)	3	C(3)	并不能进行优化
```

![img](bf3-5.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | -2   | 3    | 0    | 5    | 13   |

### 第四次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(-2)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(-2)	6	E(5)	将E点到源点的最短距离由5优化为-2+6=4
边C(3)	-2	B(-2)	并不能进行优化
边C(3)	5	E(4)	并不能进行优化
边D(0)	-4	B(-2)	将B点到源点的最短距离由-2优化为0-4=-4
边E(4)	-5	D(0)	将D点到源点的最短距离由0优化为4-5=-1
边E(4)	8	F(13)	将F点到源点的最短距离由13优化为4+8=12
边F(12)	3	C(3)	并不能进行优化
```

![img](bf3-6.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | -4   | 3    | -1   | 4    | 12   |

### 第五次松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(-4)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(-4)	6	E(4)	将E点到源点的最短距离由4优化为-4+6=2
边C(3)	-2	B(-4)	并不能进行优化
边C(3)	5	E(2)	并不能进行优化
边D(-4)	-4	B(1)	将B点到源点的最短距离由-4优化为-4-4=-8
边E(2)	-5	D(-1)	将D点到源点的最短距离由-1优化为2-5=-3
边E(2)	8	F(12)	将F点到源点的最短距离由12优化为2+8=10
边F(10)	3	C(3)	并不能进行优化
```

![img](bf3-7.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | -5   | 3    | -3   | 2    | 10   |

到此为止五次松弛操作都已经结束

还能不能继续松弛呢，这里再进行一次额外的松弛操作

### 额外松弛

遍历所有边，根据其权值与两端点到源点的距离进行松弛操作。

```
边A(0)	2	B(-5)	并不能进行优化
边A(0)	3	C(3)	并不能进行优化
边B(-5)	6	E(2)	将E点到源点的最短距离由2优化为-5+6=1
边C(3)	-2	B(-5)	并不能进行优化
边C(3)	5	E(1)	并不能进行优化
边D(-3)	-4	B(-5)	将B点到源点的最短距离由-5优化为-3-4=-7
边E(1)	-5	D(-3)	将D点到源点的最短距离由-3优化为1-5=-4
边E(1)	8	F(10)	将F点到源点的最短距离由10优化为1+8=9
边F(9)	3	C(3)	并不能进行优化
```

![img](bf3-8.svg)

当前A点到所有点的最短路长度表 

| A    | B    | C    | D    | E    | F    |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0    | -7   | 3    | -4   | 1    | 9    |

果然还可以优化。
```
6 9
0 1 2
0 2 3
1 4 6
2 1 -2
2 4 5
3 1 -4
4 3 -5
4 5 8
5 2 3
0
```

### 负环处理

如何处理这种有负环的情况呢？当然是不去处理它，同上述步骤一样，我们多进行一次额外松弛，如果额外松弛时已经有优化发送，我们就认为这个图有负环，然后报告主函数即可。

### 算法实现

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e3;
const int INF = 1e6;
struct node{
    int v, dis;
};
vector<node> Adj[MAXN];
int n;
int d[MAXN];
bool BF(int s){
    fill(d, d + MAXN, INF);//初始化起点到所有点的最短路长度为无穷大
    d[s] = 0;   //起点至本身的距离为0
    for(int i = 0; i < n - 1; i ++){    //遍历n - 1遍（一条最短路除去源点最多包含n-1个点，这里可以做优化）
        for(int u = 0; u < n; u ++){    //遍历所有顶点
            for(auto j : Adj[u]){   //找到所有与当前点连通的点
                if(d[j.v] > d[u] + j.dis){
                    d[j.v] = d[u] + j.dis;  //松弛操作
                }
            }
        }
        /*
        for(int i = 0; i < n; i++){
            cout << " " << i << ":" << d[i];
        }
        cout << endl;*/
    }
    for(int u = 0; u < n; u ++){    //检测负环 如果已经执行上述n-1遍理应成功获取起点至所有点的最短路 若还可以继续松弛则存在负环
        for(auto j : Adj[u]){
            if(d[j.v] > d[u] + j.dis)
                return false;
          }
    }
    return true;
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
    if(BF(s)){
        cout << "最短路长度:" << d[e] << endl;
        for(int i = 0; i < n; i ++){
            cout << i << " : " << d[i] << endl;
        }
    }else{
        cout << "最短路不存在" << endl;
    }
    system("pause");
    return 0;
}
```

