---
title: HDU 5444 Elven Postman （2015 ACM/ICPC Asia Regional Changchun Online）
date: 2018-10-30 17:20:51
categories: 
- 算法相关
tags:
- 二叉搜索树
- HDU
---
Elves are very peculiar creatures. As we all know, they can live for a very long time and their magical prowess are not something to be taken lightly. Also, they live on trees. However, there is something about them you may not know. Although delivering stuffs through magical teleportation is extremely convenient (much like emails). They still sometimes prefer other more “traditional” methods. 

　　So, as a elven postman, it is crucial to understand how to deliver the mail to the correct room of the tree. The elven tree always branches into no more than two paths upon intersection, either in the east direction or the west. It coincidentally looks awfully like a binary tree we human computer scientist know. Not only that, when numbering the rooms, they always number the room number from the east-most position to the west. For rooms in the east are usually more preferable and more expensive due to they having the privilege to see the sunrise, which matters a lot in elven culture. 

　　Anyways, the elves usually wrote down all the rooms in a sequence at the root of the tree so that the postman may know how to deliver the mail. The sequence is written as follows, it will go straight to visit the east-most room and write down every room it encountered along the way. After the first room is reached, it will then go to the next unvisited east-most room, writing down every unvisited room on the way as well until all rooms are visited. 

　　Your task is to determine how to reach a certain room given the sequence written on the root. 

　　For instance, the sequence 2, 1, 4, 3 would be written on the root of the following tree. 


Input

　　First you are given an integer T(T≤10)T(T≤10)indicating the number of test cases. 

　　For each test case, there is a number n(n≤1000)n(n≤1000)on a line representing the number of rooms in this tree. nn integers representing the sequence written at the root follow, respectively a1,...,ana1,...,an where a1,...,an∈{1,...,n}a1,...,an∈{1,...,n}. 

　　On the next line, there is a number qqrepresenting the number of mails to be sent. After that, there will be qq integers x1,...,xqx1,...,xqindicating the destination room number of each mail.OutputFor each query, output a sequence of move (EE or WW) the postman needs to make to deliver the mail. For that EE means that the postman should move up the eastern branch and WW the western one. If the destination is on the root, just output a blank line would suffice. 

　　Note that for simplicity, we assume the postman always starts from the root regardless of the room he had just visited.Sample

Input

　　2
　　4
　　2 1 4 3
　　3
　　1 2 3
　　6
　　6 5 4 3 2 1
　　1
　　1
Sample Output

　　E

　　WE
　　EEEEE
解题思路：
　　本题有多组数据，每组数据包含第一行结点数量，第二行结点权值，第三行目标点数量，第行目标点权值要求建立二叉搜索树后，在树中查找目标点输出查找路径，查找左子树输出E，查找右子树输出W，若目标点为根结点输出一个空行。


样例解析：
　　2　　　　　　　　 //测试组数
　　4 　　　　　　　　//二叉搜索树结点数量（第一组）
　　2 1 4 3 　　　　     //二叉搜索树结点权值（第一组） 　　　　//所建树前序遍历2 1 4 3
　　3 　　　　　　　   //目标点数量（第一组）
　　1 2 3 　　　　　　//目标点权值（第一组） 　　　　　　　　//输出1:E 2:空行 3:WE
　　6 　　　　　　　  //二叉搜索树结点数量（第二组）
　　6 5 4 3 2 1 　　　 //二叉搜索树结点权值（第二组） 　　　　//所建树前序遍历6 5 4 3 2 1
　　1 　　　　　　　 //目标点数量（第二组）
　　1　　　　　　　  //目标点权值（第二组） 　　　　　　　　//输出1:EEEEE

```c++
#include <bits/stdc++.h>
using namespace std;
typedef int dataType;
vector<int> arrayn;
vector<int> pattern;
struct node{
    dataType data;
    node *leftChild;
    node *rightChild;
    node(){
        data = 0;
        leftChild = NULL;
        rightChild = NULL;
    }
};
void searchBST(node *root, dataType x){ //查找
    if(root == NULL){   //找到空位置查找失败返回
        return;
    }
    if(root->data == x){    //找到目标点换行
        printf("\n");
    }else if(root->data > x){   //x比根结点数据域小 查找左子树输出E
        printf("E");
        searchBST(root->leftChild, x);  //x比根结点数据域大 查找右子树输出W
    }else if(root->data < x){
        printf("W");
        searchBST(root->rightChild, x);
    }
}
void insertBST(node *&root, dataType x){    //插入
    if(root == NULL){   //找到空位置即使插入位置
        root = new node();  //新建结点权值为x
        root->data = x;
        return;
    }
    if(x == root->data){    //要插入结点已存在直接返回
        return;
    }
    else if(root->data > x){    //x比根结点数据域小 需要插在左子树
        insertBST(root->leftChild, x);
    }
    else if(root->data < x){    //x比根结点数据域大 需要插在右子树
        insertBST(root->rightChild, x);
    }
}
node *createBST(){  //以arrayn中记录的结点建树
    node *root = NULL;
    for(vector<int>::iterator it = arrayn.begin(); it != arrayn.end(); it++){
        insertBST(root, *it);
    }
    return root;
}
/*void preorder(node *root){
    if(root == NULL)
        return;
    printf("%d", root->data);
    preorder(root->leftChild);
    preorder(root->rightChild);
}*/
int main()
{
    int t;  //测试组数
    while(scanf("%d", &t) != EOF){
        int n;  //二叉搜索树结点数量和目标点数量
        while(t--){
            arrayn.clear(); //清空储存结点权值的容器
            scanf("%d", &n);    //输入结点数
            int temp;
            for(int i = 0; i < n; i++){
                scanf("%d", &temp); //输入权值
                arrayn.push_back(temp); //将权值储存在arrayn中
            }
            node *root = NULL;
            root = createBST(); //建树
            //preorder(rood);
            scanf("%d", &n);    //输入目标点数量
            for(int i = 0; i < n; i++){
                scanf("%d", &temp); //输入目标点权值
                searchBST(root, temp);  //查找并输出路径
            }
        }
    }
    return 0;
}
```

