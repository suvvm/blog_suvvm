---
title: PTA (Advanced Level) 1012 The Best Rank
date: 2018-12-15 20:30:56
categories: 
- 算法相关
tags:
- STL
- PTA
---

　　To evaluate the performance of our first year CS majored students, we consider their grades of three courses only: `C` - C Programming Language, `M` - Mathematics (Calculus or Linear Algrbra), and `E` - English. At the mean time, we encourage students by emphasizing on their best ranks -- that is, among the four ranks with respect to the three courses and the average grade, we print the best rank for each student.

　　For example, The grades of `C`, `M`, `E` and `A` - Average of 4 students are given as the following:

```
StudentID  C  M  E  A
310101     98 85 88 90
310102     70 95 88 84
310103     82 87 94 88
310104     91 91 91 91
```

　　Then the best ranks for all the students are No.1 since the 1st one has done the best in C Programming Language, while the 2nd one in Mathematics, the 3rd one in English, and the last one in average.

### Input Specification:

　　Each input file contains one test case. Each case starts with a line containing 2 numbers N and M (≤), which are the total number of students, and the number of students who would check their ranks, respectively. Then N lines follow, each contains a student ID which is a string of 6 digits, followed by the three integer grades (in the range of [0, 100]) of that student in the order of `C`, `M` and `E`. Then there are M lines, each containing a student ID.

### Output Specification:

　　For each of the M students, print in one line the best rank for him/her, and the symbol of the corresponding rank, separated by a space.

　　The priorities of the ranking methods are ordered as `A` > `C` > `M` > `E`. Hence if there are two or more ways for a student to obtain the same best rank, output the one with the highest priority.

　　If a student is not on the grading list, simply output `N/A`.

### Sample Input:

```in
5 6
310101 98 85 88
310102 70 95 88
310103 82 87 94
310104 91 91 91
310105 85 90 90
310101
310102
310103
310104
310105
999999
```

### Sample Output:

```out
1 C
1 M
1 E
1 A
3 A
N/A
```

解题思路：
　　本题给出学生数量n，与查询数量m，之后n行为学生信息，包括id，C语言得分， 数学得分，英语得分，按照平均分、C语言、数学、英语分别排序（项目名分别为A, C, M, E），之后m行查询为学生id，要求输出查询学生的这四个排名中的最高排名，若出现某几项排名相同且都该学生最高排名，输出优先级平均分 > C语言 > 数学 > 英语。

　　定义一个结构体student记录每个学生的信息

```c++
struct student{
    int id; //记录学号
    int score[4];   //score[0]为平均分 score[1]C语言 score[2]数学 score[3]英语 
    //为了方便输出直接按优先级顺序记录学生分数
};
```

　　用一个vector容器储存所有学生信息，一个int型的二维数组rank_stu\[ i ][ j ]记录排名信息，i为学生排名， j为排名依照项，同样 0 为平均分 1 C语言 2 数学 3 英语 。之后对所有项目进行排名，将得到的排名记录入rank_stu中就可以开始查找了。

　　查找时要先判断输入的查询学号存不存在，可以将rank_stu初始化为0，如果查询当前学号某一排名为 0 证明该学号不存在，若存在，比较其所有项目排名并输出最高项排名与项目名。

　　AC代码

```c++
#include<bits/stdc++.h>
using namespace std;
int inf = INT_MAX;
//无穷大
const int maxn = 1000000;
//学号最大值
const char course[4] = {'A', 'C', 'M', 'E'};
//记录项目名
struct student{
    int id; //记录学号
    int score[4];   //score[0]为平均分 score[1]C语言 score[2]数学 score[3]英语
    //为了方便输出直接按优先级顺序记录学生分数
};
vector<student> stu;    //记录学生信息
int rank_stu[maxn][4];  //记录排名
int courseNow;  //当前正在排名的课程
int n, m;   //学生数量，查询数量
bool cmp(student a, student b){
    return a.score[courseNow] > b.score[courseNow];
}
//排序依照当前courseNow的对应成绩
void getRank(){
    memset(rank_stu, 0, sizeof(rank_stu));
    //初始化rank_stu为0
    for(int i = 0; i < 4; i++){ //获得并记录四个项目的排名
        courseNow = i;  //设定当前排名课程
        sort(stu.begin(), stu.end(), cmp);  //对stu进行排序
        int cnt = 0;
        vector<student>::iterator preit = stu.begin();  //preit记录获取排名过程中上一个位置迭代器
        for(vector<student>::iterator it = stu.begin(); it != stu.end(); it++){
            cnt++;  //记录当前学生位置
            if(it == stu.begin()){  //若it排名第一直接记录排名为cnt（cnt当前值为1）
                rank_stu[it->id][courseNow] = cnt;
                preit = it; //记录下一个学生的前一个迭代器为it
            }else{
                if(preit->score[courseNow] == it->score[courseNow]){
                    //前一个学生与本学生成绩相同则排名相同
                    rank_stu[it->id][courseNow] = rank_stu[preit->id][courseNow];
                }else{
                    rank_stu[it->id][courseNow] =  cnt;
                    //否则记录当前排名为cnt
                }
                preit = it;//记录下一个学生的前一个迭代器为it
            }
        }
    }
}
int main(){
    while(scanf("%d%d", &n, &m) != EOF){    //输入学生数量与排名数量
        while(n--){
            student temp;
            scanf("%d%d%d%d", &temp.id, &temp.score[1], &temp.score[2], &temp.score[3]);
            //输入学生信息
            temp.score[0] = (temp.score[1] + temp.score[2] + temp.score[3]) / 3;
            //计算平均分
            stu.push_back(temp);
        }

        getRank();
        //获取排名
        while(m--){
            int query;
            scanf("%d", &query);
            //输入查询id
            if(rank_stu[query][0] == 0){    //学生不存在
                printf("N/A\n");
                continue;
            }
            int hRank = inf;    //初始化最高排名为无穷大
            int hRcourse = -1;  //hRcourse记录最高排名对应的项目
            for(int i = 0; i < 4; i++){
                if(rank_stu[query][i] < hRank){
                    hRank = rank_stu[query][i];
                    hRcourse = i;
                }
            }
            printf("%d %c\n", hRank, course[hRcourse]);
            //输出排名与项目名
        }
    }
    return 0;
}
```

