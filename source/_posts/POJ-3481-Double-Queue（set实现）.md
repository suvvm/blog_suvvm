---
title: POJ 3481 Double Queue（set实现）
date: 2018-10-31 23:21:05
categories: 
- 算法相关
tags:
- STL
- POJ
---

The new founded Balkan Investment Group Bank (BIG-Bank) opened a new office in Bucharest, equipped with a modern computing environment provided by IBM Romania, and using modern information technologies. As usual, each client of the bank is identified by a positive integer Kand, upon arriving to the bank for some services, he or she receives a positive integer priority P. One of the inventions of the young managers of the bank shocked the software engineer of the serving system. They proposed to break the tradition by sometimes calling the serving desk with the lowest priority instead of that with the highest priority. Thus, the system will receive the following types of request:



| 0        | The system needs to stop serving                             |
| -------- | ------------------------------------------------------------ |
| 1*K* *P* | Add client *K* to the waiting list with priority *P*         |
| 2        | Serve the client with the highest priority and drop him or her from the waiting list |
| 3        | Serve the client with the lowest priority and drop him or her from the waiting list |

Input

　　Each line of the input contains one of the possible requests; only the last line contains the stop-request (code 0). You may assume that when there is a request to include a new client in the list (code 1), there is no other request in the list of the same client or with the same priority. An identifier K is always less than 106, and a priority P is less than 107. The client may arrive for being served multiple times, and each time may obtain a different priority.

Output

　　For each request with code 2 or 3, the program has to print, in a separate line of the standard output, the identifier of the served client. If the request arrives when the waiting list is empty, then the program prints zero (0) to the output.

Sample Input

2
1 20 14
1 30 3
2
1 10 99
3
2
2
0
Sample Output

0
20
30
10
0
解题思路：
　　本题要求处理银行排队问题，根据每行的输入处理数据；若输入为1则继续输入两个整数，以第一个数为权值，第二个数为优先级（数值越大优先级越高）加入排队系统中；若输入为2 则处理当前优先级最高的用户，并输出用户的权值；若输入为3 则处理当前优先级最低的用户，并输出用户权值；若输入为0结束运行。若当没有排队人数则无论输入2、3都输出0。

　　利用set自带红黑树排序的性质，可以在集合中直接获取最高与最低优先级，用pair记录权值和优先级，将pair传入集合。用迭代器可以获得集合首位（最大优先级）和集合末尾（最小优先级），数据处理完毕后可以通过erase()删除当前用户以便后续操作。

样例分析：
Sample Input

2　　　　　　　　　　//处理高优先级用户，当前无人排队输出0
1 20 14　　　　　　//优先级为14的用户20开始排队 
1 30 3　　　　　　 //优先级为3的用户30开始排队
2　　　　　　　　　　//处理高优先级用户，当前队列中有 20 14   30 3 最高优先级14 输出20 
1 10 99　　　　　　//优先级为99的用户10开始排队 
3　　　　　　　　　　//处理低优先级用户，当前队列中有 10 99   30 3 最低优先级3 输出30
2　　　　　　　　　　//处理高优先级用户，当前队列中有 10 99 最高优先级99 输出10
2　　　　　　　　　　//处理高优先级用户， 当前无人排队输出0
0　　　　　　　　　　//输入为0结束

```c++
#include <cstdio>
#include <iterator>
#include <set>
//bits/stdc++.h编译错误
using namespace std;
//利用set的排序性质
struct cmp{ //set比较方法以pair的第二个成员为基准，数值越大的在前
    bool operator() (const pair<int, int> &a, const pair<int, int> &b)
    {
        return a.second > b.second;
    }
};
set<pair<int, int>, cmp> waitingList;  //用set记录当前队列
int main()
{
    int n;
    while(scanf("%d", &n) != EOF){  //输入数据
        if(n == 0)  //输入为0结束运行
            break;
        if(n == 1){ //输入为1
            pair<int, int> temp;
            scanf("%d%d", &temp.first, &temp.second);   //记录用户权值与优先级
            waitingList.insert(temp);   //用户开始排队
        }
        if(n == 2){ //输入为2
            if(!waitingList.empty()){
                set<pair<int, int>, cmp>::iterator it = waitingList.begin();
                //获取优先级最高的用户
                printf("%d\n", (*it).first);    //输出用户权值
                waitingList.erase(it);  //删除用户
            }else{
                printf("0\n");  //队中无人
            }
        }
        if(n == 3){
            if(!waitingList.empty()){
                set<pair<int, int>, cmp>::iterator it = waitingList.end();
                it--;
                //获取优先级最小的用户
                printf("%d\n", (*it).first);    //输出用户权值
                waitingList.erase(it);  //删除用户
            }else{
                printf("0\n");//队中无人
            }
        }
    }
    return 0;
}
```

