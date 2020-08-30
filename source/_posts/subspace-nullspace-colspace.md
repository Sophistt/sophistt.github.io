---
title: Subspace, Null Space and Col Space in Linear Algebra
date: 2020-08-29 10:15:26
mathjax: true
tags: linear-algebra
categories: 
  - Study
  - Math
---

## subspace

A subspace of a vector space $V$ is a subset $H$ of $V$ that has two properties. 

1. $H$ is closed under vector addition. That is, for each $u$ and $v$ in $H$, the sum $u+v$ is in $H$.
2. $H$ is closed under multiplication by scalars. That is, for each $u$ in $H$ and each scalar $c$, the vector $cu$ is in $H$.

**Note**

In some materials, $H$ has the third property that **The zero vector of** $V$ **is in** $H$. This property could be inclued in **Property 2** while $c=0$. 

For example,

- A line through the origin in $R^2$ is a subspace.

- The $xy$-plane in $R^3$ is a subspace.
- The set of all polynomials $P$ is a subspace of $C[0,1]$.

### Proof

如何证明一个空间是subspace？

只需要根据定义，将该空间的向量 $x, y$ 代入两个条件验证即可。

## Span

Span（线性生成空间）is the set of all linear combination of specialized vectors.

For example,
$$
span(v_1, v_2, ... v_n) = \{ \sum_{i=1}^{n}\alpha_iv_i|\alpha_i \in \Re \} 
$$
Means span($v_1, v_2, ... v_n$) is generated / spanned by $v_1, v_2, ... v_n$.

**Theorem**

span($v_1, v_2, ... v_n$) is a subspace of $V$.

## Null space

Given a matrix $A \in \Re^{m \times n}$. The null space or kernel of $A$ is defined as
$$
Nul A = Ker A = \{ x \in \Re^n | Ax = 0\}
$$

## Column space

Given a matrix $A \in \Re^{m \times n}$. The column space or image of $A$ is defined as the set of all linear combination of the colums of $A$.
$$
col A = ImA=\{b \in \Re^m | b = Ax \quad \mathrm{for \quad some} \quad x \in \Re^n\}
$$

### Computation

But how to compute the null space and column space of a speified matrix?

For example,
$$
A = 
\begin{bmatrix}
1 \quad 2 \quad 1 \quad 3 \quad 2 \\
3 \quad 4 \quad 9 \quad 0 \quad 7 \\
2 \quad 3 \quad 5 \quad 1 \quad 8 \\
2 \quad 2 \quad 8 \quad -3 \quad 5 
\end{bmatrix}
$$
how to get $Nul(A)$ and $Col(A)$?

**Answer**

Convert matrix $A$ to the row minimal matrix. The specific calculation process is omitted here.
$$
A=>
\begin{bmatrix}
1 \quad 0 \quad 7 \quad 0 \quad 85 \\
0 \quad 1 \quad -3 \quad 0 \quad 31 \\
0 \quad 0 \quad 0 \quad 1 \quad -7 \\
\end{bmatrix}
$$
We know that the **pivot number** represents the location of **dependent variables** and the remaining variables are called **free variables**. In this row minimal matrix, the pivot numbers are 1, 2, 4.（非零行的第一个非零元所在的位置）Hence we could get two basis of the vector space:
$$
\xi_1 = 
\begin{bmatrix}
x_1 \\
y_1 \\
0 \\
z_1 \\
1
\end{bmatrix} \quad
\xi_2 = 
\begin{bmatrix}
x_2 \\
y_2 \\
1 \\
z_2 \\
0
\end{bmatrix}
$$
let $A\xi_1 = 0$ and $A\xi_2 = 0$, we get the values of $x, y, z$ respectively. 
$$
\xi_1 = 
\begin{bmatrix}
-85 \\
-31 \\
0 \\
7 \\
1
\end{bmatrix} \quad
\xi_2 = 
\begin{bmatrix}
-7 \\
3 \\
1 \\
0 \\
0
\end{bmatrix}
$$
**Null space**

Use the basis to represent null space
$$
\mathrm{Nul}A = \mathrm{span}\{\xi_1, \xi_2\}
$$
**Column space**

After we get **pivot number**, we get the column space as well since column space consists of the column vectors of pivot number in $A$.
$$
\mathrm{Col}A=\mathrm{span}\{
\begin{bmatrix}
1 \\
3 \\
2 \\
2 
\end{bmatrix},
\begin{bmatrix}
2 \\
4 \\
3 \\
2 
\end{bmatrix},
\begin{bmatrix}
3 \\
0 \\
1 \\
-3 
\end{bmatrix}
\}
$$

# References

- [如何快速写出方程组的解](https://www.q-math.com/?p=629)
- [线性代数复习（六）：子空间，零空间及列空间 subspace, null and column space](https://www.youtube.com/watch?v=KiRKulz0OGw&feature=youtu.be)

