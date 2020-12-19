---
title: PTA (Advanced Level) 1001 A+B Format
date: 2019-03-28 19:24:33
categories: 
- 算法相关
tags:
- 模拟
- PTA
---

　　Calculate a+b and output the sum in standard format -- that is, the digits must be separated into groups of three by commas (unless there are less than four digits).

**Input Specification:**

　　Each input file contains one test case. Each case contains a pair of integers a and b where −. The numbers are separated by a space.

**Output Specification:**

　　For each test case, you should output the sum of a and b in one line. The sum must be written in the standard format.

**Sample Input:**

-1000000 9

**Sample Output:**

-999,991

题目解析

​    本题给出两个正整数，要求将其和由最后一位的数字开始每隔三位加一个,但是首尾与负数的符号后第一位都不可以有,

​    可以使用头文件string中的to_string()函数将两数加和转化为字符串。

​    将其反转，反转后首位便是和的最后一位，遍历反转后字符串，将其加入ans，每隔3位向ans中添加一位 , 对末位与符号前位进行特判即可。

```c++
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int a, b;
    scanf("%d%d", &a, &b);  //输入a b
    string str = to_string(a + b);
    //将a与b的和转换为字符串储存在str中
    int cnt = 1;
    string ans = "";
    //ans记录最终答案
    reverse(str.begin(),str.end());
    //由于要从最后一个数字开始每隔三个数字添加一个 ,
    //将str反转
    for(auto i : str){
        ans += i;
        //每隔3个数字添加一个 , 最后一个位置与-前的位置不能添加 ,
        if(cnt % 3 == 0 && cnt != str.size() && !(cnt == str.size() - 1 && str[str.size() - 1] == '-'))
            ans += ',';
        cnt++;
    }
    reverse(ans.begin(),ans.end());
    cout << ans << endl;
    return 0;
}
```

