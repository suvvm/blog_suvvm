---
title: ZOJ 2971 Give Me the Number
date: 2018-11-28 18:58:45
categories: 
- 算法相关
tags:
- 模拟
- ZOJ
---

　　Numbers in English are written down in the following way (only numbers less than *109*are considered). Number *abc,def,ghi* is written as "*[abc]* million *[def]* thousand *[ghi]*". Here "*[xyz]* " means the written down number *xyz* .

　　In the written down number the part "*[abc]* million" is omitted if *abc = 0* , "*[def]*thousand" is omitted if *def = 0* , and "*[ghi]* " is omitted if *ghi = 0* . If the whole number is equal to *0* it is written down as "zero". Note that words "million" and "thousand" are singular even if the number of millions or thousands respectively is greater than one.

　　Numbers under one thousand are written down in the following way. The number *xyz* is written as "*[x]* hundred and *[yz]* ”. ( If *yz = 0* it should be only “*[x]* hundred”. Otherwise if *y = 0* it should be only “*[x]* hundred and [z]”.) Here "*[x]* hundred and" is omitted if *x = 0* . Note that "hundred" is also always singular.

　　Numbers under *20* are written down as "zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten", "eleven", "twelve", "thirteen", "fourteen", "fifteen", "sixteen", "seventeen", "eighteen", and "nineteen" respectively. Numbers from *20* to *99* are written down in the following way. Number *xy* is written as "*[x0]**[y]* ", and numbers divisible by ten are written as "twenty", "thirty", "forty", "fifty", "sixty", "seventy", "eighty", and "ninety" respectively.

　　For example, number *987,654,312* is written down as "nine hundred and eighty seven million six hundred and fifty four thousand three hundred and twelve", number *100,000,037* as "one hundred million thirty seven", number *1,000* as "one thousand". Note that "one" is never omitted for millions, thousands and hundreds.

　　Give you the written down words of a number, please give out the original number.

Input

　　Standard input will contain multiple test cases. The first line of the input is a single integer *T* (1 <= *T* <= 1900) which is the number of test cases. It will be followed by *T* consecutive test cases.

　　Each test case contains only one line consisting of a sequence of English words representing a number.

Output

　　For each line of the English words output the corresponding integer in a single line. You can assume that the integer is smaller than *109*.

Sample Input

```
3
one
eleven
one hundred and two
```

Sample Output

```
1
11
102
```


解题思路：
　　给出行数t，之后t行每行给出一个用英文描述的数字，要求输出这个数字。

　　这里从头开始记录这个数字，每记录一个数字就将它加入答案，根据题目语法以百万"million"，千"thousand"和百"hundred"这几个在英文中有特殊表示形式的单词为标值，每次遇到这些标值就将目前的答案乘以标志数，如果是"million"或"thousand"（"hundred"不用）从0开始重新记录下一个标值前的数字，一直记录到字符串的最后一个单词。

样例分析：

nine hundred and eighty seven million six hundred and fifty four thousand three hundred and twelve

nine： 9 + 0 = 9

hundred： 9 * 100 = 900

eighty ：80 + 900 = 980

and ：0 + 980 = 980 

seven ：7 + 980 = 987

million ：987 * 1000000 = 987000000　　（遇到million重新从0开始记录）

six ： 6 + 0 = 6

hundred ： 6 * 100 = 600

and : 0 + 600 = 600

fifty ： 50 + 600 = 650

four ： 4 + 650 = 654

thousand ： 654 * 1000 + 987000000 = 987654000　　（遇到thousand重新从0开始记录）

three ： 0 + 3 = 3

hundred ： 3 * 100 = 300

and ： 0 + 300 = 300；

twelve ： 12 + 300 = 312；

987654000 + 312 = 987654312

　　知道解法后就要思考怎样实现它，由于每一行为一个数字，输入时我们用一个getline之间获得一行存入string型的变量str中，用头文件sstream下的istringstream可以以空格为界读取str中的每个单词，将获取的每个单词计入string型变量temp中，并根据temp的内容进行操作，我们可以用map来建立每个(非"million"，"thousand"，"hundred")单词和其对应数字的关系。

AC代码

```c++
#include<bits/stdc++.h>
using namespace std;
map<string, int> m;
string s1[21] = {"zero","one","two","three","four","five","six","seven","eight","nine","ten","eleven","twelve","thirteen","fourteen","fifteen","sixteen","seventeen","eighteen","nineteen"};
string s2[9] = {"twenty","thirty","forty","fifty","sixty","seventy","eighty","ninety"};
string s3 = "and";
string str, temp;
int main(){
    for(int i = 0; i < 20; i++){    //建立0~19的映射
        m[s1[i]] = i;
    }
    for(int i = 0; i < 8; i++){ //建立20，30……，90的映射
        m[s2[i]] = 20 + i * 10;
    }
    m[s3] = 0;  //如果遇到and不需要操作，就将and映射为0即可
    int t;  //行数
    scanf("%d", &t);
    getchar();  //吸收换行符
    while(t--){
        getline(cin,str);   //获取一行
        istringstream cinstr(str);
        int ans = 0, num = 0;   //ans记录答案，temp记录当前数字
        while(cinstr >> temp){  //在str中读取单词
            if(temp == "million"){  //遇到million乘以1000000并从0开始重新记录
                ans += num * 1000000;
                num = 0;
            }else if(temp == "thousand"){   //遇到thousand乘以1000并从0开始重新记录
                ans += num * 1000;
                num = 0;
            }else if(temp == "hundred"){    //遇到hundred乘以100
                num *= 100;
            }else{
                num += m[temp]; //记录数字
            }
        }
        ans += num; //将最后记录的数字加入答案
        printf("%d\n", ans);
    }
    return 0;
}
```

