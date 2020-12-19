---
title: PTA (Advanced Level) 1020 Tree Traversals
date: 2019-01-08 21:01:30
categories: 
- 算法相关
tags:
- PTA
- 二叉树遍历
- BFS（广度优先搜索)
---

　　Suppose that all the keys in a binary tree are distinct positive integers. Given the postorder and inorder traversal sequences, you are supposed to output the level order traversal sequence of the corresponding binary tree.

### Input Specification:

　　Each input file contains one test case. For each case, the first line gives a positive integer N (≤), the total number of nodes in the binary tree. The second line gives the postorder sequence and the third line gives the inorder sequence. All the numbers in a line are separated by a space.

### Output Specification:

　　For each test case, print in one line the level order traversal sequence of the corresponding binary tree. All the numbers in a line must be separated by exactly one space, and there must be no extra space at the end of the line.

### Sample Input:

```in
7
2 3 1 5 7 6 4
1 2 3 4 5 6 7
```

### Sample Output:

```out
4 1 6 3 5 7 2
```

题目解析
　　本题第一行给出二叉树的结点数量n，之后第二行给出二叉树的后序遍历，第三行给出二叉树的中序遍历，要求输出二叉树的层序遍历。（保证树中的每个结点数据都不同）

　　后序遍历中最后一个元素便是当前树的根结点，在中序遍历中找到根结点对应的值，其左侧便是左子树的中序遍历，在知道左子树中序遍历后可以根据结点数量在后续遍历中找到左子树的后续遍历，确定了左子树与根结点后剩下的便是右子树信息了，以此类推可以得到整棵二叉树。

　　建立二叉树后使用广搜，由于广搜二叉树维护队列时，每次出队一个结点并将其对应的左孩子与右孩子入队，这就是层序遍历。

　　AC代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int maxn = 35;
int postorder[maxn];    //储存后续遍历
int inorder[maxn];      //储存中序遍历
struct node{    //二叉树结点信息
    int data;
    node *lchild;
    node *rchild;
    node(int tdata){    //构造函数
        data = tdata;
        lchild = NULL;
        rchild = NULL;
    }
};
node *create(int postL, int postR, int inL, int inR){   
    //由于要递归建树我们并不需要将每个树的左子树右子树的后序遍历与中序遍历储存下来
    //只需要在原始postorder与inorder数组中标记出其位置即可
    //postL与postR表示当前后续遍历的左右两端，inL与inR表示当前中序遍历的左右两端
    if(postL > postR)   //如果后续遍历左端位置大于右端位置，证明当前正在建立的树为空树
        return NULL;
    node *root = new node(postorder[postR]);    //建立根结点，权值为postorder[postR]左右子树都为空
    int i;
    for(i = inL; inorder[i] != postorder[postR]; i++);  //在中序遍历中找到根结点
    int lenl = i - inL; //记录左子树长度
    root->lchild = create(postL, postL + lenl - 1, inL, inL + lenl - 1);
    //建立左子树
    root->rchild = create(postL + lenl, postR - 1, inL + lenl + 1, inR);
    //建立右子树
    return root;
}
int n;  //n记录结点数量
void levelorder(node *root){    //层序遍历
    if(root == NULL)    
        return;
    queue<node*> Q;
    Q.push(root);   //根结点入队
    int cnt = 0;    //记录当前已经输出了几个数，用于之后判断是否输出空格
    while(!Q.empty()){
        cnt++;
        node *top = Q.front();
        Q.pop();
        printf("%d", top->data);//输出结点权值
        if(cnt != n)
            printf(" ");
        if(top->lchild != NULL) //左儿子入队
            Q.push(top->lchild);
        if(top->rchild != NULL)//右儿子入队
            Q.push(top->rchild);
    }
}
int main(){

    scanf("%d", &n);    //输入结点数量
    for(int i = 0; i < n; i++)  
        scanf("%d", &postorder[i]); //输入后序遍历
    for(int i = 0; i < n; i++)
        scanf("%d", &inorder[i]);   //输入中序遍历
    node *root = create(0, n - 1, 0, n - 1);    //建树
    levelorder(root);   //输出层序遍历
    return 0;
}
```

