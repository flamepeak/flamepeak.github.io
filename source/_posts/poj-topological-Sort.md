---
title: poj 拓扑排序
date: 2019-03-12 21:54:28
tags: [C]
categories:
---


```
4084:拓扑排序
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
给出一个图的结构，输出其拓扑排序序列，要求在同等条件下，编号小的顶点在前。

输入
若干行整数，第一行有2个数，分别为顶点数v和弧数a，接下来有a行，每一行有2个数，分别是该条弧所关联的两个顶点编号。
v<=100, a<=500
输出
若干个空格隔开的顶点构成的序列(用小写字母)。
样例输入
6 8
1 2
1 3
1 4
3 2
3 5
4 5
6 4
6 5
样例输出
v1 v3 v2 v6 v4 v5

```


```C++



#include <bits/stdc++.h>

using namespace std;

vector <int> G[105];
int indegree[505];
int v,a;

priority_queue<int , vector<int>, greater<int> > Q;

int numV=0;

void topSort()
{
    for(int i=1;i<=v;i++)
    {
        if(indegree[i]==0)
            Q.push(i);
    }
    while(!Q.empty())
    {
        int top = Q.top();
        Q.pop();
        cout<<"v"<<top<<" ";
        for(int i=0;i<G[top].size();i++)
        {
            int tmpV = G[top][i];
            indegree[tmpV]--;
            if(indegree[tmpV]==0)
                Q.push(tmpV);
        }
    }
}


int main()
{
    cin>>v>>a;
    int v1, v2;
    for(int i=0;i<a;i++)
    {
        cin>>v1>>v2;
        G[v1].push_back(v2);
        indegree[v2]++;
    }
    topSort();


    return 0;
}

```