---
title: 编程-模拟-经典例子-花生采摘 
date: 2019-02-16 21:43:21
tags: [C]
categories:
---

原题：[花生采摘](http://noi.openjudge.cn/ch0113/38)

感谢：https://blog.csdn.net/suntengnb/article/details/74500984

“模拟”类型的题目，读懂过程，建立数据结构，然后模拟过程即可。

以下代码注释，很好的解释了过程


```C++
int a[25][25];//数组a记录地图
struct node
{
    int num;//花生个数
    int x;//横坐标
    int y;//纵坐标
};
node b[500];
int comp(node a,node b)//降序
{
    return a.num>b.num;
}
int main()
{
    int m,n,i,j,k,c=1,sum=0,mark;//sum记录采摘到的花生的个数，mark记录上一次累加的花生的个数
    scanf("%d%d%d",&m,&n,&k);//输入
    for(i=1;i<=m;i++)
        for(j=1;j<=n;j++)
        {
            scanf("%d",&a[i][j]);
            if(a[i][j]!=0)//如果花生个数不为0
            {
                b[c].num=a[i][j];//记入结构体数组中
                b[c].x=i;
                b[c].y=j;
                c++;
            }
        }
    sort(b+1,b+c,comp);//排序（降序）
    for(i=1;i<=c-1;i++)//从大到小，模拟花生采摘过程
    {
        if(i==1)//首先，要到达第一个花生所在处
            k-=b[1].x;//减去所花费的时间
        k--;//采摘-1s
        sum+=b[i].num;//累加采摘到的花生的个数
        mark=b[i].num;//记录
        if(k<(abs(b[i+1].x-b[i].x)+abs(b[i+1].y-b[i].y)+1+b[i+1].x))//如果剩下的时间不够移动到下一个花生所在处+采摘+返回到路边......①
            break;//跳出循环
        else//否则
            k-=abs(b[i+1].x-b[i].x)+abs(b[i+1].y-b[i].y);//减去移动到下一个花生所在处所花费的时间
    }
    if(k<b[i].x)//如果时间不仅不够做①，还不够返回路边
        printf("%d\n",sum-mark);//那就不摘这个花生
    else
        printf("%d\n",sum);
    return 0;
}

```


```C
#include <iostream>
#include <cmath>

using namespace std;

int F[55][55];

int main()
{
    int T, M, N, K;
    cin>>T;
    while(T--)
    {
        cin>>M>>N>>K;
        for(int i=1;i<=M;i++)
        {
            for(int j=1;j<=N;j++)
            {
                cin>>F[i][j];
            }
        }
        int totalTime = 0, totalHS=0, maxHS=0, xCur=0,yCur, xMax, yMax;
        while(totalTime < K)
        {
            maxHS=0;
            for(int i=1;i<=M;i++)//寻找下一个最大花生的位置
            {
                for(int j=1;j<=N;j++)
                {
                    if(F[i][j]>maxHS)
                    {
                        maxHS = F[i][j];
                        xMax = i;
                        yMax = j;
                    }
                }
            }
            if(maxHS==0)//没有花生，退出
                break;
            if(xCur==0)//如果在路上，那就跳到横坐标yMax出
                yCur=yMax;
            //查看剩余时间是否够跳到下一株，采摘，并返回路上
            if(totalTime + abs(xMax-xCur)+abs(yMax-yCur)+ 1 + xMax<=K)
            {
                totalTime += abs(xMax-xCur)+abs(yMax-yCur)+ 1;
                totalHS += maxHS;
                xCur = xMax;
                yCur = yMax;
                F[xMax][yMax]=0;
            }
            else
                break;
        }
        cout<<totalHS<<endl;
    }


    return 0;
}



```