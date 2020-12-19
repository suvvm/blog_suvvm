---
title: HDU 1074 Doing Homework
date: 2018-12-08 16:07:30
categories: 
- 算法相关
tags:
- 动态规划（DP）
- HDU
---

　　Ignatius has just come back school from the 30th ACM/ICPC. Now he has a lot of homework to do. Every teacher gives him a deadline of handing in the homework. If Ignatius hands in the homework after the deadline, the teacher will reduce his score of the final test, 1 day for 1 point. And as you know, doing homework always takes a long time. So Ignatius wants you to help him to arrange the order of doing homework to minimize the reduced score.

Input

　　The input contains several test cases. The first line of the input is a single integer T which is the number of test cases. T test cases follow. 
Each test case start with a positive integer N(1<=N<=15) which indicate the number of homework. Then N lines follow. Each line contains a string S(the subject's name, each string will at most has 100 characters) and two integers D(the deadline of the subject), C(how many days will it take Ignatius to finish this subject's homework). 

　　Note: All the subject names are given in the alphabet increasing order. So you may process the problem much easier. 
Output

　　For each test case, you should output the smallest total reduced score, then give out the order of the subjects, one subject in a line. If there are more than one orders, you should output the alphabet smallest one. 
Sample Input

```
2
3
Computer 3 3
English 20 1
Math 3 2
3
Computer 3 3
English 6 3
Math 6 3
```

Sample Output

```
2
Computer
Math
English
3
Computer
English
Math


        
 
```

Hint

```
In the second test case, both Computer->English->Math and Computer->Math->English leads to reduce 3 points, but the 
word "English" appears earlier than the word "Math", so we choose the first order. That is so-called alphabet order.
```

 

解题思路：
　　本题给出多组数据，每组数据给出一个整数t为测试数量，给出一个整数n代表项目数量，之后n行跟随每行给出一个项目信息，包括项目名name 最晚上交时间deadline 完成该项目需要的时间costDays。每个项目只要晚于deadline所规定的时间上交，每晚一天扣一分。对于每组测试要求输出最少的扣分数之后按提交顺序输出项目名。

　　本题限定了项目的数量n <= 15。假设有A、B、C、D四个项目，每个项目有已上交和未上交两种状态，我们可以用一个四位二进制表示这四个项目的所有状态，例如0001表示A上交其他所有项目未上交，0011表示AB项目上交CD项目未上交，1111表示所有项目都上交，以此类推，若想n取到最大15，需要15位2进制数表示所有情况，表示15个项目全部提交的二进制数最大，为111111111111111，其所代表的10进制整数为32767，其大小远小于int上限，那么我们只需要令maxn = 1 << 15 即32768 就可以表示本题中所有可能出现的状态，

　　既然所有状态都已经可以表示出来，现在就可以考虑一下如何获得状态，例如AB项目上交CD项目未上交的状态0011，可以由A上交其他都没上交的状态0001提交B（0010）获得，也可以由状态B上交其他都没上交的状态0010提交A（0001）获得，那么我们如果想要算出获得当前状态的最小扣分值，只需要遍历所有只需一次提交便可以得到当前状态的状态（以后称为前状态）找到前状态的扣分加上本次提交项目所需的扣分的最小值即可，这就是本题的解题思想——状态压缩dp。

　　状态压缩dp也是dp，这里用一个结构体subject型的数组Subject[16]储存所有项目信息subject中包括项目名，最晚提交日期与消耗时间；用一个结构体nodeInformation型的数组dp[maxn]保存每个状态的信息，nodeInformation包括现在状态已经使用的天数，现在状态的前状态，前状态到现在状态所需提交的项目，现在状态的最少扣分。

```c++
struct subject{
    string name;    //项目名
    int deadline;   //最后期限
    int costDays;   //耗费时间
}Subject[maxn];
struct nodeInformation{
    int costNow;    //现在状态做项目已经使用的天数
    int now;    //前状态到现在状态所需提交的项目
    int pre;    //现在状态的前状态
    int subScore;   //现在状态的最少扣分
}dp[maxn];
```

 　　之后遍历所有状态，开始时假设现在状态疯狂扣分扣无限分，之后遍历所有项目作为前项目到现在状态所提交的项目，每次遍历看看能不能减少现在状态的扣分，能的话就跟新dp数组信息。遍历结束后用一个栈后进先出的特性从所有项目都提交的状态向前寻找前状态并入栈。之后出栈输出项目名即可获得提交的顺序。 

```c++
int lastSubNow = endn;  //全部提交状态
while(lastSubNow){  //到全部未提交为止
    subName.push(dp[lastSubNow].now);   //入站前状态
    lastSubNow = dp[lastSubNow].pre;
}
while(!subName.empty()){    //输出提交顺序
    cout << Subject[subName.top()].name << endl;
    subName.pop();
}
```

 AC代码 

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1 << 15;
const int inf = 0x7fffffff;
struct subject{
    string name;    //项目名
    int deadline;   //最后期限
    int costDays;   //耗费时间
}Subject[maxn];
struct nodeInformation{
    int costNow;    //现在状态做项目已经使用的天数
    int now;    //前状态到现在状态所需提交的项目
    int pre;    //现在状态的前状态
    int subScore;   //现在状态的最少扣分
}dp[maxn];
stack<int> subName; //记录路径
int main()
{
    int t;  //测试数量
    while(scanf("%d", &t) != EOF){
        while(t--){
            memset(dp, 0, sizeof(dp));
            int n;
            scanf("%d", &n);    //输入项目数量
            for(int i = 0; i < n; i++){ //输入每个项目
                cin >> Subject[i].name >> Subject[i].deadline >> Subject[i].costDays;
            }
            int endn = (1 << n) - 1;  //所有项目都提交
            for(int i = 1; i <= endn; i++){   //i所对应的2进制数代表每一个项目的完成情况，即i对应的二进制就是当前状态
                dp[i].subScore = inf;   //初始化完成这种情况疯狂扣分减无限分
                for(int j = n - 1; j >= 0; j--){
                    //遍历所有项目，j为当前选择作为前状态提交项目的项目
                    int nowSubj = 1 << j;   //nowSubj代表只提交下标为j的项目的状态
                    if(i & nowSubj){    //判断i情况中下标为j的项目是否提交
                        //若已经提交进行如下操作
                        int past = i - nowSubj;//把i状态的中代表j号项目的状态改为没有提交，就可以获得i状态提交j的前状态past
                        int subScorej;
                        //subScorej代表past状态提交j号项目的扣分
                        //它就等于past状态已经消耗的天数加上j项目需要消耗的天数减去j号项目的要求的最晚提交日期。
                        subScorej = dp[past].costNow + Subject[j].costDays - Subject[j].deadline;
                        if(subScorej < 0){  //扣分不可能小于0，如果小于0就证明时间充足，扣分为0
                            subScorej = 0;
                        }
                        if(subScorej + dp[past].subScore < dp[i].subScore){ //如果从past状态到现在状态可以使i状态当前的扣分减小
                            dp[i].subScore = subScorej + dp[past].subScore;
                            //更新i状态的扣分
                            dp[i].costNow = dp[past].costNow + Subject[j].costDays;
                            //记录以past为前状态的i状态消耗的天数
                            dp[i].now = j;
                            //前状态past到现在状态i并使其扣分最少所提交的项目为j项目
                            dp[i].pre = past;
                            //记录i的前状态为past
                        }
                    }
                }
            }
            printf("%d\n", dp[endn].subScore);
            //输出提交全部项目的最少扣分（所有的项目都提交状态最少扣分）
            int lastSubNow = endn;  //全部提交状态
            while(lastSubNow){  //到全部未提交为止
                subName.push(dp[lastSubNow].now);   //入站前状态
                lastSubNow = dp[lastSubNow].pre;
            }
            while(!subName.empty()){    //输出提交顺序
                cout << Subject[subName.top()].name << endl;
                subName.pop();
            }
        }
    }
    return 0;
}
```

