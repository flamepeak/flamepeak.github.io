---
title: python acm tips 20190325
date: 2019-03-25 17:42:57
tags: [Python]
categories: [Python]
---

### 收集一下使用Python做ACM的知识点

---

python的排序，sort与sorted

参考：[排序函数(sort()、sorted()、argsort()函数)](https://www.cnblogs.com/yushuo1990/p/5880041.html)

注意区别：

如果使用`list.sort()`，那么list **本身将会被修改**；然而，使用`sorted(L)`会返回一个排序之后的序列，**原序列不会被修改** .
list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。

如下：

```python
L=[9,1,2,3]

print sorted(L)
print L

print L.sort()
print L

#输出结果
[1, 2, 3, 9]
[9, 1, 2, 3]
None
[1, 2, 3, 9]
```

---

python字符串的逆序：

参考：[python对指定字符串逆序的6种方法](https://www.pythontab.com/html/2018/pythonjichu_0426/1283.html)

[关于python切片](https://blog.csdn.net/weixin_41980474/article/details/80065652)

---

python 以逗号分隔输出列表：

```python
#python3
a={1:1,2:2,3:3}
for item in a.keys():
    print(item, end=',')
```

这种写法在输出的最后有一个逗号，不符合要求，如果以空格分隔，有可能可以

```python
a={1:1,2:2,3:3} 
print ','.join(str(i) for i in sorted(a.keys()))
```

[python基础_格式化输出（%用法和format用法）](https://www.cnblogs.com/fat39/p/7159881.html)

---

`ord()`函数主要用来返回对应字符的ascii码；

`chr()`主要用来表示ascii码对应的字符


---

一个深搜的例子: 在C中可以用二维数组，在python中如何标记已访问位置？

```
下过象棋的人都知道，马只能走'日'字形（包括旋转90°的日），现在想象一下，给你一个n行m列网格棋盘，
棋盘的左下角有一匹马，请你计算至少需要几步可以将它移动到棋盘的右上角，若无法走到，则输出-1.
如n=1，m=2,则至少需要1步；若n=1，m=3,则输出-1。
```


```python

vis=dict()
dr=(1,1,-1,-1,2,2,-2,-2)
dc=(2,-2,2,-2,1,-1,1,-1)
def dfs(r,c,step):
    if(r<0 or c<0 or r>n or c>m):return
    if(step < vis.setdefault((r,c),9999999)):
        vis[(r,c)]=step
        for i in range(8):
            dfs(r+dr[i],c+dc[i],step+1)
dfs(0,0,0)
print(vis.setdefault((n,m),-1))
```

```
python中setdefault的使用
setdefault(key[, default])
If key is in the dictionary, return its value. If not, insert key with a value of default and return default. default defaults to None.

```

---



