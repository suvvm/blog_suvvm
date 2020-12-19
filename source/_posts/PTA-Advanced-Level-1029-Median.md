---
title: PTA (Advanced Level) 1029 Median
date: 2019-11-02 00:11:27
categories: 
- 算法相关
tags:
- PAT
---

Given an increasing sequence S of N integers, the median is the number at the middle position. For example, the median of S1 = { 11, 12, 13, 14 } is 12, and the median of S2 = { 9, 10, 15, 16, 17 } is 15. The median of two sequences is defined to be the median of the nondecreasing sequence which contains all the elements of both sequences. For example, the median of S1 and S2 is 13.

Given two increasing sequences of integers, you are asked to find their median.

### Input Specification:

Each input file contains one test case. Each case occupies 2 lines, each gives the information of a sequence. For each sequence, the first positive integer N (≤2×105) is the size of that sequence. Then N integers follow, separated by a space. It is guaranteed that all the integers are in the range of **long int**.

### Output Specification:

For each test case you should output the median of the two given sequences in a line.

### Sample Input:

```in
4 11 12 13 14
5 9 10 15 16 17
```

### Sample Output:

```out
13
```

解题思路：
　　本题没有任何难度，为何会分到25分，这谁也不知道，题意就是给予两行两个long int序列，每个序列第一位为序列长度，求两个序列合并后的中位数。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 2e5 + 10;
int main()
{
    int n;
    long long val;
    vector<long long> seq;
    scanf("%d", &n);

    for(int i = 0; i < n; i++){
        scanf("%lld", &val);
        seq.push_back(val);
    }
    scanf("%d", &n);
    for(int i = 0; i < n; i++){
        scanf("%lld", &val);
        seq.push_back(val);
    }
    sort(seq.begin(), seq.end());
    printf("%lld\n", seq[(seq.size() -1) / 2]);
    return 0;
}
```

