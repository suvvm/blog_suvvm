---
title: PTA (Advanced Level) 1007 Maximum Subsequence Sum
date: 2019-04-08 17:25:01
categories: 
- 算法相关
tags:
- 动态规划（DP）
- PTA
---

　　Given a sequence of K integers { N1, N2, ..., NK }. A continuous subsequence is defined to be { Ni, Ni+1, ..., Nj } where 1. The Maximum Subsequence is the continuous subsequence which has the largest sum of its elements. For example, given sequence { -2, 11, -4, 13, -5, -2 }, its maximum subsequence is { 11, -4, 13 } with the largest sum being 20.

　　Now you are supposed to find the largest sum, together with the first and the last numbers of the maximum subsequence.

**Input Specification:**

　　Each input file contains one test case. Each case occupies two lines. The first line contains a positive integer K (≤). The second line contains K numbers, separated by a space.

**Output Specification:**

　　For each test case, output in one line the largest sum, together with the first and the last numbers of the maximum subsequence. The numbers must be separated by one space, but there must be no extra space at the end of a line. In case that the maximum subsequence is not unique, output the one with the smallest indices i and j (as shown by the sample case). If all the K numbers are negative, then its maximum sum is defined to be 0, and you are supposed to output the first and the last numbers of the whole sequence.

**Sample Input:**

10

-10 1 2 3 4 -5 -23 3 7 -21

**Sample Output:**

10 1 4

题目解析

　　本题第一行给出数组长度k，下一行给出k个整数，要求计算出最大连续子串和，并输出最大连续字串的首位与末位，如果整个字符串全为负数，则输出0并输出整个字符串的首位与末位。

　　用数组arr记录给出的数组，最大连续子串和，果断dp。用数组dp[i]记录当字符串以第i位结尾时的最大连续子串和。当i = 0时以首位为结尾的最大连续子串和为首位arr[ i ]的值。

之后的值只会有两种情况：

　　1）以当前位i为结尾时最大连续子串只有其本身arr[ i ]一个元素;

　　2）以当前位i为结尾时最大连续子串有多个元素，即dp[ i - 1] + arr[ i ];

　　可以得到状态转移方程dp[ i ] = max(arr[ i ], dp[ i - 1] + arr[ i ]);

　　至于最大连续子串的首尾两位我们可以在计算dp数组时一并计算，以bg[ i ]记录dp[ i ]对应的最大连续子串的首位，以ed[ i ]记录dp[ i ]对应的最大连续子串的末尾。

注意：

​    若不对整个字符串全为负数的情况做出处理， 测试点4会错误；

　　若对整个字符串全为负数的情况做出处理了，但将字符串存在0的情况也视为全为负数处理，测试点5会错误。

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 1e4+10;
int arr[MAX];
int dp[MAX], bg[MAX], ed[MAX];
int main()
{
    int k;
    scanf("%d", &k);    //输入数组长度k
    bool flag = false;  //flag记录数组是否全为负数
    for(int i = 0; i < k; i++){ //输入数组
        scanf("%d", &arr[i]);
        if(arr[i] >= 0) //只要存在正数或0则将flag标记位true
            flag = true;
    }
    dp[0] = arr[0]; //以首位为结尾的最大连续连续子串和为首位arr[i]的值
    int maxSum = arr[0], index = 0;
    //maxSum记录最大连续子串和
    //index记录最大连续子串的末位下标
    for(int i = 1; i < k; i++){
        if(dp[i - 1] + arr[i] >= arr[i]){   //情况2
            dp[i] = dp[i - 1] + arr[i];
            bg[i] = bg[i - 1];  //记录当前最大连续子串首位下标
            ed[i] = i;  //记录最大连续子串末位下标
        }else{  //情况1
            dp[i] = arr[i];
            bg[i] = ed[i] = i;
            //最大连续子串首尾下标都为i
        }
        if(dp[i] > maxSum){
            maxSum = dp[i];
            index = i;
        }
    }
    if(flag)
        printf("%d %d %d\n", maxSum, arr[bg[index]], arr[ed[index]]);
    else
        printf("0 %d %d\n", arr[0], arr[k - 1]);
    return 0;
}
```

　　当然本题数据量最大只有1e4，暴力的O(n^2)并不会超时，所以本题暴力依然没有问题。

暴力过一切：

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 1e4+10;
int arr[MAX], sum[MAX];
int main(){
    int k;
    scanf("%d", &k);
    int temp = 0;
    for(int i = 0; i < k; i++){
        scanf("%d", &arr[i]);
        temp += arr[i];
        sum[i] = temp;
    }
    int maxSum = -1, bg = 0, ed = k - 1;
    for(int i = 0; i < k; i++){
        for(int j = i; j < k; j++){
            if(sum[j] - sum[i] + arr[i] > maxSum){
                maxSum = sum[j] - sum[i] + arr[i];
                bg = i;
                ed = j;
            }
        }
    }
    if(maxSum < 0)
        maxSum = 0;
    printf("%d %d %d\n", maxSum, arr[bg], arr[ed]);
    return 0;
}
```

