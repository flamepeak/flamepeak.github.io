---
title: poj 并查集汇总
date: 2019-03-05 10:56:10
tags: [C]
categories:
---

[基础介绍：](https://blog.csdn.net/fkjslee/article/details/48809903)

---

[2492:A Bug's Life](http://bailian.openjudge.cn/practice/2492/)

```
Background
Professor Hopper is researching the sexual behavior of a rare species of bugs. He assumes that they feature two different genders and that they only interact with bugs of the opposite gender. In his experiment, individual bugs and their interactions were easy to identify, because numbers were printed on their backs.
Problem
Given a list of bug interactions, decide whether the experiment supports his assumption of two genders with no homosexual bugs or if it contains some bug interactions that falsify it.
输入
The first line of the input contains the number of scenarios. Each scenario starts with one line giving the number of bugs (at least one, and up to 2000) and the number of interactions (up to 1000000) separated by a single space. In the following lines, each interaction is given in the form of two distinct bug numbers separated by a single space. Bugs are numbered consecutively starting from one.
输出
The output for every scenario is a line containing "Scenario #i:", where i is the number of the scenario starting at 1, followed by one line saying either "No suspicious bugs found!" if the experiment is consistent with his assumption about the bugs' sexual behavior, or "Suspicious bugs found!" if Professor Hopper's assumption is definitely wrong.

样例输入
2
3 3
1 2
2 3
1 3
4 2
1 2
3 4
样例输出
Scenario #1:
Suspicious bugs found!

Scenario #2:
No suspicious bugs found!

```

```C++
#include <iostream>
#include <cstring>
#include <cstdio>

using namespace std;

const int maxn = 5000;

int par[maxn];

int find(int x)
{
	if (x != par[x])
		par[x] = find(par[x]);
	return par[x];
}

void merge(int a, int b)
{
	int x = find(a);
	int y = find(b);
	if (x != y)
		par[x] = y;
}

bool judge(int x, int y) 
{
	x = find(x);
	y = find(y);
	if (x != y)
		return true;
	return false;
}


int main(void)
{
	int t, n, m, cas = 1;
	scanf("%d", &t);
	for (int i = 1; i <= t; i++)
	{
		scanf("%d%d", &n, &m);
		for (int i = 0; i <= 2 * n; i++)
			par[i] = i;
		bool l = true;
		int x, y;
		while (m--)
		{
			scanf("%d%d", &x, &y);
			if (judge(x, y) || judge(x + n, y + n))
			{
				merge(x, y + n);
				merge(x + n, y);
			}
			else
				l = false;
		}
		if (i != 1)
			puts(""); 
		printf("Scenario #%d:\n", cas++);
		if (l)
			printf("No suspicious bugs found!\n");
		else
			printf("Suspicious bugs found!\n");

	}
	return 0;
}
```




[1703:发现它，抓住它](http://bailian.openjudge.cn/practice/1703/)

```
一个城市中有两个犯罪团伙A和B，你需要帮助警察判断任意两起案件是否是同一个犯罪团伙所为，警察所获得的信息是有限的。假设现在有N起案件（N<=100000），编号为1到N，每起案件由团伙A或团伙B所为。你将按时间顺序获得M条信息（M<=100000），这些信息分为两类：
1. D [a] [b]
其中[a]和[b]表示两起案件的编号，这条信息表明它们属于不同的团伙所为

2. A [a] [b]
其中[a]和[b]表示两起案件的编号，这条信息需要你回答[a]和[b]是否是同一个团伙所为
注意你获得信息的时间是有先后顺序的，在回答的时候只能根据已经接收到的信息做出判断。


输入
第一行是测试数据的数量T（1<=T<=20）。
每组测试数据的第一行包括两个数N和M，分别表示案件的数量和信息的数量，其后M行表示按时间顺序收到的M条信息。
输出
对于每条需要回答的信息，你需要输出一行答案。如果是同一个团伙所为，回答"In the same gang."，如果不是，回答"In different gangs."，如果不确定，回答”Not sure yet."。
```


```C++
#include <iostream>
#include <cstdio>
using namespace std;

char cmd;
int T, N, M, num1, num2, fx, fy;
int father[100100], relation[100100]; //father[i] 表示 i 的父节点是谁; relation[i] 表示 i 这个节点和他父节点的关系，0表示同一个犯罪组织，1表示不同。

int Find_father(int x)
{
	if (x == father[x])
		return x;
	else//路径压缩
	{
		int F = Find_father(father[x]);//递归找到根节点
		relation[x] = (relation[father[x]] + relation[x]) % 2;//修改关系
		father[x] = F;
		return F;
	}
}

void Union(int x, int y)//x和y是两个不同的犯罪团伙
{
	fx = Find_father(x);
	fy = Find_father(y);
	father[fx] = fy;//将x那一支归并到y的祖先节点下
	if (relation[y] == 0)//y和fy相同
		relation[fx] = 1 - relation[x];
	else
		relation[fx] = relation[x];
}

int main()
{
	scanf("%d", &T);
	while (T--)
	{
		scanf("%d%d", &N, &M);//N个案件，M条信息
		for (int i = 1; i <= N; i++)
		{
			father[i] = i;//将父节点初始化为本身
			relation[i] = 0;
		}
		while (M--)
		{
			getchar();//debug时发现如果不加这个读回车，cmd就变成了回车。

			scanf("%c%d%d", &cmd, &num1, &num2);

			if (cmd == 'D')
				Union(num1, num2);
			else if (cmd == 'A')
			{
				fx = Find_father(num1);
				fy = Find_father(num2);

				if (fx != fy)//不在并查集中
					printf("Not sure yet.\n");
				else if (relation[num1] == relation[num2])//与祖先节点关系相同
					printf("In the same gang.\n");
				else
					printf("In different gangs.\n");
			}
		}
	}
	return 0;
}

```


[1988 Cube Stacking](http://poj.org/problem?id=1988)

```
Description

Farmer John and Betsy are playing a game with N (1 <= N <= 30,000)identical cubes labeled 1 through N. They start with N stacks, each containing a single cube. Farmer John asks Betsy to perform P (1<= P <= 100,000) operation. There are two types of operations: 
moves and counts. 
* In a move operation, Farmer John asks Bessie to move the stack containing cube X on top of the stack containing cube Y. 
* In a count operation, Farmer John asks Bessie to count the number of cubes on the stack with cube X that are under the cube X and report that value. 

Write a program that can verify the results of the game. 
Input

* Line 1: A single integer, P 

* Lines 2..P+1: Each of these lines describes a legal operation. Line 2 describes the first operation, etc. Each line begins with a 'M' for a move operation or a 'C' for a count operation. For move operations, the line also contains two integers: X and Y.For count operations, the line also contains a single integer: X. 

Note that the value for N does not appear in the input file. No move operation will request a move a stack onto itself. 
Output

Print the output from each of the count operations in the same order as the input file. 
Sample Input

6
M 1 6
C 1
M 2 4
M 2 6
C 3
C 4
Sample Output

1
0
2
```




```C++
#include<stdio.h>
#include<iostream>
#include<string.h>
using namespace std;
#define MAXN 30010

int f[MAXN], top[MAXN], ans[MAXN];
//f 数组表示该元素所在堆栈的最上方元素（根）
//top 数组表示该元素上方有多少元素
//ans 数组表示该堆栈最上方元素所在堆栈的元素总数

int getf( int v )
{
    if( v != f[v] )//若该元素不是堆栈最上方元素（根），需更新数组
    {
        int t = f[v];//该元素当前的根
        f[v] = getf(f[v]);//更新该元素所在堆栈的最上方元素（根）
        top[v] += top[t];//该元素上方元素个数为本身个数加上根元素上方个数
    }
    return f[v];//返回最上方（根）元素
}

void lian( int t1, int t2 )
{
    int v1 = getf(t1);//需要移动的那个堆栈的最上方（根）元素
    int v2 = getf(t2);//加入该堆栈之前，该堆栈的最上方（根）元素
    if( v1 != v2 )//两个不是同一堆栈（其实肯定不是同一堆栈）
    {
        f[v2] = v1;//新堆栈的最上方（根）元素
        top[v2] = ans[v1];//之前堆栈根元素上方就有了加入的堆栈的所有元素
        ans[v1] += ans[v2];//新堆栈的元素总数就是两个堆栈元素数之和
    }
}

int main()
{
    int n;
    while( ~scanf("%d",&n) )
    {
        for( int i = 1; i < MAXN; i++ )
        {
            f[i] = i;//初始化所有根节点都是自己
            ans[i] = 1;//初始化所有元素所在堆栈的元素个数是1（自己一个）
            top[i] = 0;//初始化所有元素上方无元素
        }
        char cz;
        int t1, t2;
        for( int i = 0; i < n; i++ )
        {
            cin>>cz;
            if( cz == 'M' )
            {
                scanf("%d%d",&t1,&t2);
                lian(t1,t2);
            }
            else
            {
                scanf("%d",&t1);
                printf("%d\n",ans[getf(t1)]-top[t1]-1);
                //t1下方元素个数就是t1所在堆栈的总元素个数-t1上方元素个数-本身
            }
        }
    }
    return 0;
}

```




[畅通工程](http://acm.hdu.edu.cn/showproblem.php?pid=1232)

```
Problem Description
某省调查城镇交通状况，得到现有城镇道路统计表，表中列出了每条道路直接连通的城镇。省政府“畅通工程”的目标是使全省任何两个城镇间都可以实现交通（但不一定有直接的道路相连，只要互相间接通过道路可达即可）。问最少还需要建设多少条道路？ 
 

Input
测试输入包含若干测试用例。每个测试用例的第1行给出两个正整数，分别是城镇数目N ( < 1000 )和道路数目M；随后的M行对应M条道路，每行给出一对正整数，分别是该条道路直接连通的两个城镇的编号。为简单起见，城镇从1到N编号。 
注意:两个城市之间可以有多条道路相通,也就是说
3 3
1 2
1 2
2 1
这种输入也是合法的
当N为0时，输入结束，该用例不被处理。 
 

Output
对每个测试用例，在1行里输出最少还需要建设的道路数目。 
 

Sample Input
4 2
1 3
4 3
3 3
1 2
1 3
2 3
5 2
1 2
3 5
999 0
0
 

Sample Output
1
0
2
998
```

[参考1：HDU1232畅通工程(经典并查集)](https://blog.csdn.net/qq_41658955/article/details/81392960)

[参考2：ACM-并查集之畅通工程——hdu1232](https://blog.csdn.net/lttree/article/details/23820679)