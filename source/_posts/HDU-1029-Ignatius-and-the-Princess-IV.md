---
title: HDU 1029 Ignatius and the Princess IV
date: 2018-12-07 15:18:04
categories: 
- 算法相关
tags:
- 数论
- HDU
---

　　"OK, you are not too bad, em... But you can never pass the next test." feng5166 says. 

　　"I will tell you an odd number N, and then N integers. There will be a special integer among them, you have to tell me which integer is the special one after I tell you all the integers." feng5166 says. 

　　"But what is the characteristic of the special integer?" Ignatius asks. 

　　"The integer will appear at least (N+1)/2 times. If you can't find the right integer, I will kill the Princess, and you will be my dinner, too. Hahahaha....." feng5166 says. 

　　Can you find the special integer for Ignatius? 

Input

　　The input contains several test cases. Each test case contains two lines. The first line consists of an odd integer N(1<=N<=999999) which indicate the number of the integers feng5166 will tell our hero. The second line contains the N integers. The input is terminated by the end of file. 
Output

　　For each test case, you have to output only one line which contains the special number you have found. 
Sample Input

```
5
1 3 2 3 3
11
1 1 1 1 1 5 5 5 5 5 5
7
1 1 1 1 1 1 1
```

Sample Output

```
3
5
1
```

解题思路：
　　本题给出多组数据每组数据给出一个数n，下一行给出n个数，要求找出出现次数超过一半的数字。

试想一个数的数量超过总数的一半其出现次数一定是所有的数中出现最多的那个数，我们就可以在输入时用一个ans记录当前出现次数最多的数，cnt记录ans比其他数的出现次数多多少。每当cnt等于0时便说明在当前输入已经输入完的数中ans所代表的数出现的次数不足一半，则cnt重新记录一个数，输入结束后获得的ans即为所取数。

　　AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int n;
    while(scanf("%d", &n) != EOF){
        int ans, cnt = 0, temp;
        //ans记录当前出现次数最多的数，cnt记录ans比其他数的出现次数多多少
        //temp记录当前输入的数
        for(int i = 0; i < n; i++){
            scanf("%d", &temp);
            if(cnt == 0){//cnt等于0重新记录一个数
                ans = temp;
                cnt++;
            }else if(ans == temp){
                cnt++;  //输入的数等于ans cnt++
            }else{
                cnt--;
            }
        }
        printf("%d\n", ans);    //输出答案
    }
    return 0;
}
```

