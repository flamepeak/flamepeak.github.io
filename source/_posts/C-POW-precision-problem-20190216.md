---
title: C++ POW 精度问题
date: 2019-02-16 16:17:09
tags: [C]
categories: []
---


参考：https://blog.csdn.net/a812073479/article/details/21292065

对于幂函数`pow`，是用数值逼近的方法给出的，并不保证严格精确，执行效率也不高，在不同平台精确度可能有一定的差异，所以也就会导致结果不是我们预期想要的。

例如：在CodeBlocks中，

```C++

#include <bits/stdc++.h>

using namespace std;

int trans(int x, int N)
{
    int sum = 0, i=0;
    while(x!=0)
    {
        sum += (x%10)*pow(N,i++);
        x /= 10;
    }
    return sum;
}

int main()
{
    int p, q, r;
    //cin>>p>>q>>r;
    cout<<trans(42,13);


    return 0;
}
```

会输出错误结果53，而不是正确结果54.

可以做如下修改：

```C++
#include <bits/stdc++.h>

using namespace std;

int trans(int x, int N)
{
    int sum = 0, i=0, powVal=1;
    while(x!=0)
    {
        sum += (x%10)*powVal;
        x /= 10;
        powVal *= N;
    }
    return sum;
}

int main()
{
    int p, q, r;
    //cin>>p>>q>>r;
    cout<<trans(42,13);


    return 0;
}
```

