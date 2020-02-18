---
title: Python Algorithms 读书笔记 递归
date: 2017-04-24 15:44:23
tags: [笔记, Python]
categories: [Python]
mathjax: true
---


### 插入排序
假设前$n-1$个元素已经完成排序了，现在要将第$n$个元素插入到正确的位置，这种方式称为插入排序法（insertion sort）。

**递归版的插入排序**
```Python
def ins_sort_rec(seq, i):
    if i==0:
        return
    ins_sort_rec(seq, i-1)
    j = i
    while j>0 and seq[j-1]>seq[j]:
        seq[j-1], seq[j] = seq[j], seq[j-1]
        j -= 1
```


**插入排序**
```Python
def ins_sort(seq):
    for i in range(1, len(seq)):
        j = i
        while j>0 and seq[j-1]>seq[j]:
            seq[j-1], seq[j] = seq[j], seq[j-1]
            j -= 1
```


### 选择排序
先找到最大的元素，并将其放到$n$的位置上，然后继续递归排序剩下的元素，被称为选择排序法（selection sort）。

**递归版的选择排序**
```Python
def sel_sort_rec(seq, i):
    if i==0:return
    max_j = i                                # Idx. of largest value so far
    for j in range(i):                       # Look for a larger value
        if seq[j] > seq[max_j]: max_j = j    # Found one? Update max_j
    seq[i], seq[max_j] = seq[max_j], seq[i]  # Switch largest into place
    sel_sort_rec(seq, i-1)                   # Sort 0..i-1
```

**选择排序**
```Python
def sel_sort(seq):
    for i in range(len(seq)-1, 0, -1):       # n..i+i sorted so far
        max_j = i                            # Idx. of largest value so far
        for j in range(i):                   # Look for a larger value
            if seq[j] > seq[max_j]: max_j = j  # Found one? Update max_j
        seq[i], seq[max_j] = seq[max_j], seq[i]   #Switch largest into place
```

两者都是先找出最大元素，并将其交换到序列尾端。


### 明星问题
所谓明星问题，就是要在人群中找出一位明星人士。它被用于社交网络的分析中，比如Facebook、Twitter。问题的思路是这样的：该明星不认识人群中的其他人，但人人都认识这位明星。它的核心表现形式是一个图结构，我们要寻找的是一个其他所有节点对它都有入边，但它自身却没有出边的节点。

**朴素版的明星问题方案**
```Python
def naive_celeb(G):
    n = len(G)
    for u in range(n):              # For every candidate...
        for v in range(n):          # For everyone else...
            if u ==v :continue      # Same person? Skip
            if G[u][v]:break        # Candidate knows other
            if not G[v][u]: break   # Other doesn't know candidate
        else:
          return u                  # No breaks? Celebrity
    return None                     # Couldn't find anyone
```


**明星问题的解决方案**
```Python
def celeb(G):
    n = len(G)
    u, v = 0, 1                 # The first two
    for c in range(2, n+1):     # Others to check
      if G[u][v]:               # u knows v? Replace u
          u = c                 
      else:                     # Ohterwise, replace v
          v = c                 
    if u == n:                  # u was replaced last; use v
        c = v                   
    else:                       
        c = u                   # Otherwise, u is a candidate
    for v in range(n):          # For everyone else...
        if c == v: continue     # Same person? Skip
        if G[c][v]: break       # Candidate knows other
        if not G[v][c]: break   # Other doesn't know candidate
    else:
        return c                # No breaks? Celebrity
    return None                 # Couldn't find anyone
```


### 拓扑排序(topological sorting)问题
拓扑排序问题往往出现在一些中等复杂程度的计算系统中，这些系统往往会有一堆事情要做，而这些事又依赖于另外一些事。。。所以从哪里开始是个问题。

**有向无环路图（DAG）**
![DAG](/sourcepictures/2017/04/24/DAG.png)

**朴素版的拓扑排序法**
```Python
def naive_topsort(G, S=None):
    if S is None: S = set(G)        # Default: All nodes
    if len(S) == 1: return list(S)  # Base case, single node
    v = S.pop()                     # Reduction: Remove a node
    seq = naive_topsort(G, S)       # Recursion (assumption), n-1
    min_i = 0
    for i, u in enumerate(seq):
        if v in G[u]: min_i = i+1   # After all dependencies
    seq.insert(min_i, v)
    return seq    
```

**有向无环路图的拓扑排序**
```Python
def top_sort(G):
    count = dict((u，0) for u in G)      # The in-degree for each node
    for u in G:
        for v in G[u]:
            count[v] += 1                # Count every in-edge
    Q = [u for u in G if count[u] == 0]  # Valid initial nodes
    S = []                               # The result
    while Q:                             # While we have start nodes...
        u = Q.pop()                      # Pick one
        S.append(u)                      # Use it as first of the rest
        for v in G[u]:
            count[v] -= 1                # "Uncount" its out-edges
            if count[v] == 0:            # New valid start nodes?
                Q.append(v)              # Deal with them next
    return S
```

***

### 遍历
