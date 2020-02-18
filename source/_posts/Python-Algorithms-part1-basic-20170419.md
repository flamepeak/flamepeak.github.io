---
title: Python Algorithms 读书笔记 基础
date: 2017-04-19 20:37:45
tags: [笔记, Python]
categories: [Python]
mathjax: true
---

### 算法

通常来说，所谓算法，实际上指的是一个执行过程，包含了能够解决某个特定问题的有限步骤集。

Python中的list并不是我们传统意义上的列表，这也是`append`操作会比`insert`操作效率高的原因所在。传统列表——通常也叫做链表（linked list）——通常是由一系列节点实现的，其每个节点（尾节点除外）中都有一个指向下一节点的引用。简单实现起来就像下面这样：

```Python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next
```

接下来，我们可以把所有节点构造成一个列表了：
`L = Node("a", Node("b", Node("c", Node("d"))))`
这样，`L.next.next.value`值就是`c`

但是Python中的list则与此有所不同。它不是由若干个独立的节点相互引用而成的，而是一整块单一连续的内存区块——我们称之为数组（array）。这直接导致了它与链表之间的一些重要区别。例如，尽管两者在遍历时的效率相差无几，但是如果我们要按既定索引值对某元素进行直接访问的话，显然使用数组会更有效率。因为，在数组中，我们可以直接计算出目标元素在内存中的位置，并能对其进行直接访问。而对于链表来说，我们必须从头开始遍历整个链表。  
但是具体到`insert`操作上，情况又会有所不同。对于链表而言，只要知道了要在哪里执行insert操作，其操作成本是非常低的。无论链表中有多少元素，其操作时间大致是相同的。而数组就不一样了，它每次执行insert操作都需要移动插入点右边的所有元素，甚至在必要时，我们可能还需要将这些列表元素整体搬到一个更大的数组中去。

### 渐近记法

表达式$O(g)$代表的是一组与某个函数$g(n)$有关的**函数集合**。若要让某个函数$f(n)$属于该集合，该函数须满足以下关系：存在自然数$n_0$和正数$c$，对于所有的$n \geq n_0$都有：
$$
f(n) \leq cg(n)
$$

对Python中的list而言，append n个数字所需要的时间约为$O(n)$， 而在其首端insert相同数量的数字需要的时间则约为$O(n^2)$

而另外两种记法 $\Omega$ 和 $\Theta$ 则可以视为$O$记法的变体。其中$\Omega$的定义正好与之相反。也就是说，若要让函数$f(n)$属于$\Omega(g)$，该函数须满足以下关系：存在自然数$n_0$和正数$c$，对于所有的$n \geq n_0$都有：
$$
f(n) \geq cg(n)
$$
因此，$O$记法代表的其实是所谓的渐近上界，而$\Omega$记法代表的是渐近下界。
而$\Theta$记号所代表的集合正好是前面两种记号的交集，即$\Theta(g) = O(g) \cap \Omega(g)$。换句话说，若要某个函数属于$\Theta(g)$，该函数须满足以下条件：存在自然数$n_0$和正数$c$，对于所有的$n \geq n_0$都有
$$
c_1g(n) \leq f(n) \leq c_2g(n)
$$
这意味着$f$和$g$有着相同程度的渐近式增长。例如，当$3n^2+2$属于$\Theta(n^2)$成立时，同样的关系也可以被写成$n_2$属于$\Theta(3n^2+2)$。而且，由于$\Theta$记号同时提供了函数的上下界，这使得它成为三个记号中最常用的一个。

***

### 图与树的实现

算法学中最强大的框架之一——图（graph），在很多情况下，如果我们可以将自己的问题诠释成一个图问题的话，那么该问题至少已经接近解决方案了。而如果我们的问题实例可以用树结构（tree）来诠释的话，那么我们基本上已经拥有一个真正有效的解决方案了。

**邻接列表及其类似结构**  
![adjacency.png](/sourcepictures/2017/04/20/adjacency.png)

**邻接集表示法：**
```Python
a, b, c, d, e, f, g, h = range(8)
N = [
    {b, c, d, e, f},  #a
    {c, e},           #b
    {d},              #c
    {e},              #d
    {f},              #e
    {c, g, h},        #f
    {f, h},           #g
    {f, g}            #h
]
```
注意这里的{}代表的是集合（集合是一个无序不重复元素的集。基本功能包括关系测试和消除重复元素。）大括号或 set() 函数可以用来创建集合。注意：想要创建空集合，你必须使用 set() 而不是 {}，后者用于创建空字典。大括号也不可以创建元素含有字典与列表的集合。这里没有使用元组tuple，因为tuple大小不可变。  
在图论中，$N(v)$就代表的是$v$的邻居节点集，同样，在代码中$N[v]$就是一个$v$邻居节点集。可以如下操作：  
```Bash
>>> b in N[a]  # Neighborhood membership
True
>>> len(N[f])  # degree
3
```

**邻接列表**  
```Python
a, b, c, d, e, f, g, h = range(8)
N = [
    [b, c, d, e, f],  #a
    [c, e],           #b
    [d],              #c
    [e],              #d
    [f],              #e
    [c, g, h],        #f
    [f, h],           #g
    [f, g]            #h
]
```
这种方式使我们可以对$N(v)$中所有节点$v$进行有效遍历。

**加权邻接字典**
除了以上两种表示方法，还有一种方式，就是用`dict`类型来代替这里的`set`或`list`。在`dict`类型中，每个邻居节点都会有一个键和一个额外的值，用于表示与其邻居节点之间的关联性，如边的权重。
```Python
a, b, c, d, e, f, g, h = range(8)
N = [
    {b:2, c:1, d:3, e:9, f:4},  #a
    {c:4, e:3},                 #b
    {d:8},                      #c
    {e:7},                      #d
    {f:5},                      #e
    {c:2, g:2, h:2},            #f
    {f:1, h:6},                 #g
    {f:9, g:8}                  #h
]
```
操作如下：
```Bash
>>> b in N[a]  # Neighborhood membership
True
>>> len(N[f])  # Degree
3
>>> N[a][b]    # Edge weight for (a, b)
2
```

***

### 邻接矩阵
图的另一种表示方法就是邻接矩阵了。这种表示方法的不同之处在于，它不再列出每个节点的所有邻居节点，而是会将每个节点可能的邻居位置排成一行（也就是一个数组，用于对应图中每一个节点），然后用某种值（如True或False）来表示相关节点是否为当前节点的邻居。最简单的形式是嵌套list。  

**嵌套list实现邻接矩阵**
```Python
a, b, c, d, e, f, g, h = range(8)

#     a, b, c, d, e, f, g, h
N = [[0, 1, 1, 1, 1, 1, 0, 0], # a
     [0, 0, 1, 0, 1, 0, 0, 0], # b
     [0, 0, 0, 1, 0, 0, 0, 0], # c
     [0, 0, 0, 0, 1, 0, 0, 0], # d
     [0, 0, 0, 0, 0, 1, 0, 0], # e
     [0, 0, 1, 0, 0, 0, 1, 1], # f
     [0, 0, 0, 0, 0, 1, 0, 1], # g
     [0, 0, 0, 0 ,0, 1, 1, 0]  # h
]
```
这样，我们要检查的不是$b$是否在$N[a]$中，而是检查矩阵单元$N[a][b]$是否为真，同样不能使用`len(N[a])`来获取相关邻居节点数，而是用`sum`函数。  
```Bash
>>> N[a][b]  # Neighborhood membership
1
>>> sum(N[f])  # Degree
3
```
在邻接矩阵中，只要我们不允许出现自循环状态，其对角线的值应该全为假。

将邻接矩阵扩展成允许对边进行加权处理：在原来的存储真值的地方直接存储相关权值即可。通常会将一些实际不存在的边的权值设置为无穷大。一种方法是设置为非法的权值，如`None`、-1。更常用的做法是设置一个非常大的值，通常对于整型加权值来说，用`sys.maxint`即可，对于浮点数，可以使用代表无穷大的值：`inf`（虽然该值在Python中没有直接可用的名称，但我们可以通过`float('inf')`）表达式来获取它。  

**对不存在的边赋予无限大权值的加权矩阵**
```Python
a, b, c, d, e, f, g, h = range(8)
inf = float('inf')
#       a,   b,   c,   d,   e,   f,   g,   h
W = [[  0,   1,   1,   1,   1,   1, inf, inf], # a
     [inf,   0,   1, inf,   1, inf, inf, inf], # b
     [inf, inf,   0,   1, inf, inf, inf, inf], # c
     [inf, inf, inf,   0,   1, inf, inf, inf], # d
     [inf, inf, inf, inf,   0,   1, inf, inf], # e
     [inf, inf,   1, inf, inf,   0,   1,   1], # f
     [inf, inf, inf, inf, inf,   1,   0,   1], # g
     [inf, inf, inf, inf ,inf,   1,   1,   0]  # h
]
```
成员检测可以如下实现：

```
>>> W[a][b] < inf  # Neighborhood membership
True
>>> W[c][e] < inf  # Neighborhood membership
False
>>> sum(1 for w in W[a] if w < inf) -1  # Degree
5
```

***  


### 树的实现

![tree.png](/sourcepictures/2017/04/20/tree.png)

上面的树可以表示成一个二维列表（lists of lists), 如下：
```Bash
>>> T = [['a', 'b'], ['c'], ['d', ['e', 'f']]]
>>> T[0][1]
'b'
>>> T[2][1][0]
'e'
```

在某些情况下，由于我们可以实现知道其内部节点下所能拥有的最大子节点数，所以可以选择其他表示法，如为各子节点设置相应的属性, 还可以用`None`值来表示不存在的子节点：

**二叉树类**
```Python
class Tree:
    def __init__(self, left, right):
        self.left = left
        self.right = right
```
这样一来，我们就可以像下面这样使用这个Tree类了：
```Bash
>>> t = Tree(Tree("a", "b"), Tree("c", "d"))
>>> t.right.left
'c'
```

对于没有内置list类型的语言，还有另一种常见的树实现方式，即采取“先子节点，后兄弟节点”的表示法。在这种表示法里，每一个树节点都有两个用于引用其他节点的“指针”或属性，这似乎与二叉树的情况很类似。然而在这里，第一个引用指向的是当前节点的第一个子节点，而第二个引用所指向的则是其下一个兄弟节点。这就是变成一个多路搜素树（multiway tree）。  
**多路搜索树**  
```Python
class Tree:
    def __init__(self, kids, next=None):
        self.kids = self.val = kids
        self.next = next
```
在这里，独立属性val只是为相关的值（例如“c”）提供一个更具描述性的名称。使用如下：
```Bash
>>> t = Tree(Tree("a", Tree("b", Tree("c", Tree("d")))))
>>> t.kids.next.next.val
'c'
```
此树的结构如下：  
![multiwayTree.png](/sourcepictures/2017/04/20/multiwayTree.png)

***

### Bunch模式
![Bunch_mode.png](/sourcepictures/2017/04/20/Bunch_mode.png)


***

### 子问题图（subproblem graph）

子问题图（subproblem graph）是一个非常有深度的概念，简单的说就是，绝大部分问题应该都可以被分成若干个子问题，这些子问题在结构上往往都非常相似。因此，这些结构可以被看作相关子问题图中的节点，而它们间的依赖关系就成为该图的边。尽管我们很少有机会直接在子问题图上运用相关的图算法（毕竟它更多时候只是一种概念或思考工具而已），但它往往能够提供一些非常重要的分析。例如，分治法以及动态规划等技术就出自这些分析。

图结构库，提供一些即时可用的图结构算法。推荐以下第三方库：NetworkX, python-graph, Graphine, Graph-tool, Pygr, Gato, PADS

***

### 不要对浮点数进行等值比较
不要对浮点数进行等值比较，因为通常情况下这是毫无意义的。但是有时候我们必须如此，这时，我们可以检查它们是否接近于相等。例如，我们可以采用与`unittest`模块中的`assertAlmostEqual`方法类似的思路来做到这点：
```Python
def almost_equal(x, y, places=7):
    return round(abs(x-y), places) == 0
```

```Bash
>>> print sum(0.1 for i in range(10)) == 1.0
False
```
