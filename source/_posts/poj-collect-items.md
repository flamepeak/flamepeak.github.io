---
title: poj 考试题目整理
date: 2019-02-27 13:40:17
tags: [C]
categories:
---





[POJ-1182 食物链](http://poj.org/problem?id=1182):最经典的并查集，[参考1](https://blog.csdn.net/niushuai666/article/details/6981689),[参考2：食物链（种类并查集）](https://blog.csdn.net/QQ_774682/article/details/81126457)


[1248:Safecracker](http://bailian.openjudge.cn/practice/1248/):【密码+暴力＋水题】:[参考](https://blog.csdn.net/tigerisland45/article/details/76222705)

[1252 Euro Efficiency ]():正负完全背包. [参考1](https://blog.csdn.net/a601025382s/article/details/9376637),[参考2](https://www.xuebuyuan.com/2234169.html),[参考2](https://www.xuebuyuan.com/357707.html)

[1481:The Die Is Cast](http://bailian.openjudge.cn/practice/1481/)：两层 DFS 搜索中搜索。[参考](https://blog.csdn.net/juncoder/article/details/23225631)

[1287:Networking](http://bailian.openjudge.cn/practice/1287/):最小生成树。一个网络中，一些节点间存在网路，你现在需要找出一条可以连接所有点的最短网路。[参考](https://blog.csdn.net/nucleare/article/details/80587769)

[1323:Game Prediction](http://bailian.openjudge.cn/practice/1323/):贪心 [参考](https://www.cnblogs.com/jinglecjy/p/5671912.html)

[Poj 1339 poker card game](http://bailian.openjudge.cn/practice/1339/):哈夫曼树。n张扑克牌放到如图1的空框上，得分是扑克牌点数 乘 空框到树根距离。如图2，非空框（Q）的孩子不能再放纸牌。求最小得分。
[参考](https://blog.csdn.net/crystal_02101/article/details/76164294)

[1664:放苹果](http://bailian.openjudge.cn/practice/1664/)

[1703:发现它，抓住它](http://bailian.openjudge.cn/practice/1703/)：并查集，

[1840:Eqs](http://bailian.openjudge.cn/practice/1840/):Hash方法。[参考](https://blog.csdn.net/ypopstar/article/details/51089951)

[1847:Tram](http://bailian.openjudge.cn/practice/1847/):最短路径。[参考:Floyd](https://www.cnblogs.com/zhengguiping--9876/p/5827636.html)， [参考2:Dijkstra](https://www.cnblogs.com/xiaozhuyang/p/poj1847.html)

[1922:Ride to School](http://bailian.openjudge.cn/practice/1922/)

[1976:A Mini Locomotive](http://bailian.openjudge.cn/practice/1976/):背包问题，有三个火车头，n个车厢，每个车厢里面对应的有一定的人数。规定每个火车头最多拉m个连续的车厢而且他们拉的车厢一定是从左到右连续的，问它能够拉的最多的人数；题意：一个数列，n个数，找三个k个连续数的子数列，使其和最大。



[2051:Argus](http://bailian.openjudge.cn/practice/2051/)

[2115:C Looooops](http://bailian.openjudge.cn/practice/2115/)：[参考：扩展欧几里德 + 解同余方程](https://blog.csdn.net/weixin_39792252/article/details/79232585)

[2240:Arbitrage](http://bailian.openjudge.cn/practice/2240/):Floyd

[2355 Railway tickets ](http://bailian.openjudge.cn/practice/2355/):线性dp，注意理解，[POJ 2355 Railway tickets （线性dp）](https://blog.csdn.net/bolininahuaalex/article/details/24798313)

[2356:Find a multiple](http://bailian.openjudge.cn/practice/2356/):组合数学：鸽巢原理，抽屉原理,
`题意：给出n个数，任选几个数，使其和为n的倍数。
编程思想：鸽巢原理的简单应用。设前k项和Sk表示a1+a2+……ak,如果Sk是n的倍数，那就直接取Sk了，否则S1-Sn除n的余数分布在1---（n-1）这n-1个数中，运用鸽巢原理，必然有两个的余数相同，即(Si%n)=(Sj%n),即(Sj-Si)%n=0，证毕。所以落在区间i+1至j的所有数的和就是n的倍数。`
[参考1](https://blog.csdn.net/Enjoying_Science/article/details/49150115)，[参考2](https://www.cnblogs.com/BlackStorm/p/5243156.html)，[POJ 3370 Halloween treats 同题型](https://www.cnblogs.com/BlackStorm/p/5245868.html)

[2387:Til the Cows Come Home](http://bailian.openjudge.cn/practice/2387/):最短路径 [参考:spfa算法：邻接矩阵+SLF策略](https://www.cnblogs.com/cszlg/p/3299555.html), [参考:dijkstra](https://blog.csdn.net/vizard_/article/details/77161447)

[2492:A Bug's Life](http://bailian.openjudge.cn/practice/2492/): 并查集，[参考](https://www.cnblogs.com/Asumi/p/9747701.html)

[2312:Battle City](http://bailian.openjudge.cn/practice/2312/):BFS变形  优先队列，[参考1](https://blog.csdn.net/qq_33279781/article/details/64122069),[参考2](https://blog.csdn.net/xiang_hehe/article/details/81322812)


[POJ-2502 Subway](http://bailian.openjudge.cn/practice/2502/)：(最短路 Dijkstra) [参考](https://www.cnblogs.com/kuangbin/archive/2013/06/18/3142560.html)

[2736:大整数减法](http://bailian.openjudge.cn/practice/2736/):python

[2737:大整数除法](http://bailian.openjudge.cn/practice/2737/):python

[2250:Compromise](http://bailian.openjudge.cn/practice/2250/): DP问题，DFS输出序列，经典.[参考](https://blog.csdn.net/laojiu_/article/details/70798333)

[2756:二叉树](http://bailian.openjudge.cn/practice/2756/)

[2760:数字三角形](http://bailian.openjudge.cn/practice/2760/)



[2778:Ride to School](http://bailian.openjudge.cn/practice/2778/)

[2783:Holiday Hotel](http://bailian.openjudge.cn/practice/2783/)

[2788:二叉树](http://bailian.openjudge.cn/practice/2788/)

[2805:正方形](http://bailian.openjudge.cn/practice/2805/):STL map struct, [参考1：使用map](https://blog.csdn.net/qq_26919935/article/details/77891222)， [参考2：map](https://blog.csdn.net/becky_w/article/details/80774273), [参考3:详细讲解，使用hash](https://blog.csdn.net/u013686535/article/details/70255422)

[2812:恼人的青蛙](http://bailian.openjudge.cn/practice/2812/)

[2814:拨钟问题](http://bailian.openjudge.cn/practice/2814/):枚举问题：[参考](https://www.cnblogs.com/FengZeng666/p/10417191.html)

[2816:红与黑](http://bailian.openjudge.cn/practice/2816/)

[2871:整数奇偶排序](http://bailian.openjudge.cn/practice/2871/)：循环使用时，要注意两点：一是，参数的初始化位置，防止参数值受循环影响；二是，结尾数据的输出可能有换行。

[2910:提取数字](http://bailian.openjudge.cn/practice/2910/):???,这题比较坑，题目中说是连续字符字符组成的正整数，容易理解出现偏差，首先不要考虑负号的问题，就当没看到，其次，数字不是连续出现的也可以输出，比如a8b，你要输出8.最后，不要考虑超出整数范围，没必要。


[2912:三个完全平方数](http://bailian.openjudge.cn/practice/2912/)

[2915:字符串排序](http://bailian.openjudge.cn/practice/2915/):这个题要注意一下，在cin读入整数后，会把换行符留下，在接下来使用getline(cin,str)时，会导致读取0个字符的情况，所以在cin读入整数后必须使用getchar()将回车符读取掉。

[2945:拦截导弹](http://bailian.openjudge.cn/practice/2945/)

[2950:摘花生](http://bailian.openjudge.cn/practice/2950/)

[2981:大整数加法](http://bailian.openjudge.cn/practice/2981/)：重视，比较经典

[2982:Sudoku](http://bailian.openjudge.cn/practice/2982/):DFS，[参考](https://blog.csdn.net/Sharing_Li/article/details/9271045)

[3126 Prime Path](http://poj.org/problem?id=3126):BFS, 经典。[参考：](https://blog.csdn.net/super604zong/article/details/60324567)

[3142:球弹跳高度的计算](http://bailian.openjudge.cn/practice/3142/)

[3247:回文素数](http://bailian.openjudge.cn/practice/3247/):[参考](https://blog.csdn.net/qq_26919935/article/details/78011163),[参考2](https://blog.csdn.net/acerandaker/article/details/79377671)

[POJ 3259 Wormholes](http://poj.org/problem?id=3259):Bellman_ford 判断负环问题 [参考：代码很好，比书上好](https://blog.csdn.net/freezhanacmore/article/details/9712799)

[3343:热血格斗场](http://noi.openjudge.cn/ch0309/3343/): STL map pair，low_bound().

[3727:摘花生2](http://bailian.openjudge.cn/practice/3727/)


[4002:谁是你的潜在朋友](http://bailian.openjudge.cn/practice/4002/)

[4116:拯救行动](http://bailian.openjudge.cn/practice/4116/):BFS+优先队列，拯救公主系列之杀死卫士.[参考1](https://blog.csdn.net/anxdada/article/details/77032320),[参考2](https://blog.csdn.net/qq_37275680/article/details/81812158)：注意几点，一是多组数据初始化，fill如何初始化二维数组，二是BFS的返回值问题，默认返回值-1，要么就把输出直接写入BFS

[4147:汉诺塔问题(Hanoi)](http://bailian.openjudge.cn/practice/4147/)：递归问题。[参考](https://blog.csdn.net/algzjh/article/details/72902340)

[4128:单词序列](http://bailian.openjudge.cn/practice/4128/):BFS 数组搜索, 或者使用Floyd。[参考](https://blog.csdn.net/qq_35747066/article/details/78005638),[参考：Floyd](https://www.cnblogs.com/ssfzmfy/p/5772175.html)

[4135:月度开销](http://bailian.openjudge.cn/practice/4135/):二分查找。[参考]()

[4078:实现堆结构](http://bailian.openjudge.cn/practice/4078/):使用STL priority_queue实现堆。`priority_queue<int, vector<int>, greater<int> > Q;`,注意，push、top、pop操作。

[4977:怪盗基德的滑翔翼](http://noi.openjudge.cn/ch0206/4977/):注意两点：1.在多组数据时，一定要数组、数据初始化；2.能加括号尽量加括号。 注意理解题意，基德可以降落在任何一个地方，意味着求最长升序或降序子序列。[参考](https://blog.csdn.net/qq_38961124/article/details/78051279)


[NOI 1.13 41判断元素是否存在](http://noi.openjudge.cn/ch0113/41/)：递归。[参考](https://blog.csdn.net/wuzhenzi5193/article/details/80298737)












[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()
[]()