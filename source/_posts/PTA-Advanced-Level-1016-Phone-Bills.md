---
title: PTA (Advanced Level) 1016 Phone Bills
date: 2018-12-20 19:33:16
categories: 
- 算法相关
tags:
- STL
- PTA
- 模拟
---

　　A long-distance telephone company charges its customers by the following rules:

　　Making a long-distance call costs a certain amount per minute, depending on the time of day when the call is made. When a customer starts connecting a long-distance call, the time will be recorded, and so will be the time when the customer hangs up the phone. Every calendar month, a bill is sent to the customer for each minute called (at a rate determined by the time of day). Your job is to prepare the bills for each month, given a set of phone call records.

### Input Specification:

　　Each input file contains one test case. Each case has two parts: the rate structure, and the phone call records.

　　The rate structure consists of a line with 24 non-negative integers denoting the toll (cents/minute) from 00:00 - 01:00, the toll from 01:00 - 02:00, and so on for each hour in the day.

　　The next line contains a positive number N (≤), followed by N lines of records. Each phone call record consists of the name of the customer (string of up to 20 characters without space), the time and date (`mm:dd:hh:mm`), and the word `on-line`or `off-line`.

　　For each test case, all dates will be within a single month. Each `on-line` record is paired with the chronologically next record for the same customer provided it is an `off-line` record. Any `on-line` records that are not paired with an `off-line` record are ignored, as are `off-line` records not paired with an `on-line` record. It is guaranteed that at least one call is well paired in the input. You may assume that no two records for the same customer have the same time. Times are recorded using a 24-hour clock.

### Output Specification:

　　For each test case, you must print a phone bill for each customer.

　　Bills must be printed in alphabetical order of customers' names. For each customer, first print in a line the name of the customer and the month of the bill in the format shown by the sample. Then for each time period of a call, print in one line the beginning and ending time and date (`dd:hh:mm`), the lasting time (in minute) and the charge of the call. The calls must be listed in chronological order. Finally, print the total charge for the month in the format shown by the sample.

### Sample Input:

```in
10 10 10 10 10 10 20 20 20 15 15 15 15 15 15 15 20 30 20 15 15 10 10 10
10
CYLL 01:01:06:01 on-line
CYLL 01:28:16:05 off-line
CYJJ 01:01:07:00 off-line
CYLL 01:01:08:03 off-line
CYJJ 01:01:05:59 on-line
aaa 01:01:01:03 on-line
aaa 01:02:00:01 on-line
CYLL 01:28:15:41 on-line
aaa 01:05:02:24 on-line
aaa 01:04:23:59 off-line
```

### Sample Output:

```out
CYJJ 01
01:05:59 01:07:00 61 $12.10
Total amount: $12.10
CYLL 01
01:06:01 01:08:03 122 $24.40
28:15:41 28:16:05 24 $3.85
Total amount: $28.25
aaa 01
02:00:01 04:23:59 4318 $638.80
Total amount: $638.80
```

解题思路：
　　本题是一个计算电话费用的题目，每组测试首先给出24个整数，代表一天中00：00 ~ 24：00每个小时每分钟通话的收费（单位是美分），下一行给出一个整数n表示通话记录的数量，之后n行跟随表示每条通话记录的信息，包括用户名 时间（月:日:时:分） 开始通话或结束通话标识（on-line / off-line）。要求按时间输出所有用户的通话信息，格式为

```
用户名  通话月份
开始通话时间  结束通话时间  本次通话金额（单位为美元）
……
……
Total amount: 全部通话金额总和单位为美元）
```

　　这里首先用一个数组toll[25]记录24小时通话各自的收费，用一个结构体node记录每条通话记录

```
int toll[25];   //记录每个小时每分钟通话的收费
struct node{    
    string name;    //客户名
    int month, dd, hh,mm;   //时间
    bool line;  //结束通话标识 true表示on-line false表示off—line
};
```

　　我们想要在输入是将每个客户的通话记录都分开记录，这里可以利用map将每个客户名映射到一个vector容器，容器中装载通话记录。

```
map<string, vector<node> > customer;    
//customer["张三"]现在就代表着姓名为张三的用户的所有通话记录
```

　　这样我们就可以开始录入通话记录了

```c++
while(n--){
        node temp;  //temp记录当前输入的通话记录信息
        cin >> temp.name;   //输入姓名
        scanf("%d:%d:%d:%d", &temp.month, &temp.dd, &temp.hh, &temp.mm);
        //输入时
        string tline;
        cin >> tline;   //输入通话标识
        temp.line = mp[tline];  //这里的mp将"on-line"映射为true将"off-line"映射为false
        customer[temp.name].push_back(temp);
        //将当前信息记录入其客户所对应的容器中
    }
```

 之后我们就需要遍历所有客户与其通话记录，将客户的通话记录按时间升序排序，根据排序后的通话记录，我们就可以获得该客户的所有完整通话（时间相邻最近的on_line与off-line，中间不能夹着其他on_line与off-line）。我们需要获得每一个完整通话并将其记录在对应用户名下，我们用结构体call记录每一个完整通话的信息，包括客户名，开始通话时间，结束通话时间，通话时长，通话消费。 

```c++
struct call{
    string name;    //客户名
    int onMonth, onDd, onHh, onMm;  //开始通话时间
    int offMonth, offDd, offHh, offMm;  //结束通话时间
    int sumTime;    //通话时长
    int cost;   //通话消耗
};
```

　为了记录每个客户的完整通话，我们可以采用和每个客户通话记录一样的记录方式，利用map将每个客户名映射到一个vector容器，只不过这次容器中装载的是完整通话。

```c++
map<string, vector<call> > costInf;
//costInf["张三"]现在就代表着姓名为张三的客户的所有完整通话
```

　　遍历信息　　

```c++
for(auto i : customer){ //遍历所有客户
        sort((i.second).begin(), (i.second).end(), cmp);
        //将每个用户的通话记录按时间升序排序
        bool isOnLine = false;
        //isOnLine用来标记客户当前是否正在通话
        call temp;  //temp记录一次完整通话
        for(auto j : (i.second)){   //遍历该客户所有通话记录
            if(j.line){ //遇到开始通话的通话记录
                isOnLine = true;    //标记当前状态为正在通话
                temp.onMonth = j.month, temp.onDd = j.dd, temp.onHh = j.hh, temp.onMm = j.mm;
                //记录开始通话时间
            }else if(!j.line && isOnLine){  //如果现在正在通话且当前通话记录为结束通话
                isOnLine = false;   //标记当前状态为结束通话
                temp.offMonth = j.month, temp.offDd = j.dd, temp.offHh = j.hh, temp.offMm = j.mm;
                //记录通话结束时间
                temp.sumTime = 0;
                temp.cost = 0;
                clt(temp.onDd, temp.onHh, temp.onMm, temp.offDd, temp.offHh, temp.offMm, temp.sumTime, temp.cost);
                //计算本次通话的时长于消耗
                costInf[j.name].push_back(temp);
                //将当前完整通话信息记录入其客户所在容器中
            }
        }
    }
```

　　由于我们之前已经将通话记录排序，所以获取的完整通话记录一定是时间升序。

　　对与计算每次完整通话时间与消耗，我们只需要由开始通话时间开始，每次加一分钟，到开始时间等于结束时间为止，在过程中记录通话时间与花费金额即可。

```c++
void clt(int bgdd, int bghh, int bgmm, int eddd, int edhh, int edmm, int &sumTime, int &cost){
    //传入开始时间与结束时间，把我们要计算的两个值传引用。
    while(bgdd < eddd || bghh < edhh || bgmm < edmm){   //开始时间小于结束时间
        sumTime++;  //通话时间加一分钟
        cost += toll[bghh]; //将该分钟的收费计入消耗
        bgmm++; //开始时间加一分钟
        if(bgmm == 60){ //分钟进位
            bgmm = 0;
            bghh++;
        }
        if(bghh == 24){ //小时进位
            bghh = 0;
            bgdd++;
        }
    }
```

 AC代码 

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+100;
int toll[25];   //记录每个小时每分钟通话的收费
struct node{
    string name;    //客户名
    int month, dd, hh,mm;   //时间
    bool line;  //结束通话标识 true表示on-line false表示off—line
};
struct call{
    string name;    //客户名
    int onMonth, onDd, onHh, onMm;  //开始通话时间
    int offMonth, offDd, offHh, offMm;  //结束通话时间
    int sumTime;    //通话时长
    int cost;   //通话消耗
};
map<string, bool> mp;
map<string, vector<node> > customer;
//customer["张三"]现在就代表着姓名为张三的用户的所有通话记录
map<string, vector<call> > costInf;
//costInf["张三"]现在就代表着姓名为张三的客户的所有完整通话
bool cmp(node a, node b){   //按时间升序排序
    if(a.month != b.month)
        return a.month < b.month;
    else if(a.dd != b.dd){
        return a.dd < b.dd;
    }else if(a.hh != b.hh){
        return a.hh < b.hh;
    }else if(a.mm != b.mm)
        return a.mm < b.mm;
}
void clt(int bgdd, int bghh, int bgmm, int eddd, int edhh, int edmm, int &sumTime, int &cost){
    //传入开始时间与结束时间，把我们要计算的两个值传引用。
    while(bgdd < eddd || bghh < edhh || bgmm < edmm){   //开始时间小于结束时间
        sumTime++;  //通话时间加一分钟
        cost += toll[bghh]; //将该分钟的收费计入消耗
        bgmm++; //开始时间加一分钟
        if(bgmm == 60){ //分钟进位
            bgmm = 0;
            bghh++;
        }
        if(bghh == 24){ //小时进位
            bghh = 0;
            bgdd++;
        }
    }
}
int main()
{
    mp["on-line"] = true;
    mp["off-line"] = false;
    for(int i = 0; i < 24; i++){
        scanf("%d", &toll[i]);
    }
    int n;
    scanf("%d", &n);
    while(n--){
        node temp;  //temp记录当前输入的通话记录信息
        cin >> temp.name;   //输入姓名
        scanf("%d:%d:%d:%d", &temp.month, &temp.dd, &temp.hh, &temp.mm);
        //输入时
        string tline;
        cin >> tline;   //输入通话标识
        temp.line = mp[tline];  //这里的mp将"on-line"映射为true将"off-line"映射为false
        customer[temp.name].push_back(temp);
        //将当前信息记录入其客户所对应的容器中
    }
    for(auto i : customer){ //遍历所有客户
        sort((i.second).begin(), (i.second).end(), cmp);
        //将每个用户的通话记录按时间升序排序
        bool isOnLine = false;
        //isOnLine用来标记客户当前是否正在通话
        call temp;  //temp记录一次完整通话
        for(auto j : (i.second)){   //遍历该客户所有通话记录
            if(j.line){ //遇到开始通话的通话记录
                isOnLine = true;    //标记当前状态为正在通话
                temp.onMonth = j.month, temp.onDd = j.dd, temp.onHh = j.hh, temp.onMm = j.mm;
                //记录开始通话时间
            }else if(!j.line && isOnLine){  //如果现在正在通话且当前通话记录为结束通话
                isOnLine = false;   //标记当前状态为结束通话
                temp.offMonth = j.month, temp.offDd = j.dd, temp.offHh = j.hh, temp.offMm = j.mm;
                //记录通话结束时间
                temp.sumTime = 0;
                temp.cost = 0;
                clt(temp.onDd, temp.onHh, temp.onMm, temp.offDd, temp.offHh, temp.offMm, temp.sumTime, temp.cost);
                //计算本次通话的时长于消耗
                costInf[j.name].push_back(temp);
                //将当前完整通话信息记录入其客户所在容器中
            }
        }
    }
    for(auto i : costInf){  //遍历所有客户，输出信息
        cout << (i.first) << " ";   //输出客户名
        bool flag = false;  //标记月份是否已经输出
        double tam = 0.0;
        for(auto j : (i.second)){
            if(!flag){  //没有输出月份
                printf("%02d\n", j.onMonth);
                flag = true;    //格式化输出月份并将flag标记为true
            }
            printf("%02d:%02d:%02d %02d:%02d:%02d %d $%.2f\n", j.onDd, j.onHh, j.onMm, j.offDd, j.offHh, j.offMm, j.sumTime, j.cost / 100.0);
            //格式化输出开始时间 结束时间 通话时长 通话消耗（美分转为美元）
            tam += j.cost/ 100.0;
        }
        printf("Total amount: $%.2f\n", tam);
        //输出总消耗
    }
    return 0;
}
```

