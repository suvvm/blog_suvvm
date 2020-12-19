---
title: HDU 1710 Binary Tree Traversals
date: 2018-11-04 22:17:26
categories: 
- 算法相关
tags:
- 二叉树遍历
- HDU
---
<meta name="referrer" content="no-referrer"/>
A binary tree is a finite set of vertices that is either empty or consists of a root r and two disjoint binary trees called the left and right subtrees. There are three most important ways in which the vertices of a binary tree can be systematically traversed or ordered. They are preorder, inorder and postorder. Let T be a binary tree with root r and subtrees T1,T2. 

　　In a preorder traversal of the vertices of T, we visit the root r followed by visiting the vertices of T1 in preorder, then the vertices of T2 in preorder. 

　　In an inorder traversal of the vertices of T, we visit the vertices of T1 in inorder, then the root r, followed by the vertices of T2 in inorder. 

　　In a postorder traversal of the vertices of T, we visit the vertices of T1 in postorder, then the vertices of T2 in postorder and finally we visit r. 

　　Now you are given the preorder sequence and inorder sequence of a certain binary tree. Try to find out its postorder sequence. 
 ![img](http://acm.hdu.edu.cn/data/images/C57-1005-1.jpg) 

Input

　　The input contains several test cases. The first line of each test case contains a single integer n (1<=n<=1000), the number of vertices of the binary tree. Followed by two lines, respectively indicating the preorder sequence and inorder sequence. You can assume they are always correspond to a exclusive binary tree. 
Output

　　For each test case print a single line specifying the corresponding postorder sequence. 
Sample Input

```
9
1 2 4 7 3 5 8 9 6
4 7 2 1 8 5 9 3 6
```

Sample Output

```
7 4 2 8 9 5 6 3 1
```

解题思路：
　　本题给二叉树的结点数量，之后第一行给出二叉树的先序遍历，第二行给出二叉树的中序遍历，根据前序遍历的性质，先序遍历的首位为根结点，以该点为根结点建树，从中序遍历中寻找该点，假设找到新的位置为k，则从中序遍历首位到k-1，为左子树中序遍历的范围，从k+1到中序遍历末位为右子树中序范围，，这样我们就可以得知左子树长度，右子树长度，进而获得左子树与右子树的前序遍历。对获得的左子树右子树的前序遍历中序遍历进行同样的操作，便可以建立该树。

```c++
#include <bits/stdc++.h>
using namespace std;
typedef int typeData;
const int maxn = 1100;
int pre[maxn];  //记录前序遍历
int in[maxn];   //记录中序遍历
struct node{
    typeData data; 
    node* leftChild;
    node* rightChild;
    node(){
        leftChild = NULL;
        rightChild = NULL;
    }
};

void postorder(node *root, node *flag){ //输出后序遍历
    if(root == NULL)
        return;
    postorder(root->leftChild, flag);
    postorder(root->rightChild, flag);

    printf("%d", root->data);
    if(root != flag)
        printf(" ");
}
//传入先序遍历起始位置，中序遍历起始位置
node* create(int preL, int preR, int inL, int inR){
    if(preL > preR) //如果前序遍历中没有数值返回NULL
        return NULL;
    node* root = new node();    //建立新结点，其权值为，当前先序遍历的首位，即当前树根结点
    root->data = pre[preL];
    int k;  //k记录根结点在中序遍历中的位置
    for(k = inL; k < inR; k++){ //在中序遍历中寻找根结点位置
        if(pre[preL] == in[k])
            break;
    }
    int numLeft = k - inL;  //计算左子树长度
    root->leftChild = create(preL + 1, preL + numLeft, inL, k - 1); //递归建立左子树
    //先序遍历中左子树区域为根结点的下一位到左子树起始点加左子树长度
    //中序遍历中左子树区域为中序遍历首位到根结点之前
    root->rightChild = create(preL + numLeft + 1, preR, k + 1, inR);    //递归建立右子树
    //先序遍历中右子树区域为左子树末尾的下一位到先序遍历末位
    //中序遍历中右子树区域为根结点后一位到中序遍历末位
    return root;
}
int main()
{
    int n;
    while(cin >> n){
        memset(pre, 0, sizeof(pre));
        memset(in, 0, sizeof(in));
        for(int i = 0; i < n; i++){
            scanf("%d", &pre[i]);   //输入先序遍历
        }
        for(int i = 0; i < n; i++){ //输入中序遍历
            scanf("%d", &in[i]);
        }
        node* root = create(0, n - 1, 0, n - 1);    //建树
        postorder(root, root);  //输出后序遍历
        printf("\n");
    }
}
```

