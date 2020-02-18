---
title: poj 2724 same birthdays
date: 2019-02-26 21:49:37
tags: [C]
categories:
---

```
2724:生日相同

总时间限制: 1000ms 内存限制: 65536kB
描述
在一个有180人的大班级中，存在两个人生日相同的概率非常大，现给出每个学生的学号，出生月日。试找出所有生日相同的学生。
输入
第一行为整数n，表示有n个学生，n<100。
此后每行包含一个字符串和两个整数，分别表示学生的学号（字符串长度小于10）和出生月(1<=m<=12)日(1<=d<=31)。
学号、月、日之间用一个空格分隔。
输出
对每组生日相同的学生，输出一行，
其中前两个数字表示月和日，后面跟着所有在当天出生的学生的学号，数字、学号之间都用一个空格分隔。
对所有的输出，要求按日期从前到后的顺序输出。
对生日相同的学号，按输入的顺序输出。
样例输入
5
00508192 3 2
00508153 4 5
00508172 3 2
00508023 4 5
00509122 4 5
样例输出
3 2 00508192 00508172
4 5 00508153 00508023 00509122
```

遇到题目要认真思考一下，如果没有很确定的把握，尽量从简单的方法入手。

本题，如果开始用数据结构，就会变得很复杂，其实可以用多个对应的数组简单遍历解决。

另外，本题题意是要求输出具有相同生日的同学，不输出生日不一样的同学。全部输出，会很简单。


```C++

#include <iostream>
#include <algorithm>

using namespace std;

int main()
{
    char id[110][15];
    int m[110], d[110], date[15][40]={0};
    int n;
    cin>>n;
    for(int i=0;i<n;i++)
    {
        cin>>id[i]>>m[i]>>d[i];
        date[m[i]][d[i]]++;
    }
    for(int i=1;i<=12;i++)
    {
        for(int j=1;j<=31;j++)
        {
            if(date[i][j]>=2)
            {
                cout<<i<<" "<<j;
                for(int k=0;k<n;k++)
                {
                    if(m[k]==i&&d[k]==j)
                        cout<<" "<<id[k];
                }
                cout<<endl;
            }
        }
    }

    return 0;
}

```