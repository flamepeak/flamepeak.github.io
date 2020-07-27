---
title: Hexo 主题测试
date: 2017-01-24 15:09:04
tags: [Hexo]
categories: [Web前端]
mathjax: true
---

# Heading 1

## Heading 2

### Heading 3

#### Heading 4

##### Heading 5

###### Heading 6

---

## Paragraph

Lorem ipsum dolor sit amet, [test link]() consectetur adipiscing elit. **Strong text** pellentesque ligula commodo viverra vehicula. *Italic text* at ullamcorper enim. Morbi a euismod nibh. <u>Underline text</u> non elit nisl. ~~Deleted text~~ tristique, sem id condimentum tempus, metus lectus venenatis mauris, sit amet semper lorem felis a eros. Fusce egestas nibh at sagittis auctor. Sed ultricies ac arcu quis molestie. Donec dapibus nunc in nibh egestas, vitae volutpat sem iaculis. Curabitur sem tellus, elementum nec quam id, fermentum laoreet mi. Ut mollis ullamcorper turpis, vitae facilisis velit ultricies sit amet. Etiam laoreet dui odio, id tempus justo tincidunt id. Phasellus scelerisque nunc sed nunc ultricies accumsan.

Interdum et malesuada fames ac ante ipsum primis in faucibus. `Sed erat diam`, blandit eget felis aliquam, rhoncus varius urna. Donec tellus sapien, sodales eget ante vitae, feugiat ullamcorper urna. Praesent auctor dui vitae dapibus eleifend. Proin viverra mollis neque, ut ullamcorper elit posuere eget.

> Praesent diam elit, interdum ut pulvinar placerat, imperdiet at magna.

Maecenas ornare arcu at mi suscipit, non molestie tortor ultrices. Aenean convallis, diam et congue ultricies, erat magna tincidunt orci, pulvinar posuere mi sapien ac magna. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Praesent vitae placerat mauris. Nullam laoreet ante posuere tortor blandit auctor. Sed id ligula volutpat leo consequat placerat. Mauris fermentum dolor sed augue malesuada sollicitudin. Vivamus ultrices nunc felis, quis viverra orci eleifend ut. Donec et quam id urna cursus posuere. Donec elementum scelerisque laoreet.

## List Types

### Definition List (dl)

<dl><dt>Definition List Title</dt><dd>This is a definition list division.</dd></dl>

### Ordered List (ol)

1. List Item 1
2. List Item 2
3. List Item 3

### Unordered List (ul)

- List Item 1
- List Item 2
- List Item 3

## Table

| Table Header 1 | Table Header 2 | Table Header 3 |
| - | - | - |
| Division 1 | Division 2 | Division 3 |
| Division 1 | Division 2 | Division 3 |
| Division 1 | Division 2 | Division 3 |

## Misc Stuff - abbr, acronym, sub, sup, etc.

Lorem <sup>superscript</sup> dolor <sub>subscript</sub> amet, consectetuer adipiscing elit. Nullam dignissim convallis est. Quisque aliquam. <cite>cite</cite>. Nunc iaculis suscipit dui. Nam sit amet sem. Aliquam libero nisi, imperdiet at, tincidunt nec, gravida vehicula, nisl. Praesent mattis, massa quis luctus fermentum, turpis mi volutpat justo, eu volutpat enim diam eget metus. Maecenas ornare tortor. Donec sed tellus eget sapien fringilla nonummy. <acronym title="National Basketball Association">NBA</acronym> Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus.  <abbr title="Avenue">AVE</abbr>


## Code Block

```
alert('Hello World!');
```

## Bold italic

**Bold**

*italic*





## Pandoc公式测试

在含有公式的文章里开头设置`mathjax: true`

首先，插入一个行内公式，需要使用`$...$`来实现：$x^2+y^2=z^2$
插入单独一行的公式，需要使用`$$...$$`的方式来实现，如：
$$
\theta_i = \theta_i - \alpha\frac\partial{\partial\theta_i}J(\theta)
$$


**行内公式**

霍夫曼树又称最优二叉树，是一种带权路径长度最短的二叉树。所谓树的带权路径长度，就是树中所有的叶结点的权值乘上其到根结点的路径长度（若根结点为0层，叶结点到根结点的路径长度为叶结点的层数）。树的路径长度是从树根到每一结点的路径长度之和，记为 $WPL=（W_1*L_1+W_2*L_2+W_3*L_3+...+W_n*L_n）$，$N$个权值$W_i$（$i=1,2,...n$）构成一棵有$N$个叶结点的二叉树，相应的叶结点的路径长度为$L_i$（$i=1,2,...n$）。可以证明霍夫曼树的WPL是最小的。

class $i$ given an input $x$ is ，测试$x$



**行间公式**
$$
\text{softmax}(x)_i = \frac{\exp(x_i)}{\sum_j \exp(x_j)}
$$




