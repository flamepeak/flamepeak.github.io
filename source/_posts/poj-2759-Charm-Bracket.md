---
title: poj 2759 Charm Bracket 神奇口袋
date: 2019-02-26 22:30:41
tags: [C]
categories:
---

[2759 Charm Bracket](http://bailian.openjudge.cn/practice/2759/)

```
2759:神奇的口袋(2)
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
有一个神奇的口袋，总的容积是400，用这个口袋可以变出一些物品，这些物品的总体积必须是400。John现在有n个想要得到的物品，每个物品的体积分别是a1，a2……an。John可以从这些物品中选择一些，如果选出的物体的总体积是400，那么利用这个神奇的口袋，John就可以得到这些物品。现在的问题是，John有多少种不同的选择物品的方式。
输入
输入的第一行是正整数n (1 <= n <= 200)，表示不同的物品的数目。接下来的n行，每行有一个1到400之间的正整数，分别给出a1，a2……an的值。
输出
输出不同的选择物品的方式的数目对10000取模的结果（因为结果可能很大，为了避免高精度计算，只要求对10000取模的结果）。
```

有几种解法：

方法一：不会超时

```C++
#include <bits/stdc++.h>

using namespace std;
const int nl=404;
const int mod=10000;
int a[nl];
int f[nl];
int main(){
    int n;
    cin>>n;
    f[0]=1;
    for(int i=1;i<=n;i++){
        scanf("%d",&a[i]);
    }
    for(int i=1;i<=n;i++){
        for(int j=400;j>=a[i];j--){
            f[j]+=f[j-a[i]];
            f[j]%=mod;
        }
    }
    cout<<f[400];
}

```

或者：

```C++
#include<iostream>

using namespace std;
long long ans[410];
int main()
{
    int n;
    while(cin>>n)
    {
        ans[0] = 1;
        int w;
        for(int i = 1;i<=n;i++)
        {
            cin>>w;
            for(int v = 400;v>=w;v--)
            {
                ans[v] +=ans[v-w];
                ans[v]%=10000;
            }
        }
        cout<<ans[400];
    }
}

```

方法二：可能超时

```C++
#include <iostream>

using namespace std;

int A[205], n;
int cnt=0, maxvol=40;

void dfs(int index, int vol)
{
    if(vol==maxvol)
    {
        cnt++;
        return;
    }
    if(index==n+1)
        return;
    dfs(index+1,vol);
    dfs(index+1,vol+A[index]);
}

int main()
{
    cin>>n;
    for(int i=0;i<n;i++)
        cin>>A[i];
    dfs(0,0);
    cout<<cnt;



    return 0;
}
```

方法三:


```C++
#include <iostream>
using namespace std;

const int maxn = 25;
int n, a[maxn];

int work(int num, int w) {
    if (w == 0)
        return 1;
    else if (num < 0)
        return 0;
    else
        return work(num - 1, w) + work(num - 1, w - a[num]);
}

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    cout << work(n, 40) << endl;
    return 0;
}
```
