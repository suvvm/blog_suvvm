---
title: PTA (Advanced Level) 1024 Palindromic Number
date: 2019-01-23 21:39:34
categories: 
- 算法相关
tags:
- PTA
- 模拟
- 大数运算
---

　　A number that will be the same when it is written forwards or backwards is known as a Palindromic Number. For example, 1234321 is a palindromic number. All single digit numbers are palindromic numbers.

　　Non-palindromic numbers can be paired with palindromic ones via a series of operations. First, the non-palindromic number is reversed and the result is added to the original number. If the result is not a palindromic number, this is repeated until it gives a palindromic number. For example, if we start from 67, we can obtain a palindromic number in 2 steps: 67 + 76 = 143, and 143 + 341 = 484.

　　Given any positive integer N, you are supposed to find its paired palindromic number and the number of steps taken to find it.

### Input Specification:

　　Each input file contains one test case. Each case consists of two positive numbers N and K, where N (≤) is the initial numer and K (≤) is the maximum number of steps. The numbers are separated by a space.

### Output Specification:

　　For each test case, output two numbers, one in each line. The first number is the paired palindromic number of N, and the second number is the number of steps taken to find the palindromic number. If the palindromic number is not found after K steps, just output the number obtained at the Kth step and Kinstead.

### Sample Input 1:

```in
67 3
```

### Sample Output 1:

```out
484
2
```

### Sample Input 2:

```in
69 3
```

### Sample Output 2:

```out
1353
3
```

题目解析
　　本题给出两个数字n与k，n代表未转换数字，k代表最大转换步数，如果n不是回文数字屎就执行将n反转，n + 反转后的数字，若n还不是回文数字则继续执行上述操作，直到n为回文数字或执行次数超过最大转换步数为止。

　　本题数据可能超过int范围，既然既要判断回文又要反转数字，那么索性之间用处理大数的方法——用字符串记录数字。

　　之后模拟出加法操作与反转操作，判断执行后的数组是否为回文串即可。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int MAX = 1e3;
struct Number{  //记录数字
    int num[MAX];   //存储数字的每一位（为了方便加法运算倒序存储）
    int len;    //存储数字位数
    Number(){   //构造函数
        memset(num, 0, sizeof(num));    //初始化num每一位都为0
        len = 0;    //位数为0
    }
};
Number toNumber(string temp){   //字符串转化为Number
    Number a;
    a.len = temp.size();
    for(int i = 0; i < a.len; i++)  //倒序存储
        a.num[i] = temp[a.len - i - 1] - '0';
    return a;
}
Number add(Number a, Number b){ //加法函数
    Number c;
    int carry = 0;  //进位
    for(int i = 0; i < a.len || i < b.len; i++){    //以较长的长度为界限（当然本题两个数长度相等，其实以任意一个为界都可以）
        int temp = a.num[i] + b.num[i] + carry; //两个位置相加后加上进位
        c.num[c.len++] = temp % 10; //记录新数字的对应位
        carry = temp / 10;  //计算新的进位
    }
    if(carry != 0)  //若最高位有进位
        c.num[c.len++] = carry;
    return c;
}
Number reversed(Number a){  //反转函数
    Number b;
    for(int i = a.len - 1; i >= 0; i--){
        b.num[b.len++] = a.num[i];
    }
    return b;

}
bool judgePalindromic(Number a){    //判断回文
    for(int i = 0; i < a.len / 2; i++){
        if(a.num[i] != a.num[a.len - i - 1])    //从两端像中间匹配，若失匹返回false
            return false;
    }
    return true;
}
int main()
{
    string str;
    int k;
    cin >> str >> k;    //输入数字与最大转化次数
    Number n = toNumber(str);   //将数字转化为Number型
    int cnt = 0;    //cnt记录当前转化次数
    while(cnt < k && !judgePalindromic(n)){ //转化次数超限或成功转化为回文时结束循环
        Number temp = reversed(n);  //反转
        n = add(n, temp);   //相加
        cnt++;  //转化次数加1
    }
    for(int i = n.len - 1; i >= 0; i--) //输出转化后的数字
        printf("%d", n.num[i]);
    printf("\n");
    printf("%d\n", cnt);    //输出转化次数
    return 0;
}
```

