---
title: PTA (Advanced Level) 1005 Spell It Right
date: 2019-04-02 12:41:48
categories: 
- 算法相关
tags:
- 大数运算
- PTA
---

　　Given a non-negative integer N, your task is to compute the sum of all the digits of N, and output every digit of the sum in English.

### Input Specification:

　　Each input file contains one test case. Each case occupies one line which contains an N (≤).

### Output Specification:

　　For each test case, output in one line the digits of the sum in English words. There must be one space between two consecutive words, but no extra space at the end of a line.

### Sample Input:

```
12345
```

### Sample Output:

```
one five
```

题目解析

　　本题给出一个小于10的100次方的数字，要求计算其每一位的数字的和，并由最大为开始以英文输出和的每一位。

　　将给出的数字记录在字符串num中计算字符串每一位减去字符’0’后的和即可得到给出数字每一位的和，以字符串数组str[ ]记录其下标数字对应的英文单词。

　　最后将得到的和每一位对应的单词记录并输出即可。

```c++
#include <bits/stdc++.h>
using namespace std;
const string str[10] = {"zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
int getSum(string num){ //计算给出数字每一位的和
    int ans = 0;
    for(auto i : num)
        ans += i - '0';
    return ans;
}
int main()
{
    string num;
    cin >> num; //输入num
    int sum = getSum(num);  //求每一位的和
    vector<string> ans; //记录sum每一位对应的英文单词
    do{ //由于可能出现sum为0的情况，所以用do while
        ans.push_back(str[sum % 10]);
        sum /= 10;
    }while(sum);
    reverse(ans.begin(), ans.end());    //由于记录时时由低位到高位记录，所以将其反转ans
    for(int i = 0; i < ans.size(); i++){    //输出答案
        if(i != 0)
            printf(" ");
        cout << ans[i];
    }
    return 0;
}
```

