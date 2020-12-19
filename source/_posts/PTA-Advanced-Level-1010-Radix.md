---
title: PTA (Advanced Level) 1010 Radix
date: 2018-12-14 14:38:12
categories: 
- 算法相关
tags:
- 二分
- PTA
---

　　Given a pair of positive integers, for example, 6 and 110, can this equation 6 = 110 be true? The answer is `yes`, if 6 is a decimal number and 110 is a binary number.

　　Now for any pair of positive integers N1 and N2, your task is to find the radix of one number while that of the other is given.

### Input Specification:

　　Each input file contains one test case. Each case occupies a line which contains 4 positive integers:

```
N1 N2 tag radix
```

　　Here `N1` and `N2` each has no more than 10 digits. A digit is less than its radix and is chosen from the set { 0-9, `a`-`z` } where 0-9 represent the decimal numbers 0-9, and `a`-`z` represent the decimal numbers 10-35. The last number `radix` is the radix of `N1` if `tag` is 1, or of `N2` if `tag` is 2.

### Output Specification:

　　For each test case, print in one line the radix of the other number so that the equation `N1` = `N2` is true. If the equation is impossible, print `Impossible`. If the solution is not unique, output the smallest possible radix.

### Sample Input 1:

```in
6 110 1 10
```

### Sample Output 1:

```out
2
```

### Sample Input 2:

```in
1 ab 1 2
```

### Sample Output 2:

```out
Impossible
```

解题思路：
　　本题给出两个数字n1、n2，给出其中一个数字tag的进制radix，要求判断是否存在某一进制可以使另一个数字与给定数字相等。

　　为了方便运算，用字符串tn1、tn2记录输入的两个数字，字符串n1记录给定进制的数字，字符串n2记录未确定数字。用map<char, int > mp，记录每个字符所对应的数值，之后，可以先将给定进制的数字n1转化为10进制，n2的进制最小为其包含的最大数字+1记为leftn，且由于n2是整数，所以其进制最大不会超过n1的十进制与leftn中较大的一个+1，记为rightn。以leftn和rightn分别为左右边界二分所有进制，记mid为中点进制，将n2按mid进制转化为10进制与n1的十进制进行比较，如果n2较大证明mid取值过大，将rightn记为mid - 1；若小了，证明mid取值过小，leftn记为mid+1，若正好相等则找到答案。若无法找到某进制使得n1与n2相等，返回-1，如果返回的答案不为-1，输出答案，否则输出Impossible。

　　AC代码

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
map<char, LL> mp;
void init(){
    for(char i = '0'; i <= '9'; i++){
        mp[i] = i - '0';    //初始化0 - 9
    }
    for(char i = 'a'; i <= 'z'; i++){
        mp[i] = i - 'a' + 10;   //初始化a - z
    }
}
LL toDecimal(string a, LL radix, LL maxn){  //转化为10进制的函数，所转化后的数不会超过给出的maxn
    int len = a.size();
    LL ans = 0;
    for(int i = 0; i < len; i++){
        ans = ans * radix + mp[a[i]];
        if(ans < 0 || ans > maxn){  //如果数据溢出了或超过上限
            return -1;//返回-1
        }
    }
    return ans;//返回转化后的值
}
int cmp(string a, LL radix, LL n1){ //比较函数，用于比较n2的radix进制转化为10进制后与n1的十进制的大小
    LL n2_10 = toDecimal(a, radix, n1);
    //获得n2转化为10进制的值
    if(n2_10 == n1) //如果n2的10进制与n1的十进制相同证明该进制是我们要获得的进制，返回0
        return 0;
    else if(n2_10 < 0)  //如果toDecimal函数返回的n2小于0，证明n2在该进制下转化为十进制后大于n1的十进制
        return 1;   //进制过大返回1
    else if(n1 > n2_10) //如果n2在当前进制下转化为10进制小于n1的十进制
        return -1;  //进制过小返回-1
    else    //否则返回1
        return 1;
}
LL getRadix(string a, LL leftn, LL rightn, LL n1){
    //二分函数传入n2字符串，最小进制，最大进制，n1的十进制值
    while(leftn <= rightn){
        LL mid = (leftn + rightn) / 2;
        //获得中点
        LL flag = cmp(a, mid, n1);
        //判断终点进制n1与n2状态
        if(flag == 0)   //若比较函数返回了0，证明在mid进制下n1与n2相等
            return mid; //返回mid
        else if(flag == -1){    //进制过小
            leftn = mid + 1;
        }else if(flag == 1){    //进制过大
            rightn = mid - 1;
        }
    }
    return -1;
}
int getMaxNum(string a){    //获得n2中最大的数字
    LL ans = -1;
    for(string::iterator it = a.begin(); it != a.end(); it++){
        ans = max(ans, mp[*it]);
    }
    return ans;
}
int main(){
    init(); //初始化mp
    string tn1, tn2, n1, n2;
    int tag, radix;
    cin >> tn1 >> tn2 >> tag >> radix;
    //输入 tn1 tn2 tag radix;
    if(tag == 1){
        n1 = tn1;
        n2 = tn2;
    }else{
        n1 = tn2;
        n2 = tn1;
    }
    //n1记录已经确定进制的数字，n2记录未确定的数字
    LL n1_10 = toDecimal(n1, radix, INT_MAX);
    //将n1转化为10进制其上限为无穷大
    LL leftn = getMaxNum(n2) + 1;
    //获得n2的最小进制
    LL rightn = max(leftn, n1_10) + 1;
    //获得n2的最大进制
    LL ans = getRadix(n2, leftn, rightn, n1_10);
    //二分所有进制
    if(tn1 == tn2)
        printf("%d\n", radix);
    else if(ans == -1){
        printf("Impossible\n");
    }else{
        printf("%lld\n", ans);
    }
    return 0;
}
```

