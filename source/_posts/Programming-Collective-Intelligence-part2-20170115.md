---
title: 机器学习（二）数据聚类
date: 2017-01-15 21:24:49
tags: [笔记, Machine Learning]
categories: [人工智能]
mathjax: true
---

数据聚类(Data Clustering)是一种用以寻找紧密相关的事、人或观点，并将其可视化的方法。

聚类时常被用于数据量很大（data-intensive）的应用中。跟踪消费者购买行为的零售商们，除了利用常规的消费者投机信息外，还可以利用这些信息自动检测出具有相似购买模式的消费者群体，并开发出相应的零售或市场策略。
聚类在计量生物学领域也有大量的运用，可以用来寻找具有相似行为的基因组。

## 监督学习与无监督学习 Supervised and Unsupervised Learning
利用样本输入和期望输出来学习如何预测的技术被称为**监督学习**(Supervised Learning Methods)。其中包括：神经网络、决策树、向量支持机，以及贝叶斯过滤。采用这些方法的应用程序，会通过检查一组输入和期望的输出来进行“学习”。

聚类是**无监督学习**（Unsupervised Learning）的一个例子。与神经网络或决策树不同，无监督学习算法不是利用带有正确答案的样本数据进行“训练”。它们的目的是要在一组数据中找**寻某种结构**，而这些数据本身并不是我们要找的答案。**聚类算法的目标是采集数据，然后从中找出不同的群组。**
其它无监督学习的例子还包括**非负矩阵因式分解**(non-negative matrix factorization)和**自组织映射**(self-organizing maps)。


## 聚类

### 分级聚类 Hierarchical Clustering
分级聚类通过连续不断地将最为相似的群组两两合并，来构造出一个群组的层级结构。其中的每个群组都是从单一元素开始的。在每次迭代过程中，分级聚类算法会计算每两个群组间的距离，并将距离最近的两个群组合并成一个新的群组。这一过程会一直重复下去，直到只剩下一个群组为止。

![clustering.png](/sourcepictures/2017/01/15/clustering.png)

在上图中，元素的相似程度是通过它们的相对位置来体现的——两个元素距离越近，它们就越相似。开始时，群组内还只有一个元素。在第二布中，我们看到A和B，这两个紧靠在一起的元素，已经合并成一个新的群组，新群组所在的位置位于这两个元素的中间。第三步中，新群组又与C进行了合并。因为D和E现在是距离最近的两个元素，所以它们共同组成了一个新的群组。最后一步将两个群组合并到一起。

通常，待分级聚类完成以后，我们可以采用一种图形化的方式来展现所得的结果，这种图被称为**树状图**（dendrogram），图中显示了按层级排列的节点。

![cluster_dendrogram.png](/sourcepictures/2017/01/15/cluster_dendrogram.png)
树状图是分级聚类的一种可视化形式

树状图不仅可以利用连线来表达每个聚类的构成情况，而且还可以利用距离来体现构成聚类各元素间相隔的远近。这种图形绘制方式能够帮助我们有效的确定一个聚类中各元素间的相似程度，并以此来指示聚类的紧密程度。

加载数据文件：
```Python
def readfile(filename):
    lines = [line for line in open(filename)]

    #第一行是列标题
    colnames = line[0].strip().split('\t')[1:]
    rownames = []
    data = []
    for line in lines[1:]:
        p = line.strip().split('\t')
        #每行的第一列是行名
        rownames.append(p[0])
        #剩下部分就是该行对应的数据
        data.append([float(x) for x in p[1:]])
    return rownames, colnames, data
```

使用皮尔逊相关度定义**紧密度**（closeness），此处皮尔逊相关度的计算代码将接受两个数字列表作为参数，并返回这两个列表的相关度分值。
```Python
from math import sqrt
def pearson(v1, v2):
    #简单求和
    sum1 = sum(v1)
    sum2 = sum(v2)

    #求平方和
    sum1Sq = sum([pow(v, 2) for v in v1])
    sum2Sq = sum([pow(v, 2) for v in v2])

    #求乘积之和
    pSum = sum([v1[i]*v2[i] for i in range(len(v1))])

    #计算r (Pearson score)
    num = pSum - sum1*sum2/len(v1)
    den = sqrt((sum1Sq-pow(sum1, 2)/len(v1))*(sum2Sq-pow(sum2, 2)/len(v1)))
    if den == 0:
        return 0

    return 1.0-num/den
```
皮尔逊相关度的计算结果在两者完全匹配的情况下为1.0, 而在两者毫无关系的情况下为0.0。上面代码的最后一行，返回的是1.0减去皮尔逊相关度之后的结果，这样的目的是为了让相似度大的两个元素之间的距离变的更小。

分级聚类算法中的每一个聚类，可以是树中的枝节点，也可以是与数据集中实际数据行相对应的叶节点。每一个聚类还包含指示其位置的信息，这一信息可以是来自叶节点的行数据，也可以是来自枝节点的经合并后的数据。我们可以新建一个bicluster类，将所有这些属性存放其中，并以此来描述这棵层级树。
```Python
class bicluster:
    def __init__(self, vec, left=None, right=None, distance=0.0, id=None):
        self.left = left
        self.right = right
        self.vec = vec
        self.id = id
        self.distance = distance
```

分级聚类算法以一组对应于原始数据项的聚类开始。函数的主循环部分会尝试每一组可能的配对并计算它们的相似度，以此来找出最佳配对。最佳配对的两个聚类会被合并成一个新的聚类。新生成的聚类中所包含的数据，等于将两个旧聚类的数据求平均值后得到的结果。这一过程会一直重复下去，直到只剩下一个聚类为止。
```Python
def hcluster(rows, distance=pearson):
    distances = {}
    currentclustid = -1

    #最开始的聚类就是数据集中的行
    clust = [bicluster(rows[i], id=i) for i in range(len(rows))]

    while len(clust)>1:
        lowestpair = (0,1)
        closest = distance(clust[0].vec, clust[1].vec)

        #遍历每一个配对，寻找最小距离
        for i in range(len(clust)):
            for j in range(i+1, len(clust)):
                #用distances来缓存距离的计算值
                if (clust[i].id, clust[j].id) not in distances:
                    distances[(clust[i].id, clust[j].id)] = distance(clust[i].vec, clust[j].vec)

                d = distances[(clust[i].id, clust[j].id)]

                if d < closest:
                    closest = d
                    lowestpair = (i, j)

        #计算两个聚类的平均值
        mergevec = [(clust[lowestpair[0]].vec[i] + clust[lowestpair[1]].vec[i])/2.0 for i in range(len(clust[0].vec))]

        #建立新的聚类
        newcluster = bicluster(mergevec, left=clust[lowestpair[0]], right=clust[lowestpair[1]], distance=closest, id=currentclustid)

        #不在原始集合中的聚类，其id为负数
        currentclustid -= -1
        del clust[lowestpair[1]]
        del clust[lowestpair[0]]
        clust.append(newcluster)

    return clust[0]
```

加载文件并调用hcluster方法：
```Python
blognames, words, data = readfile('blogdata.txt')
clust = hcluster(data)
```

我们可以编写一个程序，递归遍历聚类树，并将其以类似文件系统层级结构的形式打印出来：
```Python
def printclust(clust, labels=None, n=0):
    #利用缩进来建立层级布局
    for i in range(n):
        print ' ',
    if clust.id <0:
        #负数表示这是一个分支
        print '-'
    else:
        #正数表示这是一个叶节点
        if labels ==None:
            print clust.id
        else:
            print labels[clust.id]

    #现在开始打印右侧分支和左侧分支
    if clust.left !=None:
        printclust(clust.left, labels=labels, n=n+1)
    if clust.right !=None:
        printclust(clust.right, labels=labels, n=n+1)
```
结果如图：

![printcluster.png](/sourcepictures/2017/01/15/printcluster.png)
**数据矩阵转置rotate**
```Python
#矩阵转置rotate
def rotatematrix(data):
    newdata = []
    for i in range(len(data[0])):
        newrow=[data[j][i] for j in range(len(data))]
        newdata.append(newrow)
    return newdata
```

***

### K-均值聚类 K-Means Clustering
分级聚类的结果为我们返回了一棵形象直观的树，但是这种方法有两个缺点。在没有额外投入的情况下，树形视图是不会真正将数据拆分成不同组的，而且该算法的计算量非常惊人。因为我们必须计算每两个配对项之间的关系，并且在合并项之后，这些关系还得重新再计算，所以在处理很大规模的数据集时，该算法的运行速度会非常缓慢。

除了分级聚类外，另一种可供选择的聚类方法被称为**K-均值聚类**。这种算法完全不同于分级聚类，因为我们会预先告诉算法希望生成的聚类数量，然后算法会根据数据的结构状况来确定聚类的大小。

K-均值聚类算法首先会随机确定`k`个中心位置（位于空间中代表聚类中心的点），然后将各个数据项分配给最临近的中心点。待分配完成后，聚类中心就会移到分配给该聚类的所有节点的平均位置处，然后整个分配过程重新开始。这一过程会一直重复下去，直到分配过程不再产生变化为止。下图显示了这一过程，涉及5个数据项和2个聚类。

![k_means_clustering.png](/sourcepictures/2017/01/15/k_means_clustering.png)
```Python
import random

def kcluster(rows, distance=pearson,k=4):
    #确定每个点的最小值和最大值
    ranges = [(min([row[i] for row in rows]),max([row[i] for row in rows])) for i in range(len(rows[0]))]

    #随机创建k个中心点
    clusters = [[random.random()*(ranges[i][1]-ranges[i][0])+ranges[i][0] for i in range(len(rows[0]))] for j in range(k)]

    lastmatches = None
    for t in range(100):
        print 'Iteration %d' %t
        bestmatches = [[] for i in range(k)]

        #在每一行中寻找距离最近的中心点
        for j in range(len(rows)):
            row = rows[j]
            bestmatch = 0
            for i in range(k):
                d = distance(clusters[i], row)
                if d <distance(clusters[bestmatch], row):
                    bestmatch = i
            bestmatches[bestmatch].append(j)

        #如果结果与上次相同，则整个过程结束
        if bestmatches == lastmatches:
            break
        lastmatches = bestmatches

        #把中心点移到其所有成员的平均位置处
        for i in range(k):
            avgs = [0.0]*len(rows[0])
            if len(bestmatches[i])>0:
                for rowid in bestmatches[i]:
                    for m in range(len(rows[rowid])):
                        avgs[m] += rows[rowid][m]
                for j in range(len(avgs)):
                    avgs[j] /= len(bestmatches[i])
                clusters[i] = avgs
    return bestmatches
```

上述代码在每个变量的值域范围内构造了一组聚类。当每次迭代进行的时候，算法会将每一行数据分配给某个中心点，然后再将中心点的数据更新为分配给它的所有项的平均位置。当分配情况与前一次相同时，迭代过程就结束了，同时算法会返回`k`组序列，其中每个序列代表一个聚类。与分级聚类相比，该算法为产生最终结果所须迭代的次数是非常少的。

由于函数选用的中心点作为开始，所以返回结果的顺序几乎总是不同的。根据中心点的初始位置的不同，最终聚类中所包含的内容也可能会有所不同。
