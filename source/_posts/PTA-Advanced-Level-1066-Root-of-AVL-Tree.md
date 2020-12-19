---
title: PTA (Advanced Level) 1066 Root of AVL Tree
date: 2018-11-01 23:18:37
categories: 
- 算法相关
tags:
- 平衡二叉树（AVL树）
- PAT
---
<meta name="referrer" content="no-referrer"/>
An AVL tree is a self-balancing binary search tree. In an AVL tree, the heights of the two child subtrees of any node differ by at most one; if at any time they differ by more than one, rebalancing is done to restore this property. Figures 1-4 illustrate the rotation rules.

 

![img](https://images.ptausercontent.com/31)![img](https://images.ptausercontent.com/32)

![img](https://images.ptausercontent.com/33) ![img](https://images.ptausercontent.com/34)

Now given a sequence of insertions, you are supposed to tell the root of the resulting AVL tree.

 

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer N (≤) which is the total number of keys to be inserted. Then Ndistinct integer keys are given in the next line. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, print the root of the resulting AVL tree in one line.

### Sample Input 1:

```in
5
88 70 61 96 120
```

### Sample Output 1:

```out
70
```

### Sample Input 2:

```
7
88 70 61 96 120 90 65
```

### Sample Output 2:

```
88
```

AVL树作用：
　　对于正常二叉搜索树建立过程，以第一个结点为根结点，若输入的结点权值大于第一个结点则插入右子树，小于第一个结点则插入左子树。若遇到出入数据为有序的情况，普通二叉搜索树就会建立一个长链式的树，其查询复杂度就会达到O(n)，如以{1， 2， 3， 4 ，5 ，6， 7， 8， 9， 10}建立的二叉搜索树：

![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181102213013498-1251399768.png)

若要保持查询复杂度为O(logn)则需要建立AVL树，只需在插入过程中通过左旋右旋操作保证叶子结点的最大高度差不超过1，以{1， 2， 3， 4 ，5 ，6， 7， 8， 9， 10}建立AVL树：

![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181102213306307-2093137586.png)
解题思路：
　　AVL树模板题要求按输入建立AVL树即在二叉搜索树叶子结点最大高度差大于等于二的时候进行左旋或右旋进行结构优化使结点深度保持在O(logn)的级别,输出AVL树根结点。

```c++
#include <bits/stdc++.h>
using namespace std;
typedef int dataType;
vector<dataType> data;
struct node{
    dataType data;
    int height; //AVL树结点比起普通二叉搜索树需要记录height
    node *leftChild;
    node * rightChild;
    node(){
        height = 1;
        leftChild = NULL;
        rightChild = NULL;
    }
};
int getHeight(node *root){  //获取高度
    if(root == NULL)  
        return 0;
    else
        return root->height;
}
int getBalanceFactor(node *root){   //获取树的叶子结点高度差左高为正右高为负
    return getHeight(root->leftChild) - getHeight(root->rightChild);
}
int updateHeight(node *root){   //更新高度
    root->height = max(getHeight(root->leftChild),getHeight(root->rightChild)) + 1;
}
void leftRotation(node *&root){ //左旋
    node *temp = root->rightChild;      //root指向先前根结点temp指向右子树根结点
    root->rightChild = temp->leftChild; //temp指向根结点的右子树，所以其所有结点都大于根结点
    //由于在左旋中需要使temp成为新的根结点，所以将root右子树指向temp左子树，再让temp左子树指向root
    temp->leftChild = root;
    //更新root与temp的树高
    updateHeight(root);
    updateHeight(temp);
    root = temp;    //temp成为新的根结点
}
void rightRotation(node *&root){    //右旋思路同左旋
    node *temp = root->leftChild;
    root->leftChild = temp->rightChild;
    temp->rightChild = root;
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}
void insertAVLTree(node *&root, int x){ //插入结点
    if(root == NULL){   //找到插入位置
        root = new node();
        root->data = x;
        return;
    }
    if(root->data == x){    //结点已存在
        return;
    }else if(root->data > x){   //要插入的数据比根结点权值小
        insertAVLTree(root->leftChild, x);  //插入左子树
        updateHeight(root);
        if(getBalanceFactor(root) == 2){　　//插入左子树时只可能出现左子树比右子树高的情况
            if(getBalanceFactor(root->leftChild) == 1){　　//若左子树的左子树较高直接右旋
                rightRotation(root);
            }else if(getBalanceFactor(root->leftChild) == -1){　　//若左子树中右子树较高则将其通过右旋转化为左子树高的情况　　种情况详见下图
                leftRotation(root->leftChild);
                rightRotation(root);
            }
        }
    }else if(root->data < x){   //要插入的数据比根结点权值大
        insertAVLTree(root->rightChild, x); //插入右子树
        updateHeight(root);
        if(getBalanceFactor(root) == -2){
            if(getBalanceFactor(root->rightChild) == -1){
                leftRotation(root);
            }else if(getBalanceFactor(root->rightChild) == 1){
                rightRotation(root->rightChild);
                leftRotation(root);
            }
        }
    }
}
node *createAVLTree(){
    node *root = NULL;
    for(vector<dataType>::iterator it = data.begin(); it != data.end(); it++){
        insertAVLTree(root, *it);
    }
    return root;
}
/*void preorder(node *root){
    if(root == NULL)
        return;
    cout << root -> data << " ";
    preorder(root -> leftChild);
    preorder(root -> rightChild);
}*/
int main()
{
    int n;
    while(scanf("%d", &n) != EOF){
        data.clear();
        for(int i = 0; i < n; i++){
            dataType temp;
            scanf("%d", &temp);
            data.push_back(temp);
        }
        node *root = createAVLTree();
        //preorder(root);
        //cout << endl;
        printf("%d\n", root->data);
    }
    return 0;
}
```

左子树的左子树较高

![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181102214830714-561437459.png)

左子树的右子树较高

 ![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181102215548311-750974049.png) 

