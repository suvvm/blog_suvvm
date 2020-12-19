---
title: POJ3253-Fence Repair
date: 2018-11-03 21:26:01
categories: 
- 算法相关
tags:
- 哈夫曼编码
- POJ
---

Farmer John wants to repair a small length of the fence around the pasture. He measures the fence and finds that he needs *N* (1 ≤ *N* ≤ 20,000) planks of wood, each having some integer length *Li* (1 ≤ *Li* ≤ 50,000) units. He then purchases a single long board just long enough to saw into the *N* planks (i.e., whose length is the sum of the lengths *Li*). FJ is ignoring the "kerf", the extra length lost to sawdust when a sawcut is made; you should ignore it, too.

　　FJ sadly realizes that he doesn't own a saw with which to cut the wood, so he mosies over to Farmer Don's Farm with this long board and politely asks if he may borrow a saw.

　　Farmer Don, a closet capitalist, doesn't lend FJ a saw but instead offers to charge Farmer John for each of the *N*-1 cuts in the plank. The charge to cut a piece of wood is exactly equal to its length. Cutting a plank of length 21 costs 21 cents.

　　Farmer Don then lets Farmer John decide the order and locations to cut the plank. Help Farmer John determine the minimum amount of money he can spend to create the *N* planks. FJ knows that he can cut the board in various different orders which will result in different charges since the resulting intermediate planks are of different lengths.

Input

　　Line 1: One integer *N*, the number of planks 
　　Lines 2.. *N*+1: Each line contains a single integer describing the length of a needed plank

Output

　　Line 1: One integer: the minimum amount of money he must spend to make *N*-1 cuts

Sample Input

```
3
8
5
8
```

Sample Output

```
34
```

Hint

　　He wants to cut a board of length 21 into pieces of lengths 8, 5, and 8. 
　　The original board measures 8+5+8=21. The first cut will cost 21, and should be used to cut the board into pieces measuring 13 and 8. The second cut will cost 13, and should be used to cut the 13 into 8 and 5. This would cost 21+13=34. If the 21 was cut into 16 and 5 instead, the second cut would cost 16 for a total of 37 (which is more than 34).

 

解题思路：
　　本题意思是一名农民需要在邪恶的资本家那里从一块足够长度的木板上锯下一些给定长度的木板，每锯下一块就要支付和被锯的原木板长度相同的钱财，题目每组测试给定一个数组n为需要的木板数量，之后n行每行一个数为木板的长度。既然初始木板长度足够，那我们便可将它视为一块正合适的木板，从这块木板正好可以锯下所以需要的长度零浪费，既然想要耗费最少的钱财，我们可以将切割问题转化为拼接问题，我们在所有需要锯下的长度中挑选两个最小的长度，将它们拼接成一块，可以得知锯开我们刚刚拼接的这一块木板需要考费的钱财就等于它的长度，记录下这个数字，之后从我们需要没有锯的木板中以刚刚拼接好的这一块代替拼接它的两块木板，在选两块最小的拼接，以此类推，我们最终可以把所有需要的长度拼成一块木板，就是我们要锯的初始木板，锯开它所耗费的钱财为我们在拼接过程中记录的数字之和。这就完美的转化为一个哈夫曼树问题。

　　我们用优先队列来记录输入的所有需要锯下木板的长度，每次取出两个最小的进行加和成为一段新长度，将这段长度数值加入需要耗费的钱财后，入队这段新长度，重复操作直到只剩一块木板。

 

样例解析:

　　3　　//木板数量

　　8　　//长度1

　　5　　//长度2

　　8　　//长度3

　　入队结束后队中有 8 8 5 ， 5出队 8出队，拼接为13，耗费13钱财，13入队。 队中现有 13 8，8出队，13出队，拼接为21，耗费现为之前的13 + 21 = 34钱财，21入队，队中只剩一块木板，输出钱财为34。

```c++
#include <cstdio>
#include <queue>
// bits/stdc++.h编译错误
using namespace std;
priority_queue<long long, vector<long long>, greater<long long> > value;
//优先队列设定数值小的先出队
int main()
{
    int n;
    long long ans = 0;  //ans记录耗费的钱财
    while(scanf("%d", &n) != EOF){  //输入木板数量
        ans = 0;    //没有切割是耗费钱财为0
        while(!value.empty()){  //清空队列
            value.pop();
        }
        for(int i = 0; i < n; i++){ //输入并入队所有长度
            int temp;
            scanf("%d", &temp);
            value.push(temp);
        }
        while(value.size() > 1){    //如果队中所剩木板数量大于则1进行拼接
            int len1 = value.top();
            value.pop();
            int len2 = value.top();
            value.pop();
            //出队两个最小长度进行拼接操作
            ans += len1 + len2; //记录消耗的钱财
            value.push(len2 + len1);    //新长度入队
        }
        printf("%lld\n", ans);  //输出答案
    }
    return 0;
}
```

