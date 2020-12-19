---
title: HDU 6225 Little Boxes
date: 2018-11-28 19:46:53
categories: 
- 算法相关
tags:
- 大数运算
- HDU
---
<meta name="referrer" content="no-referrer"/>
　　Little boxes on the hillside. 
　　Little boxes made of ticky-tacky. 
　　Little boxes. 
　　Little boxes. 
　　Little boxes all the same. 
　　There are a green boxes, and b pink boxes. 
　　And c blue boxes and d yellow boxes. 
　　And they are all made out of ticky-tacky. 
　　And they all look just the same. 

Input

　　The input has several test cases. The first line contains the integer t (1 ≤ t ≤ 10) which is the total number of test cases. 
　　For each test case, a line contains four non-negative integers a, b, c and d where a, b, c, d ≤ 2^62, indicating the numbers of green boxes, pink boxes, blue boxes and yellow boxes. 
Output

　　For each test case, output a line with the total number of boxes. 
Sample Input

```
4
1 2 3 4
0 0 0 0
1 0 0 0
111 222 333 404
```

Sample Output

```
10
0
1
1070
```

解题思路：
　　测试数量t，每个测试给出四个巨大的数a，b，c，d要求计算他们的和，由于数的长度过大这里采用字符串模拟加法（好像ull也可以）。

　　小学我们学过一种极为好用的方法——竖式计算

![img](https://img2018.cnblogs.com/blog/1447131/201811/1447131-20181128192148504-1139918077.png)

对于两个数字，将它们记录为字符串，从个位开始按位计算，记录记录完后的结果与进位，计算下一位时将进位加上即可。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e5+10;
struct BigNum{  //记录大数
    int num[maxn];
    int len;    //长度
    BigNum(){
        memset(num, 0, sizeof(num));
        len = 0;
    }
};
BigNum change(string temp){ //将字符串转换为大数类型
    //为了方便计算将字符串倒过来储存
    BigNum a;
    a.len = temp.size();
    for(int i = 0; i < a.len; i++)  //逆序储存字符串
        a.num[i] = temp[a.len - i - 1] - '0';
    return a;
}
BigNum add(BigNum a, BigNum b){
    BigNum c;
    int carry = 0;//进位
    for(int i = 0; i < a.len || i < b.len; i++){//以较长的长度为界限
        int temp = a.num[i] + b.num[i] + carry;//两个位置相加后加上进位
        c.num[c.len++] = temp % 10; //记录该位结果
        carry = temp / 10;  //记录进位
    }
    if(carry != 0)  //记录首位进位
        c.num[c.len++] = carry;
    return c;
}
int main()
{
    int t; 
    while(scanf("%d", &t) != EOF){ //输入测试数量
        while(t--){
            string temp1;
            BigNum sum, temp;
            for(int i = 0; i < 4; i++){
                cin >> temp1;    
                temp = change(temp1);   //输入大数并记录为BigNum型
                sum = add(sum, temp);
                //计算和
            }
            for(int i = sum.len - 1; i >= 0; i--)
                printf("%d",sum.num[i]);
            printf("\n");
        }
    }
    return 0;
}
```

