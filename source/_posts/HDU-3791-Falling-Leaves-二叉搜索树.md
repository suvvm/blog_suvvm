---
title: HDU 3791 Falling Leaves 二叉搜索树
date: 2018-10-29 22:57:34
categories: 
- 算法相关
tags:
- 二叉搜索树
- HDU
---
Figure 1 shows a graphical representation of a binary tree of letters. People familiar with binary trees can skip over the definitions of a binary tree of letters, leaves of a binary tree, and a binary search tree of letters, and go right to The problem. 

A binary tree of letters may be one of two things: 
It may be empty. 
It may have a root node. A node has a letter as data and refers to a left and a right subtree. The left and right subtrees are also binary trees of letters.

In the graphical representation of a binary tree of letters: 
Empty trees are omitted completely. 
Each node is indicated by 
Its letter data, 
A line segment down to the left to the left subtree, if the left subtree is nonempty, 
A line segment down to the right to the right subtree, if the right subtree is nonempty.

A leaf in a binary tree is a node whose subtrees are both empty. In the example in Figure 1, this would be the five nodes with data B, D, H, P, and Y. 

The preorder traversal of a tree of letters satisfies the defining properties: 
If the tree is empty, then the preorder traversal is empty. 
If the tree is not empty, then the preorder traversal consists of the following, in order 
The data from the root node, 
The preorder traversal of the root's left subtree, 
The preorder traversal of the root's right subtree.

The preorder traversal of the tree in Figure 1 is KGCBDHQMPY. 

A tree like the one in Figure 1 is also a binary search tree of letters. A binary search tree of letters is a binary tree of letters in which each node satisfies: 

The root's data comes later in the alphabet than all the data in the nodes in the left subtree. 

The root's data comes earlier in the alphabet than all the data in the nodes in the right subtree. 

The problem: 

Consider the following sequence of operations on a binary search tree of letters 

Remove the leaves and list the data removed 
Repeat this procedure until the tree is empty 
Starting from the tree below on the left, we produce the sequence of trees shown, and then the empty tree 


by removing the leaves with data 

BDHPY 
CM 
GQ 
K 

Your problem is to start with such a sequence of lines of leaves from a binary search tree of letters and output the preorder traversal of the tree.
Input

The input will contain one or more data sets. Each data set is a sequence of one or more lines of capital letters. 

The lines contain the leaves removed from a binary search tree in the stages described above. The letters on a line will be listed in increasing alphabetical order. Data sets are separated by a line containing only an asterisk ('*'). 

The last data set is followed by a line containing only a dollar sign ('$'). There are no blanks or empty lines in the input.
Output

For each input data set, there is a unique binary search tree that would produce the sequence of leaves. The output is a line containing only the preorder traversal of that tree, with no blanks.
Sample Input

BDHPY
CM
GQ
K
*
AC
B
$
Sample Output

KGCBDHQMPY
BAC

解题思路：　　　　
　　本题给出多组字符串每组以*为结尾以$为结束条件要求输出每一组数据所建立的二叉搜索树的先序遍历
如例中输入：
BDHPY 其先序遍历为:KGCBDHQMPY。 

```c++
#include <cstdio>
#include <cstring>
#include <vector>
#include <iterator>
#include <iostream>
#include <algorithm>
// bits/stdc++.h会编译错误
using namespace std;
typedef char typeData;
struct node{
    typeData data;
    node *leftChild;
    node *rightChild;
    node(){
        leftChild = NULL;
        rightChild = NULL;
    }
};
vector<typeData> data;
string temp, str;
void insertBST(node *&root, typeData x){ //二叉搜索树插入函数
     //注意函数要进行插入操作，根结点要传引用
    if(root == NULL){   //找到空位置即使插入位置
        root = new node();  //新建结点权值为x
        root->data = x;
        return;
    }
    if(x == root->data){    //要插入结点已存在直接返回
        return;
    }else if(root->data > x){   //x比根结点数据域小 需要插在左子树
        insertBST(root->leftChild, x);   //往左子树搜索
    }else if(root->data < x){    //x比根结点数据域大 需要插在右子树
        insertBST(root->rightChild, x); //往右子树搜索
    }
}
node *createBST(){  //建树
    node *root = NULL;
    for(string::iterator it = str.begin(); it != str.end(); it++){
        insertBST(root, *it);   //以str为数据组建树
    }
    return root;    //返回根结点
}
void preorder(node *root){
    if(root == NULL)//到达空树为递归边界
        return;
    printf("%c", root->data);  //访问根结点输出权值
    preorder(root->leftChild); //访问左子树
    preorder(root->rightChild);  //访问右子树
}
int main()
{
    while(cin >> temp){  //输入字符串
        if(temp != "*" && temp != "$"){
            //如果输入的字符串不是组结束符或结尾符
            str += temp;    //将出入的字符串加到str字符串尾部
            continue;
        }
        //如果输入的时组结束符或结尾符
        reverse(str.begin(),str.end()); //将str数组反转
        node *root = createBST();   //建树
        str = "";   //将str清空
        preorder(root); //输出前序遍历
        printf("\n");
        if(temp == "$")//如果是结尾符跳出循环
            break;
    }
    return 0;
}
```

