---
title: poj 1164 The Castle
date: 2019-02-26 16:08:20
tags: [C]
categories:
---

[1164:The Castle](http://bailian.openjudge.cn/practice/1164/)

1. 这是深度优先搜索的标准模板
2. 这里要注意关于门方向的判断，利用与运算，特别要注意·与或非·运算一定要加括号，因为优先级较低



```C++
#include <iostream>

using namespace std;

int R,C;
int cntRoom=0, maxRoom;

int A[202][202];
int visited[202][202];
int X[4]={0,0,1,-1};
int Y[4]={1,-1,0,0};

void DFS(int x1, int y1)
{
    maxRoom++;
    visited[x1][y1]=1;
    int x, y;

    if((A[x1][y1]&1)==0)
    {
        x = x1 + X[1];
        y = y1 + Y[1];
        if(x1>=1&&x1<=R&&y1>=1&&y<=C&&visited[x][y]==0)
            DFS(x, y);
    }
    if((A[x1][y1]&2)==0)
    {
        x = x1 + X[3];
        y = y1 + Y[3];
        if(x>=1&&x<=R&&y>=1&&y<=C&&visited[x][y]==0)
            DFS(x, y);
    }
    if((A[x1][y1]&4)==0)
    {
        x = x1 + X[0];
        y = y1 + Y[0];
        if(x>=1&&x<=R&&y>=1&&y<=C&&visited[x][y]==0)
            DFS(x, y);
    }
    if((A[x1][y1]&8)==0)
    {
        x = x1 + X[2];
        y = y1 + Y[2];
        if(x>=1&&x<=R&&y>=1&&y<=C&&visited[x][y]==0)
            DFS(x, y);
    }

}

int main()
{
    cin>>R>>C;
    int Max=0;
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
            cin>>A[i][j];
    }
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
        {
            maxRoom = 0;
            if(visited[i][j]==0)
            {
                DFS(i, j);
                cntRoom++;
            }
            Max = max(maxRoom, Max);
        }
    }
    cout<<cntRoom<<endl<<Max;


    return 0;
}

```