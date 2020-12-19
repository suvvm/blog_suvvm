---
title: PTA (Advanced Level) 1022 Digital Library
date: 2019-01-22 21:43:53
categories: 
- 算法相关
tags:
- PTA
- 模拟
---

　　A Digital Library contains millions of books, stored according to their titles, authors, key words of their abstracts, publishers, and published years. Each book is assigned an unique 7-digit number as its ID. Given any query from a reader, you are supposed to output the resulting books, sorted in increasing order of their ID's.

### Input Specification:

　　Each input file contains one test case. For each case, the first line contains a positive integer N (≤) which is the total number of books. Then N blocks follow, each contains the information of a book in 6 lines:

- Line #1: the 7-digit ID number;
- Line #2: the book title -- a string of no more than 80 characters;
- Line #3: the author -- a string of no more than 80 characters;
- Line #4: the key words -- each word is a string of no more than 10 characters without any white space, and the keywords are separated by exactly one space;
- Line #5: the publisher -- a string of no more than 80 characters;
- Line #6: the published year -- a 4-digit number which is in the range [1000, 3000].

　　It is assumed that each book belongs to one author only, and contains no more than 5 key words; there are no more than 1000 distinct key words in total; and there are no more than 1000 distinct publishers.

　　After the book information, there is a line containing a positive integer M (≤) which is the number of user's search queries. Then M lines follow, each in one of the formats shown below:

- 1: a book title
- 2: name of an author
- 3: a key word
- 4: name of a publisher
- 5: a 4-digit number representing the year

### Output Specification:

　　For each query, first print the original query in a line, then output the resulting book ID's in increasing order, each occupying a line. If no book is found, print `Not Found` instead.

### Sample Input:

```in
3
1111111
The Testing Book
Yue Chen
test code debug sort keywords
ZUCS Print
2011
3333333
Another Testing Book
Yue Chen
test code sort keywords
ZUCS Print2
2012
2222222
The Testing Book
CYLL
keywords debug book
ZUCS Print2
2011
6
1: The Testing Book
2: Yue Chen
3: keywords
4: ZUCS Print
5: 2011
3: blablabla
```

### Sample Output:

```out
1: The Testing Book
1111111
2222222
2: Yue Chen
1111111
3333333
3: keywords
1111111
2222222
3333333
4: ZUCS Print
1111111
5: 2011
1111111
2222222
3: blablabla
Not Found
```

题目解析
　　本题给出n个书本信息，每个书本给出id，书名，作者，关键词，出版社，发行年份，这五个信息，要求分别按照五个信息建立索引，之后给出m行查询，查询格式如下：

1：书名

2：作者

3：***一个\***关键词

4：出版商

5：年份

　　要求输出查询信息所包含的所有书的id。

　　虽然这是一个30分的题目但是它并不是膨胀困难，书名，作者，关键词，出版社，发行年份我们都可以用string类型保存，对于书籍id我们则用int进行存储，这里可以用map建立string与set<int>的对应关系，set<int>里储存的便是对应string所包含所有书的id（由于书的id一定不重复，且最后要求由小到大输出id，用set会方便很多）。注意关键词有很多个，每个都需要建立对应关系。

AC代码

```c++
#include <bits/stdc++.h>
using namespace std;
map<string, set<int> > title, author, keyWord, publisher, year;
//分别对应 同名的书 同作者的书 同关键词的书 同出版社的书 同年的书
int n;
int main()
{
    scanf("%d", &n);    //输入书的个数；
    for(int i = 0; i < n; i++){
        int id;
        string  tTitle, tAuthor, tKeyWord, tPublisher, tYear;

        cin >> id;  //输入id
        getchar();  //吸收换行符
        getline(cin, tTitle);   //输入书名
        getline(cin, tAuthor);  //输入作者
        getline(cin, tKeyWord); //输入该书所有关键词
        getline(cin, tPublisher); //输入出版社
        getline(cin, tYear);    //输入年份


        title[tTitle].insert(id);   //对应书名的集合中加入id
        author[tAuthor].insert(id); //对应作者的集合中加入id
        string temp;    //temp用来获取每一个关键词
        istringstream cinKeyWord(tKeyWord); //用tKeyWord作为输入流输入每一个关键词
        while(cinKeyWord >> temp){
            keyWord[temp].insert(id);   //每一个关键词对应的集合中都加入id
        }
        publisher[tPublisher].insert(id);   //对应出版社的集合加入id
        year[tYear].insert(id); //对应年份的集合加入id
    }
    scanf("%d", &n);    //输入查询个数
    for(int i = 0; i < n; i++){
        int query;
        string temp;
        scanf("%d: ", &query);  //输入查询控制符
        getline(cin, temp); //输入查询内容
        cout << query << ": " << temp << endl;
        //由于string传送速度较慢，若将查询过程包装为一个函数，最后一个结点会超时
        //不过传引用仿佛可以解决这个问题
        if(query == 1){ //控制符为1 输出 对应书名集合中包含的书
            if(title.find(temp) == title.end())
                printf("Not Found\n");
            else
                for(auto i : title[temp])
                    printf("%07d\n", i);
        }else if(query == 2){   //控制符为2 输出 对应作者集合中包含的书
            if(author.find(temp) == author.end())
                printf("Not Found\n");
            else
                for(auto i : author[temp])
                    printf("%07d\n", i);
        }else if(query == 3){   //控制符为3 输出 对应关键词集合中包含的书
            if(keyWord.find(temp) == keyWord.end())
                printf("Not Found\n");
            else
                for(auto i : keyWord[temp])
                    printf("%07d\n", i);
        }else if(query == 4){   //控制符为4 输出 对应出版社集合中包含的书
            if(publisher.find(temp) == publisher.end())
                printf("Not Found\n");
            else
                for(auto i : publisher[temp])
                    printf("%07d\n", i);
        }else if(query == 5){   //控制符为5 输出 对应年份集合中包含的书
            if(year.find(temp) == year.end())
                printf("Not Found\n");
            else
                for(auto i : year[temp])
                    printf("%07d\n", i);
        }
    }
    return 0;
}
```

