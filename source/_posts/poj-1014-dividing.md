---
title: poj 1014 Dividing
date: 2019-02-27 10:32:52
tags: [C]
categories:
---

[1014](http://bailian.openjudge.cn/practice/1014/)


```
1014:Dividing
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
Marsha and Bill own a collection of marbles. They want to split the collection among themselves so that both receive an equal share of the marbles. This would be easy if all the marbles had the same value, because then they could just split the collection in half. But unfortunately, some of the marbles are larger, or more beautiful than others. So, Marsha and Bill start by assigning a value, a natural number between one and six, to each marble. Now they want to divide the marbles so that each of them gets the same total value. Unfortunately, they realize that it might be impossible to divide the marbles in this way (even if the total value of all marbles is even). For example, if there are one marble of value 1, one of value 3 and two of value 4, then they cannot be split into sets of equal value. So, they ask you to write a program that checks whether there is a fair partition of the marbles.
输入
Each line in the input file describes one collection of marbles to be divided. The lines contain six non-negative integers n1 , . . . , n6 , where ni is the number of marbles of value i. So, the example from above would be described by the input-line "1 0 1 2 0 0". The maximum total number of marbles will be 20000. 
The last line of the input file will be "0 0 0 0 0 0"; do not process this line.
输出
For each collection, output "Collection #k:", where k is the number of the test case, and then either "Can be divided." or "Can't be divided.". 
Output a blank line after each test case.
样例输入
1 0 1 2 0 0 
1 0 0 0 1 1 
0 0 0 0 0 0 
样例输出
Collection #1:
Can't be divided.

Collection #2:
Can be divided.
```



```C++
#include <iostream>

using namespace std;

int n[9];
int sum, index=0;
bool flag = false;

void dfs(int val, int now)
{
    if(flag)
        return;
    if(val==sum/2)
    {
        flag = true;
        return;
    }
    for(int i=now;i>=1;i--)
    {
        if(n[i])
        {
            if(val+i<=sum/2)
            {
                n[i]--;
                dfs(val+i,i);
                if(flag)
                    return;
            }
        }
    }
}

int main()
{
    while(true)
    {
        sum = 0;
        for(int i=1;i<=6;i++)
        {
            cin>>n[i];
            sum += n[i]*i;
        }
        if(sum==0)
            return 0;
        index++;
        if(sum%2==1)
        {
            cout<<"Collection #"<<index<<":"<<endl;
            cout<<"Can't be divided."<<endl<<endl;
            continue;
        }
        flag = false;
        dfs(0,6);
        if(flag)
        {
            cout<<"Collection #"<<index<<":"<<endl;
            cout<<"Can be divided."<<endl<<endl;
        }
        else
        {
            cout<<"Collection #"<<index<<":"<<endl;
            cout<<"Can't be divided."<<endl<<endl;
        }

    }

    return 0;
}


```