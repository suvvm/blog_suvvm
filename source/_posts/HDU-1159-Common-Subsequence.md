---
title: HDU 1159 Common Subsequence
date: 2018-11-08 19:03:47
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---

A subsequence of a given sequence is the given sequence with some elements (possible none) left out. Given a sequence X = < x1, x2, ..., xm > another sequence Z = < z1, z2, ..., zk > is a subsequence of X if there exists a strictly increasing sequence < i1, i2, ..., ik > of indices of X such that for all j = 1,2,...,k, x ij = zj. For example, Z = < a, b, f, c > is a subsequence of X = < a, b, c, f, b, c > with index sequence < 1, 2, 4, 6 >. Given two sequences X and Y the problem is to find the length of the maximum-length common subsequence of X and Y.Input
　　The program input is from the std input. Each data set in the input contains two strings representing the given sequences. The sequences are separated by any number of white spaces. The input data are correct.
Output
　　For each set of data the program prints on the standard output the length of the maximum-length common subsequence from the beginning of a separate line.
Sample Input
abcfbc abfcab
programming contest 
abcd mnp
Sample Output
　　4
　　2
　　0

解题思路：
　　本题有多组测试数据，每组数据给出两个字符串要求求出两个字符串的最长公共子序列长度如样例abcfbc abfcab 最长公共子串abfb长度为4。

　　用一个数组dp[ i ][ j ]记录以第一个字符串第 i 位结尾的字符串与以第二个字符串第 j 位为结尾的字符串的最长公共子串长度。根据动态规划思想对于每个dp[ i ][ j ]可以分成两种情况。

　　1、若 i j 所对应的字符相等，则该问题可以转化为求在第一个字符串中以 i 的前一个字符为结尾， 在第二个字符串中以 j 的前一个字符为结尾的最长公共子串 + 1。

　　2、若 i j 所对应的字符不等，则该问题转化为求在第一个字符串中以 i 的前一个字符为结尾， 在第二个字符串中以 j 为结尾的最长公共子串，与在第一个字符串中以 i 为结尾， 在第二个字符串中以 j 的前一个字符为结尾的最长公共子串两者的最大值问题。

动态转移方程：
$$
1、dp[ i ][ j ] = d[ i - 1][ j - 1] + 1　　(str1[ i ] == str2[ j ]) \\

　　2、dp[ i ][ j ] = max(dp[ i - 1][ j ] , dp[ i ][ j - 1][ i ])　　(str1[ i ] != str2[ j ])
$$
　　在运算中只要有一个数组结尾位下标为0那么无论谁与它的最长公共子串长度都为0，这便是边界条件，即所有的dp\[][0],dp\[0][]为0。

```c++
//#include <bits/stdc++.h>  编译错误
#include <iostream>
#include <string>
#include <cstring>
#include <cstdio>
using namespace std;
const int maxn = 1e4 + 100;
string tstr1, tstr2, str1, str2;    
//我们需要的字符串下标从1开始，所以先用tstr1 与 tstr2记录用户输入
//str1 str2为需要参与运算的字符串
int dp[maxn][maxn];
int main()
{
    while(cin >> tstr1){
        str1 = str2 = "";   //每次计算开始前使str1 str2为空
        cin >> tstr2;
        str1 += "*";    //为了方便运算str1首位随便加一个字符
        str1 += tstr1;  //将先前记录的tstr1加入str1末尾
        str2 += "0";    //str2与str1操作相同
        str2 += tstr2;
        int len1 = str1.size() - 1; //获取str1有效位长度（第一位没用）
        int len2 = str2.size() - 1; //获取str2有效位长度（第一位没用）
        //初始化dp数组的边界值
        //这里不能用memset，如果使用的话会超内存
        for(int i = 0; i <= len1; i++){
            dp[i][0] = 0;
        }
        for(int i = 0; i <= len2; i++){
            dp[0][i] = 0;
        }
        //遍历所有情况
        for(int i = 1; i <= len1; i++){
            for(int j = 1; j <= len2; j++){
                if(str1[i] == str2[j])
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                else if(str1[i] != str2[j]){
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        printf("%d\n", dp[len1][len2]);
    }
    return 0;
}
```

