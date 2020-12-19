---
title: PTA (Advanced Level) 1040 Longest Symmetric String
date: 2018-11-10 20:57:01
categories: 
- 算法相关
tags:
- 动态规划（DP）
- PAT
---

　Given a string, you are supposed to output the length of the longest symmetric sub-string. For example, given `Is PAT&TAP symmetric?`, the longest symmetric sub-string is `s PAT&TAP s`, hence you must output `11`.

### Input Specification:

　　Each input file contains one test case which gives a non-empty string of length no more than 1000.

### Output Specification:

　　For each test case, simply print the maximum length in a line.

### Sample Input:

```in
Is PAT&TAP symmetric?
```

### Sample Output:

```out
11
```

解题思路：
　　本题给出一行字符这里记为text，要求输出改行字符中最大回文串的长度。这里采用动态规划做法，用布尔数组dp[ bg ][ ed ]记录下标 bg ~ ed 组成的子串是否为回文串。设长度为1的字符串为回文串，在初始化dp数组是可以将长度为1与长度为2的字符串是否为回文串都标记完成。

　　对任意一个字符串都有两种情况：

　　1、是回文串,其对应dp为true;

　　2、不是回文串，其对应dp为false;

　　判断一个字符串是否为回文串只需要判断其首尾字符是否相等与其除首尾字符外的子串是否为回文串即可，这就转化为了求dp[bg + 1][ ed - 1]，和判断text[ bg ]与text[ ed ]是否相等的问题。

　　动态转移方程：dp[bg][ed] = 1（text[ bg ] == text[ ed ]，dp[bg + 1][ ed - 1 ] == true）

之后只要遍历所有子串长度，遍历所有起始下标，并计根据其实下标与子串长度计算出末尾下标，根据动态转移方程即可求出答案。

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+100;
bool dp[maxn][maxn] = {false};  //默认所有字符串都不是回文
string text;    //text为出入的字符串
int main()
{
    getline(cin, text);
    int len = text.size();
    int ans = 0;
    for(int i = 0; i < len; i++){
        dp[i][i] = true;    //标记长度为1的字符串为回文串
        ans = 1;    //最大回文串长度为1
        if(i < len - 1 && text[i] == text[i + 1]){  //如果有两个连续字符
            //（长度为2的回文串只有两个连续字符这种情况）
            dp[i][i + 1] = 1;   //标记其组成的字符串为回文串
            ans = 2;    //最大回文串长度为2
        }
    }
    for(int tempLen = 3; tempLen <= len; tempLen++){    //遍历子串长度长度从3 ~ len的
        for(int bg = 0; bg + tempLen - 1 < len; bg++){  
        //遍历起始位置，末尾下标为起始位置+当前子串长度-1，末尾下标不能达到给定字符串末位
            int ed = bg + tempLen - 1;
            if(text[bg] == text[ed] && dp[bg + 1][ed - 1] == true){ //动态转移方程
                dp[bg][ed] = 1;
                ans = tempLen;  //记录答案
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
```

