---
title: BZOJ 1050 旅行comf（kruskal）
date: 2018-11-16 16:00:59
categories: 
- 算法相关
tags:
- 最小生成树
- 并查集
- BZOJ
---

给你一个无向图，N(N<=500)个顶点, M(M<=5000)条边，每条边有一个权值Vi(Vi<30000)。给你两个顶点S和T，求一条路径，使得路径上最大边和最小边的比值最小。如果S和T之间没有路径，输出”IMPOSSIBLE”，否则输出这个比值，如果需要，表示成一个既约分数。 备注： 两个顶点之间可能有多条路径。

Input

　　第一行包含两个正整数，N和M。下来的M行每行包含三个正整数：x，y和v。表示景点x到景点y之间有一条双向公路，车辆必须以速度v在该公路上行驶。最后一行包含两个正整数s，t，表示想知道从景点s到景点t最大最小速度比最小的路径。s和t不可能相同。1<N<=500,1<=x,y<=N，0<v<30000，0<M<=5000

Output

　　如果景点s到景点t没有路径，输出“IMPOSSIBLE”。否则输出一个数，表示最小的速度比。

　　如果需要，输出一个既约分数。

Sample Input

【样例输入1】

　　4 2

　　1 2 1

　　3 4 2

　　1 4

【样例输入2】

　　3 3

　　1 2 10

　　1 2 5

　　2 3 8

　　1 3

【样例输入3】

　　3 2

　　1 2 2

　　2 3 4

　　1 3

Sample Output

【样例输出1】

　　IMPOSSIBLE

【样例输出2】

　　5/4

【样例输出3】

　　2

解题思路：
　　本题给出景点数量n，道路数量m，之后给出m行，每行包括三个整数，分别为道路两端的景点，x，y与该道路的行驶速度v，下一行为两个整数，分别为起始景点s，目标景点t，如果s与t之间连通，就选择一条道路使从s到达t的最小速度比最大，输出其最大边和最小边的比值的最小值的最简分数形式，若不连通，输出IMPOSSIBLE。

　　本题最小速度比是指一条道路上最大的速度与最小的速度的比值最小。且我们首先思考如何找到一条连通s与t道路上的最大边与最小边，怎么办？最小生成树！克鲁斯卡尔！

kruskal算法核心思想：　　

　　既然已经给出了邻接表。将道路按速度由小到大排序，枚举最小速度，初始视所有景点都为不连通，之后由最小速度的道路开始以速度从小到大枚举所有道路，判断道路两端的景点是否已经连通，若已经连通不做处理，若不连通则将该道路记录入最小生成树，标记道路两端为连通，判断s与t是否连通，若连通标记s与t可达，计算此时的最大速度与最小速度比值（因为道路速度由小到大遍历所以最小速度为开始遍历时的道路速度，最大速度为当前道路速度），将现在的比值与之前记录的比值比较，如果现在的值小于先前的值，将比值记录为现在的比值，并记录这时的最大速度与最小速度。

```c++
bool kruskal(int n, int m, int s, int t, int &maxL, int &minL){
    //传入的n为景点数量，m为道路数量，s为起点，t为目标景点
    //由于要改变maxL与minL记录最终的最大值与最小值，所以maxL与minL传引用
    double ans = inf;   //初始化比值为无穷大
    bool flag = false;  //标记s与t为不可达
    sort(Edge + 1, Edge + 1 + m, cmp);  //将道路由小到大排序
    for(int i = 1; i <= m; i++){    //枚举最小速度
        for(int k = 1; k <= n; k++){    //初始化所有景点为不连通
            father[k] = k;
        }
        int minlen = Edge[i].v, maxlen = 0; //记录最小速度，初始化最大速度为0
        for(int j = i; j <= m; j++){    //以速度由小到大枚举所有道路
            int faNode1 = getFather(Edge[j].node1);
            int faNode2 = getFather(Edge[j].node2);
            maxlen = Edge[j].v; //记录最大速度为当前道路速度
            if(faNode1 != faNode2){ //判断道路两端顶点是否连通
                father[faNode1] = faNode2;  //不连通就标记为连通
                if(getFather(s) == getFather(t)){   //判断s与t是否连通
                    flag = true;    //如果连通标记s与t为可达
                    if(ans > (double)maxlen / minlen){  
            //计算此时的最大速度与最小速度比值，将现在的比值与之前记录的比值比较
                        ans = (double)maxlen / minlen;
                    //如果现在的值小于先前的值，将比值记录为现在的比值
                        maxL = maxlen;
                        minL = minlen;
                        //记录这时的最大速度与最小速度
                        break;
                    }
                }
            }
        }
    }
    if(flag){   //如果s与t可达返回true，否则返回false
        return true;
    }else{
        return false;
    }
}
```

 是否连通用并查集进行判断 

```c++
int father[maxn], maxL, minL;   //并查集部分
int getFather(int x)
{
    if(father[x] == x)
        return x;
    else
        return father[x] = getFather(father[x]);
}
```

 之后得到了最小速度与最大速度，只需要让最大速度与最小速度除以他们的最大公约数便可以获得分子与分母。 

 AC代码 

```c++
#include <bits/stdc++.h>
using namespace std;
const int inf = 0x7fffffff; //无穷大
const int maxn = 3e4+10;
struct edge{    //edge保存道路
    int node1, node2;   //道路两端景点
    int v;  //道路速度
}Edge[maxn];
int gcd(int a, int b){  //计算最大公约数
    if(b == 0)
        return a;
    else
        return gcd( b, a % b);
}
bool cmp(edge e1, edge e2){ //道路按速度由小到大排序
    return e1.v < e2.v;
}
int father[maxn], maxL, minL;   //并查集部分
int getFather(int x)
{
    if(father[x] == x)
        return x;
    else
        return father[x] = getFather(father[x]);
}
bool kruskal(int n, int m, int s, int t, int &maxL, int &minL){
    //传入的n为景点数量，m为道路数量，s为起点，t为目标景点
    //由于要改变maxL与minL记录最终的最大值与最小值，所以maxL与minL传引用
    double ans = inf;   //初始化比值为无穷大
    bool flag = false;  //标记s与t为不可达
    sort(Edge + 1, Edge + 1 + m, cmp);  //将道路由小到大排序
    for(int i = 1; i <= m; i++){    //枚举最小速度
        for(int k = 1; k <= n; k++){    //初始化所有景点为不连通
            father[k] = k;
        }
        int minlen = Edge[i].v, maxlen = 0; //记录最小速度，初始化最大速度为0
        for(int j = i; j <= m; j++){    //以速度由小到大枚举所有道路
            int faNode1 = getFather(Edge[j].node1);
            int faNode2 = getFather(Edge[j].node2);
            maxlen = Edge[j].v; //记录最大速度为当前道路速度
            if(faNode1 != faNode2){ //判断道路两端顶点是否连通
                father[faNode1] = faNode2;  //不连通就标记为连通
                if(getFather(s) == getFather(t)){   //判断s与t是否连通
                    flag = true;    //如果连通标记s与t为可达
                    if(ans > (double)maxlen / minlen){  
            //计算此时的最大速度与最小速度比值，将现在的比值与之前记录的比值比较
                        ans = (double)maxlen / minlen;
                    //如果现在的值小于先前的值，将比值记录为现在的比值
                        maxL = maxlen;
                        minL = minlen;
                        //记录这时的最大速度与最小速度
                        break;
                    }
                }
            }
        }
    }
    if(flag){   //如果s与t可达返回true，否则返回false
        return true;
    }else{
        return false;
    }
}
int main()
{
    int numNode, numEdge;   
    while(scanf("%d%d", &numNode, &numEdge)!= EOF){ //输入景点数与道路数
        for(int i = 1; i <= numEdge; i++){  //输入道路
            scanf("%d%d%d", &Edge[i].node1, &Edge[i].node2, &Edge[i].cost);
        }
        int s, t;
        scanf("%d%d", &s, &t);  //输入起点终点
        int maxL  = 0;
        int minL = 0;
        //初始化最大最小速度都为0
        if(kruskal(numNode, numEdge, s, t, maxL, minL)){    //如果s与t连通
            int temp = gcd(maxL, minL); //计算最大最小速度的最大公约数
            //printf("%d\n", gcd(maxL, minL));
            int a = maxL/temp, b = minL/temp;   //计算分子分母
            if(b != 1)  //分子不等于1输出最简分数
                printf("%d/%d\n", a, b);
            else
                printf("%d\n", a);  //分子为1直接输出分母
        }else{  //不连通输出IMPOSSIBLE
            printf("IMPOSSIBLE\n");
        }
    }
    return 0;
}
```

