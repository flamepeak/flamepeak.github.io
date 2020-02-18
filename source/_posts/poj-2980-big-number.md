---
title: poj 2980 big number
date: 2019-02-27 08:54:32
tags: [C]
categories:
---

[2980](http://bailian.openjudge.cn/practice/2980/)

要注意：0的问题


```C++
#include <iostream>
#include <cstdio>
#include <cstring>

using namespace std;

char S1[202], S2[202];
int A[202], B[202];
int C[410];
bool zeroFlag=true;

int main()
{
    scanf("%s%s", S1, S2);
    int len1 = strlen(S1);
    int len2 = strlen(S2);
    for(int i=0;i<len1;i++)
    {
        A[i] = (S1[len1-1-i]-'0');
    }
    for(int i=0;i<len2;i++)
    {
        B[i] = (S2[len2-1-i]-'0');
    }
    for(int j=0;j<len2;j++)
    {
        for(int i=0;i<len1;i++)
        {
            C[i+j] += B[j]*A[i];
            if(C[i+j]!=0)
                zeroFlag = false;
        }
    }
    if(zeroFlag)
    {
        cout<<0;
        return 0;
    }
    for(int i=0;i<len1+len2;i++)
    {
        C[i+1] += C[i]/10;
        C[i] %= 10;
    }

    bool flag = true;
    for(int i=len1+len2;i>=0;i--)
    {
        if(C[i]==0)
        {
            if(flag)
            {
                continue;
            }
            else
                cout<<C[i];
        }
        else
        {
            if(flag)
            {
                flag = false;
                cout<<C[i];
            }
            else
                cout<<C[i];
        }

    }

    return 0;
}
```


