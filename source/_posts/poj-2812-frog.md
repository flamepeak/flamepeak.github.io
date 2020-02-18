---
title: poj 2812 恼人的青蛙
date: 2019-02-28 16:17:35
tags: [C]
categories:
---

```C++
/*
解题思路：因为要求出最长的路径，所以需要比较全部的路径长度。对于每一条路径，因为步长相等，所以只要确定开始两个被踩的点就可以求出整条路径。假设前两个点为（x1,y1）,(x2,y2),则步长为dx=x2-x1,dy=y2-y1,需要判断下面三个条件是否都满足。

（1）之后每个点（xi,yi）=（xi-1+dx,yi-1+dy）=(x2+(i-2)*dx,y2+(i-2)*dy),要依次判断各个点是否被踩过。

（2）（x1-dx,y1-dy）需要落在稻田之外。

（3）将路径上的最后一棵水稻记作（xk,yk）,则（xk+dx,yk+dy）需要落在稻田之外。

因此，程序只要分别枚举前两个点，然后判断整条路径是否存在并判断长度是否大于当前最大值即可。判断一个点是否被踩过时，可以用一个布尔矩阵。布尔矩阵中一开始就存好各个点的状态，true表示被踩过，false表示未被踩过，这样之后判断时可以用O（1）的效率确定一个点是否被踩过。

由于时限要求，实现时要注意一些条件的判断，以提高效率。例如，当下列条件之一满足时，当前枚举的两个点的路径就不满足最长路径。

（1）青蛙不能经过一跳从稻田外跳到（x1,y1）上（此时一定不满足条件）。

（2）按照（x1,y1）,(x2,y2)确定的步长，从（x1,y1）出发，青蛙经过（MAXSTEPS-1）步，就会跳到稻田之外，其中MAXSTEPS是当前已经找到的最好答案（此时即使满足条件，答案也不会更优）。
*/



#include<cstdio>
#include<iostream>
#include<algorithm>
using namespace std;

#define MAXN (5000+10)
struct Point{
	int x,y;
};
int r,c;
int n;
int ans=0;
bool flag[MAXN][MAXN];
Point p[MAXN];

bool operator<(const Point &a,const Point &b)  //重载运算符
{
	return(a.x<b.x||(a.x==b.x&&a.y<b.y));
}

bool Outside(int x1,int y1)     //判断点是否在农田之中
{
	if(x1<=0||y1<=0||x1>r||y1>c)return true;
	return false;
}

//判断以点a和b为起点的路径是否存在
bool count(int a,int b)
{
	int dx=p[b].x-p[a].x;
	int dy=p[b].y-p[a].y;
	//优化条件1：如果不能从稻田外跳到a点，则直接退出
	if(!Outside(p[a].x-dx,p[a].y-dy))return false;
	//优化条件2：如果青蛙经过MAXSTEPS-1步会跳出去，那么直接退出
	if(p[a].x+ans*dx<=0||p[a].x+ans*dx>r)return false;
	if(p[a].y+ans*dy<=0||p[a].y+ans*dy>c)return false;
	int k=2;
	int x1=p[b].x+dx;
	int y1=p[b].y+dy;
	while(!Outside(x1,y1)&&flag[x1][y1]) //按照步长将路径延伸
	{
		++k;
		x1+=dx;
		y1+=dy;
	}
	if(Outside(x1,y1)&&k>ans)ans=k;
}

int main()
{
	scanf("%d%d%d",&r,&c,&n);
	for(int i=0;i<n;i++)
	{
		scanf("%d%d",&p[i].x,&p[i].y);  //读入点
		flag[p[i].x][p[i].y]=true;
	}
	sort(p,p+n);   //将点排序，注意这里重载了Point的<运算符
	for(int i=0;i<n;i++)
		for(int j=i+1;j<n;j++)
			count(i,j);   //枚举每一种情况
	if(ans<3) ans=0;
	printf("%d\n",ans);
	return 0;
}
```