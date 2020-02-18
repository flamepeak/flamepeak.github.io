---
title: poj 递归
date: 2019-03-07 09:00:43
tags: [C]
categories:
---


```
1696:逆波兰表达式
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
逆波兰表达式是一种把运算符前置的算术表达式，例如普通的表达式2 + 3的逆波兰表示法为+ 2 3。逆波兰表达式的优点是运算符之间不必有优先级关系，也不必用括号改变运算次序，例如(2 + 3) * 4的逆波兰表示法为* + 2 3 4。本题求解逆波兰表达式的值，其中运算符包括+ - * /四个。
输入
输入为一行，其中运算符和运算数之间都用空格分隔，运算数是浮点数。
输出
输出为一行，表达式的值。
可直接用printf("%f\n", v)输出表达式的值v。
样例输入
* + 11.0 12.0 + 24.0 35.0
样例输出
1357.000000
提示
可使用atof(str)把字符串转换为一个double类型的浮点数。atof定义在math.h中。
此题可使用函数递归调用的方法求解。
```

[参考](https://blog.csdn.net/mrh929/article/details/50574056)

```C++
#include <bits/stdc++.h>

using namespace std;

char A[10000];

double work()
{
    scanf("%s", A);
    int len=strlen(A);
    if(len==1&&!(A[0]>='0'&&A[0]<='9'))
    {
        switch(A[0])
        {
            case '+':return work() + work();break;
            case '-':return work() - work();break;
            case '*':return work() * work();break;
            case '/':return work() / work();break;
        }
    }
    else
    {
        return atof(A);
    }
}

int main()
{
    printf("%f", work());

    return 0;
}

```



---


```
1750:全排列
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
给定一个由不同的小写字母组成的字符串，输出这个字符串的所有全排列。 我们假设对于小写字母有'a' < 'b' < ... < 'y' < 'z'，而且给定的字符串中的字母已经按照从小到大的顺序排列。

输入
输入只有一行，是一个由不同的小写字母组成的字符串，已知字符串的长度在1到6之间。
输出
输出这个字符串的所有排列方式，每行一个排列。要求字母序比较小的排列在前面。字母序如下定义：

已知S = s1s2...sk , T = t1t2...tk，则S < T 等价于，存在p (1 <= p <= k)，使得
s1 = t1, s2 = t2, ..., sp - 1 = tp - 1, sp < tp成立。
样例输入
abc
样例输出
abc
acb
bac
bca
cab
cba
```


使用递归：

```C++
#include <bits/stdc++.h>

using namespace std;

char A[1000];
char result[1000];
bool visited[1000]={false};
int n;

void generateP(int index)
{
    if(index==n)
    {
        result[n]=0x0;
        printf("%s\n", result);
        return;
    }
    for(int i=0;i<n;i++)
    {
        if(!visited[i])
        {
            result[index]=A[i];
            visited[i]=true;
            generateP(index+1);
            visited[i]=false;
        }
    }
}

int main()
{
    scanf("%s", A);
    n = strlen(A);
    sort(A, A+n);
    generateP(0);
    return 0;
}
```





使用`next_permutation()`:


```C++
#include <bits/stdc++.h>

using namespace std;

int main()
{
    char A[1000];
    gets(A);
    do
    {
        puts(A);
    }
    while(next_permutation(A, A+strlen(A)));

    return 0;
}
```




---


```
1751:分解因数
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
给出一个正整数a，要求分解成若干个正整数的乘积，即a = a1 * a2 * a3 * ... * an，并且1 < a1 <= a2 <= a3 <= ... <= an，问这样的分解的种数有多少。注意到a = a也是一种分解。
输入
第1行是测试数据的组数n，后面跟着n行输入。每组测试数据占1行，包括一个正整数a (1 < a < 32768)
输出
n行，每行输出对应一个输入。输出应是一个正整数，指明满足要求的分解的种数
样例输入
2
2
20
样例输出
1
4
```

DFS: [参考](https://blog.csdn.net/zqwang/article/details/79462941)

```C++
#include<bits/stdc++.h>
using namespace std;
int x,ans;
void dfs(int p,int k){ //p表示乘积，k表示当前的因子
    if(p==x){
        ans++;
        return;
    }
    for(int i=k;i<=x;i++){
        if(p*i>x)break;
        if(x%i==0)dfs(p*i,i);
    }
}
int main(){
    int n;
    scanf("%d",&n);
    while(n--){
        ans=0;
        scanf("%d",&x);
        dfs(1,2);
        printf("%d\n",ans);
    }
    return 0;
}

```

递归: [参考](https://blog.csdn.net/Wchenchen0/article/details/81077489)

```C++
#include <bits/stdc++.h>

using namespace std;

int a, ans=0;

void factor(int k, int x)
{
    for(int i=k;i<=x;i++)
    {
        if(x%i==0&&i<=x/i)
        {
            ans++;
            factor(i, x/i);
        }
        if(i>x/i)
            return;
    }
}

int main()
{
    int n;
    cin>>n;
    while(n--)
    {
        cin>>a;
        ans = 1;
        factor(2,a);
        cout<<ans<<endl;
    }

    return 0;
}
```


---

```
1755:菲波那契数列
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
菲波那契数列是指这样的数列: 数列的第一个和第二个数都为1，接下来每个数都等于前面2个数之和。
给出一个正整数a，要求菲波那契数列中第a个数是多少。
输入
第1行是测试数据的组数n，后面跟着n行输入。每组测试数据占1行，包括一个正整数a(1 <= a <= 20)
输出
输出有n行，每行输出对应一个输入。输出应是一个正整数，为菲波那契数列中第a个数的大小
样例输入
4
5
2
19
1
样例输出
5
1
4181
1
```

```C++
#include <iostream>

using namespace std;
int f[25]={1,1};

int fib(int n)
{
    if(n==1||n==2)
        return 1;
    if(f[n]!=0)
        return f[n];
    else
    {
        f[n]=fib(n-1)+fib(n-2);
        return f[n];
    }
}

int main()
{
    int n;
    cin>>n;
    while(n--)
    {
        int a;
        cin>>a;
        cout<<fib(a)<<endl;
    }

    return 0;
}

```



---


```
1777:文件结构“图”
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
在计算机上看到文件系统的结构通常很有用。Microsoft Windows上面的"explorer"程序就是这样的一个例子。但是在有图形界面之前，没有图形化的表示方法的，那时候最好的方式是把目录和文件的结构显示成一个"图"的样子，而且使用缩排的形式来表示目录的结构。比如：


ROOT
|     dir1
|     file1
|     file2
|     file3
|     dir2
|     dir3
|     file1
file1
file2
这个图说明：ROOT目录包括三个子目录和两个文件。第一个子目录包含3个文件，第二个子目录是空的，第三个子目录包含一个文件。

输入
你的任务是写一个程序读取一些测试数据。每组测试数据表示一个计算机的文件结构。每组测试数据以'*'结尾，而所有合理的输入数据以'#'结尾。一组测试数据包括一些文件和目录的名字（虽然在输入中我们没有给出，但是我们总假设ROOT目录是最外层的目录）。在输入中,以']'表示一个目录的内容的结束。目录名字的第一个字母是'd'，文件名字的第一个字母是'f'。文件名可能有扩展名也可能没有（比如fmyfile.dat和fmyfile）。文件和目录的名字中都不包括空格,长度都不超过30。一个目录下的子目录个数和文件个数之和不超过30。
输出
在显示一个目录中内容的时候，先显示其中的子目录（如果有的话），然后再显示文件（如果有的话）。文件要求按照名字的字母表的顺序显示（目录不用按照名字的字母表顺序显示，只需要按照目录出现的先后显示）。对每一组测试数据，我们要先输出"DATA SET x:"，这里x是测试数据的编号（从1开始）。在两组测试数据之间要输出一个空行来隔开。

你需要注意的是，我们使用一个'|'和5个空格来表示出缩排的层次。
样例输入
file1
file2
dir3
dir2
file1
file2
]
]
file4
dir1
]
file3
*
file2
file1
*
#
样例输出
DATA SET 1:
ROOT
|     dir3
|     |     dir2
|     |     file1
|     |     file2
|     dir1
file1
file2
file3
file4

DATA SET 2:
ROOT
file1
file2
提示
一个目录和它的子目录处于不同的层次
一个目录和它的里面的文件处于同一层次
来源
翻译自 Pacific Northwest 1998 的试题
```


```C++
#include <bits/stdc++.h>

using namespace std;

string A[40];
int len;

void output(string str, int level)
{
    for(int i=0;i<level;i++)
        cout<<"|     ";
    cout<<str<<endl;
    string tmp, B[50];
    int k=0;
    while(true)
    {
        cin>>tmp;
        if(tmp[0]==']')
            break;
        if(tmp[0]=='f')
            B[k++] = tmp;
        if(tmp[0]=='d')
            output(tmp, level+1);
    }
    sort(B, B+k);
    for(int i=0;i<k;i++)
    {
        for(int i=0;i<level;i++)
            cout<<"|     ";
        cout<<B[i]<<endl;
    }

}

int main()
{
    string str;
    bool flag = true;
    int cnt = 0;
    while(cin>>str)
    {
        len=0;
        if(flag)
            flag = false;
        else
            cout<<endl;
        if(str=="#")
            break;
        printf("DATA SET %d:\nROOT\n", ++cnt);
        string A[100];
        while(true)
        {
            if(str[0]=='f')
                A[len++] = str;
            if(str[0]=='d')
                output(str, 1);
            if(str[0]=='*')
                break;
            cin>>str;
        }
        sort(A, A+len);
        for(int i=0;i<len;i++)
            cout<<A[i]<<endl;
    }

    return 0;
}


```



---

```
2705:扩号匹配问题
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
在某个字符串（长度不超过100）中有左括号、右括号和大小写字母；规定（与常见的算数式子一样）任何一个左括号都从内到外与在它右边且距离最近的右括号匹配。写一个程序，找到无法匹配的左括号和右括号，输出原来字符串，并在下一行标出不能匹配的括号。不能匹配的左括号用"$"标注,不能匹配的右括号用"?"标注.
输入
输入包括多组数据，每组数据一行，包含一个字符串，只包含左右括号和大小写字母，字符串长度不超过100
注意：cin.getline(str,100)最多只能输入99个字符！
输出
对每组输出数据，输出两行，第一行包含原始输入字符，第二行由"$","?"和空格组成，"$"和"?"表示与之对应的左括号和右括号不能匹配。
样例输入
((ABCD(x)
)(rttyy())sss)(
样例输出
((ABCD(x)
$$
)(rttyy())sss)(
?            ?$
```


使用堆栈：[参考](https://www.cnblogs.com/huashanqingzhu/p/6546598.html)

```C++
#include <bits/stdc++.h>

using namespace std;

struct Node
{
    char ch;
    int index;
};

Node tmp;

char A[105];
char B[105];

int main()
{
    while(scanf("%s", A)!=EOF)
    {
        fill(B,B+100, ' ');
        stack<Node> S;
        int len = strlen(A);
        for(int i=0;i<len;i++)
        {
            if(A[i]=='(')
            {
                tmp.ch = '(';
                tmp.index = i;
                S.push(tmp);
            }
            else if(A[i]==')')
            {
                if(S.empty())
                    B[i]='?';
                else
                {
                    S.pop();
                    B[i]=' ';
                }

            }
            else
                B[i]=' ';
        }
        while(!S.empty())
        {
            Node remain = S.top();
            S.pop();
            B[remain.index] = '$';
        }
        B[len]=0x0;
        printf("%s\n", A);
        printf("%s\n", B);
    }

    return 0;
}


```

递归：

```C++
#include<iostream>  
    #include<cstring>  
    using namespace std;  
      
    int pp(int);  
    char c[300] =  
    { };  
    int l;  
    int main()  
    {  
      
        while (cin.getline(c, 120))  
        {  
            cout<<c<<endl;  
            l = strlen(c);  
            int i;  
            for (i = 0; i < l; i++)  
                if (c[i] != '(' && c[i] != ')')  
                    c[i] = ' ';  
            i = 0;  
            while (c[i] != 0)  
            {  
                while (c[i] != '(' && c[i] != 0)  
                    i++;  
                if (c[i] == '(')  
                    i = pp(i);  
            }  
            i = 0;  
            while (c[i] != 0)  
            {  
                if (c[i] == ')')  
                    c[i] = '?';  
                i++;  
            }  
            cout << c << endl;  
        }  
        return 0;  
    }  
      
    int pp(int pos)  
    {  
        int i;  
        i = pos + 1;  
        while (1)  
        {  
            while (c[i] != '(' && c[i] != ')' && c[i] != 0)  
                i++;  
            if (c[i] == '(')  
            {  
                i = pp(i);  
            }  
            else if (c[i] == ')')  
            {  
                c[pos] = ' ';  
                c[i] = ' ';  
                return i + 1;  
            }  
            else  
            {  
                c[pos] = '$';  
                return l;  
            }  
        }  
    }
```


---

```
3089:爬楼梯
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
树老师爬楼梯，他可以每次走1级或者2级，输入楼梯的级数，求不同的走法数
例如：楼梯一共有3级，他可以每次都走一级，或者第一次走一级，第二次走两级
也可以第一次走两级，第二次走一级，一共3种方法。

输入
输入包含若干行，每行包含一个正整数N，代表楼梯级数，1 <= N <= 30
输出
不同的走法数，每一行输入对应一行输出
样例输入
5
8
10
样例输出
8
34
89
```


```C++
#include <iostream>

using namespace std;

int N;

int walk(int n)
{
    if(n==1)
        return 1;
    if(n==2)
        return 2;
    return walk(n-1)+walk(n-2);
}


int main()
{
    while(cin>>N)
    {
        cout<<walk(N)<<endl;
    }

    return 0;
}

```



---

```
6261:汉诺塔问题
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
约19世纪末，在欧州的商店中出售一种智力玩具，在一块铜板上有三根杆，最左边的杆上自上而下、由小到大顺序串着由64个圆盘构成的塔。目的是将最左边杆上的盘全部移到中间的杆上，条件是一次只能移动一个盘，且不允许大盘放在小盘的上面。 
这是一个著名的问题，几乎所有的教材上都有这个问题。由于条件是一次只能移动一个盘，且不允许大盘放在小盘上面，所以64个盘的移动次数是：18,446,744,073,709,551,615
这是一个天文数字，若每一微秒可能计算(并不输出)一次移动，那么也需要几乎一百万年。我们仅能找出问题的解决方法并解决较小N值时的汉诺塔，但很难用计算机解决64层的汉诺塔。 

假定圆盘从小到大编号为1, 2, ...

输入
输入为一个整数后面跟三个单字符字符串。
整数为盘子的数目，后三个字符表示三个杆子的编号。
输出
输出每一步移动盘子的记录。一次移动一行。
每次移动的记录为例如 a->3->b 的形式，即把编号为3的盘子从a杆移至b杆。
样例输入
2 a b c
样例输出
a->1->c
a->2->b
c->1->b
```



```C++
#include <bits/stdc++.h>

using namespace std;

int n;
char a, b, c;

void output(int num, char st, char en)
{
    printf("%c->%d->%c\n", st, num, en);
}

void Move(int num, char st, char en, char tmp)
{
    if(num==1)
    {
        output(num, st, en);
        return;
    }
    else
    {
        Move(num-1, st, tmp, en);;
        output(num, st, en);
        Move(num-1, tmp, en, st);
    }
}

int main()
{

    cin>>n>>a>>b>>c;
    Move(n, a, b, c);
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
    return apple(m, n-1)+apple(m-n, n);
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
8758:2的幂次方表示
查看 提交 统计 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
任何一个正整数都可以用2的幂次方表示。例如：

    137=27+23+20

同时约定方次用括号来表示，即ab可表示为a(b)。由此可知，137可表示为：

    2(7)+2(3)+2(0)

进一步：7=22+2+20（21用2表示）

        3=2+20

所以最后137可表示为：

    2(2(2)+2+2(0))+2(2+2(0))+2(0)

又如：

    1315=210+28+25+2+1

所以1315最后可表示为：

    2(2(2+2(0))+2)+2(2(2+2(0)))+2(2(2)+2(0))+2+2(0)

输入
一个正整数n（n≤20000）。
输出
一行，符合约定的n的0，2表示（在表示中不能有空格）。
样例输入
137
样例输出
2(2(2)+2+2(0))+2(2+2(0))+2(0)
```



```C++
#include <bits/stdc++.h>

using namespace std;


void extract(int n)
{
    bool p=0;//判断有无加号
    while(n!=0)
    {
        if(!p)
            cout<<"2";
        else
            cout<<"+2";
        for(int i=0;i<=15;i++)//最大2的15次方
        {
            if(pow(2, i*1.0)>n)
            {
                n -= pow(2, i-1.0);
                p=1;
                if(i>3)
                {
                    cout<<"(";
                    extract(i-1);
                    cout<<")";
                }
                else//输出特殊的0或2
                {
                    if(i-1==0)
                        cout<<"(0)";
                    if(i-1==2)
                        cout<<"(2)";
                }
                break;
            }
        }
    }
}

int main()
{
    int n;
    cin>>n;
    extract(n);
    return 0;
}

```


---

[9273:PKU2506Tiling](http://noi.openjudge.cn/ch0202/9273/)


[参考](https://blog.csdn.net/wuzhenzi5193/article/details/80327958)