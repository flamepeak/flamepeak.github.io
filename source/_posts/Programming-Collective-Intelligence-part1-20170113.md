---
title: 机器学习（一）推荐
date: 2017-01-13 08:53:11
tags: [笔记, Machine Learning]
categories: [人工智能]
mathjax: true
---

本文主要基于《集体智慧编程》第二章，并对其进行了拓展。

集体智慧：为了创造新的想法，而将一群人的行为、偏好或思想组合在一起。

## 什么是机器学习Machine Learning
机器学习是人工智能（AI， artificial intelligence）领域与算法相关的一个子域，它允许计算机不断地进行学习。大多数情况下，这相当于将一组数据传递给算法，并由算法推断出与这些数据的属性相关的信息——借助这些信息，算法就能够预测出未来有可能会出现的其他数据。这种预测是完全有可能的，因为几乎所有的非随机数据中，都会包含这样或那样的“模式（pattern）”，这些模式的存在使得机器得以据此进行归纳。为了实现归纳，机器会利用它所认定的出现于数据中的重要特征对数据进行“训练“，并借此得到一个模型。

## 提供推荐 Making Recommendation
### 协作型过滤 Collaborative filtering
一个协作型过滤算法通常的做法是对一大群人进行搜索，并从中找出与我们相近的一小群人。算法会对这些人所偏爱的其它内容进行考查，并将它们组合起来构造出一个经过排名的推荐列表。

如何寻找紧密相关的事物，找到有相同品味的人？给他们推荐他们喜欢或者需要的东西

我们需要有一种方法来确定人们在品味方面的相似程度，为此，我们可以将每个人与所有其他人进行对比，并计算他们的相似度评价值。主要介绍两种相似度评价值的体系：**欧几里德距离**和**皮尔逊相关度**。

### 欧几里德距离评价 Euclidean Distance Score
计算相似度评价值的一个非常简单的方法是使用欧几里德距离评价方法，它以经过人们一致评价的物品为坐标轴，然后将参与评价的人绘制到图上，并考查他们彼此之间的距离远近。
```Python
#返回一个有关person1和person2的基于距离的相似度评价
def sim_distance(prefs, person1, person2):
    si = {}
    for item in prefs[person1]:
        if item in prefs[person2]:
            si[item] = 1
    #如果person1与person2没有相同之处，返回0
    if len(si) ==0:
        return 0
    #计算所有差值的平方和
    sum_of_squares = sum(pow(prefs[person1][item]-prefs[person2][item], 2) for item in si.keys())
    return 1/(1+sqrt(sum_of_squares))
```

### 皮尔逊相关度评价 Pearson Correlation Score
皮尔逊相关系数是判断两组数据与某一直线（其绘制原则是尽可能的靠近图上所有的坐标点，故被称为最佳拟合线best-fit line）拟合程度的一种度量。它的公式比欧几里德距离评价的计算公式要复杂，但是它在数据不是很规范（normalized）的时候，会倾向于给出更好的结果。

皮尔逊相关系数就是：
$$
\frac{x和y的协方差}{(x的标准差 * y的标准差)}
$$
判断两组数的线性关系程度。

![pear.gif](/sourcepictures/2017/01/13/pear.gif)

皮尔逊相关度的计算结果在两者完全匹配的情况下为1.0, 而在两者毫无关系的情况下为0.0。

如果某人总是倾向与给出比另一个人更高的分值，而二者的分值之差又始终保持一致，则他们依然可能存在很好的相关性。欧几里德距离评价方法会因为一个人的评价始终比另一个人的更为“严格”，而得出两者不相近的结论，即使他们的品味很相似。

皮尔逊相关度评价算法首先会找出两位评论者都曾评价过的物品，然后计算两者的评分总和与平方和，并求得评分的乘积之和。最后，算法利用这些计算结果计算出皮尔逊相关系数。不同于距离度量法，这一公式不是非常直观，但是通过除以将所有变量的变化值相乘后得到的结果，它的确能够告诉我们变量的总体变化情况。

``` Python
# Input: 2 objects
# Output: Pearson Correlation Score
def pearson_correlation(object1, object2):
    values = range(len(object1))

    # Summation over all attributes for both objects
    sum_object1 = sum([float(object1[i]) for i in values])
    sum_object2 = sum([float(object2[i]) for i in values])

    # Sum the squares
    square_sum1 = sum([pow(object1[i],2) for i in values])
    square_sum2 = sum([pow(object2[i],2) for i in values])

    # Add up the products
    product = sum([object1[i]*object2[i] for i in values])

    #Calculate Pearson Correlation score
    numerator = product - (sum_object1*sum_object2/len(object1))
    denominator = ((square_sum1 - pow(sum_object1,2)/len(object1)) * (square_sum2 -
    	pow(sum_object2,2)/len(object1))) ** 0.5

    # Can"t have division by 0
    if denominator == 0:
        return 0

    result = numerator/denominator
    return result
```
或者：
```Python
#返回p1和p2的皮尔逊相关系数
def sim_pearson(prefs, p1, p2):
    #得到双方都评价过的物品
    si = {}
    for item in prefs[p1]:
        if item in prefs[p2]:
            si[item] = 1
    #得到列表元素的个数
    n = len(si)
    # 如果两者没有共同之处，则返回1
    if n==0:
        return 1
    # 对所有偏好求和
    sum1 = sum(prefs[p1][it] for it in si)
    sum2 = sum(prefs[p2][it] for it in si)
    #求平方和
    sum1Sq = sum(pow(prefs[p1][it], 2) for it in si)
    sum2Sq = sum(pow(prefs[p2][it], 2) for it in si)
    #求乘积之和
    pSum = sum(prefs[p1][it]*prefs[p2][it] for it in si)
    #计算pearson
    num = pSum - sum1*sum2/n
    den = sqrt((sum1Sq - pow(sum1,2)/n) * (sum2Sq - pow(sum2,2)/n))
    r = num/den
    return r
```
**推荐物品** Recommendation Items

使用一个经过加权的评价值来为影片打分，即用户相似度*评分,使得那些相似度高的人对整体评价值有更多的贡献。
```Python
#利用所有他人评价值的加权平均，为某人提供建议
def getRecommendations(prefs, person, similarity=sim_pearson):
    totals= {}
    simSums = {}
    for other in prefs:
        #不要和自己比较
        if other == person:
            continue
        sim = similarity(prefs, person, other)

        #忽略评价值小于0的情况
        if sim <=0:
            continue
        for item in prefs[other]:
            #只对自己未看过的影片进行评价
            if item not in prefs[person] or prefs[person][item]==0:
                #相似度×评价值
                totals.setdefault(item, 0)
                totals[item] += sim*prefs[other][item]
                #相似度之和
                simSums.setdefault(item, 0)
                simSums[item] += sim
    #建立一个归一化的列表
    rankings = [(total/simSums[item], item) for item, total in totals.items()]    

    #返回经过排序的列表
    rankings.sort()
    rankings.reverse()
    return rankings
```
上述代码循环便利所有位于字典prefs中的其他人，针对每一次循环，它会计算由person参数所制定的人与这些人的相似度。然后它会循环遍历所有打过分的项，用每一项的评价值乘以相似度，并将所得结果累加起来。最后，我们将每个总计值除以相似度之和，借此对评价值进行归一化处理，然后返回一个经过排序的结果。



### Jaccard / Tanimoto Coefficient
In some case, each attribute is binary such that each bit represents the absence of presence of a characteristic, thus, it is better to determine the similarity via the overlap, or intersection, of the sets.

Simply put, the Tanimoto Coefficient uses the ratio of the intersecting set to the union set as the measure of similarity. Represented as a mathematical equation:

Tanimoto Coefficient主要用于计算符号度量或布尔值度量的个体间的相似度，因为个体的特征属性都是由符号度量或者布尔值标识，因此无法衡量差异具体值的大小，只能获得“是否相同”这个结果，所以Tanimoto Coefficient只关心个体间共同具有的特征是否一致这个问题。Tanimoto Coefficient又被叫做Jaccard Coefficient，其值等于两个用户共同关联（不管喜欢还是不喜欢）的物品数量除于两个用户分别关联的所有物品数量。
$$
Jaccard(X, Y) = \frac{X \cap Y}{X \cup Y}
$$

$$
T(a,b) = \frac{N_c}{N_a + N_b - N_c}
$$

In this equation, N represents the number of attributes in each object (a,b). C in this case is the intersection set.

```Python
# Inputs: two lists
# Output: the Tanimoto Coefficient
def tanimoto (list1, list2):
  intersection = [common_item for common_item in list1 if common_item in list2]
  return float(len(intersection))/(len(list1) + len(list2) - len(intersection))
```

### 余弦相似度 Cosine Similarity
This metric is frequently used when trying to determine similarity between two documents.

In this similarity metric, the attributes (or words, in the case of the documents) is used as a vector to find the normalized dot product of the two documents. By determining the cosine similarity, the user is effectively trying to find cosine of the angle between the two objects. For cosine similarities resulting in a value of 0, the documents do not share any attributes (or words) because the angle between the objects is 90 degrees.
Expressed as a mathematical equation:

$$
similarity(x,y) = \cos(\theta) = \frac{x \cdot y}{\left \| x \right \| * \left \| y \right \| } = \frac{\sum\limits_{i=1}^n A_i B_i}{\sqrt{\sum\limits_{i=1}^n A_i^2} \sqrt{\sum\limits_{i=1}^n B_i^2}}
$$

余弦相似度（cosine similarity）是资讯检索中常用的相似度计算方式，可用来计算文件之间的相似度，也可以计算词汇之间的相似度，更可以计算查询字串与文件之间的相似度。
此相似度将向量长度（即文件长度）正规化成长度为1.0的向量，因此文件的长短对相似度的影响不大，而是维度的数值（亦即词汇的权重）计算方式，影响较大。常用的词汇权重计算方式，为词频与逆向文件篇数的乘积，亦即TF*IDF。
