---
title: HihoCoder 1040 矩形判断
date: 2019-01-28 19:13:57
categories: 
- 算法相关
tags:
- HihoCoder
- STL
---

　　给出平面上4条线段，判断这4条线段是否恰好围成一个面积大于0的矩形。

Input

　　输入第一行是一个整数T(1<=T<=100)，代表测试数据的数量。

　　每组数据包含4行，每行包含4个整数x1, y1, x2, y2 (0 <= x1, y1, x2, y2 <= 100000)；其中(x1, y1), (x2,y2)代表一条线段的两个端点。

Output

　　每组数据输出一行YES或者NO，表示输入的4条线段是否恰好围成矩形。

Sample Input

```
3
0 0 0 1
1 0 1 1
0 1 1 1
1 0 0 0
0 1 2 3
1 0 3 2
3 2 2 3
1 0 0 1
0 1 1 0
1 0 2 0
2 0 1 1
1 1 0 1
```

Sample Output

```
YES
YES
NO
```

时间限制:1000ms

单点时限:1000ms

内存限制:256MB

题目解析
　　本题给出测试数量t，每组测试给出4条线段，要求判断四条线段是否能围成矩形。

　　本题的数据量t很少，不会超过100，且每条线段有两个端点每组数据4条线段，所以不同的点不会超过8个，所有测试数据中包含的不同的点不会超过800个，这样我们就可以放心大胆的浪费内存空间了。

　　一、若给出的4条线段可以围成矩形，那么其中必定包含4个不同的端点，利用set的不重复性记录一下4条线段的所有端点，若最后set中的端点不为四个，那这4条线段肯定不能围成一个矩形。

　　二、若给出的4条线段可以围成矩形，所以围成的图形一定是个封闭图形，4个端点的度一定为2，利用map建立每个端点和其连通端点集的映射，检查每个端点对应连通集合中是否只有两个端点，若不为两个端点，那这4条线段肯定不能围成一个矩形。

　　三、若给出的4条线段可以围成矩形，则每条线段都会有另一条长度相等的线段与其平行，用vector保存每个线段对应的向量，则若存在两条长度相等且平行的线段，其对应向量相等或符号相反，之后遍历所有向量，与其相等或符号相反的向量定有其本身加上平行相等线段对应的向量共两条，若不为两条那这4条线段肯定不能围成一个矩形。

　　四、若给出的4条线段可以围成矩形，则每条线段都会有两条与其垂直的线段，因此每条向量都会有两条与其垂直的向量，遍历所有向量获取与其垂直的向量数量，若不为两条那这4条线段肯定不能围成一个矩形。（向量(x1, y1)与(x2, y2)垂直 : x1 * y2 + x2 * y1 == 0）。

　　若满足以上4条，给出的4条线段可以围成矩形。

　　AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
int t;  
set<pair<int, int> > nodeSet;   //nodeSet储存所有的端点
map<pair<int, int>, vector<pair<int, int> > > edgeMap;  //edgeMap[i] 储存与i相连的所有点的集合
vector<pair<int, int> > vectors;    //vectors 储存每条边对应的向量
int main()
{
    scanf("%d", &t);    //测试数量输入
    while(t--){
        //清空所有容器
        nodeSet.clear();    
        edgeMap.clear();
        vectors.clear();
        //每条输入的信息包括两个点(x1, y1) (x2, y2)
        int x1, x2, y1, y2;
        for(int i = 0; i < 4; i++){ //输入四条线段
            scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
            pair<int, int> tempNode1, tempNode2, tempVectors;
            // pair<int, int> tempNode1 记录第一个点 tempNode1.first记录x坐标 tempNode1.second记录y坐标
            // pair<int, int> tempNode2 记录第二个点 tempNode2.first记录x坐标 tempNode2.second记录y坐标  
            // pair<int, int> tempVectors 记录当前线段对应的向量 tempVectors.first记录x轴位移量 tempVectors.second记录y轴位移量
            tempNode1.first = x1;
            tempNode1.second = y1;
            nodeSet.insert(tempNode1);  //nodeSet中加入tempNode1
            tempNode2.first = x2;
            tempNode2.second = y2;
            nodeSet.insert(tempNode2);  //nodeSet中加入tempNode2
            edgeMap[tempNode1].push_back(tempNode2);    //tempNode1的连通顶点集中加入tempNode2
            edgeMap[tempNode2].push_back(tempNode1);    //tempNode2的连通顶点集中加入tempNode1
            tempVectors.first = x2 - x1;    //计算x轴位移量
            tempVectors.second = y2 - y1;   //计算y轴位移量
            vectors.push_back(tempVectors); //向量集vectors中加入tempVectors
        }
        if(nodeSet.size() != 4){    //判断端点是否为4个
            printf("NO\n");
            continue;
        }
        bool isRectangle = true;
        for(auto i : nodeSet){  //判断每个端点的度是否为2
            if(edgeMap[i].size() != 2){
                printf("NO\n");
                isRectangle = false;
                break;
                //goto here; 保持气节不要用goto goto是程序的敌人 我一开始用的goto 虽然也AC了不过我改过自新，回头是岸
            }
        }
        if(!isRectangle)
            continue;
        isRectangle = true;
        int cnt;
        for(auto i : vectors){  //判断每条向量是否有一条向量与其平行且相等
            cnt = 0;
            for(auto j : vectors){
                if(i == j || (i.first == -j.first && i.second == -j.second)){
                    cnt++;
                }
            }
            if(cnt != 2){   //加上本身一共两个
                printf("NO\n");
                isRectangle = false;
                break;
                //goto here;
            }
        }
        if(!isRectangle)
            continue;
        isRectangle = true;
        for(auto i : vectors){  //判断每条向量是否有两个与其垂直的向量
            cnt = 0;
            for(auto j : vectors){
                if(i.first * j.second + j.first * i.second == 0){
                    cnt++;
                }
            }
            if(cnt != 2){
                printf("NO\n");
                isRectangle = false;
                break;
                //goto here;
            }
        }
        if(!isRectangle)
            continue;
        printf("YES\n");
        //here:;
    }
    return 0;
}
```

