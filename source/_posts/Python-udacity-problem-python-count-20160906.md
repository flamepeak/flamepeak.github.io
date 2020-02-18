---
title: 优达学城：用Python统计字数
date: 2016-09-06 18:35:31
tags: Python
categories: [Python]
---

学习优达学城的机器学习课程，遇到测试题目，用Python统计数字，好久没有写python程序，然后就大致写了一下。
### 题目
**问题**
用 Python 实现函数 count_words()，该函数输入字符串 s 和数字 n，返回 s 中 n 个出现频率最高的单词。返回值是一个元组列表，包含出现次数最高的 n 个单词及其次数,即 [(<单词1>, <次数1>), (<单词2>, <次数2>), ... ]，按出现次数降序排列。
您可以假设所有输入都是小写形式，并且不含标点符号或其他字符（只包含字母和单个空格）。如果出现次数相同，则按字母顺序排列。
例如：
```python
print count_words("betty bought a bit of butter but the butter was bitter",3)
```
输出：
```python
[('butter', 2), ('a', 1), ('betty', 1)]
```

---

### 拙作:
``` Python
def count_words(s, n):
    s_list = s.split()
    s_dict = {}
    for s_per in s_list:
        if s_dict.has_key(s_per):
            s_dict[s_per] = s_dict[s_per]+1
        else:
            s_dict[s_per] = 1

    s_dict_list = sorted(s_dict.iteritems(), key = lambda x:x[1], reverse = True)
    i = s_dict_list[0][1]
    dest=[]
    while i>0:
        temp =[]
        for s_per_ed in s_dict_list:
            if s_per_ed[1]==i:
                temp.append(s_per_ed)        
        dest.extend(sorted(temp))
        i=i-1
    return dest[:n]
```


09.08
今日看到collections，所以就用collections写了一个简单的版本：
``` Python
import collections
def count_words(s, n):
    cnt = collections.Counter(s.split()).most_common()

    i = cnt[0][1]
    dest=[]
    while i>0:
        temp =[]
        for s_per_ed in cnt:
            if s_per_ed[1]==i:
                temp.append(s_per_ed)        
        dest.extend(sorted(temp))
        i=i-1
    return dest[:n]
```
