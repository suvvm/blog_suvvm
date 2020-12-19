---
title: PTA (Advanced Level) 1008 Elevator
date: 2019-04-04 12:47:06
categories: 
- 算法相关
tags:
- 模拟
- PTA
---

　　The highest building in our city has only one elevator. A request list is made up with Npositive numbers. The numbers denote at which floors the elevator will stop, in specified order. It costs 6 seconds to move the elevator up one floor, and 4 seconds to move down one floor. The elevator will stay for 5 seconds at each stop.

　　For a given request list, you are to compute the total time spent to fulfill the requests on the list. The elevator is on the 0th floor at the beginning and does not have to return to the ground floor when the requests are fulfilled.

### Input Specification:

　　Each input file contains one test case. Each case contains a positive integer N, followed by Npositive numbers. All the numbers in the input are less than 100.

### Output Specification:

　　For each test case, print the total time on a single line.

### Sample Input:

```
3 2 3 1
```

### Sample Output:

```
41
```

题目解析

　　本题首先给出一个整数n代表电梯会停留的层数，之后给出n个数，代表电梯具体停留的楼层，电梯初始在0层，每向上一层会消耗6秒，每向下一层会消耗4秒，在需停留层会停留5秒，电梯最后不需要返回0层。

　　模拟一下电梯的运行过程即可。

```c++
#include <bits/stdc++.h>
using namespace std;
int n, t = 0, nowf = 0; //n为电梯需停留层数， t为已经消耗的时间， nowf是电梯当前所在的楼层
int main()
{
    scanf("%d", &n);    //输入电梯需停留层数
    while(n--){
        int floors;
        scanf("%d", &floors);   //输入下一个要停留的楼层
        while(nowf < floors){   //向上运行
            nowf++;
            t += 6;
        }
        while(nowf > floors){   //向下运行
            nowf--;
            t += 4;
        }
        t += 5; //停留
    }
    printf("%d\n", t);
    return 0;
}
```

