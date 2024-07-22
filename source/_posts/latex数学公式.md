---
title: latex数学公式
date: 2024-07-11 23:24:35
tags:
mathjax: true
index_img: /img/latex.jpg
categories:
  - 学习
excerpt: 主要介绍了如何在 markdown 中使用 latex 公式
---
# 公式的插入
1. 行中公式
```
$公式$
```
2. 独立公式
```
$$
公式
$$
```

# 上下标
```
$x^{y^z} = (1+e^x)^{-2xy^w}$
```
$x^{y^z} = (1+e^x)^{-2xy^w}$
 
# 括号和分隔符
```
$f(x,y,z) = 3y^2z\left(3+\dfrac{7x+5}{1+y^2}\right)$
```
$f(x,y,z) = 3y^2z\left(3+\dfrac{7x+5}{1+y^2}\right)$

# 分数
```
$\frac{a}{b}$
```
$\frac{a}{b}$

# 开方
```
$\sqrt[根指数,省略是为 2]{被开方数}$
```
$\sqrt[根指数,省略是为 2]{被开方数}$

# 省略号
```
这些符号用于表示不同类型的省略或延续。下面是每个符号的解释：

1. **$\cdots$**（水平省略号）：表示在水平方向上的省略。例如：
   \[
   a_1, a_2, a_3, \cdots, a_n
   \]
   表示从$a_1$到$a_n$的数列，其中省略了中间的项。

2. **$\ldots$**（水平省略号）：与$\cdots$类似，表示在水平方向上的省略。通常在文本中表示省略或未完的部分。例如：
   \[
   1, 2, 3, \ldots, n
   \]
   表示从1到n的数列。

3. **$\vdots$**（竖直省略号）：表示在竖直方向上的省略。例如：
   \[
   \begin{matrix}
   a_1 \\
   a_2 \\
   \vdots \\
   a_n
   \end{matrix}
   \]
   表示从$a_1$到$a_n$的一列数，其中省略了中间的项。

4. **$\ddots$**（对角省略号）：表示在对角方向上的省略，通常用于矩阵中。例如：
   \[
   \begin{pmatrix}
   a_{11} & a_{12} & \cdots & a_{1n} \\
   a_{21} & a_{22} & \cdots & a_{2n} \\
   \vdots & \vdots & \ddots & \vdots \\
   a_{n1} & a_{n2} & \cdots & a_{nn}
   \end{pmatrix}
   \]
   表示一个矩阵，其中省略了对角线上的项。
```
$\cdots\ldots\vdots\ddots$

# 哥放弃了