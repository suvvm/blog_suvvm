---
title: HDOJ 5019 Revenge of GCD
date: 2018-11-22 20:16:52
categories: 
- 算法相关
tags:
- gcd
- HDOJ
---

　　In mathematics, the greatest common divisor (gcd), also known as the greatest common factor (gcf), highest common factor (hcf), or greatest common measure (gcm), of two or more integers (when at least one of them is not zero), is the largest positive integer that divides the numbers without a remainder. 
---Wikipedia 

　　Today, GCD takes revenge on you. You have to figure out the k-th GCD of X and Y.

Input

　　The first line contains a single integer T, indicating the number of test cases. 

　　Each test case only contains three integers X, Y and K. 

[Technical Specification] 
　　1. 1 <= T <= 100 
　　2. 1 <= X, Y, K <= 1 000 000 000 000 
Output

　　For each test case, output the k-th GCD of X and Y. If no such integer exists, output -1.

Sample Input

```
3
2 3 1
2 3 2
8 16 3
```

Sample Output

```
1
-1
2
```

解题思路：
　　本题可恶的最大公约数要向你复仇，给你测试数量t与3个整数x, y, k要求你求出x与y的第k大的公约数，如果不存在就输出-1。

　　x与y的第1大的公约数就是最大公约数，记为gcdxy，x与y小于gcdxy的其他公约数一定是gcdxy的约数。本题就是求两个数的最大公约数的约数的问题。

　　我们可以用一个容器记录x与y的所有约数，由小到大排序后如果k > 容器元素数量则不存在，若存在，则下标为容量 - k的元素即为所求。

　　注意在求解时直接遍历小于gcdxy的所有数字会超时，但由于我们找到 i 为gcdxy的约数时也可以确定 gcdxy / i 也是gcdxy的约数，这样我们只需找2-sqrt(gcdxy)即可找全所有约数。

样例解析：

　　2 3 1　　2 与 3 的最大公约数是1，1的约数只有自身，所以2 与 3 只有一个公约数1，第1大的公约数为 1；

　　2 3 2 　同上2 与 3 只有一个公约数1，第2大的公约数不存在；

　　8 16 3　　8 与 16 的最大公约数是8，8有约数 8 4 2 1，8 与 16的所有公约数有 8 4 2 1，第3大的公约数为2。

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
const int maxn = 0x7fffffff;
vector<LL> h;   //h记录x与y所有公约数
LL gcd(LL a, LL b){ //求x与y的最大公约数
    if(b == 0)
        return a;
    else
        return gcd(b , a % b);
}
int main()
{
    int t;
    scanf("%d", &t);    //输入测试数量
    while(t--){
        LL x, y, k;
        scanf("%lld%lld%lld", &x, &y, &k);  //输入x y与k
        LL gcdxy = gcd(x, y);   //求出x与y的最大公约数
        h.clear();  //清空容器
        if(gcdxy != 1)  //判断最大公约数是否为1以免重复加入容器
            h.push_back(gcdxy);
        h.push_back(1); //1肯定是x与y的公约数
        int sqrtGcd = sqrt(gcdxy);
        for(int i = 2; i <= sqrtGcd; i++){
            if(gcdxy % i == 0){ //若i为gcdxy的约数
                h.push_back(i); //i加入容器
                h.push_back(gcdxy / i); //顺便计算并记录另一个约数
            }
        }
        sort(h.begin(), h.end());   //由小到大排序
        //我做过从大到小的排序但是wa，诸位强力人要是了解为什么请指导我
        if(k > h.size()){   //判断是否存在第k大的公约数
            printf("-1\n");
        }else{
            printf("%lld\n" , h[h.size() - k]);
        }
    }
    return 0;
}
```

