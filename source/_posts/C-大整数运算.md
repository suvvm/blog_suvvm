---
title: C++大整数运算
date: 2019-11-09 21:07:58
categories: 
- 算法相关
tags:
- 大数运算
mathjax: true
---

加法运算：以两个数中较大的数的长度为边界进行遍历，由最低位开始按位进行加法运算，将对应位相加并记录进位，在下一位进行相加运算时再加上进位即可。 

 减法运算：同样以两个数中较长的长度为边界进行遍历，按位进行减法运算，不够减则从前一位借位。最终不要忘记消去最高位的0  

倍增（数量级在int内）运算：选择一个数进行遍历，将其每位依次乘另一个数，将产生的结果的个位作为结果，除个位外其他位作为进位。 

 除法（大整数除以普通数）运算：从被除数最高位开始遍历，每一个位与上一位遗留的余数结合后进行除法运算。  比较：优先比较长度，长度相同则由高位向下比较。  

相乘：使用分治思想   假设现有大整数A与大整数B要做乘法运算，如果依照暴力过一切的思想去遍历相乘时间复杂度将变为O(n^2)所以这里使用分治思想。  将长度为n的大数a分为两部分  若n为偶数则可以将a分为长度为$\frac n2$的两段$a_0$与$a_1$  若n为奇数则a0的长度为$\frac {n+1}2$,$a_1$长度为$ \frac n2 $ $a = a_0 \times 10^{\frac n2} + a_1$  则$a \times b$同样可以分为 
$$
a \times b = [a_0 \times 10^{\frac {n_1}2} + a_1] \times [b_0 \times 10^{\frac {n_2}2} + b_1]\\ =  a_0 \times b_0 \times 10^{\frac {n_1}2+\frac {n_2}2}+a_0 \times b_1 \times 10^{\frac {n_1}2} + a_1 \times b_0 \times 10^{\frac {n_2}2}+a_1 \times b_1
$$
对于新出现的 $a_0 \times b_0 \ \ a_0 \times b_1 \ \ a_1 \times b_0 \ \ a_1\ \times b_1$用相同的方法递归解决即可以。

附上代码

  ```c++
/********************************************************************************
* @File: 大面值数据处理
* @Author: suvvm
* @blog: www.suvvm.work
* @github: https://github.com/suvvm
* @email: suvvm@foxmail.com
* @Version: 1.0.0
* @Date: 2019-11-07
* @Description: 2019第2学期算法设计与分析分治实验1第2项
********************************************************************************/
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1000;
const int maxz = 1e6+10;
/*******************************************************
*
* ClassName:BigNum
* Description: 存储大数
* Parameter:
* 	@num	大数具体值（为了方便运算采取低位在前的存储方式）
* 	@len	大数位数
* 	@BigNum	无参构造，将其初始化为0
**********************************************************/
struct BigNum{
    int num[maxn];
    int len;
    BigNum(){
        memset(num, 0, sizeof(num));
        len = 0;
    }
};

/*******************************************************
*
* FunctionName:change
* Description: 将C++ string转换为BigNum
* Parameter:
* 	@temp	传入的字符串
* Return:
*   返回转换后的BigNum
**********************************************************/
BigNum change(string temp){
    BigNum a;
    a.len = temp.size();
    for(int i = 0; i < a.len; i++)
        a.num[i] = temp[a.len - i - 1] - '0';
    return a;
}

/*******************************************************
*
* FunctionName:compare
* Description: 比较两个BigNum的大小
* Parameter:
* 	@a	需要比较的参数1
*   @b  需要比较的参数2
* Return:
*   参数1大于参数2返回(int)1
*   参数2大于参数1返回(int)-1
*   相等返回(int)0
**********************************************************/
int compare(BigNum a, BigNum b){
    if(a.len > b.len)
        return 1;
    else if(a.len < b.len)
        return -1;
    else{
        for(int i = a.len -1; i >= 0; i--){
            if(a.num[i] > b.num[i])
                return 1;
            else if(a.num[i] < b.num[i])
                return -1;
        }
        return 0;
    }
}
/*******************************************************
*
* FunctionName:add
* Description: 两个BigNum大数相加
* Parameter:
* 	@a	要进行加法运算的BigNum1
*   @b  要进行加法运算的BigNum2
* Return:
*   返回相加后的BigNum
**********************************************************/
BigNum add(BigNum a, BigNum b){
    BigNum c;
    int carry = 0;//进位
    for(int i = 0; i < a.len || i < b.len; i++){//以较长的长度为界限
        int temp = a.num[i] + b.num[i] + carry;//两个位置相加后加上进位
        c.num[c.len++] = temp % 10;
        carry = temp / 10;
    }
    if(carry != 0)
        c.num[c.len++] = carry;
    return c;
}
/*******************************************************
*
* FunctionName:sub
* Description: 两个BigNum大数相减
* Parameter:
* 	@a	被减数
*   @b  减数
* Return:
*   返回相减后的BigNum
**********************************************************/
BigNum sub(BigNum a, BigNum b){
    BigNum c;
    for(int i = 0; i < a.len || i < b.len; i++){//较长为界
        if(a.num[i] < b.num[i]){//如果不够减
            a.num[i + 1]--;//借位
            a.num[i] += 10;//加10
        }
        c.num[c.len++] = a.num[i] - b.num[i];//相减结果为当前位结果
    }
    while(c.len - 1 >= 1 && c.num[c.len - 1] == 0)
        c.len--;//去除最高位的0
    return c;
}
/*******************************************************
*
* FunctionName: multi
* Description: 将BigNum扩大指定倍数（int）
* Parameter:
* 	@a	要扩大的BigNum
*   @b  扩大倍数
* Return:
*   返回扩大后的BigNum
**********************************************************/
BigNum multi(BigNum a, int b){
    BigNum c;
    int carry = 0;//进位
    for(int i = 0; i < a.len; i++){//a每位依次乘b
        int temp = a.num[i] * b + carry;
        c.num[c.len++] = temp % 10;//temp个位作为该位结果
        carry = temp / 10;//temp高位作为进位
    }
    while(carry != 0){//乘法的进位可能不止一位
        c.num[c.len++] = carry % 10;
        carry /= 10;
    }
    return c;
}
int mod = 0;
int &r = mod;
/*******************************************************
*
* FunctionName: divide
* Description: 大数除int内数字
* Parameter:
* 	@a	被除数
*   @b  除数
*   @r  余数
* Return:
*   返回相除后的BigNum
**********************************************************/
BigNum divide(BigNum a, int b, int &r){//r为余数
    BigNum c;
    c.len = a.len;//被除数和商的每一位是一一对应的，所以先令长度相等
    for(int i = a.len - 1; i >= 0; i--){//从最高位开始
        r = r * 10 + a.num[i];//每一位和上一位遗留的余数组合
        if(r < b)
            c.num[i] = 0;//若不够除该位为0
        else{//够除
            c.num[i] = r / b;//得到该位的商
            r = r % b;//新的余数
        }
    }
    while(c.len - 1 >= 1 && c.num[c.len - 1] == 0)
        c.len--;//去除最高位的0
    return c;
}
/*******************************************************
*
* FunctionName: rmZero
* Description: 在传入大数前方去除0
* Parameter:
* 	@a	要去除0的大数
**********************************************************/
void rmZero(BigNum &a) {
    while(a.len - 1 >= 1 && a.num[a.len - 1] == 0)
        a.len--;//去除最高位的0
}
/*******************************************************
*
* FunctionName: addAfterZero
* Description: 在传入大数后方添加0
* Parameter:
* 	@a	要添加0的大数
*   @l  要添加0的数量
* Return:
*   返回添加0后的BigNum
**********************************************************/
BigNum addAfterZero(BigNum a, int l) {
    int tmp[maxn];
    BigNum res;
    for(int i = 0; i < l; i++) {
        tmp[i] = 0;
    }
    memcpy(res.num, tmp, sizeof(tmp));
    memcpy(res.num + l, a.num, sizeof(a.num));
    res.len = l + a.len;
    return res;
}
/*******************************************************
*
* FunctionName: addBeforeZero
* Description: 在传入大数前方添加0
* Parameter:
* 	@a	要添加0的大数
*   @l  要添加0的数量
* Return:
*   返回添加0后的BigNum
**********************************************************/
BigNum addBeforeZero(BigNum a, int l) {
    int tmp[maxn];
    BigNum res;
    for(int i = 0; i < l; i++) {
        tmp[i] = 0;
    }
    memcpy(res.num, a.num, sizeof(a.num));
    memcpy(res.num + a.len, tmp, sizeof(tmp));
    res.len = l + a.len;
    return res;
}
/*******************************************************
*
* FunctionName: subBigNum
* Description: 大数截取
* Parameter:
* 	@a	要被截取的大数
*   @bg 开始截取的位置
*   @l  截取的长度
* Return:
*   返回截取后的BigNum
**********************************************************/
BigNum subBigNum(BigNum a, int bg, int l) {
    BigNum res;
    res.len = l;
    int tmp = l-1;
    for(int i = a.len - bg; i > a.len - bg - l; i--){
        //cout << "i:" << i << " a.num[i]:" << a.num[i] << endl;
        res.num[tmp--] = a.num[i];
    }
    return res;
}

/*******************************************************
*
* FunctionName: multi_2
* Description: 大数乘法（分治）
* Parameter:
* 	@a	乘数1
*   @b  乘数2
* Return:
*   返回相乘后的BigNum
**********************************************************/
BigNum multi_2(BigNum a, BigNum b){
    BigNum c;
    int n = 2;	// 计算当前大数长度
    if(a.len > 2 || b.len > 2) {
        n = 4;
        while(n < a.len || n < b.len)	// n一直乘二直到n大于两数中较长者的长度
            n <<= 1;
        // 在大数前方补零直至两者长度都为n
        a = addBeforeZero(a, n - a.len);
        b = addBeforeZero(b, n - b.len);
    }
    // 长度为一则证明当前n依旧为2 将两数长度补至二
    if(a.len == 1)  a = addBeforeZero(a, 1);
    if(b.len == 1)  b = addBeforeZero(b, 1);
    if(n == 2){	// 当前长度为2
        int inta = 0, intb = 0;
        for(int i = a.len - 1; i >= 0; i--){	// 用inta记录第一个数
            inta += a.num[i];
            if(i != 0){
                inta *= 10;
            }
        }
        for(int i = b.len - 1; i >= 0; i--){	// 用intb记录第二个数
            intb += b.num[i];
            if(i != 0){
                intb *= 10;
            }
        }
        int inttmp = inta * intb;	// 两数相乘
        string tmp;	// 将整数转为字符串 当然如果环境支持c11以上也可以直接使用std::itos
        stringstream sst;
        sst << inttmp;
        sst >> tmp;
        c = change(tmp);	// 将当前两数乘积转为大数c
        return c;	// 返回大数c
    }else{
        BigNum a0, a1, b0, b1, c0, c1, c2;
        a1 = subBigNum(a, 1, (n/2));// a1为大数a的后半部分（我上方大数类中为了方便运算逆序存储）
        a0 = subBigNum(a, (n/2+1), a.len - (n/2) );	// a0为大数a的前半部分
        // b0 b1同理
        b1 = subBigNum(b, 1, (n/2));
        b0 = subBigNum(b, (n/2+1), b.len - (n/2));
        c2 = multi_2(a1, b1);
        c0 = multi_2(a0, b0);
        c1 = sub(multi_2(add(a0,a1),add(b0,b1)),add(c2, c0));
        c2 = addAfterZero(c2, n);
        c1 = addAfterZero(c1, n / 2);
        c = add(add(c2, c1), c0);
    }
    return c;
}
int main()
{
    ios::sync_with_stdio(false);
    BigNum num1,num2,c,e;
    string temp;
    cin >> temp;
    num1 = change(temp);
    cin >> temp;
    num2 = change(temp);
    cout << "num1: ";
    for(int i = num1.len-1; i >= 0; i--) {
        cout << num1.num[i];
    }
    cout << endl;

    cout << "num2: ";
    for(int i = num2.len-1; i >= 0; i--) {
        cout << num2.num[i];
    }
    cout << endl;


    cout << "比较大小";
    if(compare(num1, num2) == 1)
        cout << ">" << endl;
    else if(compare(num1, num2) == -1)
        cout << "<" << endl;
    else
        cout << "=" << endl;
    cout << "乘积";
    BigNum num3 = multi_2(num1, num2);
    rmZero(num3);
    for(int i = num3.len-1; i >= 0; i--) {
        cout << num3.num[i];
    }
    cout << endl;
    cout << "加和" ;
    c = add(num1, num2);
    for(int i = c.len - 1; i >= 0; i--)
         cout << c.num[i];
    cout << endl;
    cout << "相减" ;
    c = sub(num1, num2);
    for(int i = c.len - 1; i >= 0; i--)
         cout << c.num[i];
    cout << endl;
    cout << "请输入int值计算与int的乘积（默认使用num1）";
    int mul;
    cin >> mul;
    c = multi(num1, mul);
    for(int i = c.len - 1; i >= 0; i--)
        cout << c.num[i];
    cout << endl;
    cout << "请输入int值计算与int的商与余数（默认使用num1）";
    cin >> mul;
    c = divide(num1, mul, r);
    cout << "商：";
     for(int i = c.len - 1; i >= 0; i--)
        cout << c.num[i];
    cout << " 余数" << r << endl;
    return 0;
}
  ```

