---
title: 排序算法的Python实现
date: 2017-02-13 19:29:37
tags: [算法, Python]
categories: [Python]
mathjax: true
---

很多资源参考自：[https://github.com/hustcc](https://github.com/hustcc)，在此致谢！

### 分类
+ 计算的时间复杂度（最差、平均、和最好性能），依据列表（list）的大小($n$)。一般而言，好的性能是$O(n log n)$，且坏的性能是$O(n^2)$。对于一个排序理想的性能是$O(n)$。仅使用一个抽象关键比较运算的排序算法总平均上总是至少需要$O(n log n)$。
+ 存储器使用量（以及其他电脑资源的使用）
+ 稳定性：稳定排序算法会让原本有相等键值的纪录维持相对次序。也就是如果一个排序算法是稳定的，当有两个相等键值的纪录$R$和$S$，且在原本的列表中R出现在$S$之前，在排序过的列表中$R$也将会是在$S$之前。
+ 依据排序的方法：插入、交换、选择、合并等等。

### 简要比较


### 冒泡排序 Bubble Sort
冒泡排序（Bubble Sort）也是一种简单直观的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

作为最简单的排序算法之一，冒泡排序给我的感觉就像 Abandon 在单词书里出现的感觉一样，每次都在第一页第一位，所以最熟悉。冒泡排序还有一种优化算法，就是立一个 flag，当在一趟序列遍历中元素没有发生交换，则证明该序列已经有序。但这种改进对于提升性能来说并没有什么太大作用。

#### 算法步骤
1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

#### 动画演示
![Bubble Sort](/sourcepictures/2017/02/13/bubbleSort.gif)

#### Python代码
```Python
def bubbleSort(arr) :
    for i in range(1,len(arr)):
        for j in range(0,len(arr)-1):
            if arr[j]>arr[j+1]:
                arr[j], arr[j+1]= arr[j+1],arr[j]
    return arr
```

### 选择排序 Selection Sort
选择排序是一种简单直观的排序算法，是表现最稳定的排序算法之一，因为无论什么数据进去都是 $O(n^2)$ 的时间复杂度。所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。

#### 算法步骤
1. 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置
2. 再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
3. 重复第二步，直到所有元素均排序完毕。

#### 动画演示
![selectionSort](/sourcepictures/2017/02/13/selectionSort.gif)

#### Python代码
```Python
def selectionSort(arr):
    for i in range(len(arr)-1):
        for j in range(i+1, len(arr)):
            if arr[i]>arr[j]:
                arr[i],arr[j]=arr[j],arr[i]
    return arr
```


### 插入排序 Insertion Sort
插入排序的代码实现虽然没有冒泡排序和选择排序那么简单粗暴，但它的原理应该是最容易理解的了，因为只要打过扑克牌的人都应该能够秒懂。插入排序是一种最简单直观的排序算法，它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从**后向前扫描**，找到相应位置并插入。
插入排序和冒泡排序一样，也有一种优化算法，叫做拆半插入。

#### 算法步骤
1. 将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列;
2. 从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。（如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。）

#### 动画演示
![insertionSort](/sourcepictures/2017/02/13/insertionSort.gif)

#### Python代码
```Python
def insertionSort(arr):
    for i in range(1,len(arr)):
        preIndex = i-1
        currentValue = arr[i]
        while preIndex >= 0 and arr[preIndex] > currentValue:
            arr[preIndex+1] = arr[preIndex]
            preIndex -= 1
        arr[preIndex+1]=currentValue
    return arr
```


### 希尔排序 Shell Sort
希尔排序，也称递减增量排序算法，是插入排序的一种更高效的改进版本。但希尔排序是非稳定排序算法。

希尔排序是基于插入排序的以下两点性质而提出改进方法的：
+ 插入排序在对几乎已经排好序的数据操作时，效率高，即可以达到线性排序的效率；
+ 但插入排序一般来说是低效的，因为插入排序每次只能将数据移动一位；

希尔排序的基本思想是：先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录“基本有序”时，再对全体记录进行依次直接插入排序。

希尔排序通过将比较的全部元素分为几个区域来提升插入排序的性能。这样可以让一个元素可以一次性地朝最终位置前进一大步。然后算法再取越来越小的步长进行排序，算法的最后一步就是普通的插入排序，但是到了这步，需排序的数据几乎是已排好的了（此时插入排序较快）。
假设有一个很小的数据在一个已按升序排好序的数组的末端。如果用复杂度为$O(n^2)$的排序（冒泡排序或插入排序），可能会进行$n$次的比较和交换才能将该数据移至正确位置。而希尔排序会用较大的步长移动数据，所以小数据只需进行少数比较和交换即可到正确位置。

步长的选择是希尔排序的重要部分。只要最终步长为1任何步长序列都可以工作。算法最开始以一定的步长进行排序。然后会继续以一定步长进行排序，最终算法以步长为1进行排序。当步长为1时，算法变为插入排序，这就保证了数据一定会被排序。

Donald Shell最初建议步长选择为 ${\frac {n}{2}}$ 并且对步长取半直到步长达到1. 已知的最好步长序列是由Sedgewick提出的$(1, 5, 19, 41, 109,...)$，该序列的项来自 $9\times 4^{i}-9\times 2^{i}+1$ 和 $ 2^{i+2}\times (2^{i+2}-3)+1$ 这两个算式.这项研究也表明“比较在希尔排序中是最主要的操作，而不是交换。”用这样步长序列的希尔排序比插入排序要快，甚至在小数组中比快速排序和堆排序还快，但是在涉及大量数据时希尔排序还是比快速排序慢。

#### 算法步骤
1. 选择一个增量序列 $t_1$，$t_2$，……，$t_k$，其中 $t_i$ > $t_j$, $t_k$ = 1；
2. 按增量序列个数 $k$，对序列进行 $k$ 趟排序；
3. 每趟排序，根据对应的增量 $t_i$，将待排序列分割成若干长度为 $m$ 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

#### 动画演示
![Sorting_shellsort_anim](/sourcepictures/2017/02/13/Sorting_shellsort_anim.gif)

#### Python代码
```Python
#希尔排序
def shellSort(list):
    n = len(list)
    #初始步长
    gap = round(n / 2)
    while gap > 0:
        for i in range(gap, n):
            #每个步长进行插入排序
            temp = list[i]
            j = i
            #插入排序
            while j >= gap and list[j-gap] > temp:
                list[j] = list[j-gap]
                j -= gap
            list[j] = temp
        #得到新步长
        gap = round(gap / 2)
    return list
```


### 归并排序 Merge Sort
归并操作（merge），也叫归并算法，指的是将两个已经排序的序列合并成一个序列的操作。归并排序算法依赖归并操作。

**迭代法**
+ 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
+ 设定两个指针，最初位置分别为两个已经排序序列的起始位置
+ 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
+ 重复步骤3直到某一指针到达序列尾
+ 将另一序列剩下的所有元素直接复制到合并序列尾

**递归法**
原理如下（假设序列共有$n$个元素）：
+ 将序列每相邻两个数字进行归并操作，形成$floor(n/2)$个序列，排序后每个序列包含两个元素
+ 将上述序列再次归并，形成 $floor(n/4)$个序列，每个序列包含四个元素
+ 重复步骤2，直到所有元素排序完毕

#### 动画演示
![Merge-sort-example.gif](/sourcepictures/2017/02/13/Merge-sort-example.gif)

![mergeSort.gif](/sourcepictures/2017/02/13/mergeSort.gif)

#### Python代码
```Python
#归并排序
from collections import deque

def merge(left, right):
    merged, left, right = deque(), deque(left), deque(right)
    while left and right:
        merged.append(left.popleft() if left[0]<=right[0] else right.popleft())
    merged.extend(right if right else left)
    return list(merged)

def mergeSort(lst):
    if len(lst) <=1:
        return lst
    middle = int(len(lst)//2)
    left = mergeSort(lst[:middle])
    right = mergeSort(lst[middle:])
    return merge(left, right)
```


### 快速排序 Quick Sort
快速排序（Quicksort），又称划分交换排序（partition-exchange sort），一种排序算法，最早由东尼·霍尔提出。在平均状况下，排序$n$个项目要$Ο(n log n)$次比较。在最坏状况下则需要$Ο(n^2)$次比较，但这种状况并不常见。事实上，快速排序通常明显比其他$Ο(n log n)$算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。

快速排序使用分治法（Divide and conquer）策略来把一个序列（list）分为两个子序列（sub-lists）。
步骤为：
1. 从数列中挑出一个元素，称为"基准"（pivot），
2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

#### 动画演示
![quickSort.gif](/sourcepictures/2017/02/13/quickSort.gif)

![Sorting_quicksort_anim.gif](/sourcepictures/2017/02/13/Sorting_quicksort_anim.gif)

#### Python代码
```Python
#快速排序
def quickSort(lst):
    if len(lst)<=1:
        return lst
    return quickSort([li for li in lst[1:] if li<lst[0]]) + [lst[0]] + quickSort([li for li in lst[1:] if li>=lst[0]])
```


### 堆排序 Heap Sort
堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

堆排序就是把最大堆堆顶的最大数取出，将剩余的堆继续调整为最大堆，再次将堆顶的最大数取出，这个过程持续到剩余数只有一个时结束。

**堆节点的访问**
通常堆是通过一维数组来实现的。在数组起始位置为0的情形中：
+ 父节点i的左子节点在位置(2*i+1);
+ 父节点i的右子节点在位置(2*i+2);
+ 子节点i的父节点在位置floor((i-1)/2);

**堆的操作**
在堆的数据结构中，堆中的最大值总是位于根节点(在优先队列中使用堆的话堆中的最小值位于根节点)。堆中定义以下几种操作：
+ 最大堆调整（Max_Heapify）：将堆的末端子节点作调整，使得子节点永远小于父节点
+ 创建最大堆（Build_Max_Heap）：将堆所有数据重新排序
+ 堆排序（HeapSort）：移除位在第一个数据的根节点，并做最大堆调整的递归运算

参见：[常见排序算法 - 堆排序 (Heap Sort)](http://bubkoo.com/2014/01/14/sort-algorithm/heap-sort/)

#### 动画演示
![Sorting_heapsort_anim.gif](/sourcepictures/2017/02/13/Sorting_heapsort_anim.gif)

![heapSort.gif](/sourcepictures/2017/02/13/heapSort.gif)

#### Python代码
```Python
def heapSort(lst):
    def shift_down(start, end):
        '''最大堆调整'''
        root = start
        while True:
            child = 2*root + 1
            if child > end:
                break;
            if child + 1 <= end and lst[child] < lst[child + 1]:
                child += 1
            if lst[root] < lst[child]:
                lst[root], lst[child] = lst[child], lst[root]
                root = child
            else:
                break
    #创建最大堆
    for start in range((len(lst)-2)//2, -1, -1):
        shift_down(start, len(lst) -1)

    #堆排序
    for end in range(len(lst)-1, 0, -1):
        lst[0], lst[end] = lst[end], lst[0]
        shift_down(0, end-1)
    return lst
```


### 计数排序Counting sort
计数排序（Counting sort）是一种稳定的线性时间排序算法。计数排序使用一个额外的数组C，其中第i个元素是待排序数组A中值等于i的元素的个数。然后根据数组C来将A中的元素排到正确的位置。

计数排序的核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

当输入的元素是$n$个$0$到$k$之间的整数时，它的运行时间是$Θ(n + k)$。计数排序不是比较排序，排序的速度快于任何比较排序算法。

由于用来计数的数组C的长度取决于待排序数组中数据的范围（等于待排序数组的最大值与最小值的差加上1），这使得计数排序对于数据范围很大的数组，需要大量时间和内存。例如：计数排序是用来排序0到100之间的数字的最好的算法，但是它不适合按字母顺序排序人名。但是，计数排序可以用在基数排序算法中，能够更有效的排序数据范围很大的数组。

通俗地理解，例如有10个年龄不同的人，统计出有8个人的年龄比A小，那A的年龄就排在第9位，用这个方法可以得到其他每个人的位置，也就排好了序。当然，年龄有重复时需要特殊处理（保证稳定性），这就是为什么最后要反向填充目标数组，以及将每个数字的统计减去1的原因。算法的步骤如下：
+ 找出待排序的数组中最大和最小的元素
+ 统计数组中每个值为$i$的元素出现的次数，存入数组 C 的第 $i$ 项
+ 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
+ 反向填充目标数组：将每个元素$i$放在新数组的第$C(i)$项，每放一个元素就将$C(i)$减去1

#### 动画演示
![countingSort.gif](/sourcepictures/2017/02/13/countingSort.gif)

#### Python代码
```Python
# 计数排序
def countingSort(lst):
   bucketLen = max(lst) + 1 #maxValue
   bucket = [0] * bucketLen
   sortedIndex = 0
   lstLen = len(lst)
   for i in range(len(lst)):
       if not bucket[lst[i]]:
           bucket[lst[i]] = 0
       bucket[lst[i]] += 1
   for j in range(bucketLen):
       while bucket[j] > 0:
           lst[sortedIndex] = j
           sortedIndex += 1
           bucket[j] -= 1
   return lst
```


### 桶排序 Bucket Sort
工作的原理是将数组分到有限数量的桶里。每个桶再个别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排序）。桶排序是鸽巢排序的一种归纳结果。当要被排序的数组内的数值是均匀分配的时候，桶排序使用线性时间$Θ(n)$。但桶排序并不是比较排序，他不受到$O(n log n)$下限的影响。

桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。为了使桶排序更加高效，我们需要做到这两点：
+ 在额外空间充足的情况下，尽量增大桶的数量
+ 使用的映射函数能够将输入的 $N$ 个数据均匀的分配到 $K$ 个桶中

同时，对于桶中元素的排序，选择何种比较排序算法对于性能的影响至关重要。

桶排序以下列程序进行：
+ 设置一个定量的数组当作空桶子。
+ 寻访序列，并且把项目一个一个放到对应的桶子去。
+ 对每个不是空的桶子进行排序。
+ 从不是空的桶子里把项目再放回原来的序列中。
