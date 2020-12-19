---
title: PTA (Advanced Level) 1027 Colors in Mars
date: 2019-01-26 15:26:45
categories: 
- 算法相关
tags:
- PTA
- 模拟
---

　　People in Mars represent the colors in their computers in a similar way as the Earth people. That is, a color is represented by a 6-digit number, where the first 2 digits are for `Red`, the middle 2 digits for `Green`, and the last 2 digits for `Blue`. The only difference is that they use radix 13 (0-9 and A-C) instead of 16. Now given a color in three decimal numbers (each between 0 and 168), you are supposed to output their Mars RGB values.

### Input Specification:

　　Each input file contains one test case which occupies a line containing the three decimal color values.

### Output Specification:

　　For each test case you should output the Mars RGB value in the following format: first output `#`, then followed by a 6-digit number where all the English characters must be upper-cased. If a single color is only 1-digit long, you must print a `0` to its left.

### Sample Input:

```in
15 43 71
```

### Sample Output:

```out
#123456
```

题目解析
　　本题给出3个10进制数字要求转换为13进制数字并输出，根据题意我们可以得出，转化后的每一个数字都不会超过两位。

　　这样我们可以将十进制数字0~12与对应13进制数字利用map建立映射。之后将每个数字对应输出即可。

```c++
#include <bits/stdc++.h>
using namespace std;
map<int, char> radix;
void init(){    //建立十进制数与对应13进制数的映射
    for(int i = 0; i < 10; i++)
        radix[i] = i + '0';
    radix[10] = 'A';
    radix[11] = 'B';
    radix[12] = 'C';
}
int main()
{
    init();
    int red, green, blue;   
    scanf("%d%d%d", &red, &green, &blue);
    //输入三个十进制数
    //输出时先输出#
    cout << "#" << radix[red / 13] << radix[red % 13];
    //输出转化后的两位数
    cout << radix[green / 13] << radix[green % 13];
    cout << radix[blue / 13] << radix[blue % 13] << endl;
    return 0;
}
```

