---
title: PTA (Advanced Level) 1023 Have Fun with Numbers
date: 2019-01-23 20:28:31
categories: 
- 算法相关
tags:
- PTA
- 模拟
- 大数运算
---

Notice that the number 123456789 is a 9-digit number consisting exactly the numbers from 1 to 9, with no duplication. Double it we will obtain 246913578, which happens to be another 9-digit number consisting exactly the numbers from 1 to 9, only in a different permutation. Check to see the result if we double it again!

Now you are suppose to check if there are more numbers with this property. That is, double a given number with k digits, you are to tell if the resulting number consists of only a permutation of the digits in the original number.

### Input Specification:

Each input contains one test case. Each case contains one positive integer with no more than 20 digits.

### Output Specification:

For each test case, first print in a line "Yes" if doubling the input number gives a number that consists of only a permutation of the digits in the original number, or "No" if not. Then in the next line, print the doubled number.

### Sample Input:

```in
1234567899
```

### Sample Output:

```out
Yes
2469135798
```

题目解析
　　本题给出一个长度再20以内的数字（由1~9组成），要求判断这个数字加倍后的新数字是不是这个数字的某一种排列，如果是的化输出Yes否则输出No，之后输出加倍后的数字。

　　由于数字最大位数为20位，超过了long long int的记录范围我们用数组num记录这个数字，用数组cnt记录num中1~9出现的次数，将num加倍后判断其中1~9出现的次数是否发送改变，若没有发送改变则证明加倍后的数字是原数字的某一种排列，反之则不是。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
int num[22];
int cnt[10];
string str;
void toInt(){
    for(int i = 0; i < str.size(); i++)
        num[i] = str[i] - '0';
}
void getCnt(){
    for(int i = 0; i < str.size(); i++)
        cnt[num[i]]++;
}
bool judge(int carry){
    if(carry != 0)  //如果最高位进位不为零，则证明加倍后的数字比原数字多一位，那么其肯定不是原数字的一个排列
        return false;
    for(int i = 0; i < str.size(); i++)
        cnt[num[i]]--;
    for(int i = 1; i <= 9; i++){    //判断新的num中1~9的数量是否和加倍前一样
        if(cnt[i] != 0)
            return false;
    }
    return true;
}
int doubleNumber(){ //将数组num加倍并返回最高位进位
    int carry = 0;
    for(int i = str.size() - 1; i >= 0; i--){
        int temp = num[i];
        num[i] = (2 * temp + carry) % 10;
        carry = 2 * temp / 10;
    }
    return carry;
}
int main()
{
    cin >> str; //输入数字
    toInt();    //将输入的数字转化为数组
    getCnt();   //获取数组中1~9出现的次数
    int carry = doubleNumber(); //将num加倍carry记录最高位的进位
    if(judge(carry)){   //判断加倍后的数字是否为原数字的某一个排列
        printf("Yes\n");
        
    }else
        printf("No\n");
    if(carry != 0)  //判断是否需要输出进位
        printf("%d", carry);
    for(int i = 0; i < str.size(); i++) //输出加倍后的数组num
        printf("%d", num[i]);
    printf("\n");
    return 0;
}
```

