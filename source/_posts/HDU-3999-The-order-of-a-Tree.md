---
title: HDU 3999 The order of a Tree
date: 2018-10-30 23:34:45
categories: 
- 算法相关
tags:
- 二叉搜索树
- HDU
---
As we know,the shape of a binary search tree is greatly related to the order of keys we insert. To be precisely: 
　　1.  insert a key k to a empty tree, then the tree become a tree with only one node;  
　　2.  insert a key k to a nonempty tree, if k is less than the root ,insert it to the left sub-tree;else insert k to the right sub-tree. 
　　We call the order of keys we insert “the order of a tree”,your task is,given a oder of a tree, find the order of a tree with the least lexicographic order that generate the same tree.Two trees are the same if and only if they have the same shape. 
Input 

　There are multiple test cases in an input file. The first line of each testcase is an integer n(n <= 100,000),represent the number of nodes.The second line has n intergers,k1 to kn,represent the order of a tree.To make if more simple, k1 to kn is a sequence of 1 to n. 

Output

　One line with n intergers, which are the order of a tree that generate the same tree with the least lexicographic. 
Sample Input

4

1 3 4 2
Sample Output

1 3 2 4

解题思路：
　　本题给出多组数据每组数据包括结点数量和结点权值，要求以输入的权值建立二叉搜索树，并输出可以建立同样二叉搜索树且字典序最小的一组数据，根据二叉搜索树的性质我们可以发现，最小的字典序就是二叉搜索树的前序遍历。我们只需要输出二叉搜索树的前序遍历即可。

```c++
#include <bits/stdc++.h>
using namespace std;
typedef int dataTpye;
vector<dataTpye> str;
struct node{
    dataTpye data;
    node *leftChild;
    node *rightChild;
    node(){
        leftChild = NULL;
        rightChild = NULL;
    }
};
void insertBST(node *&root, dataTpye x){//二叉搜索树插入函数
    if(root == NULL){   //找到空位置即使插入位置
        root = new node();  //新建结点权值为x
        root->data = x;
        return;
    }
    if(root->data == x) //要插入结点已存在直接返回
        return;
    else if(root->data > x){    //x比根结点数据域小 需要插在左子树
        insertBST(root->leftChild, x);
    }else if(root->data < x){   //x比根结点数据域大 需要插在右子树
        insertBST(root->rightChild, x);
    }
}
node *createBST(){   //建树
    node *root = NULL;
    for(vector<dataTpye>::iterator it = str.begin(); it != str.end(); it++){
        insertBST(root, *it);   //以str为数据组建树
    }
    return root;     //返回根结点
}
void preorder(node *root, node *flag){
    if(root == NULL)    //到达空树为递归边界
        return;
    if(root != flag)
         printf(" ");
    printf("%d", root->data);   //访问根结点输出权值
    preorder(root->leftChild, flag);    //访问左子树
    preorder(root->rightChild, flag);    //访问右子树

}
int main()
{
    int t;
    while(cin >> t){    //输入结点数量
        str.clear();    //清空权值记录容器
        for(int i = 0; i < t; i++){
            dataTpye temp;
            scanf("%d", &temp); //输入权值
            str.push_back(temp);
        }
        node *root = createBST();   //建树
        preorder(root, root);   //输出先序遍历
        printf("\n");
    }
    return 0;
}
```

