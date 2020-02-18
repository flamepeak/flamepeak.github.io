---
title: poj search
date: 2019-03-10 22:20:05
tags: [C]
categories:
---


---

```
4081:树的转换
查看 提交 统计 提示 提问
总时间限制: 5000ms 单个测试点时间限制: 1000ms 内存限制: 65536kB
描述
我们都知道用“左儿子右兄弟”的方法可以将一棵一般的树转换为二叉树，如：

    0                             0
  / | \                          /
 1  2  3       ===>             1
   / \                           \
  4   5                           2
                                 / \
                                4   3
                                 \
                                  5
现在请你将一些一般的树用这种方法转换为二叉树，并输出转换前和转换后树的高度。

输入
输入是一个由“u”和“d”组成的字符串，表示一棵树的深度优先搜索信息。比如，dudduduudu可以用来表示上文中的左树，因为搜索过程为：0 Down to 1 Up to 0 Down to 2 Down to 4 Up to 2 Down to 5 Up to 2 Up to 0 Down to 3 Up to 0。
你可以认为每棵树的结点数至少为2，并且不超过10000。
输出
按如下格式输出转换前和转换后树的高度：
h1 => h2
其中，h1是转换前树的高度，h2是转换后树的高度。
样例输入
dudduduudu
样例输出
2 => 4
```


```C++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
#include <functional>
#include <cctype>
#include <cmath>
using namespace std;
 
const int N = 100100;
char s[N];
int res1, res2;
void dfs(int &i, int dep1, int dep2)
{
    res1 = max(res1, dep1);
    res2 = max(res2, dep2);
    int cnt  = 1;
    while(true && s[i])
    {
        if(s[i] == 'd')
            dfs(++i, dep1 + 1, dep2 + cnt), cnt++;
        else
        {
            i++;
            return;
        }
    }
}
int main()
{
    while(~ scanf(" %s", s))
    {
        int i = 0;
        res1 = res2 = -1;
        dfs(i, 0, 0);
        printf("%d => %d\n", res1, res2);
    }
    return 0;
}
```

```C++
#include <iostream>
#include<string.h> 
#include<stdio.h>
using namespace std; 
const int M=41000;//用的比较多 
//http://bailian.openjudge.cn/practice/4081/
//神奇的题，想了很久递归去构造树，结果做不出来，下面是参考的 
//使用dp来做，当前节点在二叉树表示中的高度=父节点高度+左兄弟数目+1
int h1=0,h2=0,len;
int f[M],dep[M],lch[M];//f数组是保存的上次的值，相当于对一个个字符dp 
char a[M];
int main(){
    cin>>a;
    len=strlen(a);
    int h=0;//用于循环过程中控制高度 
    for(int i=1;i<=len;i++){
        //i其实存储的是i-1节点的信息
        char tmp=a[i-1];
        if(tmp=='d'){
            h++;
            h1=max(h1,h);
            f[i]=i-1;
            dep[i]=dep[i-1]+lch[i-1]+1;
            lch[i-1]++;//对于i来讲，有一个左兄弟 
            h2=max(h2,dep[i]); 
        } 
        else if(tmp=='u'){
            h--;
            f[i]=f[f[i-1]];
            dep[i]=dep[f[i-1]];
            lch[i]=lch[f[i-1]];
        }
    }
    printf ("%d => %d\n",h1, h2);
} 

```



---

```
7834:分成互质组
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
给定n个正整数，将它们分组，使得每组中任意两个数互质。至少要分成多少个组？

输入
第一行是一个正整数n。1 <= n <= 10。
第二行是n个不大于10000的正整数。
输出
一个正整数，即最少需要的组数。
样例输入
6
14 20 33 117 143 175
样例输出
3
```

搜索，如果能加入已有组就加入，不能加入新建组

```C++
#include<iostream>  
using namespace std;  
int n,ans=1e9+1;  
bool flag;  
int a[10010],b[10010];//a[]保存数
int gcd(int a,int b)//递归法判断互质  
{  
    if(b==0)return a;  
    return gcd(b,a%b);  
}  
void dfs(int x,int y)//搜到了第x个数，现在有y个集合
{  
    if(x==n+1)//搜索完成，取最小值，退出
    {  
        ans=min(ans,y); 
        return;  
    }  
    for(int i=1;i<=y;i++)//组数
    {  
        flag=true;  
        for(int j=1;j<x;j++)//不能加‘=’，因为如果加了‘=’，一个数就可以进这重循环，即x=1时，不可能自己比自己吧。
        {  
            if(b[j]==i)  
            {  
                if(gcd(a[x],a[j])!=1)//如果不为互质数
                {  
                    flag=false;//就没必要比，再继续加下去了，
                    break;//跳出
                }  
            }  
        }  
        if(flag)//如果前边加的数字都为互质数，就再加数，继续判断
        {  
            b[x]=i;//保存组数
            dfs(x+1,y);//加数字，但不加组 
            b[x]=0;//回溯
        }  
    }  
    b[x]=y+1;//集合数加1  
    dfs(x+1,y+1);//进行下一次查找
    b[x]=0; //回溯 
}  
int main()  
{  
    cin>>n;  
    for(int i=1;i<=n;i++)  
        cin>>a[i];  
    dfs(1,1);//从第一个（数，组）开始  
    cout<<ans<<endl;  
    return 0;  
}  

```


```C++
#include<cstdio>  
#include<cstring>  
int n,a[20],b[20],c=1;  
int fun(int x,int y) //递归法判断互质  
{  
    if(!y) return x; //y==0
    return fun(y,x%y); 
}  
int main()  
{  
    memset(b,1,sizeof(b)); //以便于"b[j]*=a[i];"  
    scanf("%d",&n);  
    for(int i=1;i<=n;i++)  
        scanf("%d",&a[i]);  
    b[1]=a[1];  
    for(int i=2;i<=n;i++)  
    {  
        int j;  
        for(j=1;j<=c;j++)  
            if(fun(a[i],b[j])==1)  //也就是说两个整数的最大公约数为1，即两个正整数互质 
            {  
                b[j]*=a[i];  
                break;  
            }  
        if(j-1==c) //意思就是上面的"break"一次都没有执行  
            b[++c]=a[i];  
    }  
    printf("%d",c);  
}  

```

---

```
8783:单词接龙
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
单词接龙是一个与我们经常玩的成语接龙相类似的游戏，现在我们已知一组单词，且给定一个开头的字母，要求出以这个字母开头的最长的“龙”（每个单词都最多在“龙”中出现两次），在两个单词相连时，其重合部分合为一部分，例如beast和astonish，如果接成一条龙则变为beastonish，另外相邻的两部分不能存在包含关系，例如at和atide间不能相连。

输入
输入的第一行为一个单独的整数n(n<=20)表示单词数，以下n行每行有一个单词（只含有大写或小写字母，长度不超过20），输入的最后一行为一个单个字符，表示“龙”开头的字母。你可以假定以此字母开头的“龙”一定存在。
输出
只需输出以此字母开头的最长的“龙”的长度。
样例输入
5
at
touch
cheat
choose
tact
a
样例输出
23
```


[参考](https://blog.csdn.net/c20190102/article/details/52311322)

```
此题乍一看，似乎并不是很难，但需要处理以下一些情况：
1.单词不能包含。
2.每个单词可以用2次。
3.重合部分长度需减去。
4.重合长度是不确定的。

现在谈谈大体思路：

首先此题一定用深搜。

其次存单词时，从f[1]开始存，而最后一个表示开头的字母存f[0]。
why?我们接龙时就直接从f[0]开始便不需要处理开头的单词。

接下来处理问题：
1.关于包含，只需查到长度len-1即可，如果还不符合便不符合。
2.我们可以定义一个v数组，判断单词使用次数，在使用前判断if(v[i]<2)每使用一次变v[i]++，注意递归完后v[i]--。

然后先看4.在判断单词是否可以连接时，我们从i=0,循环到i=n，即所有单词查完，如果单词可用便接着循环，查待连接单词，如果某一位与可连接单词相同，便再循环，定义一个临时变量t，从后一位开始，查，如果不同，直接t=0,break,出循环后if(t)便代表可连接，开始递归下一个，别忘了v[i]++。（此部分便是核心算法）

关于3.在判断可连接时，总长度L便加上两个单词长度，然后前面的最后盘点是否完全符合的循环计数变量定义在外面，L直接减去它便是接龙长度了。

```


```C++
#include<cstdio>
#include<cstring>
struct le//定义结构体可以更方便
{
	char s[22];//单词
	int len;//单词长度
	int v;//单词访问次数
}c[22];//单词数量不超过20个，所以c[22]即可
int n;
int maxn;
void jl(int x,int len)//两个参数分别代表待连接单词的结构体下标和长度
{
	for(int i=1;i<=n;i++)
		if(c[i].v<2)//判断访问次数
			for(int j=0;j<c[x].len;j++)//查待连接单词的每一个字母
				if(c[x].s[j]==c[i].s[0])
				{
					int k=1;//将循环变量定义在外面，方便以后相减
					int t=1;//临时变量（其实bool就行）
					for(int l=j+1;l<c[x].len&&k<c[i].len;k++,l++)//l表示待连接单词下标，k是可连接单词下标，所以在循环条件中需满足它们小于（因为不能包含，所以不能等于）单词长度
						if(c[x].s[l]!=c[i].s[k])
						{
							t=0;
							break;
						}
					if(t)
					{
						c[i].v++;
						jl(i,len+c[i].len-k);//更新长度和下标后递归
						c[i].v--;//完成后一定记得--
					}
				}
	if(len>maxn)
		maxn=len;//不解释
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%s",c[i].s);
		c[i].len=strlen(c[i].s);
	}
	scanf("%s",c[0].s);
	c[0].len=strlen(c[0].s);//直接将开头字母存在0号结构体
	jl(0,c[0].len);//开头字母当第一个单词处理
	printf("%d",maxn);
}
```


---

```
8465:马走日
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 1024kB
描述
马在中国象棋以日字形规则移动。

请编写一段程序，给定n*m大小的棋盘，以及马的初始位置(x，y)，要求不能重复经过棋盘上的同一个点，计算马可以有多少途径遍历棋盘上的所有点。

输入
第一行为整数T(T < 10)，表示测试数据组数。
每一组测试数据包含一行，为四个整数，分别为棋盘的大小以及初始位置坐标n,m,x,y。(0<=x<=n-1,0<=y<=m-1, m < 10, n < 10)
输出
每组测试数据包含一行，为一个整数，表示马能遍历棋盘的途径总数，0为无法遍历一次。
样例输入
1
5 4 0 0
样例输出
32
```


```C++
#include <iostream>

using namespace std;

int n, m, cnt;
int vis[15][15];
int X[10]={1,2,2,1,-1,-2,-2,-1};
int Y[10]={-2,-1,1,2,2,1,-1,-2};

void dfs(int x, int y, int visitNum)
{
    if(visitNum==0)
    {
        cnt++;//深搜成功次数加1
        return;
    }
    for(int i=0;i<8;i++)
    {
        int newX=x+X[i];
        int newY=y+Y[i];
        if(newX>=0&&newX<m&&newY>=0&&newY<n&&vis[newX][newY]==0)
        {
            vis[x][y]=1;
            dfs(newX,newY,visitNum-1);//一次深搜
            vis[x][y]=0;
        }
    }
    return;//一定需要，表示无法遍历一次的返回
}

int main()
{
    int x,y;
    int T;
    cin>>T;
    while(T--)
    {
        cin>>m>>n>>x>>y;
        cnt = 0;
        dfs(x, y, n*m-1);
        cout<<cnt<<endl;

    }


    return 0;
}


```



---

```
666:放苹果
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
把M个同样的苹果放在N个同样的盘子里，允许有的盘子空着不放，问共有多少种不同的分法？（用K表示）5，1，1和1，5，1 是同一种分法。
输入
第一行是测试数据的数目t（0 <= t <= 20）。以下每行均包含二个整数M和N，以空格分开。1<=M，N<=10。
输出
对输入的每组数据M和N，用一行输出相应的K。
样例输入
1
7 3
样例输出
8
```


```C++
#include <iostream>

using namespace std;

int apple(int m, int n)
{
    if(n==1||m==0)//m必须为0
        return 1;
    if(n>m)
        return apple(m, m);
    return apple(m, n-1)+apple(m-n, n);//至少有一个为空+全部不空
}

int main()
{
    int t;
    cin>>t;
    while(t--)
    {
        int M, N;
        cin>>M>>N;
        cout<<apple(M, N)<<endl;
    }
    return 0;
}

```


---


```
6264:走出迷宫
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
当你站在一个迷宫里的时候，往往会被错综复杂的道路弄得失去方向感，如果你能得到迷宫地图，事情就会变得非常简单。 
假设你已经得到了一个n*m的迷宫的图纸，请你找出从起点到出口的最短路。

输入
第一行是两个整数n和m(1<=n,m<=100)，表示迷宫的行数和列数。
接下来n行，每行一个长为m的字符串，表示整个迷宫的布局。字符'.'表示空地，'#'表示墙，'S'表示起点,'T'表示出口。
输出
输出从起点到出口最少需要走的步数。
样例输入
3 3
S#T
.#.
...
样例输出
6
```


```C++
#include <bits/stdc++.h>

using namespace std;

char A[105][105];
int vis[105][105], n, m;
int X[4]={0,0,1,-1};
int Y[4]={1,-1,0,0};

struct Node
{
    int x;
    int y;
    int step;
    Node()
    {
        step=0;
    }
};

queue <Node> Q;

void BFS(int stx, int sty, int enx, int eny)
{
    Node stNode;
    stNode.x=stx;
    stNode.y=sty;
    Q.push(stNode);
    while(!Q.empty())
    {
        Node top, newNode;
        top = Q.front();
        Q.pop();
        for(int i=0;i<4;i++)
        {
            newNode.x = top.x + X[i];
            newNode.y = top.y + Y[i];
            newNode.step = top.step+1;
            if(newNode.x==enx&&newNode.y==eny)
            {
                cout<<newNode.step;
                return;
            }
            if(A[newNode.x][newNode.y]=='.'&&vis[newNode.x][newNode.y]==0&&newNode.x>=1&&newNode.x<=n&&newNode.y>=1&&newNode.y<=m)
            {
                Q.push(newNode);
                vis[newNode.x][newNode.y]=1;
            }
        }
    }
    return;
}

int main()
{
    int  stx,sty,enx,eny;
    cin>>n>>m;
    for(int i=1;i<=n;i++)
    {
        getchar();
        for(int j=1;j<=m;j++)
        {
            cin>>A[i][j];
            if(A[i][j]=='S')
            {
                stx=i;
                sty=j;
            }
            if(A[i][j]=='T')
            {
                enx=i;
                eny=j;
            }
        }
    }
    BFS(stx,sty,enx,eny);


    return 0;
}

```



---


```
2753:走迷宫
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
一个迷宫由R行C列格子组成，有的格子里有障碍物，不能走；有的格子是空地，可以走。
给定一个迷宫，求从左上角走到右下角最少需要走多少步(数据保证一定能走到)。只能在水平方向或垂直方向走，不能斜着走。
输入
第一行是两个整数，Ｒ和Ｃ，代表迷宫的长和宽。（ 1<= R，C <= 40)
接下来是Ｒ行，每行Ｃ个字符，代表整个迷宫。
空地格子用'.'表示，有障碍物的格子用'#'表示。
迷宫左上角和右下角都是'.'。
输出
输出从左上角走到右下角至少要经过多少步（即至少要经过多少个空地格子）。计算步数要包括起点和终点。
样例输入
5 5
..###
#....
#.#.#
#.#.#
#.#..
样例输出
9
```

```C++
#include <bits/stdc++.h>

using namespace std;

int R, C;
char A[45][45];
int X[4]={0,0,1,-1};
int Y[4]={1, -1, 0, 0};

struct Node
{
    int x;
    int y;
    int step;
    Node()
    {
        step=0;
    }
};

bool vis[45][45]={false};

queue <Node> Q;

void BFS()
{
    Node st;
    st.x=1;
    st.y=1;
    Q.push(st);
    while(!Q.empty())
    {
        Node top;
        top = Q.front();
        Q.pop();
        for(int i=0;i<4;i++)
        {
            Node tmp;
            tmp.x=top.x+X[i];
            tmp.y=top.y+Y[i];
            tmp.step=top.step+1;
            if(tmp.x==R&&tmp.y==C)
            {
                cout<<tmp.step+1;
                return;
            }
            if(A[tmp.x][tmp.y]=='.'&&!vis[tmp.x][tmp.y]&&tmp.x>=1&&tmp.x<=R&&tmp.y>=1&&tmp.y<=C)
            {
                Q.push(tmp);
                vis[tmp.x][tmp.y]=true;
            }
        }
    }
    return;

}



int main()
{
    cin>>R>>C;
    for(int i=1;i<=R;i++)
    {
        getchar();
        for(int j=1;j<=C;j++)
        {
            cin>>A[i][j];
        }
    }
    BFS();

    return 0;
}

```


---

```
1818:红与黑
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
有一间长方形的房子，地上铺了红色、黑色两种颜色的正方形瓷砖。你站在其中一块黑色的瓷砖上，只能向相邻的黑色瓷砖移动。请写一个程序，计算你总共能够到达多少块黑色的瓷砖。
输入
包括多个数据集合。每个数据集合的第一行是两个整数W和H，分别表示x方向和y方向瓷砖的数量。W和H都不超过20。在接下来的H行中，每行包括W个字符。每个字符表示一块瓷砖的颜色，规则如下
1）‘.’：黑色的瓷砖；
2）‘#’：白色的瓷砖；
3）‘@’：黑色的瓷砖，并且你站在这块瓷砖上。该字符在每个数据集合中唯一出现一次。
当在一行中读入的是两个零时，表示输入结束。
输出
对每个数据集合，分别输出一行，显示你从初始位置出发能到达的瓷砖数(记数时包括初始位置的瓷砖)。
样例输入
6 9 
....#. 
.....# 
...... 
...... 
...... 
...... 
...... 
#@...# 
.#..#. 
0 0
样例输出
45
```

BFS:

```C++
#include <bits/stdc++.h>

using namespace std;

char A[25][25];
int vis[25][25];
int W, H;
int X[4]={0, 0, 1, -1};
int Y[4]={1, -1, 0, 0};

int cnt=0;

struct Node
{
    int x;
    int y;
};

queue <Node> Q;

void BFS(int stx, int sty)
{
    Node st;
    st.x=stx;
    st.y=sty;
    Q.push(st);
    while(!Q.empty())
    {
        Node top;
        top=Q.front();
        Q.pop();
        for(int i=0;i<4;i++)
        {
            Node tmp;
            tmp.x=top.x+X[i];
            tmp.y=top.y+Y[i];
            if(vis[tmp.x][tmp.y]==0&&A[tmp.x][tmp.y]=='.'&&tmp.x>=1&&tmp.x<=H&&tmp.y>=1&&tmp.y<=W)
            {
                vis[tmp.x][tmp.y]=true;
                Q.push(tmp);
                cnt++;
            }
        }
    }

}




int main()
{
    int stx,sty;
    while(cin>>W>>H)
    {
        fill(vis[0], vis[0]+25*25,0);
        if(W==0&&H==0)
            break;
        for(int i=1;i<=H;i++)
        {
            getchar();
            for(int j=1;j<=W;j++)
            {
                cin>>A[i][j];
                if(A[i][j]=='@')
                {
                    stx = i;
                    sty = j;
                }
            }
        }
        cnt=0;
        BFS(stx, sty);
        cout<<cnt+1<<endl;
    }

    return 0;
}


```


递归：

```C++
#include <iostream>
#include <cstdio>

using namespace std;

char A[25][25];
int W, H;

int f(int x, int y)
{
    if(x<0||x>=H||y<0||y>=W)
        return 0;
    if(A[x][y]=='#')
        return 0;
    else
    {
        A[x][y]='#';
        return 1+f(x-1,y)+f(x+1,y)+f(x,y+1)+f(x,y-1);
    }
}

int main()
{
    while(true)
    {
        cin>>W>>H;
        if(W==0&&H==0)
            break;
        for(int i=0;i<H;i++)
        {
            cin>>A[i];
        }
        for(int i=0;i<H;i++)
        {
            for(int j=0;j<W;j++)
            {
                if(A[i][j]=='@')
                    cout<<f(i,j)<<endl;
            }
        }
    }
    return 0;
}
```



---

```
2698:八皇后问题
查看 提交 统计 提示 提问
总时间限制: 10000ms 内存限制: 65536kB
描述
在国际象棋棋盘上放置八个皇后，要求每两个皇后之间不能直接吃掉对方。
输入
无输入。
输出
按给定顺序和格式输出所有八皇后问题的解（见Sample Output）。
样例输入
样例输出
No. 1
1 0 0 0 0 0 0 0 
0 0 0 0 0 0 1 0 
0 0 0 0 1 0 0 0 
0 0 0 0 0 0 0 1 
0 1 0 0 0 0 0 0 
0 0 0 1 0 0 0 0 
0 0 0 0 0 1 0 0 
0 0 1 0 0 0 0 0 
No. 2
1 0 0 0 0 0 0 0 
0 0 0 0 0 0 1 0 
0 0 0 1 0 0 0 0 
0 0 0 0 0 1 0 0 
0 0 0 0 0 0 0 1 
0 1 0 0 0 0 0 0 
0 0 0 0 1 0 0 0 
0 0 1 0 0 0 0 0 
```


```C++

#include <iostream>
#include <cmath>

using namespace std;

int n = 8, cnt=0, temp;
int P[15];
int ht[15]={false};

void GP(int index)
{
    if(index == n+1)
    {
        for(int i=1;i<=n;i++)
        {
            for(int j=i+1;j<=n;j++)
            {
                if(abs(i-j)==abs(P[i]-P[j]))
                    return;
            }
        }

        cnt++;
        cout<<"No. "<<cnt<<endl;
        for(int i=1;i<=n;i++)
        {
            for(int j=1;j<=n;j++)
            {
                if(P[j]==i)
                    cout<<1<<" ";
                else
                    cout<<0<<" ";
            }
            cout<<endl;
        }
        return ;
    }

    for(int x=1;x<=n;x++)
    {
        if(ht[x]==false)
        {
            ht[x]=true;
            P[index]=x;
            GP(index+1);
            ht[x]=false;
        }
    }


}

int main()
{
    GP(1);
    return 0;
}

```


---

```
2754:八皇后
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
会下国际象棋的人都很清楚：皇后可以在横、竖、斜线上不限步数地吃掉其他棋子。如何将8个皇后放在棋盘上（有8 * 8个方格），使它们谁也不能被吃掉！这就是著名的八皇后问题。 
对于某个满足要求的8皇后的摆放方法，定义一个皇后串a与之对应，即a=b1b2...b8，其中bi为相应摆法中第i行皇后所处的列数。已经知道8皇后问题一共有92组解（即92个不同的皇后串）。
给出一个数b，要求输出第b个串。串的比较是这样的：皇后串x置于皇后串y之前，当且仅当将x视为整数时比y小。
输入
第1行是测试数据的组数n，后面跟着n行输入。每组测试数据占1行，包括一个正整数b(1 <= b <= 92)
输出
输出有n行，每行输出对应一个输入。输出应是一个正整数，是对应于b的皇后串。
样例输入
2
1
92
样例输出
15863724
84136275
```

使用next_permutation

```C++
#include <bits/stdc++.h>


using namespace std;

int src[8]={1,2,3,4,5,6,7,8};
int tmp[8];

bool check()
{
    for(int i=0;i<8;i++)
    {
        for(int j=i+1;j<8;j++)
        {
            if(abs(j-i)==abs(tmp[j]-tmp[i]))
                return false;
        }
    }
    return true;
}



int main()
{
    int n, b;
    cin>>n;
    while(n--)
    {
        cin>>b;
        memcpy(tmp, src, sizeof(src));
        while(next_permutation(tmp, tmp+8))
        {
            if(check())
                b--;
            if(b==0)
                break;
        }
        for(int i=0;i<8;i++)
        {
            cout<<tmp[i];
        }
        cout<<endl;
    }

    return 0;
}

```

使用递归全排列：

```C++
#include <bits/stdc++.h>

using namespace std;

int vis[9], P[9], cnt=1;
int sum[95];

void GP(int index)
{
    if(index==8+1)
    {
        for(int i=1;i<=8;i++)
        {
            for(int j=i+1;j<=8;j++)
            {
                if(abs(j-i)==abs(P[j]-P[i]))
                    return;
            }
        }
        int tmp=0;
        for(int i=1;i<=8;i++)
        {
            tmp = tmp*10 + P[i];
        }
        sum[cnt++]=tmp;
    }

    for(int x=1;x<=8;x++)
    {
        if(vis[x]==0)
        {
            vis[x]=1;
            P[index]=x;
            GP(index+1);
            vis[x]=0;
        }
    }
}


int main()
{
    int n,b;
    cin>>n;
    GP(1);
    while(n--)
    {
        cin>>b;
        cout<<sum[b]<<endl;
    }
    return 0;
}
```


---



```
1792:迷宫
查看 提交 统计 提问
总时间限制: 3000ms 内存限制: 65536kB
描述
一天Extense在森林里探险的时候不小心走入了一个迷宫，迷宫可以看成是由n * n的格点组成，每个格点只有2种状态，.和#，前者表示可以通行后者表示不能通行。同时当Extense处在某个格点时，他只能移动到东南西北(或者说上下左右)四个方向之一的相邻格点上，Extense想要从点A走到点B，问在不走出迷宫的情况下能不能办到。如果起点或者终点有一个不能通行(为#)，则看成无法办到。
输入
第1行是测试数据的组数k，后面跟着k组输入。每组测试数据的第1行是一个正整数n (1 <= n <= 100)，表示迷宫的规模是n * n的。接下来是一个n * n的矩阵，矩阵中的元素为.或者#。再接下来一行是4个整数ha, la, hb, lb，描述A处在第ha行, 第la列，B处在第hb行, 第lb列。注意到ha, la, hb, lb全部是从0开始计数的。
输出
k行，每行输出对应一个输入。能办到则输出“YES”，否则输出“NO”。
样例输入
2
3
.##
..#
#..
0 0 2 2
5
.....
###.#
..#..
###..
...#.
0 0 4 0
样例输出
YES
NO
```


```C++
#include <bits/stdc++.h>

using namespace std;

char A[105][105];
int vis[105][105];
int X[4]={0,0,1,-1};
int Y[4]={1,-1,0,0};
int n;

struct Node
{
    int x;
    int y;
};

queue <Node> Q;

void BFS(int stx, int sty, int enx, int eny)
{
    while(!Q.empty())
    {
        Q.pop();
    }
    Node st;
    st.x=stx;
    st.y=sty;
    vis[stx][sty]=1;
    Q.push(st);
    while(!Q.empty())
    {
        Node top;
        top=Q.front();
        Q.pop();
        for(int i=0;i<4;i++)
        {
            Node tmp;
            tmp.x=top.x+X[i];
            tmp.y=top.y+Y[i];
            if(tmp.x==enx&&tmp.y==eny)
            {
                cout<<"YES"<<endl;
                return;
            }
            if(vis[tmp.x][tmp.y]==0&&A[tmp.x][tmp.y]=='.'&&tmp.x>=1&&tmp.x<=n&&tmp.y>=1&&tmp.y<=n)
            {
                vis[tmp.x][tmp.y]=1;
                Q.push(tmp);
            }
        }
    }
    cout<<"NO"<<endl;
}

int main()
{
    int k;
    cin>>k;
    while(k--)
    {

        cin>>n;
        fill(vis[0], vis[0]+105*105, 0);
        for(int i=1;i<=n;i++)
        {
            getchar();
            for(int j=1;j<=n;j++)
            {
                cin>>A[i][j];
            }
        }
        int ha, la, hb, lb;
        cin>>ha>>la>>hb>>lb;
        if(A[ha+1][la+1]=='#'||A[hb+1][lb+1]=='#')
            cout<<"NO"<<endl;
        else
            BFS(ha+1, la+1, hb+1, lb+1);
    }
    return 0;
}

```


---


```
2971:抓住那头牛
查看 提交 统计 提问
总时间限制: 2000ms 内存限制: 65536kB
描述
农夫知道一头牛的位置，想要抓住它。农夫和牛都位于数轴上，农夫起始位于点N(0<=N<=100000)，牛位于点K(0<=K<=100000)。农夫有两种移动方式：

1、从X移动到X-1或X+1，每次移动花费一分钟
2、从X移动到2*X，每次移动花费一分钟

假设牛没有意识到农夫的行动，站在原地不动。农夫最少要花多少时间才能抓住牛？


输入
两个整数，N和K
输出
一个整数，农夫抓到牛所要花费的最小分钟数
样例输入
5 17
样例输出
4
```


```C++
#include <cstdio>
using namespace std;
bool f[100001]; int ans[100001],father[100001],stat[100001],an;
int make(int i,int x){
    if (i==0) return x*2; //第一种情况变为2x
    if (i==1) return x-1; //第二种情况变为x-1
    if (i==2) return x+1; //第三种情况变为x+1
}
void bfs(){
    int head=0,tail=1,x; f[stat[1]]=1; //初始化
    do{
        head++;  //队首加一（出队）
        for (int i=0;i<3;i++){
            x=make(i,stat[head]);//下一个点可以到哪里
            if (x>=0&&x<=100000&&!f[x]){ //判断
                tail++; //队尾+1（入队）
                father[tail]=head; //指向父节点（可以不需要）
                stat[tail]=x;  //加入队尾（入队）
                ans[tail]=ans[head]+1; //步数增加
                f[x]=1; //该点已经走过了
                if (x==an){ //走到了终点
                    printf("%d",ans[tail]); //输出最小步数
                    return; //退出
                }
            }
        }
    }while (head<tail);
}
int main(){
    scanf("%d%d",&stat[1],&an); //终点
    if (stat[1]==an){printf("0"); return 0;} //特殊情况
    bfs(); return 0;
}

```

BFS

```C++
#include <iostream>
#include <queue>
#include <cstring>

using namespace std;

const int maxn = 100005;

int vis[maxn], p[3]; //vis数组储存最小分钟数，且标记已经访问 

queue <int> Q;

void BFS(int n, int k)
{
    Q.push(n);
    vis[n] = 0;
    while (!Q.empty())
    {
        int top = Q.front();
        Q.pop();
        if (top == k)
        {
            cout << vis[top];
            return;
        }
        p[0] = top - 1;
        p[1] = top + 1;
        p[2] = top * 2;
        for (int i = 0; i < 3; i++)
        {
            if (p[i] >= 0 && p[i] <= maxn && vis[p[i]] == -1)
            {
                Q.push(p[i]);
                vis[p[i]] = vis[top] + 1;
            }
        }
    }
}


int main()
{
    int N, K;
    cin >> N >> K;
    memset(vis, -1, sizeof(vis));//初始化vis数组 -1
    BFS(N, K);
}
```




---


```
1789:算24
查看 提交 统计 提问
总时间限制: 3000ms 内存限制: 65536kB
描述
给出4个小于10个正整数，你可以使用加减乘除4种运算以及括号把这4个数连接起来得到一个表达式。现在的问题是，是否存在一种方式使得得到的表达式的结果等于24。

这里加减乘除以及括号的运算结果和运算的优先级跟我们平常的定义一致（这里的除法定义是实数除法）。

比如，对于5，5，5，1，我们知道5 * (5 – 1 / 5) = 24，因此可以得到24。又比如，对于1，1，4，2，我们怎么都不能得到24。
输入
输入数据包括多行，每行给出一组测试数据，包括4个小于10个正整数。最后一组测试数据中包括4个0，表示输入的结束，这组数据不用处理。
输出
对于每一组测试数据，输出一行，如果可以得到24，输出“YES”；否则，输出“NO”。
样例输入
5 5 5 1
1 1 4 2
0 0 0 0
样例输出
YES
NO
```

```C++
#include <bits/stdc++.h>

using namespace std;

double A[4];
double EPS=1e-6;

bool isEqu(double a, double b)
{
    if(fabs(a-b)<EPS)
        return true;
    return false;
}

//用n个数算24，先取两个数进行某种运算，然后再用算得的结果，和剩下的n-2个数，凑成n-1个数去算24
bool cal24(double a[], int n)
{
    if(n==1)//边界条件，如果只剩一个数，那他必须为24
    {
        if(isEqu(a[0],24))
            return true;
        else
            return false;
    }
    double b[5];
    for(int i=0;i<n-1;i++)//先对a中的两个数进行运算，枚举这两个数
    {
        for(int j=i+1;j<n;j++)
        {
            int m=0;
            for(int k=0;k<n;k++)
            {
                if(k!=i&&k!=j)
                    b[m++]=a[k];//将选出的两个数以外的数存入b数组
            }
            b[m]=a[i]+a[j];
            if(cal24(b, m+1))
                return true;
            b[m]=a[i]-a[j];
            if(cal24(b, m+1))
                return true;
            b[m]=a[j]-a[i];
            if(cal24(b, m+1))
                return true;
            b[m]=a[i]*a[j];
            if(cal24(b, m+1))
                return true;
            if(a[i]!=0)
            {
                b[m]=a[j]/a[i];
                if(cal24(b, m+1))
                    return true;
            }
            if(a[j]!=0)
            {
                b[m]=a[i]/a[j];
                if(cal24(b, m+1))
                    return true;
            }
        }
    }
    return false;
}

int main()
{
    while(true)
    {
        for(int i=0;i<4;i++)
            cin>>A[i];
        if(A[0]==0&&A[1]==0&&A[3]==0&&A[2]==0)
            break;
        if(cal24(A, 4))
            cout<<"YES"<<endl;
        else
            cout<<"NO"<<endl;
    }

    return 0;
}

```




---

```
2815:城堡问题
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述

     1   2   3   4   5   6   7  
   #############################
 1 #   |   #   |   #   |   |   #
   #####---#####---#---#####---#
 2 #   #   |   #   #   #   #   #
   #---#####---#####---#####---#
 3 #   |   |   #   #   #   #   #
   #---#########---#####---#---#
 4 #   #   |   |   |   |   #   #
   #############################
           (图 1)

   #  = Wall   
   |  = No wall
   -  = No wall

图1是一个城堡的地形图。请你编写一个程序，计算城堡一共有多少房间，最大的房间有多大。城堡被分割成mn(m≤50，n≤50)个方块，每个方块可以有0~4面墙。
输入
程序从标准输入设备读入数据。第一行是两个整数，分别是南北向、东西向的方块数。在接下来的输入行里，每个方块用一个数字(0≤p≤50)描述。用一个数字表示方块周围的墙，1表示西墙，2表示北墙，4表示东墙，8表示南墙。每个方块用代表其周围墙的数字之和表示。城堡的内墙被计算两次，方块(1,1)的南墙同时也是方块(2,1)的北墙。输入的数据保证城堡至少有两个房间。
输出
城堡的房间数、城堡中最大房间所包括的方块数。结果显示在标准输出设备上。
样例输入
4 
7 
11 6 11 6 3 10 6 
7 9 6 13 5 15 5 
1 10 12 7 13 7 5 
13 11 10 8 10 12 13 
样例输出
5
9
```

BFS:

```C++
#include <bits/stdc++.h>

using namespace std;

const int maxn=55;

int A[maxn][maxn];
int vis[maxn][maxn];
int R, C;

struct Node
{
    int x;
    int y;
};

queue <Node> Q;

int BFS(int x, int y)
{
    int tmpSize=1;
    Node st;
    st.x=x;
    st.y=y;
    Q.push(st);
    vis[st.x][st.y]=1;
    while(!Q.empty())
    {
        Node top;
        top = Q.front();
        Q.pop();
        Node tmp;
        if((A[top.x][top.y]&1)==0&&vis[top.x][top.y-1]==0)
        {
            tmp.x=top.x;
            tmp.y=top.y-1;
            Q.push(tmp);
            tmpSize++;
            vis[top.x][top.y-1]=1;
        }
        if((A[top.x][top.y]&2)==0&&vis[top.x-1][top.y]==0)
        {
            tmp.x=top.x-1;
            tmp.y=top.y;
            Q.push(tmp);
            tmpSize++;
            vis[top.x-1][top.y]=1;
        }
        if((A[top.x][top.y]&4)==0 &&vis[top.x][top.y+1]==0)
        {
            tmp.x=top.x;
            tmp.y=top.y+1;
            Q.push(tmp);
            tmpSize++;
            vis[top.x][top.y+1]=1;
        }
        if((A[top.x][top.y]&8)==0&&vis[top.x+1][top.y]==0)
        {
            tmp.x=top.x+1;
            tmp.y=top.y;
            Q.push(tmp);
            tmpSize++;
            vis[top.x+1][top.y]=1;
        }
    }
    return tmpSize;
}


int main()
{

    cin>>R>>C;
    int cntHome=0, sizeHome, maxSize=0;
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
        {
            cin>>A[i][j];
        }
    }
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
        {
            if(vis[i][j]==0)
            {
                sizeHome = BFS(i,j);
                cntHome++;
                if(sizeHome>maxSize)
                    maxSize = sizeHome;
            }
        }
    }
    cout<<cntHome<<endl;
    cout<<maxSize<<endl;
    return 0;
}

```

DFS:

```C++
#include <bits/stdc++.h>

using namespace std;

const int maxn=55;
int sizeHome;

int A[maxn][maxn];
int vis[maxn][maxn];

void DFS(int x, int y)
{
    if(vis[x][y]==1)
        return;
    vis[x][y]=1;
    sizeHome++;
    if((A[x][y]&1)==0)
    {
        DFS(x, y-1);
    }
    if((A[x][y]&2)==0)
    {
        DFS(x-1, y);
    }
    if((A[x][y]&4)==0)
    {
        DFS(x, y+1);
    }
    if((A[x][y]&8)==0)
    {
        DFS(x+1, y);
    }
}


int main()
{
    int R, C;
    cin>>R>>C;
    int cntHome=0, maxSize=0;
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
        {
            cin>>A[i][j];
        }
    }
    for(int i=1;i<=R;i++)
    {
        for(int j=1;j<=C;j++)
        {

            if(vis[i][j]==0)
            {
                sizeHome=0;
                DFS(i, j);
                cntHome++;
                maxSize=max(maxSize, sizeHome);
            }

        }
    }
    cout<<cntHome<<endl;
    cout<<maxSize<<endl;
    return 0;
}

```




---

```
323:棋盘问题
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
在一个给定形状的棋盘（形状可能是不规则的）上面摆放棋子，棋子没有区别。要求摆放时任意的两个棋子不能放在棋盘中的同一行或者同一列，请编程求解对于给定形状和大小的棋盘，摆放k个棋子的所有可行的摆放方案C。
输入
输入含有多组测试数据。
每组数据的第一行是两个正整数，n k，用一个空格隔开，表示了将在一个n*n的矩阵内描述棋盘，以及摆放棋子的数目。 n <= 8 , k <= n
当为-1 -1时表示输入结束。
随后的n行描述了棋盘的形状：每行有n个字符，其中 # 表示棋盘区域， . 表示空白区域（数据保证不出现多余的空白行或者空白列）。
输出
对于每一组数据，给出一行输出，输出摆放的方案数目C （数据保证C<2^31）。
样例输入
2 1
#.
.#
4 4
...#
..#.
.#..
#...
-1 -1
样例输出
2
1
```

DSF

```C++
#include <bits/stdc++.h>

using namespace std;

const int maxn=20;
int cnt;
int n, k;
char A[maxn][maxn];
int vis[maxn];

void DFS(int row, int step)
{
    if(step==k)
    {
        cnt++;
        return;
    }
    for(int i=row;i<n;i++)
    {
        for(int j=0;j<n;j++)
        {
            if(A[i][j]=='#'&&!vis[j])
            {
                vis[j]=1;
                DFS(i+1, step+1);
                vis[j]=0;
            }
        }
    }

}

int main()
{

    while(cin>>n>>k)
    {
        if(n==-1&&k==-1)
            break;
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<n;j++)
            {
                cin>>A[i][j];
            }
        }
        cnt=0;
        memset(vis, 0, sizeof(vis));
        DFS(0, 0);
        cout<<cnt<<endl;
    }

    return 0;
}

```