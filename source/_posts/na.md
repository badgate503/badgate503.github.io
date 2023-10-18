---
title: 数值分析笔记
date: 2023-10-17 18:18:17
tags: [数学,技术,课程,编程]
index_img: /img/naimg.jpg
categories: 课程
---
### Direct Methods for Solving Linear Systems
#### Gaussian Elimination
**Elimination**:Let $A^{(1)}=A$
Step k: if $a_{kk}^{(k)} \neq 0$, compute $m_{ik}=a_{ik}^{(k)}/a_{kk}^{(k)}$, $$a_{ij}^{(k+1)}=a_{ij}^{(k)}-m_{ik}a_{kj}^{(k)}$$
$$b_i^{(k+1)}=b_i^{(k)}-m_{ik}b_k^{(k)}$$
There are $n-1$ steps.  
**Backward-substitution**: $x_n=b_n^{(n)}/a_{nn}^{(n)}; x_i=(b_i^{(i)}-\Sigma _{j=i+1}^n a_{ij}^{(i)}x_j)/a_{ii}^{(i)}$
if $a_{ii}^{(i)}=0$, then we must find $k$ such that $a_{ki}^{(i)} \neq 0$ and exchange row $i$ and row $k$. If there doesn't exist such k, no unique solution exists.
Amount of Computation :
+ Elimination:$\Sigma_{k=1}^{n-1}(n-k)(n-k+2)=\frac{n^3}{3}+\frac{n^2}{2}-\frac{5n}{6}$
+ Backward-substituion: $1+\Sigma_{i=2}^{n-1}(n-i+1)=\frac{n^2}{2}+\frac{n}{2}$
#### Pivoting Strategies
    Problem: Small pivot element may cause trouble.
**Partial Pivoting**: Determine the smallest $p>k$ such that $|a_{pk}^{(k)}|=\mathop{max}\limits_{k\le i \le n}|a_{ik}^{(k)}|$, then interchange row $E_k$ and $E_p$.
**Scaled Partial Pivoting**: Place the element in the pivot position that is largest relative to the entries in its row.
+ Define a scale factor $s_i$ for each row as $s_i=\mathop{max}\limits_{1\le j\le n} |(a_{ij})|$ (Note that $s_i$ can't be 0 because the system have unique solution)
+ Determine the smallest $p>k$ such that $\frac{|a_{pk}^{(k)}|}{s_p}=\mathop{max}\limits_{i\le k\le n}\frac{|a_{pk}^{(k)}|}{s_p}$
+ Interchange row $E_i$ and $E_p$

**Complete Pivoting**: Searches all the entries $a_{ij}$ in range $i\in[k,n]$ and $j\in[k,n]$, to find the entry with the largest magnitude. Once the entry is found, both row and column interchanges are performed to bring this entry to the pivot position.  
Complete pivoting is the strategy recommended **only** for systems where accuracy is essential and the amount of time needed for this method can be justified.
**Amount of Computation**:
+ Partial Pivoting : $O(n^2)$
+ Scaled Partial Pivoting : $O(n^2)$
+ Complete Pivoting : $O(n^3)$
#### Matrix Factorization
**$LU$ factorization**: The factorization is particularly useful when it has the form $A=LU$, where $L$ is lower triangular and $U$ is upper triangular.
+ In the first step, For each $j=2,3,...n$, $E_j\leftarrow E_j-m_{j,1}E_1$, where $m_{j,1}=\frac{a_{j1}^{(1)}}{a_{11}^{(1)}}$. The operation can be represented in matrix form:
$$M^{(1)}=\begin{bmatrix}
1&0&...&0\\
-m_{21}&1&...&0\\
-m_{31}&0&...&0\\
...&...&...&...\\
-m_{n1}&0&...&1            \end{bmatrix}$$
+ Then, for every step, repeat the same operation, we can obtain $M^{(1)}$,$M^{(2)}$...$M^{(n-1)}$.
+ Note that ${M^{(k)}}^{-1} = -M^{(k)}+2I$, $M^{(k)}M^{(j)}=M^{(k)}+M^{(j)}$, we can let $L = \prod\limits_{j=1}^{n-1}{M^{(j)}}^{-1}$, which can be represented by
$$L=\begin{bmatrix}
1&0&0&...&0\\
m_{21}&1&0&...&0\\
m_{31}&m_{32}&1&...&0\\
...&...&...&...&...\\
m_{n1}&m_{n2}&m_{n3}&...&1
\end{bmatrix}$$
+ let $U = (\prod\limits_{j=n-1}^{1}{M^{(j)}})A$, then we can obtain $LU=A$, because both $L$ and $U$ are triangular, when solving $Ax=b$, we can let $y=Ux$, then solve $Ly=b$ and $Ux=y$.
#### Special Types of Matrices
**Strictly Diagonally Dominant Matrix**: $|a_{ii}|>\Sigma_{j=1}^n |a_{ij}|$. 
+ A strictly diagonally dominant matrix $A$ is nonsingular. 
+ Gaussian elimination can be performed without row or column interchanges.
+ The computations will be stable with respect to the growth of roundoff errors.

**Positive Definite Matrix**: Definintion A matrix A is positive definite if it is symmetric and if $x^tAx >0$ for every n-dimensional vertor $x\neq0$.  
+ The symmetric matrix $A$ is positive definite if and only if Gaussian elimination without row interchanges can be performed on the linear system $Ax=b$ with all pivot elements positive.
+ The computation are stable with respect to the growth of round-off errors
+ Matrix A is positive definite iff A can be factored in the form $LDL^t$, where $L$ is lower triangular with 1s on its dagonal and $D$ is diagonal matrix with positive diagonal entries.

**Crout Reduction for Tridiagonal Linear System**
+ Find the crout factorization of $A$