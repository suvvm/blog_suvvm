---
title: PTA (Advanced Level) 1019 General Palindromic Number
date: 2019-01-01 20:05:20
categories: 
- 算法相关
tags:
- PTA
- 模拟
---

A number that will be the same when it is written forwards or backwards is known as a Palindromic Number. For example, 1234321 is a palindromic number. All single digit numbers are palindromic numbers.

Although palindromic numbers are most often considered in the decimal system, the concept of palindromicity can be applied to the natural numbers in any numeral system. Consider a number N>0 in base b≥2, where it is written in standard notation with k+1 digits ai as (. Here, as usual, 0for all i and ak is non-zero. Then N is palindromic if and only if ai=ak−i for all i. Zero is written 0 in any base and is also palindromic by definition.

Given any positive decimal integer N and a base b, you are supposed to tell if N is a palindromic number in base b.

### Input Specification:

Each input file contains one test case. Each case consists of two positive numbers N and b, where 0 is the decimal number and 2 is the base. The numbers are separated by a space.

### Output Specification:

For each test case, first print in one line `Yes` if N is a palindromic number in base b, or `No` if not. Then in the next line, print N as the number in base b in the form "ak ak−1 ... a0". Notice that there must be no extra space at the end of output.

### Sample Input 1:

```in
27 2
```

### Sample Output 1:

```out
Yes
1 1 0 1 1
```

### Sample Input 2:

```in
121 5
```

### Sample Output 2:

```out
No
4 4 1
```

解题思路：
　　本题有多组测试数据，每组数据给出一个十进制数字n，与目标进制b，要求将b转化为b进制后，判断转化完成的数字是否为回文数字。

　　我们可以写一个函数，将十进制数字n转化为b进制并将其每一位都记录在一个容器num中，之后从num第一位开始到中心为止，判断该位与其对称位置是否全相等即可。若全相等，则目标数字是回文数字，否则不是回文数字。

　　AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
vector<int> num;    //用来储存进制转换后数字的容器
void changeRadix(int n, int radix){ //进制转换函数
    while(n){
        int temp;
        temp = (n % radix);
        num.push_back(temp);
        n /= radix;
    }
}
bool judge(){   //回文判断函数
    int len = num.size();
    for(int i = 0; i < len / 2; i++){
        if(num[i] != num[len - i - 1])  //判断该位置与其对称位置是否相等
            return false;
    }
    return true;
}
int main()
{
    int n, b;
    while(scanf("%d%d", &n, &b) != EOF){    //输入数字n与目标进制b
        if(n == 0){ //0的所有进制都是回文数
            printf("Yes\n");
            printf("0\n");
            continue;
        }
        num.clear();    //清空容器num
        changeRadix(n, b);  //将n转化为b进制
        if(judge()){    //判断转换进制后是否为回文数字
            printf("Yes\n");    //是则输出Yes
            //由于我们是逆序储存转换后的数字的，所以在输出前要将其反转
            reverse(num.begin(), num.end());
            bool flag = false;
            for(auto i : num){  //输出转换后数字
                if(flag)
                    printf(" ");
                else
                    flag = true;
                cout << i;
            }
            printf("\n");
        }else{  //不是回文数字输出No，其余操作同上
            printf("No\n");
            reverse(num.begin(), num.end());
            bool flag = false;
            for(auto i : num){
                if(flag)
                    printf(" ");
                else
                    flag = true;
                cout << i;
            }
            printf("\n");
        }
    }
    return 0;
}
```

