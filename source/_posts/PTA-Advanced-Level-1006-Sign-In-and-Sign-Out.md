---
title: PTA (Advanced Level) 1006 Sign In and Sign Out
date: 2019-04-03 20:17:30
categories: 
- 算法相关
tags:
- PTA
---

　　At the beginning of every day, the first person who signs in the computer room will unlock the door, and the last one who signs out will lock the door. Given the records of signing in's and out's, you are supposed to find the ones who have unlocked and locked the door on that day.

**Input Specification:**

　　Each input file contains one test case. Each case contains the records for one day. The case starts with a positive integer M, which is the total number of records, followed by M lines, each in the format:

　　ID_number Sign_in_time Sign_out_time

　　where times are given in the format HH:MM:SS, and ID_number is a string with no more than 15 characters.

**Output Specification:**

　　For each test case, output in one line the ID numbers of the persons who have unlocked and locked the door on that day. The two ID numbers must be separated by one space.

　　Note: It is guaranteed that the records are consistent. That is, the sign in time must be earlier than the sign out time for each person, and there are no two persons sign in or out at the same moment.

**Sample Input:**

3

CS301111 15:30:28 17:00:10

SC3021234 08:00:00 11:25:25

CS301133 21:45:00 21:58:40

**Sample Output:**

SC3021234 CS301133

题目解析

　　本题给出一天内登录机房的总人数m，之后给出m行数据，每行为一个人的登录信息，其中包括一个长度不超过15的id， 一个格式为HH:MM:SS的登录时间inTime，和一个格式为HH:MM:SS的登出时间outTime。

　　第一个登录机房的人将是开门者，最后一个登出机房的人将是锁门者，要求输出开门者与锁门者的id。

　　C++的string比较两个长度相等的字符串大小是由首位开始按位比较ASCII码，首位ASCII码大的字符串视为较大字符串，若首位相等边比较第二位，依次向后比较。者正好符合我们对HH:MM:SS类型的时间比较，较大的时间在字符串比较时依然较大，这样我们便可以将时间存为string直接进行比较。

```c++
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int m;
    string unlockedId, lockedId, minTime = "24:00:00", maxTime = "00:00:00";
    scanf("%d", &m);    //输入登录总人数
    for(int i = 0; i < m; i++){
        string id, inTime, outTime;
        cin >> id >> inTime >> outTime; //输入登录者id 登录时间 登出时间
        if(minTime > inTime){   //如果登录时间早于当早登录时间
            minTime = inTime;   //记录最早登录时间为当前登录时间
            unlockedId = id;    //记录开门者id为当前id
        }
        if(maxTime < outTime){  //如果登出时间晚于最晚登录时间
            maxTime = outTime;  //记录最晚登出时间为当前登出时间
            lockedId = id;  //记录锁门者id为当前id
        }
    }
    cout << unlockedId << " " << lockedId << endl;
    //输出开门者和锁门者的id
    return 0;
}
```

