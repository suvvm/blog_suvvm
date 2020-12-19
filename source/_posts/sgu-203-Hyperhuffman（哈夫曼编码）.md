---
title: sgu-203 Hyperhuffman（哈夫曼编码）
date: 2018-10-30 23:54:21
categories: 
- 算法相关
tags:
- 哈夫曼编码
---

You might have heard about Huffman encoding - that is the coding system that minimizes the expected length of the text if the codes for characters are required to consist of an integral number of bits.

　　Let us recall codes assignment process in Huffman encoding. First the Huffman tree is constructed. Let the alphabet consist of N characters, i-th of which occurs Pitimes in the input text. Initially all characters are considered to be active nodes of the future tree, i-th being marked with Pi. On each step take two active nodes with smallest marks, create the new node, mark it with the sum of the considered nodes and make them the children of the new node. Then remove the two nodes that now have parent from the set of active nodes and make the new node active. This process is repeated until only one active node exists, it is made the root of the tree.

　　Note that the characters of the alphabet are represented by the leaves of the tree. For each leaf node the length of its code in the Huffman encoding is the length of the path from the root to the node. The code itself can be constrcuted the following way: for each internal node consider two edges from it to its children. Assign 0 to one of them and 1 to another. The code of the character is then the sequence of 0s and 1s passed on the way from the root to the leaf node representing this character.

　　In this problem you are asked to detect the length of the text after it being encoded with Huffman method. Since the length of the code for the character depends only on the number of occurences of this character, the text itself is not given - only the number of occurences of each character. Characters are given from most rare to most frequent.

　　Note that the alphabet used for the text is quite huge - it may contain up to 500 000 characters.

 

　　This problem contains multiple test cases!

　　The first line of a multiple input is an integer N, then a blank line followed by N input blocks. Each input block is in the format indicated in the problem description. There is a blank line between input blocks.

　　The output format consists of N output blocks. There is a blank line between output blocks.

 

 

Input

 

　　The first line of the input file contains N - the number of different characters used in the text (2 <= N <= 500 000). The second line contains N integer numbers Pi- the number of occurences of each character (1 <= Pi <= 109, Pi <= Pi+1 for all valid i).

 

Output

 

　　Output the length of the text after encoding it using Huffman method, in bits.

 

Sample Input

 

　　1

　　3
　　1 1 4

 

Sample Output

 

　　8

解题思路：
　　本题给出多组数据每组数据包括测试数据数量，每个测试包括一个整数代n表字符数量（即哈夫曼树叶子结点数），n个整数代表每个字符出现的次数（即哈夫曼树叶子结点权值），要求输出其哈夫曼编码的长度。

样例解析：
　　1　　　　->测试数据组数

　　3　　　　->字符数量（叶子结点数）
　　1 1 4　　 ->每个字符出现次数（哈夫曼树叶子结点权值）

```c++
#include <bits/stdc++.h>
using namespace std;
priority_queue<long long, vector<long long>, greater<long long> > value;    //优先队列数值小的先出队
int main()
{
    int t;
    while(cin >> t)     //输入测试数据组数
    {
        while(t--)
        {
            int n;  //n记录字符数量
            long long ans = 0LL;
            while(!value.empty())   //清空队列
            {
                value.pop();
            }
            scanf("%d", &n);
            for(int i = 0; i < n; i++)
            {
                long long temp; //输入出现次数
                scanf("%lld", &temp);
                value.push(temp);   //出现次数入队
            }
            while(value.size() > 1)
            {
                long long x1 = value.top();
                value.pop();
                long long x2 = value.top();
                value.pop();
                //每次出队两个最小的数据合并后入队
                ans += x1 + x2; //记录长度
                value.push(x1 + x2);
            }
            printf("%lld\n", ans);  //输出长度
            if(t)
                printf("\n");
        }
    }
    return 0;
}
```
