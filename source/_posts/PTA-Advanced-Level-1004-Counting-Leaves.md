---
title: PTA (Advanced Level) 1004 Counting Leaves
date: 2019-04-02 21:04:09
categories: 
- 算法相关
tags:
- DFS（深度优先搜索）
- PTA
---
　　A family hierarchy is usually presented by a pedigree tree. Your job is to count those family members who have no child.

Input Specification:
　　Each input file contains one test case. Each case starts with a line containing 0 < N < 100, the number of nodes in a tree, and M (<N), the number of non-leaf nodes. Then M lines follow, each in the format:

ID K ID[1] ID[2] ... ID[K]
　　where ID is a two-digit number representing a given non-leaf node, K is the number of its children, followed by a sequence of two-digit ID's of its children. For the sake of simplicity, let us fix the root ID to be 01.

The input ends with N being 0. That case must NOT be processed.

Output Specification:
　　For each test case, you are supposed to count those family members who have no child for every seniority level starting from the root. The numbers must be printed in a line, separated by a space, and there must be no extra space at the end of each line.

　　The sample case represents a tree with only 2 nodes, where 01 is the root and 02 is its only child. Hence on the root 01 level, there is 0 leaf node; and on the next level, there is 1leaf node. Then we should output 0 1 in a line.

Sample Input:
2 1
01 1 02
Sample Output:
0 1
题目解析

　　本题给出一颗家族关系树第一行首先给出两个整数0<N<100为家族中人数，M<N为非叶子节点个数（有孩子的人的数量）之后跟随M行，每行都是一个有孩子的人的信息，其中包括一个两位id为其对应编号，一个整数K为其孩子的数量，之后K个两位数为其孩子的编号。家族树的根结点编号为1，要求由根结点所在层开始输出每层拥有的叶子结点个数（每层没有孩子的成员个数）。

用一个vector<int> 类型的数组child[ ]储存每个人的孩子的编号，int型数组cnt[ ]储存每层的叶子结点数量，视根结点为第0层由根结点开始dfs搜索其每一个孩子，每个点的孩子所在的层数就是其层数加一，若搜索到没有孩子的结点便将其对应层叶子结点数量加一。将搜索到的最大层数计入maxL。

最后由0到maxL输出每层的叶子结点数量即可。

```c++
#include <bits/stdc++.h>
using namespace std;
vector<int> child[110];  //储存每个id的孩子
int cnt[110]; //记录每层叶子结点的个数
int n, m;   //n为总人数， m为非叶子结点数量
int maxL = INT_MIN; //maxL记录最大层数
void dfs(int id, int nowlevel){
    maxL = max(maxL, nowlevel); //记录最大层数
    if(child[id].empty())   //如果该点没有孩子表明其为叶子结点
        cnt[nowlevel]++;    //其对应层的叶子结点数量加一
    for(auto i : child[id]) //搜索其所有的孩子
        dfs(i, nowlevel + 1);
}
int main()
{
    scanf("%d%d", &n, &m);  //输入总人数与非叶子结点数
    for(int i = 0; i < m; i++){ //输入所有非叶子结点信息
        int id, k;  //该点id与孩子数量k
        scanf("%d%d", &id, &k);
        for(int j = 0; j < k; j++){ //输入该点所有孩子
            int cid;
            scanf("%d", &cid);
            child[id].push_back(cid);   //记录该点的孩子
        }
    }
    dfs(1, 0);  //1为根结点，由根结点第0层开始搜索
    for(int i = 0; i <= maxL; i++){
        if(i != 0)
            printf(" ");
        printf("%d", cnt[i]);
    }
    return 0;
}
```

