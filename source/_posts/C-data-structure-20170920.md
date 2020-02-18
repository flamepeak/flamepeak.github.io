---
title: C data structure 20170920
date: 2017-09-20 09:21:29
tags: [C]
categories: [Python]
mathjax: true
---

推荐阅读：
[数据结构与算法/leetcode/lintcode题解](https://algorithm.yuanbin.me/zh-hans/) : 很好的学习资料  

[VisuAlgo - visualising data structures and algorithms through animation](https://visualgo.net/en) : 相当碉堡的数据结构和算法可视化。

[Data Structure Visualization](http://www.cs.usfca.edu/~galles/visualization/Algorithms.html) : 相当碉堡的动画演示！！涵盖了常用的各种数据结构/排序/算法。

## Basic Data Structure

### Linked List - 链表

线性表有两种存储方式，一种是顺序存储结构，另一种是链式存储结构。我们常用的数组就是一种典型的顺序存储结构。
相反，链式存储结构就是两个相邻的元素在内存中可能不是相邻的，每一个元素都有一个指针域，指针域一般是存储着到下一个元素的指针。这种存储方式的优点是定点插入和定点删除的时间复杂度为 O(1)，不会浪费太多内存，添加元素的时候才会申请内存，删除元素会释放内存。缺点是访问的时间复杂度最坏为 O(n)。

顺序表的特性是随机读取，也就是访问一个元素的时间复杂度是O(1)。

Python:  
```Python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
```

C++
```C
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int val, ListNode *next=NULL):val(val),next(next){}
};
```

**反转链表** 

*单向链表*

链表的基本形式是：1 -> 2 -> 3 -> null，反转需要变为 3 -> 2 -> 1 -> null。这里要注意：  

+ 访问某个节点 curt.next 时，要检验 curt 是否为 null。  
+ 要把反转后的最后一个节点（即反转前的第一个节点）指向 null。  



Python：  
```Python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
    def reverse(self, head):
        prev = None
        while head:
            temp = head.next
            head.next = prev
            prev =  head
            head = temp
        return prev
```

C:  
```C
ListNode * ReverseList(ListNode *head){
    ListNode *pre=NULL, *tmp;
    while(head){
        tmp = head->next;
        head->next = pre;
        pre = head;
        head = tmp;
    }
    return pre;
};
```


*双向链表*

和单向链表的区别在于：双向链表的反转核心在于next和prev域的交换，还需要注意的是当前节点和上一个节点的递推。

Python：  
```Python
class DListNode:
    def _init__(self, val):
        self.val = val
        self.prev = self.next = null
    def reverse(self, head):
        curt = None
        while head:
            curt = head
            head = curt.next
            curt.next = curt.prev
            curt.prev = head
        return curt
```

**Dummy Node**

Dummy node 是链表问题中一个重要的技巧，中文翻译叫“哑节点”或者“假人头结点”。  
Dummy node 是一个虚拟节点，也可以认为是标杆节点。Dummy node 就是在链表表头 head 前加一个节点指向 head，即 dummy -> head。Dummy node 的使用多针对单链表没有前向指针的问题，保证链表的 head 不会在删除操作中丢失。除此之外，还有一种用法比较少见，就是使用 dummy node 来进行head的删除操作，比如 Remove Duplicates From Sorted List II，一般的方法current = current.next 是无法删除 head 元素的，所以这个时候如果有一个dummy node在head的前面。  
所以，当链表的 head 有可能变化（被修改或者被删除）时，使用 dummy node 可以很好的简化代码，最终返回 dummy.next 即新的链表。


**快慢指针**

快慢指针也是一个可以用于很多问题的技巧。所谓快慢指针中的快慢指的是指针向前移动的步长，每次移动的步长较大即为快，步长较小即为慢，常用的快慢指针一般是在单链表中让快指针每次向前移动2，慢指针则每次向前移动1。快慢两个指针都从链表头开始遍历，于是快指针到达链表末尾的时候慢指针刚好到达中间位置，于是可以得到中间元素的值。快慢指针在链表相关问题中主要有两个应用：  

+ 快速找出未知长度单链表的中间节点 设置两个指针 `*fast`、`*slow` 都指向单链表的头节点，其中`*fast`的移动速度是`*slow`的2倍，当`*fast`指向末尾节点的时候，slow正好就在中间了。　　
+ 判断单链表是否有环 利用快慢指针的原理，同样设置两个指针 `*fast`、`*slow` 都指向单链表的头节点，其中 `*fast`的移动速度是`*slow`的2倍。如果 `*fast = NULL`，说明该单链表 以 NULL结尾，不是循环链表；如果 `*fast = *slow`，则快指针追上慢指针，说明该链表是循环链表。

Python:  
```Python
class NodeCircle:
    def __init__(self, val):
        self.val = val
        self.next = None
    def has_circle(self, head):
        slow = head
        fast = head
        while (slow and fast):
            fast = fast.next
            slow = slow.next
            if fast:
                fast = fast.next
            if fast == slow:
                break
        if fast and slow and (fast==slow):
            return True
        else:
            return False
```

### Binary Tree - 二叉树

二叉树是每个节点最多有两个子树的树结构，子树有左右之分，二叉树常被用于实现**二叉查找树**和**二叉堆**。

二叉树的第$i$层至多有 $2^{i-1}$ 个结点；深度为k的二叉树至多有 $2^k-1$个结点；  
对任何一棵二叉树T，如果其终端结点数为 $n_0$​​ , 度为2的结点数为 $n_2$​​ , 则 $n_0=n_2+1$。因为度为1的节点对度为0的节点数目不会有影响，而每增加一个度为2的节点总的来说则会相应增加一个度为0的节点。

一棵深度为 $k$, 且有 $2^k-1$个节点称之为**满二叉树**；  
深度为 $k$，有 $n$ 个节点的二叉树，当且仅当其每一个节点都与深度为 $k$ 的满二叉树中序号为 $1$ 至 $n$ 的节点对应时，称之为**完全二叉树**。完全二叉树中重在节点标号对应。

Python:  
```Python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
```

C:  
```C
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x): val(x), left(NULL),right(NULL);
};
```

**树的遍历**

从二叉树的根节点出发，节点的遍历分为三个主要步骤：对当前节点进行操作（称为“访问”节点，或者根节点）、遍历左边子节点、遍历右边子节点。

需要注意的是树的遍历通常使用递归的方法进行理解和实现，在访问元素时也需要使用递归的思想去理解。实际实现中对于前序和中序遍历可尝试使用递归实现。

按照访问根元素(当前元素)的前后顺序，遍历方式可划分为如下几种：

+ 深度优先：先访问子节点，再访问父节点，最后访问第二个子节点。根据根节点相对于左右子节点的访问先后顺序又可细分为以下三种方式。
  1. 前序(pre-order)：先根后左再右
  2. 中序(in-order)：先左后根再右
  3. 后序(post-order)：先左后右再根
+ 广度优先：先访问根节点，沿着树的宽度遍历子节点，直到所有节点均被访问为止。

![binary_tree_traversal.png](/sourcepictures/2017/09/20/binary_tree_traversal.png)

二叉树的广度优先遍历和树的前序/中序/后序遍历不太一样，前/中/后序遍历使用递归，也就是**栈的思想**对二叉树进行遍历，广度优先一般使用**队列的思想**对二叉树进行遍历。

如果已知中序遍历和前序遍历或者后序遍历，那么就可以完全恢复出原二叉树结构。其中最为关键的是前序遍历中第一个一定是根，而后序遍历最后一个一定是根，中序遍历在得知根节点后又可进一步递归得知左右子树的根节点。但是这种方法也是有适用范围的：元素不能重复！否则无法完成定位。

Python:  
```Python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None

class Traversal(object):
    def __init__(self):
        self.traverse_path = list()
    def preorder(self, root):
        if root:
            self.traverse_path.append(root.val)
            self.preorder(root.left)
            self.preorder(root.right)
    def inorder(self, root):
        if root:
            self.inorder(root.left)
            self.traverse_path.append(root.val)
            self.inorder(root.right)
    def postorder(self, root):
        if root:
            self.postorder(root.left)
            self.postorder(root.right)
            self.traverse_path.append(root.val)
```

*树类题的复杂度分析*  
对树相关的题进行复杂度分析时可统计对每个节点被访问的次数，进而求得总的时间复杂度。

**Binary Search Tree - 二叉查找树**

一颗**二叉查找树(BST)**是一颗二叉树，其中每个节点都含有一个可进行比较的键及相应的值，且每个节点的键都**大于等于左子树中的任意节点的键**，而**小于右子树中的任意节点的键**。
使用中序遍历可得到有序数组，这是二叉查找树的又一个重要特征。   
二叉查找树使用的每个节点含有两个链接，它是将链表插入的灵活性和有序数组查找的高效性结合起来的高效符号表实现。



