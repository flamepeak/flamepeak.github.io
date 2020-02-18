---
title: poj 2673 Kicc Wants to Move a Mountain
date: 2019-02-27 11:41:41
tags: [C]
categories:
---

[2673:Kicc Wants to Move a Mountain!](http://bailian.openjudge.cn/practice/2673/)


```
2673:Kicc Wants to Move a Mountain!
查看 提交 统计 提示 提问
总时间限制: 1000ms 内存限制: 65536kB
描述
There is a huge mountain outside Kicc's house, and Kicc has to climb the mountain every time he wants to go out. It costs him a lot of time and he wants to change the situation. Yes, he figures out a good idea - to move the mountain away! But there is a problem. There are some wild animals not too far away from the mountain. Kicc will make noises when digging or moving the stones. If the animals hear the sound made by Kicc, they will come closer. If an animal reaches the mountain and sees Kicc, the animal will eat Kicc immediately. But the animals are stupid - when they do not hear the sound, they will go back along the exact route they come to the mountain until they are in the place where they start. When they hear the sound again, they will come to the mountain again no matter where they are. In this situation, Kicc has to stop sometimes before the first animal arrives to avoid being eaten. You should notice that as soon as the animal arrive in the mountain, Kicc will be eaten, no matter he stops his work or not.

Kicc has t units of time per day and he can handle x units of stones in one unit of time (This action will make noises). There are m animals near the mountain. The i-th animal stays away the mountain with di units of distance and can run si units of distance in one of unit time. Kicc wants to know the most amount of stones he can handle every day (You can assume the amount of the stones in the mountain is infinite). To make it easier, you may assume that Kicc can work or rest for only integer units of time, for example, 1 unit of time, or 2 units of time, or 3 units of time, and so on.
输入
The first line contains an integer t (0 <= t < 1000000), and an integer x (0 < x < 10). The second line contains a single integer m (0 <= m < 1000). There are m lines following the second line, each has 2 integers di (0 < di < 1000000) and si (0 < si < 1000000).
输出
The output should contain a single integer which means the most amount of stones Kicc can move away in one day.
样例输入
100 5
2
1000 5
100 1
样例输出
495
```

思路：这是一道模拟题，分析过程。

动物的跑动距离应该为di-1，否则如果di,就会被吃掉。
求出所有动物到达时间最短的minTime。

过程：Kicc开始干活，然后动物开始跑，minTime之后，Kicc开始休息，动物开始返回。此时，剩余时间T-minTime。
再之后呢，当动物回到起点时，Kicc开始干活，动物出发；动物到达，Kicc停止干活，休息。

总过程可以简单理解：动物花minTime达到Kicc处，大家开始站在同一起跑线，即Kicc休息时动物在返回，Kicc干活时动物在赶来,由于动物赶来和返回的时间一样，所以Kicc一半时间在工作。Kicc工作的时间为minTime+（T-minTime）/2;


```C++
#include <iostream>
#include <algorithm>
#include <cmath>

using namespace std;

int d[1000005],s[1000005],t[1000005];

int main()
{
    int T, X, sum=0;
    cin>>T>>X;
    int m;
    cin>>m;
    if(m==0)
    {
        cout<<T*X;
        return 0;
    }
    for(int i=0;i<m;i++)
    {
        cin>>d[i]>>s[i];
        t[i] = (d[i]-1)/s[i];
    }
    sort(t,t+m);
    int minTime = t[0];
    if(minTime<1)
    {
        cout<<0;
        return 0;
    }
    if(T<=minTime)
        sum = T*X;
    else
    {
        T -= minTime;
        sum += minTime*X;
        T /= 2;
        sum += T*X;
    }
    cout<<sum;

    return 0;
}


```