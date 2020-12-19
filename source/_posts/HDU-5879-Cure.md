---
title: HDU 5879 Cure
date: 2018-11-28 20:32:31
categories: 
- 算法相关
tags:
- 大数运算
- HDU
---

　　Given an integer nn, we only want to know the sum of 1/k21/k2 where kk from 11 to nn.

Input

　　There are multiple cases. 
　　For each test case, there is a single line, containing a single positive integer nn. 
　　The input file is at most 1M. 
Output

　　The required sum, rounded to the fifth digits after the decimal point.

Sample Input

```
1
2
4
8
15
```

Sample Output

```
1.00000
1.25000
1.42361
1.52742
1.58044
```


解题思路：
　　本题有多组测试数据，每组数据给出一个数n，数字k为从1到n所有整数，要求求和所有1/k^2。

　　由于k越来越大，1/k^2越来越小，而我们只需要输出小数点后5位，在疯狂wa11次尝试之后我发现当k到1e6左右，对于加上所有k>1e6时的1/k^2对答案都没用影响了，小数点后5位就不会变动了。那么我们只需要将所有1~1e6的结果打表计算一次，如果输入的数大于1e6那之间输出1e6时的结果即可。

　　还有一个坑就是，本题没有给出输入的数n的范围，这证明什么？n无限大！还是用字符串存吧。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
const int maxn = 1e6;
double sum[maxn];   //sum记录对于从1~1e6所有n的答案
int main()
{
    string s;   //s记录输入的数
    double ans = 1.0;
    sum[1] = 1.0;   //n为1时1/1^2 = 1.0
    for(int i = 2; i <= maxn; i++){
            ans += (double)(1.0 / i )* (double)(1.0 / i );
            sum[i] = ans;   //打表所有1~1e6的答案
    }
    while(cin >> s){    //输入字符串s
        int n = 0;
        if(s.size() <= 6){  //判断s所代表的数字长度是否超过6
            for(int i = 0; i < s.size(); i++){
                n = n * 10 + s[i] - '0';
            }
            //字符串长度不超过6将转换为数字后判断与1e6的关系
            if(n < maxn){
                printf("%.5f\n", sum[n]);
                //n小于1e6直接输出答案
            }else{
                //n大于1e6直接输出1e6时的答案
                ans = sum[maxn];
                printf("%.5f\n", ans);
            }
        }else{
            //长度超过6连拆成数字都不用直接输出1e6时的答案
            printf("%.5f\n", sum[maxn]);
        }

    }
    return 0;
}
```

