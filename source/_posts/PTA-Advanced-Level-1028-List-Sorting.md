---
title: PTA (Advanced Level) 1028 List Sorting
date: 2019-01-26 15:44:09
categories: 
- 算法相关
tags:
- PTA
- 排序
---

　　Excel can sort records according to any column. Now you are supposed to imitate this function.

### Input Specification:

　　Each input file contains one test case. For each case, the first line contains two integers N (≤) and C, where N is the number of records and C is the column that you are supposed to sort the records with. Then N lines follow, each contains a record of a student. A student's record consists of his or her distinct ID (a 6-digit number), name (a string with no more than 8 characters without space), and grade (an integer between 0 and 100, inclusive).

### Output Specification:

　　For each test case, output the sorting result in N lines. That is, if C = 1 then the records must be sorted in increasing order according to ID's; if C = 2 then the records must be sorted in non-decreasing order according to names; and if C = 3 then the records must be sorted in non-decreasing order according to grades. If there are several students who have the same name or grade, they must be sorted according to their ID's in increasing order.

### Sample Input 1:

```in
3 1
000007 James 85
000010 Amy 90
000001 Zoe 60
```

### Sample Output 1:

```out
000001 Zoe 60
000007 James 85
000010 Amy 90
```

### Sample Input 2:

```in
4 2
000007 James 85
000010 Amy 90
000001 Zoe 60
000002 James 98
```

### Sample Output 2:

```out
000010 Amy 90
000002 James 98
000007 James 85
000001 Zoe 60
```

### Sample Input 3:

```in
4 3
000007 James 85
000010 Amy 90
000001 Zoe 60
000002 James 90
```

### Sample Output 3:

```out
000001 Zoe 60
000007 James 85
000002 James 90
000010 Amy 90
```

题目解析
　　本题给出两个整数，n为学生数量，c为排序指令，之后n行为学生信息，每个学生的信息包括学生号码ID，学生姓名name与学生成绩grade，排序指令为1时要求按照id升序排序，指令为2时要求按照学生姓名name字典序升序排序，若两个学生姓名字典序相同则按id升序排序，指令为3时按成绩grade升序排序，成绩相同时按id升序排序。

　　只需要将所有学生信息记录在一个容器vector中，根据题意对应的cmp函数，根据指令调用cmp函数即可。

　　AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
struct student{ //结构体student代表一个学生的信息
    int id; //学号
    string name;    //姓名
    int grade;  //成绩
};
vector<student> V;  //容器V记录所有学生信息
bool cmp1(student a, student b){    
    //指令为1时按学号升序排序
    return a.id < b.id;
}
bool cmp2(student a, student b){
    //指令为2时按姓名字典序升序排序
    if(a.name != b.name)
        return a.name < b.name;
    else
        //姓名字典序相同时按照学号升序排序
        return a.id < b.id;
}
bool cmp3(student a, student b){
    //指令为3时按照成绩升序排序
    if(a.grade != b.grade)
        return a.grade < b.grade;
    else
        //成绩相同时按照学号升序排序
        return a.id < b.id;
}
int n, c;
int main()
{
    scanf("%d%d", &n, &c);  //输入学生数量与排序指令
    student temp;
    for(int i = 0; i < n; i++){
        cin >> temp.id >> temp.name >> temp.grade;
        //输入学生信息加入容器V
        V.push_back(temp);
    }
    //根据排序指令进行排序
    if(c == 1)
        sort(V.begin(), V.end(), cmp1);
    else if(c == 2)
        sort(V.begin(), V.end(), cmp2);
    else
        sort(V.begin(), V.end(), cmp3);
    //输出时使用cout会超时
    for(auto i : V){
        printf("%06d %s %2d\n", i.id, (i.name).c_str(), i.grade);
        //格式化输出
    }
    return 0;
}
```

