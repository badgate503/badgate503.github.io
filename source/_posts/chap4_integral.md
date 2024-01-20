---
title: 计算机学院 数值分析 （Numerical Analysis） 笔记
mathjax: true
---
## Chap 2 : 一元方程的求解
### Sec 1 二分法
**二分法**：$f$ 是定义在区间 $[a,b]$ 上的连续函数，$f(a)$ 与 $f(b)$ 异号，则存在 $p\in[a,b],f(p)=0$ 算法的核心是将搜索区间反复减半，每一步找出含有 $p$ 的那一半
```c
fa=f(a);
while(i<=n)
{
    p = (a+b)/2;
    fp = f(p);
    if(fp==0||(b-a)/2<TOL)
    {
        output(p);
    }
    i++;
    if(fa*fp>0)
        a=p;fa=fp;
    else
        b=p;
}
```

虽然二分法在概念上很容易，但他有重大缺陷：
1. 收敛较慢.
2. 好的中间近似解可能被丢弃.

但是，二分法总收敛于一个解.

二分法产生出逼近 $f$ 的零点 $p$ 的序列 $p_n$ 有：$$|p_n-p|\le\frac{b-a}{2^n},n\ge 1.$$
****
### Sec 2 不动点迭代
**不动点**：如果有 $g(p)=p$, 那么 $p$ 是 $g$ 的一个不动点.

求函数 $g$ 的不动点问题等价于求 $f(x)=x-g(x)$ 的零点的问题.

1. 如果 $g\in C[a,b],g(x)\in[a,b]$ 对所有 $x\in[a,b]$ 成立, 那么 $g$ 在 $[a,b]$ 有一个不动点.
2. 条件1前提下，若 $g^\prime$ 存在, 且存在 $0<k<1$ 使得 $|g^\prime(x)|\le k$ 对于 $x\in[a,b]$ 成立，那么该不动点是唯一的.
3. 条件1, 2前提下，对 $[a,b]$ 内任意点 $p_0$ 由 $p_n=g(p_{n-1})$ 确定的序列收敛于唯一不动点 $p$.
4. 条件1, 2前提下，用 $p_n$ 逼近 $p$ 的误差界为：$|p_n-p|\le k^n\max{\{p_0-a,b-p_0\}}.$ 且 $|p_n-p|\le \frac{k^n}{1-k}|p_1-p_0|.$

不动点迭代的代码实现是显然的.

****
### Sec 3 $Newton$ 迭代法
**$Newton$ 法**：假设 $f\in C^2[a,b]$ 利用 $Taylor$ 展开式可以得到 $Newton$ 迭代法为：$$p_n=p_{n-1}-\frac{f(p_{n-1})}{f^\prime(p_{n-1})}.$$

假设 $f\in C^2[a,b]$  如果 $p\in[a,b]$ 满足 $f(p)=0, f^\prime(p)\neq0$, 那么存在 $\xi>0$,使得任意初始近似 $p_0\in[p-\xi,p+\xi]$, $Newton$ 迭代法收敛.

**正割法**：利用 $f^\prime(p_{n-1})\approx\frac{f(p_{n-1})-f(p_{n-2})}{p_{n-1}-p_{n-2}}$. 优化 $Newton$ 法求导数的困难，有
$$p_n=p_{n-1}-\frac{f(p_{n-1})(p_{n-1}-p_{n-2})}{f(p_{n-1})-f(p_{n-2})}.$$

正割法比函数式迭代快得多，但是比 $Newton$ 迭代慢一点，由于 $Newton$ 法和正割法需要一个好的近似值，而这个近似值可以由二分法提供。

**试位法**：以与正割法相同的方式产生近似解，但是他增加了一个检验以保证在相邻的迭代间包含根。

以下是试位法的实现：
```c
int i = 2;
q0=f(p0);
q1=f(p1);
while(i<n)
{
    p = p1 - q1*(p1-p0)/(q1-q0);   //Secant method.
    if(p-p1<TOL)
        return;
    i++;
    q=f(p);
    if(q*q1<0)
    {
        p0=p1;
        q0=q1;
    }
    p1=p;
    q1=q;
}
```

****
### Sec 4 迭代法的误差界分析
假设 $p_n$ 是一个线性收敛序列，其极限为 $p$,如果存在 $\lambda$ 和 $\alpha$ 使得 $$\lim_{n\to\infty}\frac{|p_{n+1}-p|}{|p_n-p|^2}=\lambda.$$
则称 $p_n$ 以阶 $\alpha$ 收敛于 $p$，渐进误差常数为 $\lambda$.
+ $\alpha=1$ 为线性收敛，不动点迭代是线性收敛
+ $\alpha=2$ 为二次收敛

若 $p$ 是方程 $x=g(x)$ 的解。$g^{\prime\prime}$ 以 $M$ 为界，若 $g^\prime(p)=0$, 那么存在 $\delta$, 使得初值为 $p_0\in[p-\delta,p+\delta]$ 的序列至少二次收敛于 $p$.且，当 $n$ 足够大,$$|p_{n+1}-p|<\frac M2|p_n-p|^2.$$

只要 $p$ 是单重零点，存在包含 $p$ 的一个区间使得其中取的任意初值 $p_0$ $Newton$ 迭代法有至少二次收敛，如果零点不是单重的，不一定有二次收敛。

因此可以采取一种方法优化 $Newton$ 法，定义 $\mu(x)=\frac{f(x)}{f^\prime(x)},$ 再用 $Newton$ 法求 $\mu(x)$ 的零点，这样，无论 $f$ 的零点有几重，$\mu$ 的零点只有 1 重，因此总是二次收敛。

### Sec 5 加速收敛
$\mathbf{AITKEN \Delta^2}$ 方法：假设 $p_n$ 是一个线性收敛序列，其极限为 $p$ . 可以这样构造一个更快收敛的序列：$$\hat{p_n}=p_n-\frac{(p_{n+1}-p_n)^2}{p_{n+2}-2p_{n+1}+p_n}.$$

这一方法的记号 $\Delta$ 来源于以下定义：序列 $p_n$ 的向前差分 $\Delta p_n=p_{n+1}-p_n,\Delta^kp_n=\Delta(\Delta^{k-1}p_n)$, 原表达式可以写作：
$$\hat{p}=p_n-\frac{(\Delta p_n)^2}{\Delta^2p_n}.$$

把改进的 $\mathbf{AITKEN \Delta^2}$ 方法 用于根据不动点迭代得到的线性收敛序列，可以将收敛速度加速到二次，这一方法被称作 $Steffensen$ 方法.

## Chap 3 : 插值和多项式逼近
### Sec 1 $Lagrange$ 多项式
如果 $x_0,x_1,...x_n$ 是 $n+1$ 个不同的点，函数 $f$ 在这些点上的函数值已知，那么存在一个唯一的次数小于等于 $n$ 的多项式 $P(x)$ 满足:对于所有的 $k\in\{0,1,\dots n\}$ 有 $f(x_k)=P(x_k).$
该多项式 $P_n(x)$ 即为 $n$ 次 $Lagrange$ 多项式，且
$$P(x)=\sum_{k=0}^nf(x_k)L_{n,k}(x).$$

其中， $$L_{n,k}(x)=\prod_{i=0,i\neq k}^n\frac{x-x_i}{x_k-x_i}$$

$Lagrange$ 插值的误差项具有以下形式：
$$f(x) =P(x)+\frac{f^{(n+1)}(\xi(x))}{(n+1)!}\prod_{i=0}^n(x-x_i).$$

$f$ 在 $x_0,x_1,\dots,x_n$ 上有定义，若 $m_1,m_2,\dots,m_k$ 为 $k$ 个不同整数作为 $x$ 的下标，与 $f$ 在 $x_{m_1},x_{m_2},\dots,x_{m_k}$ 上一致的 $Lagrange$ 多项式被记作 $P_{m_1,m_2,\dots,m_k}(x)$
**$Nevile$ 方法**：递归产生 $Lagrange$ 多项式：
$$P(x)=\frac{(x-x_j)P_{0,1,\dots,j-1,j+1,\dots,k}-(x-x_i)P_{0,1,\dots,i-1,i+1,\dots,k}}{x_i-x_j}$$



### Sec 2 差商
假设 $f$ 在 $x_0,x_1,\dots,x_n$ 上有定义，那么 $f$ 的差商可以递归的定义。
>$f$ 关于 $x_i$ 的零阶差商 $f[x_i]=f(x_i)$
>$f$ 关于 $x_i,x_{i+1}$ 的一阶差商 $f[x_i,x_{i+1}]=\frac{f[x_{i+1}]-f[x_i]}{x_{i+1}-x_i}$
>$\dots$
>$f$ 关于 $x_i,x_{i+1},\dots,x_{i+k}$ 的 $k$阶差商 $f[x_i,x_{i+1},\dots,x_{i+k}]=\frac{f[x_{i+1},x_{i+2},\dots,x_{i+k}]-f[x_i,x_{i+2},\dots,x_{i+k-1}]}{x_{i+k}-x_i}$

$Lagrange$ 多项式可以由差商表示：
$$P_n(x)=f[x_0]+f[x_0,x_1](x-x_0)+\dots\\+f[x_0,x_1,\dots,x_n](x-x_0)(x-x_1)\dots(x-x_{n-1})$$

即
$$P_n(x)=f[x_0]+\sum_{k=1}^nf[x_0,x_1,\dots,x_k]\prod_{j=0}^{k-1}(x-x_j)$$

又被称作 $Newton$ 插值公式.  

由于差商的计算可以递归的进行，这大大的方便了 $Lagrange$ 插值的进行。
当 $x_0,x_1,\dots,x_n$ 等步长的分布时，差商型 $Newton$ 插值公式可以被简化，记步长为 $h$，令 $x=x_0+sh$，可以得到：
$$P_n(x_0+sh)=f[x_0]+shf[x_0,x_1]+s(s-1)h^2f[x_0,x_1,x_2]\\+\dots+s(s-1)\dots(s-n+1)h^nf[x_0,x_1,\dots,x_n].$$

即
$$P_n(x)=P_n(x_0+sh)=f[x_0]+\sum_{k=1}^n\tbinom{s}{k}k!h^kf[x_0,x_1,\dots,x_k]$$

这一公式被称作 $Newton$ 向前差商公式.  

**差分符号 $\Delta$**: 差商乘以步长.
$$f[x_0,x_1,\dots,x_k] = \frac1{k!h^k}\Delta^kf(x_0)$$

用差分符号取代差商符号，可以得到 $Newton$ 向前差分公式：
$$P_n(x)=f[x_0]+\sum_{k=1}^n\tbinom{s}{k}\Delta^kf(x_0)$$

将序列 $x_0,x_1,\dots,x_n$ 反转并重新编号，可以得到牛顿向后差分公式：
$$P_n(x)=f[x_n]+\sum_{k=1}^n(-1)^k\tbinom{-s}{k}\nabla^kf(x_n)$$

其中，符号 $\nabla$ 被称作向后差分符号，他与 $\Delta$ 有着类似的定义：
> $\Delta p_0=p_0-p_1,\Delta^kp_0=\Delta(\Delta^{k-1}p_0).$
> $\nabla p_n=p_n-p_{n-1},\nabla^kp_n=\nabla(\nabla^{k-1}p_n).$



### Sec 3 $Hermite$ 插值
假设区间 $[a,b]$ 上有 $n+1$ 个不同的点 $x_0,x_1,\dots,x_n$ 和非负整数 $m_0,m_1,\dots,m_n$ 和 $m=\max\{m_0,m_1,\dots,m_n\}$ 定义在 $x_i$ 处逼近函数 $f$ 的密切多项式为具有以下性质的最小次数多项式：
+ 在每一个点 $x_i$ 他的值与函数值一致.
+ 在每一个点 $x_i$ 上，小于等于 $m_i$ 阶的导数值一致.

这样的多项式最高阶数为$\sum_{i=0}^nm_i+n$
注意到：
> 当 $n=0$ ,逼近 $f$ 的密切多项式退化为 $f$ 在 $x_0$ 点的 $m_0$ 阶 $Taylor$ 展开式.
> 当 $m_i=0$ ,逼近 $f$ 的密切多项式退化为 $f$ 对于 $x_0,x_1,\dots,x_n$ 的 $Lagrange$ 插值多项式.

（事实上，密切多项式是 $Taylor$ 展开式和 $Lagrange$ 多项式的推广）  

当 $m_i=1$ 时，密切多项式给出 $Hermite$ 多项式.  

假设区间 $[a,b]$ 上的点 $x_0,x_1,\dots,x_n$ 互异，则与 $f$ 和 $f^\prime$ 在这些点上一致的 $Hermite$ 多项式阶数不超过 $2n-1$, 他由下式给出：
$$H_{2n+1}(x)=\sum_{j=0}^nf(x_j)H_{n,j}(x)+\sum_{j=0}^nf^\prime(x_j)\hat{H}_{n,j}(x),$$

$$H_{n,j}(x)=[1-2(x-x_j)L^\prime_{n,j}(x_j)]L_{n,j}^2(x),$$

$$\hat{H}_{n,j}(x)=(x-x_j)L_{n,j}^2(x).$$

其误差项由
$$f(x)=H_{2n+1}(x)+\frac{\prod_{i=0}^n(x-x_i)^2}{(2n+2)!}f^{(2n+2)}(\xi).$$

给出.  

尽管以上公式给出了 $Hermite$ 多项式的完整定义，但通过此方式计算 $Hermite$ 多项式过于冗长，另一种使用差商型 $Newton$ 插值公式的方法可以被采用：  

定义新的序列 $z_0,z_1,\dots,z_{2n+1}$ 为 $z_{2i}=z_{2i+1}=x_i$. 但这么做导致二阶差商不能被良好的定义，因此可以做这样的替换：$f[z_{2i},z_{2i+1}]=f^\prime(x_i)$ 再利用 $z_0,z_1,\dots,z_{2n+1}$ 作 $Newton$ 插值，将会产生一个 $2n+1$ 阶的多项式. 可以证明这一多项式就是 $f$ 在 $x_0,x_1,\dots,x_n$ 上的 $Hermite$ 多项式.

### Sec 4 三次样条插值
给定区间 $[a,b]$ 上定义的函数 $f$ 和一些节点 $a=x_0,x_1,\dots,x_n=b$，f的三次样条插值 $S$ 满足以下条件：
+ $S(x)$ 在子区间 $[x_j,x_{j+1}]$ 上是三次多项式，记作 $S_j(x)$.
+ $S(x_j)=f(x_j),j\in\{0,1,\dots,n\}.$
+ $S_{j+1}(x_{j+1})=S_j(x_{j+1}),j\in\{0,1,\dots,n-2\}.$
+ $S^\prime_{j+1}(x_{j+1})=S^\prime_j(x_{j+1}),j\in\{0,1,\dots,n-2\}.$
+ $S^{\prime\prime}_{j+1}(x_{j+1})=S^{\prime\prime}_j(x_{j+1}),j\in\{0,1,\dots,n-2\}.$
+ 以下两个满足其一：
    + $S^{\prime\prime}(x_0)=S^{\prime\prime}(x_n)=0$ （自然三次样条）
    + $S^{\prime}(x_0)=S^{\prime}(x_0)$ 且 $S^{\prime}(x_n)=S^{\prime}(x_n)$ （固支三次样条）

可以通过以下方式来构造一个三次样条插值：
$$S_j(x)=a_j+b_j(x-x_j)+c_j(x-x_j)^2+d_j(x-x_j)^3$$

其中，明显有 $a_j=f(x_j)$  

因为 $S_{j+1}(x_{j+1})=S_j(x_{j+1})$，定义 $h_j=x_{j+1}-x_j$, 那么有
$$a_{j+1}=a_j+b_jh_j+c_jh_j^2+d_jh_j^4.$$

对于 $j\in\{0,1,\dots,n-1\}$ 成立.  

类似的，还有
$$b_{j+1}=b_j+2c_jh_j+3d_jh_j^2.$$

对于 $j\in\{0,1,\dots,n-1\}$ 成立.  

以及
$$c_{j+1}=c_j+3d_jh_j.$$

对于 $j\in\{0,1,\dots,n-1\}$ 成立.  

整理后可以得到一个线性方程组：
$$h_{j-1}c_{j-1}+2(h_{j-1}+h_j)c_j+h_jc_{j+1}=\frac3{h_j}(a_{j+1}-a_j)-\frac3{h_{j-1}}(a_{j}-a_{j-1}).$$

$$
\begin{bmatrix}
    1 & 0 & 0 &0 &\dots &0\\
    h_0&2(h_0+h_1)&h_1&0&\dots&0\\
    0&h_1&2(h_1+h_2)&h_2&\dots&0\\
    0 & 0 & h_2 & 2(h_2+h_3)&\dots&0\\
    0&0&0&h_3&\dots&0\\
    \dots&\dots&\dots&\dots&\dots&\dots\\
    0&0&0&0&\dots&1
\end{bmatrix}\begin{bmatrix}
c_0\\c_1\\c_2\\c_3\\c_4\\\dots\\c_n
\end{bmatrix}=\\\begin{bmatrix}
0\\
\frac{3}{h_1}(a_2-a_1)-\frac{3}{h_0}(a_1-a_0)\\
\frac{3}{h_2}(a_3-a_2)-\frac{3}{h_1}(a_2-a_1)\\
\frac{3}{h_3}(a_4-a_3)-\frac{3}{h_2}(a_3-a_2)\\
\dots\\
\frac{3}{h_{n-1}}(a_n-a_{n-1})-\frac{3}{h_{n-2}}(a_{n-1}-a_{n-2})\\0
\end{bmatrix}
$$

固支的三次样条只需要对边界进行调整即可.
$$
\begin{bmatrix}
    2h_0 & h_0 & 0 &0 &\dots &0\\
    h_0&2(h_0+h_1)&h_1&0&\dots&0\\
    0&h_1&2(h_1+h_2)&h_2&\dots&0\\
    0 & 0 & h_2 & 2(h_2+h_3)&\dots&0\\
    0&0&0&h_3&\dots&0\\
    \dots&\dots&\dots&\dots&\dots&\dots\\
    0&0&0&\dots&h_{n-1}&2h_{n-1}
\end{bmatrix}\begin{bmatrix}
c_0\\c_1\\c_2\\c_3\\c_4\\\dots\\c_n
\end{bmatrix}=\\\begin{bmatrix}
\frac{3}{h_0}(a_1-a_0)-3f^\prime(a)\\
\frac{3}{h_1}(a_2-a_1)-\frac{3}{h_0}(a_1-a_0)\\
\frac{3}{h_2}(a_3-a_2)-\frac{3}{h_1}(a_2-a_1)\\
\frac{3}{h_3}(a_4-a_3)-\frac{3}{h_2}(a_3-a_2)\\
\dots\\
\frac{3}{h_{n-1}}(a_n-a_{n-1})-\frac{3}{h_{n-2}}(a_{n-1}-a_{n-2})\\3f^\prime(b)-\frac{3}{h_{n-1}}(a_n-a_{n-1})
\end{bmatrix}
$$


### Sec 5 参数曲线
对函数 $f$ 进行参数化得到 $x(t),y(t)$, 分别对 $x(t),y(t)$ 进行插值，此前讨论的插值方法都可以被应用.  

往往选择分段的三次 $Hermite$ 多项式对 $x(t),y(t)$ 进行插值，提供两个数据点 $(x(0),y(0)),(x(1),y(1))$,以及参数曲线在边界处的导数值 $\frac{dy}{dx}_{t=0},\frac{dy}{dx}_{t=1}$, 由于三次 $Hermite$ 插值需要 4 组条件，对参数化的 $f$ 进行插值需要 8 组条件，而以上内容只提供了 6 组条件，因此三次 $Hermite$ 多项式对于满足给定条件时具有一定的灵活性，即 $x^\prime(0),x^\prime(1),y^\prime(0),y^\prime(1)$ 的值将由我们给定.   

由于 $\frac{dy}{dx}_{t=0},\frac{dy}{dx}_{t=1}$ 已经确定，通过将 $x^\prime(0),y^\prime(0)$ 乘以一个公共的倍率，曲线在 $(x(0),y(0))$ 处的导数不变，但是其形状将会向切线逼近。  

在实际应用中，确定一个三次 $Hermite$ 曲线所需除了两个数据点以外，还需要两个引导点，这两个引导点与数据点的连线斜率确定 $\frac{dy}{dx}_{t=0},\frac{dy}{dx}_{t=1}$ ，其长度确定乘在 $x^\prime(0),y^\prime(0)$ 或 $x^\prime(1),y^\prime(1)$ 上的公共倍率.  

假设节点为 $(x_0,y_0)$ 和 $(x_1,y_1)$，两个引导点为 $(x_0+\alpha_0,y_0+\beta_0)$ 和 $(x_1+\alpha_1,y_1+\beta_1)$, 那么有：
$$x(0)=x_0,x(1)=x_1,x^\prime(0)=\alpha_0,x^\prime(1)=\alpha_1.$$

$$y(0)=y_0,y(1)=y_1,y^\prime(0)=\beta_0,y^\prime(1)=\beta_1.$$

分别进行 $Hermite$ 插值可得：
$$x(t)=[2(x_0-x_1)+(\alpha_0+\alpha_1)]t^3+[3(x_1-x_0)-(\alpha_1+2\alpha_0)]t^2+\alpha_0t+x_0,$$

$$y(t)=[2(y_0-y_1)+(\beta_0+\beta_1)]t^3+[3(y_1-y_0)-(\beta_1+2\beta_0)]t^2+\beta_0t+y_0.$$

参数的三次 $Hermite$ 多项式也被称为 $Bézier$ 多项式. 这样的一个曲线也被称作 $Bézier$ 曲线。
****


## Chap 4 : 数值微分与积分
### Sec 1 数值微分

**差分公式**：假设 $x_0\in(a,b), x_1=x_0+h\in[a,b]$ ，对于一个 $(a,b)$ 上函数 $f$，可以构造由 $x_0, x_1$ 确定的 $Lagrange$ 多项式：
$$f(x)=\frac{f(x_0)(x-x_0-h)}{-h}+\frac{f(x_0+h)(x-x_0)}{h}+\frac{f(x-x_0)(x-x_0-h)}{2}f^{''}(\xi(x))$$

当 $x=x_0$ 时对其求导可得：
$$f^{'}(x_0)=\frac{f(x_0+h)-f(x_0)}{h}-\frac{h}{2}f^{''}(\xi).$$

当 $h\to0$ 时，该公式的前半部分符合导数的定义。因此对于较小的 $h$ ,该公式可用于估计 $f^{'}(x_0)$ 的近似值。当 $h>0$ 时，该公式被称作**向前差分公式**（Forward Difference Formula），反之则为**向后差分公式**（Backward Difference Formula）.  

该公式的误差界为 $\frac{h}{2}f^{''}(\xi).$  

**(n+1)点公式**：假设 $\{x_0, x_1, ... x_n\}$ 是区间 $I$ 上的 $n+1$ 个不同的点，采用类似于差分公式的推导方法，我们可以得到（ $x_j$ 为 $\{x_0, x_1, ... x_n\}$ 中某个点）：  

$$f^{'}(x_j)=\sum^{n}_{k=0}{f(x_k)L_k^{'}}(x_j)+\frac{f^{(n+1)}(\xi(x_j))}{(n+1)!}\prod^n_{k=0, k\neq j}(x_j-x_k)$$

取 $n=2$ 并令三个节点等距，可以得到3点公式：  

若 $x_0$ 为两边的点：
$$f^{'}(x_0)=\frac{1}{2h}[-3f(x_0)+4f(x_0+h)-f(x_0+2h)]+\frac{h^2}{3}f^{(3)}(\xi_0).$$

若 $x_0$ 为中间的点：
$$f^{'}(x_0)=\frac{1}{2h}[f(x_0+h)-f(x_0-h)]-\frac{h^2}{6}f^{(3)}(\xi_0).$$

取 $n=4$ 并令五个节点等距，可以得到5点公式：
$$f^{'}(x_0)=\frac{1}{12h}[f(x_0-2h)-8f(x_0-h)+8f(x_0+h)-f(x_0+2h)]-\frac{h^4}{30}f^{(5)}(\xi).$$


### Sec 2 $Richardson$ 外推法
当截断误差公式具有以下形式：
$$\sum_{j=1}^{m-1}K_jh^{a_j}+O(h^{a_m})$$

时，可以运用外推法：
$$N_j(h)=N_{j-1}(\frac{h}{2})+\frac{N_{j-1}(h/2)-N_{j-1}(h)}{2^{j-1}-1}$$

得到更高阶的逼近。在应用中，类似于差分公式的迭代方法，更高阶的逼近公式可以通过表格的方式得到
|  $O(h)$  | $O(h^2)$  |$O(h^3)$|$O(h^4)$|
|  ----  | ----  |----|----|
| $N_1(h)\equiv N(h)$  ||||
| $N_1(\frac{h}{2})\equiv N(\frac{h}{2})$ | $N_2(h)$|||
| $N_1(\frac{h}{4})\equiv N(\frac{h}{4})$ | $N_2(\frac{h}{2})$ |$N_3(h)$||
| $N_1(\frac{h}{8})\equiv N(\frac{h}{8})$ | $N_2(\frac{h}{4})$ |$N_3(\frac{h}{2})$ |$N_4(h)$|

### Sec 3 数值积分
**梯形法则（Trapezoidal rule）**:令 $x_0=a, x_1=b, h=b-a$ 则对于函数 $f$
$$\int^b_af(x)dx=\frac{h}{2}[f(x_0)+f(x_1)]-\frac{h^3}{12}f^{''}(\xi)$$

该公式被称作**梯形法则**，这一法则的本质就是用一个梯形的面积来近似代替 $\int^b_af(x)dx$. 注意到误差项包含 $f$ 的二阶导，这意味着对于 $f$ 满足 $f^{''}(x)\equiv0$ （如线性函数）梯形法则将给出精确结果。

**辛普森法则（$Simpson$ Rule）**：对在区间 $[a, b]$ 上的包括节点 $\{x_0=a,x_1,x_2=b\}$ 的二次 $Lagrange$ 多项式进行积分，可以得到辛普森法则：  

$$\int^b_af(x)dx=\int^{x_2}_{x_0}[\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)}f(x_0)+\frac{(x-x_0)(x-x_2)}{(x_1-x_0)(x_1-x_2)}f(x_1)\\+\frac{(x-x_0)(x-x_1)}{(x_2-x_0)(x_2-x_1)}f(x_2)]dx+\int^{x_2}_{x_0}\frac{(x-x_0)(x-x_1)(x-x_2)}{6}f^{(3)}(\xi(x))dx$$

另一种方法处理可以导出包含包含 $f^{(4)}$ 的更高阶的误差项（$h=(b-a)/2$）：
$$\int^{x_2}_{x_0}f(x)dx=\frac{h}{3}[f(x_0)+4f(x_1)+f(x_2)]-\frac{h^5}{90}f^{(4)}(\xi)$$

注意到误差项包含 $f$ 的四阶导数，因此当辛普森法则应用到任何三次及以下的多项式时，他给出准确的结果。  

*定义*：**精确度**（Degree of accuracy **or** precision）是指使得求积公式对 $x^k$ 给出精确结果的最大整数 $k$。
> 梯形法则的精确度为 $1$.
> 辛普森法则的精确度为 $3$.

**牛顿-柯特斯公式（$Newton-Cotes$ Formula）**:区间 $[a,b]$ 上 $n+1$ 个节点 $x_i=a+ih (i=0,1,2...n)$ 其中，$h=(b-a)/n$ .
$$\int^b_af(x)dx\approx\sum_{i=0}^na_if(x_i), a_i=\int_{x_0}^{x_n}L_i(x)dx$$
> 当 $n=1$时，牛顿-柯特斯公式退化为梯形法则，精确度为 $1$.
> 当 $n=2$时，牛顿-柯特斯公式退化为辛普森法则，精确度为 $3$.
> 当 $n=3$时，牛顿-柯特斯公式退化为辛普森$3/8$法则，精确度为 $3$.
> $$\int^{x_3}_{x_0}f(x)dx=\frac{3h}{8}[f(x_0)+3f(x_1)+3f(x_2)+f(x_3)]-\frac{3h^5}{80}f^{(4)}(\xi)$$
>
> 当 $n=4$时，精确度为 $5$.
> $$\int^{x_4}_{x_0}f(x)dx=\frac{2h}{45}[7f(x_0)+32f(x_1)+12f(x_2)+32f(x_3)+7f(x_4)]-\frac{8h^7}{945}f^{(6)}(\xi)$$

**开牛顿-柯特斯公式**：与普通牛顿-柯特斯公式类似，不过给定区间为一开区间 $(a,b)$ 这意味着普通牛顿-柯特斯公式中的 $x_0=a$ 与 $x_n=b$ 无效. 我们可以将端点标记为 $x_{-1} = a, x_{n+1}=b$ 再令 $x_0=a+h, x_n=b-1, h=(b-a)/(n+2)$. 公式变为：
$$\int^b_af(x)dx\approx\sum_{i=0}^na_if(x_i), a_i=\int_{x_{-1}}^{x_{n+1}}L_i(x)dx$$
> 当 $n=0$ 时，有中点法则
> $$\int_{x_{-1}}^{x_{1}}f(x)dx=2hf(x_0)+\frac{h^3}{3}f^{''}(\xi)$$
>
> 当 $n=0$ 时，
> $$\int_{x_{-1}}^{x_{2}}f(x)dx=\frac{3h}{2}[f(x_0)+f(x_1)]+\frac{h^3}{3}f^{''}(\xi)$$

### Sec 4 复合数值积分
$Newton-Cotes$ 公式一般不适用于较大的积分区间，且其以等距节点的插值多项式为基础，由于高次多项式的震荡性，这个过程在大区间上并不精确，因此需要分段解决方法.  

我们注意到，将某个函数的区间划分为多个小区间，并在每个区间上应用 $Simpson$ 法则，最后将所有区间上的积分值相加得到的结果具有更高的精确度，为了推广这一过程，选取偶数 $n$ 把区间 $[a,b]$ 划分为 $n$ 个子区间，并在每一对相邻的子区间上应用 $Simpson$ 法则，即 $h=(b-a)/h, x_j=a+jh$.有：  

$$\int^b_af(x)dx=\sum^{n/2}_{j=1}\int_{x_{2j-2}}^{x_{2j}}f(x)dx.$$

即：
$$\int^b_af(x)dx=\frac{h}{3}[f(x_0)+2\sum_{j=1}^{(n/2)-1}f(x_{2j})+4\sum_{j=1}^{n/2}f(x_{2j-1})+f(x_n)]-\frac{b-a}{180}h^4f^{(4)}(\xi_{j})$$ 

事实上，这种划分的方法适用于任何 $Newton-Cotes$ 公式的衍生公式，例如：  

**复合梯形法则**：
$$\int^b_af(x)dx=\frac{h}{2}[f(a)+2\sum_{j=1}^{n-1}f(x_{j})+f(b)]-\frac{b-a}{12}h^2f^{''}(\xi).$$

**复合中点法则**：
$$\int^b_af(x)dx=2h\sum_{j=0}^{n/2}f(x_{2j})+\frac{b-a}{6}h^2f^{''}(\xi).$$



### Sec 5 $Romberg$ 积分
取 $m_1=1, m_2=2,m_3=4,...m_n=2^{n-1}$, 作为复合梯形法则的分段数 $n$, 则有对应于 $m_k$ 的步长 $h_k=(b-a)/2^{h-1}.$ 利用这个记号，梯形法可以变换为：
$$\int^b_af(x)dx=\frac{h_k}{2}[f(a)+f(b)+2(\sum_{i=1}^{2^{k-1}-1}f(a+ih_k))]-\frac{b-a}{12}h_k^2f^{''}(\xi_k).$$

引入记号 $R_{k,1}$ 来表示用于梯形近似的部分.
$$R_{1,1}=\frac{(b-a)}{2}[f(a)+f(b)],$$

$$R_{2,1}=\frac{(b-a)}{4}[f(a)+f(b)+2f(a+\frac{b-a}{2})]\\=\frac{1}{2}[R_{1,1}+h_1f(a+h_2)],$$

归纳后可以得到：
$$R_{k,1}=\frac{1}{2}[R_{k-1,1}+h_{k-1}f(a+(2i-1)h_k)],$$

利用 $Richardson$ 外推法可以加速 $Romberg$ 积分的收敛速度：
$$R_{k,j}=R_{k,j-1}+\frac{R_{k,j-1}-R_{k-1,j-1}}{4^{j-1}-1}.$$

以下代码给出 $Romberg$ 积分的C语言实现
```c
int h = b-a;
R[1][1] = h / 2 * (f(a) + f(b));
for(int i = 2; i <= n; i++)
{
    sum = 0;
    for(int j = 1; j <= pow(2, i-2); i++)
    {
        sum += f(a + (j-0.5)*h);
    }
    R[2][1] = 0.5*(R[1][1] + h * sum);
    for(int j = 2; j <= i; j++)
    {
        R[2][j] = R[2][j] + (R[2][j-1] - R[1][j-1])/(pow(4, j-1)-1);
    }
    h /= 2;
    for(int j = 1; j <= i; j++)
    {
        R[1][j] = R[2][j];
    }
}
```


### Sec 6 自适应求积法
复合积分公式的节点是等距离的，对于一个既包含大的函数变化区域和小的函数变化区域的区间上的函数进行积分时，这种方法存在明显的缺陷。  

**自适应求积**：将辛普森法则应用到函数区间 $[a,b]$ 的子区间 $[a,(a+b)/2]$ 和 $[(a+b)/2, b]$ 上，然后利用误差估计过程来确定每个子区间上的近似值精度是否在 $\epsilon/2$ 之内，如果某个子区间上没有满足，则在这个子区间上继续划分过程，直到精度符合。

### Sec 7 $Gauss$ 求积
$Gauss$ 求积以最优的方式而不是等距离的方式选择求值点。选取区间 $[a,b]$ 内的节点 $x_1, x_2, ... , x_n$ 和系数 $c_1,c_2,...c_n$ 使得近似
$$\int^b_af(x)dx\approx\sum_{i=1}^nc_if(x_i)$$
中所得的误差最小.
> **$Legendre$ 多项式集合**：
> 对于每一个 $n$, $P_n(x)$ 是一个次数为 $n$ 的多项式.
> $\int_{-1}^1P(x)P_n(x)dx=0$ 对于每个次数小于 $n$ 的多项式 $P(x)$ 成立.

假设 $x_1,x_2...x_n$ 是 $n$ 次 $Legendre$ 多项式 $P_n(x)$ 的根，参数 $c_i$ 定义为
$$c_i=\int_{-1}^1\prod_{j=1,j\neq i}^n\frac{x-x_j}{x_i-x_j}dx$$

若 $P(x)$ 为任意次数少于 $2n$ 的多项式，那么有：
$$\int_{-1}^1P(x)dx=\sum_{i=1}^nc_iP(x_i)$$

因此，在区间 $[-1,1]$ 上函数应用求积法所需的常数 $c_i$ 可以由 $Legendre$ 多项式得到，又由于变量替换
$$t=\frac{2x-a-b}{b-a}$$

这就允许 $Gauss$ 求积应用于任何区间，即：
$$\int_a^bf(x)dx=\int_{-1}^1f(\frac{(b-a)t+b+a}2)\frac{b-a}2dt$$

****

## Chap5 : 常微分方程的初值问题
没写...

****

## Chap6 : 解线性方程组的直接法
### Sec 1 线性方程组
具有向后代换的 $Gauss$ 消去法：求解 $n\times n$ 线性方程组
$$E_1:a_{11}x_1+a_{12}x_2+\dots+a_{1n}x_n=a_{1,n+1}\\E_2:a_{21}x_1+a_{22}x_2+\dots+a_{2n}x_n=a_{2,n+1}\\\dots\\ E_n:a_{n1}x_1+a_{n2}x_2+\dots+a_{nn}x_n=a_{n,n+1}\\$$   

可以通过以下算法求解(输入增广矩阵 $\mathbf{A}=(a_{ij})$. )：
```c
for(int i = 1;i<=n-1;i++)
{
    /* Elinmination */
    for(int j = i; j <= n; j++)   /* Find min p where a[p][i] != 0 */
        if(a[j][i] != 0) { p = j; break; }
    if(j == n + 1)                /* This means a row of A is 0, the rank is not full by then */
        output("no unique solution.");
    if(p != i) switchRow(a[i], a[p]);
    for(int j = i + 1; j <= n; j++)  /* 1 */
    {
        m[j][i] = a[j][i] / a[i][i];
        a[j] = a[j] - m[j][i] * a[i]; /* Not actual implementation */
    }
}
if(a[n][n] == 0) output("no unique solution."); /* The rank is not full */

/* Backward Substitution */
x[n] = a[n][n+1] / a[n][n];
for(int i = n - 1; i >= 1; i--)
{
    sumof_ax = ...;
    x[i] = (a[i][n+1] - sumof_ax) / a[i][i];
}
output(x); /* Solved successfully */
```

其中，`sumof_ax`的计算过程被略去，其等于$\sum^n_{j=i+1} a_{ij}x_j$.  

$Gauss$ 消去法的复杂度分析：
> 消去过程中，加/减/乘/除 运算发生在 $1$ 处的 $for$ 循环中，对于每一个 $i$, 此处一共有  
> + $(n-i)(n-i+2)$ 次乘法/除法.
>   + $for$ 循环中的第一行 1 次除法
>   + $for$ 循环中的第二行 $n-i+1$ 次乘法
>   + 总共 $n-i$ 次 $for$ 循环，且每次乘除法次数与 $for$ 循环下标无关
> + $(n-i)(n-i+1)$ 次加法/减法.
>   + $for$ 循环中的第二行 $n-i+1$ 次减法
>   + 总共 $n-i$ 次 $for$ 循环，且每次加减法次数与 $for$ 循环下标无关  
>
> 由于以上次数发生在 $for$ 循环中，求和得  
> + 乘法/除法: $\frac{2n^3+3n^2-5n}6.$
> + 加法/减法: $\frac{n^3-n}3.$  
>
> 用类似的方法也可求得向前代换过程中的加/减/乘/除个数，对 $Gauss$ 法，有  
> + 总的乘法/除法数: $\frac{n^3}3+n^2-\frac n3.$
> + 总的加法/减法数: $\frac{n^3}3+\frac{n^2}2-\frac {5n}3.$
>  
无需向后代换的 $Gauss-Jordan$ 方法：使用第 $i$ 个方程 $E_i$ 从方程 $E_1, E_2,\dots,E_{i-1}, E_{i+1}, E_{i+2}, \dots, E_n$ 中消去，将某线性方程组 $[\mathbf{A},\mathbf{b}]$ 简化为:
$$\begin{bmatrix}
    \begin{array}{cccc:c}
    a_{11}^{(1)}&0&\dots&0&a_{1,n+1}^{(1)}\\
    0&a_{22}^{(2)}&\dots&0&a_{2,n+1}^{(2)}\\
    \dots&\dots&\dots&0&\dots\\
    0&\dots&0&a_{nn}^{(n)}&a_{n,n+1}^{(n)}
    \end{array}
\end{bmatrix}$$  

后，可以快速的得出答案。这一过程避免了 $Gauss$ 法中的向后代换。使用以下程序可以实现：
```c
for(int i = 1;i<=n;i++)
{
    /* Elinmination */
    for(int j = i; j <= n; j++)   /* Find min p where a[p][i] != 0 */
        if(a[j][i] != 0) { p = j; break; }
    if(j == n + 1)                /* This means a row of A is 0, the rank is not full by then */
        output("no unique solution.");
    if(p != i) switchRow(a[i], a[p]);
    for(int j = i - 1; j >= 1; j--)  /* 1 */
    {
        n[j][i] = a[j][i] / a[i][i];
        a[j] = a[j] - n[j][i] * a[i]; /* Not actual implementation */
    }
    for(int j = i + 1; j <= n; j++)  /* 2 */
    {
        m[j][i] = a[j][i] / a[i][i];
        a[j] = a[j] - m[j][i] * a[i]; /* Not actual implementation */
    }
}
if(a[n][n] == 0) output("no unique solution."); /* The rank is not full */
output(x); /* Solved successfully */
```

$Gauss-Jordan$ 方法的复杂度分析：
> 消去过程中，加/减/乘/除 运算发生在 $1$、 $2$ 处的 $for$ 循环中，对于每一个 $i$, 两个 $for$ 一共有
> + $(n-1)(n-i+2)$ 次乘法/除法.
>   + $for$ 循环中的第一行 1 次除法
>   + $for$ 循环中的第二行 $n-i+1$ 次乘法
>   + 总共 $n-1$ 次 $for$ 循环，且每次乘除法次数与 $for$ 循环下标无关
> + $(n-1)(n-i+1)$ 次加法/减法.
>   + $for$ 循环中的第二行 $n-i+1$ 次减法
>   + 总共 $n-i$ 次 $for$ 循环，且每次加减法次数与 $for$ 循环下标无关  
>
> 由于以上次数发生在 $for$ 循环中，**注意：与 $Gauss$ 法不同的是，下标 $i$ 需要计算到 n**. 求和得  
> + 乘法/除法: $\frac{n^3}3+n^2-\frac{3n}2.$
> + 加法/减法: $\frac{n^3}2-\frac{n}2.$  
>

### Sec 2 选主元策略
在 $Gauss$ 算法中，需要考虑主元为0时的情况，进行行的交换。然而，为了减小摄入误差，即使主元不是0，有时也需要进行行交换。  

如果某方程中，某非主元系数 $a_{jk}^{(k)}$ 的绝对值比主元 $a_{kk}^{(k)}$ 大得多，那么他们的比将会比 1 大得多。计算 $a_{jl}^{(k+1)}$ 时，由于使用 $a_{kk}^{(k)}$ 相除，分子的任何误差都会被放大很多。例如，考虑线性方程组：
$$\begin{bmatrix}
    0.00300&59.14 \\
    5.291&-6.130
\end{bmatrix}\begin{bmatrix}
    x_1\\
    x_2
\end{bmatrix}=\begin{bmatrix}
    59.17\\
    46.78
\end{bmatrix}$$
采取4位舍入运算 $Gauss$ 法。  

第一主元 $a_{11}^{(1)}=0.003000$ 很小，这使得系数 $m_{21}=1763.66 \approx^4 1764$ 非常大，进行 $E_2 \leftarrow E_2-m_{21}E_1$ 得出
$$0.003000x_1+59.14x_2\approx^4 59.17.\\-104300x_2\approx^4-104400.$$
向后代换得到 $x_2\approx^41.001, x_1\approx^4-10.00$，然而 $x_1$ 的精确解为 $10$ 可以看出，误差被很大的放大了，因此我们要进行主元的选择。  
+ **部分选主元/最大列选主元**：在对角线下的同一列中选取一个具有最大绝对值的元素，即：选取最小的 $p\ge k$ 使得 $|a_{pk}^{(k)}|=max_{k\le i\le n}|a_{ik}^{(k)}|$，再进行 $E_k \leftrightarrow E_p$.  
+ **比例因子选主元**：对每一行定义一个比例因子 $s_i=\max_{i\le j\le n}|a_{ij}|$ ，如果存在 $s_i=0$ 那么说明方程组无唯一解。否则，在对角线下的同一列中选出最大的 $p\ge k$, 使得$$\frac{|a_{pi}|}{s_p}=\max_{i\le k\le n}\frac{|a_{ki}|}{s_k}.$$ 接着进行 $E_k \leftrightarrow E_p$.  
+ **完全选主元**：完全选主元策略在第 $k$ 步搜索范围 $i = k,k+1,\dots,n$ 和 $j=k,k+1,\dots,n$ 的所有项 $a_{ij}$ 中的最大绝对值项。并进行行交换和列交换把此项换到主元位置。增加的总计算量为:$$\sum_{k=2}^n(k^2-1)=\frac{n(n-1)(2n+5)}6.$$

### Sec 3 矩阵分解
$\mathbf{LU}$ 分解：找到下三角的矩阵 $\mathbf{L}$ 与上三角的矩阵 $\mathbf{U}$ 使得线性方程组 $\mathbf{A}x=\mathbf{b}$ 满足 $\mathbf{A}=\mathbf{LU}$，可以通过一个简单的二步过程容易的确定出 $x$ :
+ 设 $\mathbf{y}=\mathbf{Ux}$, 解出 $\mathbf{Ly}=\mathbf{b}$, 再将 $\mathbf{y}$ 代回，解出 $\mathbf{x}$.
+ 复杂度从 $O(n^3/3)$ 优化到了 $O(2n^2)$.  
+ 然而，$\mathbf{LU}$ 分解过程并不是无偿的，事实上，他的复杂度为 $O(n^3/3)$  

$\mathbf{LU}$ 分解的进行依赖以下步骤：
+ 记 $\mathbf{A}^{(1)}=\mathbf{A}$.
+ $\mathbf{A}^{(2)}=\mathbf{M}^{(1)}A$, 其中，$$\mathbf{M}^{(1)}=\begin{bmatrix}
    1 & 0 &\dots&0\\
    -m_{21}&1&\dots&0\\
    -m_{31}&0&\dots&0\\
    \dots&\dots&\dots&\dots\\
    -m_{n1}&0&\dots&1
\end{bmatrix}$$
+ $\mathbf{A}^{(k)}=\mathbf{M}^{(k-1)}\mathbf{M}^{(k-2)}\dots \mathbf{M}^{(1)}A$,其中，$$\mathbf{A}^{(k)}=\begin{bmatrix}
    a_{11}^{(1)} & a_{12}^{(1)}  &\dots&a_{1n}^{(1)} \\
    0&a_{22}^{(2)}&\dots&a_{2n}^{(2)} \\
    0&0&\dots&a_{3n}^{(3)}\\
    \dots&\dots&\dots&\dots\\
    0&0&\dots&a_{nn}^{(n)} 
\end{bmatrix}$$
+ 令 $\mathbf{L}^{(k)}=[\mathbf{M}^{(k)}]^{-1}.$
+ 令 $\mathbf{U}=\mathbf{A}^{(k)}, \mathbf{L}=\mathbf{L}^{(1)}\mathbf{L}^{(2)}\dots\mathbf{L}^{(n-1)}.$ 

$\mathbf{LU}$ 分解完成. 也即：
$$\mathbf{L}=\begin{bmatrix}
    1 & 0 &\dots&0\\
    m_{21}&1&\dots&0\\
    m_{31}&m_{32}&\dots&0\\
    \dots&\dots&\dots&\dots\\
    m_{n1}&m_{n2}&\dots&1
\end{bmatrix},\mathbf{U}=\begin{bmatrix}
    a_{11}^{(1)} & a_{12}^{(1)}  &\dots&a_{1n}^{(1)} \\
    0&a_{22}^{(2)}&\dots&a_{2n}^{(2)} \\
    0&0&\dots&a_{3n}^{(3)}\\
    \dots&\dots&\dots&\dots\\
    0&0&\dots&a_{nn}^{(n)} 
\end{bmatrix}$$
其中 $\mathbf{U}$ 即是 $Gauss$ 消元向前代换前的结果，继续前面的方法可以解出 $\mathbf{x}.$ 这样的解法叫做 $Doolittle$ 方法.   

$\mathbf{LU}$ 分解的一个实现：
```c
for(int k = 1; k <= n - 1; k++)                
{
    for(int i = k + 1; i <= n; i++)                /* (n-k) Div */
    {
        L[i][k]=a[i][k]/a[k][k];                   /* 1 Div */
    }
    for(int j = k; j <= n; j++)
    {
        U[k][j] = a[k][j]
    }
    for(int i = k + 1; i <= n; i++)                /* (n-k)^2 Mul; (n-k)^2 Sub */
    {
        for(int j = k + 1; j <= n; j++)            /* (n-k) Mul; (n-k) Sub */
            a[i][j] = a[i][j] - L[i][j]*U[k][j];   /* 1 Mul; 1 Sub */
    }
}
```

解出$\mathbf{y}$: $$y_1=\frac{b_1}{l_{11}},y_i=\frac1{l_{ii}}[b_i-\sum_{j=1}^{i-1}l_{ij}y_j].( i=2,3\dots n )$$
再解出 $\mathbf{x}$: $$x_n=\frac{y_n}{u_{nn}},x_i=\frac1{u_{ii}}[y_i-\sum_{j=i+1}^{n}u_{ij}x_j].( i=1,2\dots n-1 )$$
$\mathbf{LU}$ 分解的复杂度：
> $\mathbf{LU}$ 分解算法需要 $$\sum_{k=1}^{n-1}(n-k)(n-k+1)=\frac13n^3-\frac13n$$ 次乘法/除法；$$\sum_{k=1}^{n-1}(n-k)^2=\frac13n^3-\frac12n^2+\frac16n$$ 次加法/减法.    

> 从 $\mathbf{Ly}=\mathbf{b}$ ( $\mathbf{L}$ 主对角线为 1 ) 中解出 $\mathbf{y}$ 需要：$$\sum_{i=2}^n(i-1)=\frac12n^2-\frac12n$$ 次乘法/除法; $$\sum_{i=2}^n(i-1)=\frac12n^2-\frac12n$$ 次加法/减法;

> 从 $\mathbf{Ux}=\mathbf{y}$ 中解出 $\mathbf{x}$ 需要：$$1+\sum_{i=1}^{n-1}n-i+1=\frac12n^2+\frac12n$$ 次乘法/除法; $$\sum_{i=1}^{n-1}(n-i)=\frac12n^2-\frac12n$$ 次加法/减法;

> 因此，先对矩阵进行 $\mathbf{LU}$ 分解，再解出 $\mathbf{x}$ 一共需要：$$\frac13n^3+n^2-\frac13n$$ 次乘法/除法; $$\frac13n^3+\frac12n^2-\frac56n$$ 次加法/减法，可以看到这一方法与普通 $Gauss$ 消元法有着相同的计算量。  

如果需要进行行交换，可以引入 $n\times n$ 的置换矩阵 $\mathbf{P}$ , 令 $\mathbf{PA}=\mathbf{LU}$, 那么 $\mathbf{A}=\mathbf{P^{-1}LU}= \mathbf{P^TLU}$

### Sec 6 特殊类型的矩阵
**严格对角占优阵**: 矩阵 $\mathbf{A}$ 称作严格对角占优的，当 $$|a_{ii}|>\sum_{j=1,j\neq i}^n|a_{ij}|.$$对每一个 $i=1,2...n$ 成立时。  

严格对角占优阵是满秩的。并且，对于此严格对角占优阵 $\mathbf{A}$， $Gauss$ 消去法可以用在形如 $\mathbf{Ax=b}$ 的任何线性方程组以得到其唯一解而不需要行交换或列交换。对于舍入误差的增长而言计算是稳定的。  

**正定阵**：一个阵 $\mathbf{A}$ 是正定的若
+ $\mathbf{A^T=A},$
+ $\mathbf{x^TAx}>0,$ 对于任意向量 $\mathbf{x}.$  

且  $\mathbf{A}$ 是正定的 $\leftrightarrow$ 
+ $\mathbf{A}$ 满秩.
+ $\mathbf{A}$ 的主对角线元素 $a_{ii}>0.$
+ $\max_{1\le k,j\le n}|a_{kj}|\le \max_{1\le i \le n}|a_{ii}|.$
+ $(a_{ij})^2<a_{ii}a_{jj},$ 对每个 $i\neq j.$
+ 没有行交换的 $Gauss$ 消去法可以在所有主元为正的线性方程组 $\mathbf{Ax=b}$ 上进行，计算相对于舍入误差的增长稳定.  
+ $\mathbf{A}$ 可分解为形式 $\mathbf{LDL}^T$, 其中， $\mathbf{L}$ 是主对角线元素全为 1 的下三角矩阵，$\mathbf{D}$ 是主对角线值为正的对角矩阵.
+ $\mathbf{A}$ 可分解为形式 $\mathbf{LL}^T$, 其中， $\mathbf{L}$ 是具有非零主对角线元素的下三角矩阵.  

推论：若 $\mathbf{A}$ 在 $Gauss$ 消去法的过程中无需行交换，那么 $\mathbf{A}$ 可以进行 $\mathbf{LDL}^T$ 分解。

 $\mathbf{LDL}^T$ 分解：
 ```c
 for(int i = 1; i <= n; i++)
 {
    for(int j = 1; j <= i + 1; j++)
        v[j] = L[i][j]*D[j];
    sum_lv = ...;
    D[i] = a[i][i] - sum_lv
    for(int j = i + 1; j <= n; j++)
        sum_lv2 = ...;
        L[j][i] = (a[j][i] - sum_lv2) / d[i];
 }
 ```
 
其中 `sum_lv` = $\sum_{j=1}^{i-1}l_{ij}v_j,$ `sum_lv2` = $\sum_{k=1}^{i-1}l_{jk}v_k.$  

$\mathbf{LDL}^T$ 分解的复杂度：
> $\mathbf{LDL}^T$ 分解算法需要 $$\sum_{i=1}^{n}2(i-1)+i(n-i)=\frac16n^3+n^2-\frac76n$$ 次乘法/除法；$$\sum_{i=1}^{n}(i-1)(n-i+1)=\frac16n^3-\frac16n$$ 次加法/减法.    

$\mathbf{LL}^T$ 分解 ( $Choleski$ 算法 )：
```c
L[1][1] = sqrt(a[1][1]);
for(int j = 2; j <= n; j++)
    L[j][1] = a[j][1] / l[1][1];
for(int i = 2; j <= n - 1; j++)
{
    L[i][i] = sqrt(a[i][i] - sum_l);
    for(int j = i + 1; j <= n; j++)
        L[j][i] = (a[j][i] - sum_l2)/L[i][i];
}
L[n][n] = sqrt(a[n][n] - sum_l3);
```

其中 `sum_l` = $\sum_{k=1}^{i-1}l_{ik}^2,$ `sum_l2` = $\sum_{k=1}^{i-1}l_{jk}l_{ik}.$ `sum_l3` = $\sum_{k=1}^{n-1}l_{nk}^2.$   

$\mathbf{LL}^T$ 分解的复杂度：
> $\mathbf{LL}^T$ 分解算法需要 $$\sum_{i=1}^{n}(i-1)+i(n-i)=\frac16n^3+\frac12n^2-\frac23n$$ 次乘法/除法；$$\sum_{i=1}^{n}(i-1)(n-i+1)=\frac16n^3-\frac16n$$ 次加法/减法. 
> 但是，$\mathbf{LL}^T$ 还需要 $n$ 次平方根运算。

**带状矩阵**：$\mathbf{A}$ 是一个带状矩阵，如果存在整数 $p$ 和 $q$ ($1< p,q<n$) 使得当 $i+p\le j$ 或 $j+q\le i$ 时，有 $a_{ij}=0.$ $\mathbf{A}$ 的带宽定义为 $\omega=p+q-1.$  

**三对角矩阵**：当 $p=q=2, \omega=3$ 时的带状矩阵被称作三对角矩阵.  
三对角矩阵的性质可以使他的分解过程大大简化，可以对其进行 $Crout$ 分解。

$Crout$ 分解：三对角矩阵可以通过 $Crout$ 分解得到一个下三角矩阵 $\mathbf{L}$, 和一个主对角线元素为 1 的上三角矩阵 $\mathbf{U}$.
$$\begin{bmatrix}
    a_1&c_1&0&\dots&0\\
    b_2&a_2&c_2&\dots&0\\
    0&b_3&a_3&\dots&0\\
    \dots&\dots&\dots&\dots&\dots\\
    0&0&0&\dots&a_n
\end{bmatrix}=\begin{bmatrix}
    l_1&0&0&\dots&0\\
    m_2&l_2&0&\dots&0\\
    0&m_3&l_3&\dots&0\\
    \dots&\dots&\dots&\dots&\dots\\
    0&0&0&\dots&l_n
\end{bmatrix}\begin{bmatrix}
    1&u_1&0&\dots&0\\
    0&1&u_2&\dots&0\\
    0&0&1&\dots&0\\
    \dots&\dots&\dots&\dots&\dots\\
    0&0&0&\dots&1
\end{bmatrix}$$

其中，$$\begin{array}
rm_i=b_i;\\
l_i=a_i-m_iu_{i-1};\\
u_i=c_i/l_i;
\end{array}$$

以下代码可用于产生矩阵 $\mathbf{A}=(a_{ij})_{n\times n}$ 的 $Crout$ 分解并解出 $x$
```c
L[1][1] = a[1][1];
U[1][2] = a[1][2]/L[1][1];
z[1] = a[1][n+1]/L[1][1];
for(int i = 2; i <= n-1; i++)
{
    L[i][i-1] = a[i][i-1];
    L[i][i] = a[i][i]-L[i][i-1] * U[i-1][i];
    U[i][i+1] = a[i][i+1] / L[i][i];
    Z[i] = (a[i][n+1] - L[i][i-1] * z[i-1]) / L[i][i];
}
L[n][n-1] = a[n][n-1];
L[n][n] = a[n][n] - L[n][n-1] * U[n-1][n];
z[n] = (a[n][n+1] - L[n][n-1]*z[n-1])/L[n][n];
x[n] = z[n];
for(int i = n-1; i >= 1; i--)
{
    x[i] = z[i] - U[i][i+1]*x[i+1];
}
```

$Crout$ 分解的复杂度：
> $Crout$ 分解算法需要 $$4+n+\sum_{i=2}^{n-1}4=5n-4$$ 次乘法/除法；$$1+n+\sum_{i=2}^{n-1}2=3n-3$$ 次加法/减法. 
****

## Chap7 : 矩阵代数中的迭代方法
### Sec 1 向量和矩阵的范数
**向量范数**：$\mathbf{R}^n$ 上的向量范数是一个从 $\mathbf{R}^n\rightarrow\mathbf{R}$ 的函数 $f(\mathbf{x}) =||\mathbf{x}||$, 如果他满足以下的性质
1. $||\mathbf{x}||\ge0$ , 对任意 $\mathbf{x}\in\mathbf{R}^n.$
2. $||\mathbf{x}|| =0\leftrightarrow\mathbf{x=0}.$
3. $||\mathbf{\alpha x}|| =|\alpha|\cdot||\mathbf{x}||$ , 对任意 $\mathbf{x}\in\mathbf{R}^n.$
4. $||\mathbf{x+y}||\le ||\mathbf{x}||+||\mathbf{y}||$ , 对任意 $\mathbf{x},\mathbf{y}\in\mathbf{R}^n.$  

**$l_2$ 范数**：向量 $\mathbf{x}$ 的 $l_2$ 范数定义为 $$||\mathbf{x}||_2=\sqrt{\sum_{i=1}^nx_i^2}.$$  

事实上，向量的 $l_2$ 范数就是该向量在欧氏空间中到原点的距离，因此也被称作 $Euclidean$ 范数.

**$l_\infty$ 范数（无穷范数）**：向量 $\mathbf{x}$ 的 $l_\infty$ 范数定义为 $$||\mathbf{x}||_\infty=\max_{1\le i\le n}{|x_i|}.$$  

向量的范数给出了任意向量和 $\mathbf{0}$ 向量之间距离的度量，因此两个向量间的距离可以由向量之差的范数定义。

**矩阵范数**：$n\times n$ 矩阵集合上定义的 函数 $f:\mathbf{R}^{n\times n}\rightarrow\mathbf{R}$ 是一个矩阵范数, 如果对于任意矩阵 $\mathbf{A,B}\in\mathbf{R}^{n\times n}$ 满足：
1. $||\mathbf{A}||\ge0$.
2. $||\mathbf{A}|| =0\leftrightarrow\mathbf{A=0}.$
3. $||\mathbf{\alpha A}|| =|\alpha|\cdot||\mathbf{A}||$.
4. $||\mathbf{AB}||\le ||\mathbf{A}||\cdot||\mathbf{B}||$.

**自然/从属矩阵范数**：假设 $||\cdot||$ 为 $\mathbf{R}^n$ 上的一个向量范数，那么定义矩阵的自然矩阵范数为：
$$||\mathbf{A}||=\max_{||\mathbf{x}||=1}||\mathbf{Ax}||.$$

自然范数下的矩阵范数与单位向量范数密切相关。$\mathbf{Ax}$ 可以考虑为对 $\mathbf{x}$ 的一个变换.

**矩阵从属无穷范数**：定义 $\mathbf{A}=(a_{ij})$ 为一个 $n\times n$ 的矩阵，取 $||\cdot||_\infty$ 为 $\mathbf{R}^n$ 上的向量 $l_\infty$ 范数, 那么矩阵的从属无穷范数为
$$||\mathbf{A}||_\infty=\max_{||\mathbf{x}||=1}||\mathbf{Ax}||_\infty=\max_{1\le i\le n}\sum_{j=1}^n|a_{ij}|.$$

### Sec 2 特征值与特征向量
回忆线性代数知识：一个 $n\times n$ 矩阵可以看作一个 $n$ 维空间到自己的函数。此情况下，某些非零向量 $\mathbf{x}$ 与 $\mathbf{Ax}$ 平行，即存在常数 $\lambda, \mathbf{Ax}=\lambda\mathbf{x}.$ $\mathbf{A}$ 的特征多项式定义为 $p(\lambda)=\det{(\mathbf{A}-\lambda\mathbf{I})}.$ $\mathbf{A}$ 关于某特征值 $\lambda_1$ 的特征向量是 $(\mathbf{A}-\lambda\mathbf{I})\mathbf{x=0}$ 的解向量.  

**谱半径**：矩阵 $\mathbf{A}$ 的谱半径 $\rho(\mathbf{A})$ 定义为 $\rho(\mathbf{A})=\max{|\lambda|},\lambda$ 为 $\mathbf{A}$ 的一个特征值  

谱半径与矩阵范数联系紧密：如果 $\mathbf{A}$ 是一个 $n\times n$ 矩阵，那么有
1. $||\mathbf{A}||_2=\sqrt{\rho(\mathbf{A^TA})}.$
2. 对于任一自然范数，有 $\rho(\mathbf{A})\le ||\mathbf{A}||.$  

利用上述性质 1 , 我们可以计算矩阵的从属 $l_2$ 范数.  

在研究矩阵的迭代方法的过程中，需要知道矩阵的幂何时变小非常重要，此类矩阵被称作**收敛**的

矩阵的**收敛性**：若 $n\times n$ 的矩阵 $\mathbf{A}$ 满足：
$$\lim_{k\to \infty}(\mathbf{A}^k)_{ij}=0, (i=1,2\dots,n;j=1,2\dots,n ).$$

则该矩阵**收敛**.

矩阵的谱半径和其收敛性之间有重要联系，事实上，以下结论等价：
1. $\mathbf{A}$ 收敛.
2. 对于某些自然范数，有 $\lim_{n\to\infty}||\mathbf{A}^n||=0.$
3. 对于所有自然范数，有 $\lim_{n\to\infty}||\mathbf{A}^n||=0.$
4. $\rho(\mathbf{A})<1.$
5. 对任意 $\mathbf{x}$, 有 $\lim_{n\to\infty}\mathbf{A}^n\mathbf{x}=\mathbf{0}.$


### Sec 3 求解线性方程组的迭代法
求解线性方程组的迭代法的基本思路就是先给出解 $\mathbf{x}$ 的一个初始近似 $\mathbf{x}^{(0)}$, 接着一系列近似解向量可以通过以下公式求得：
$$\mathbf{x}^{(k)}=\mathbf{Tx}^{(k-1)}+\mathbf{c}.$$

**$Jacobi$ 迭代**：把矩阵 $\mathbf{A}$ 分解为严格上三角部分 $\mathbf{U}$, 严格下三角部分 $\mathbf{L}$ 和对角线部分 $\mathbf{D}$，有
$$\mathbf{A}=\mathbf{D-L-U}.$$

方程 $\mathbf{Ax=b}$，即 $\mathbf{(D-L-U)x=b}$ 被转换为 $\mathbf{Dx=(L+U)x+b}$, 即
$$\mathbf{x}=\mathbf{D}^{-1}\mathbf{(L+U)x+D}^{-1}\mathbf{b}.$$

即可得到迭代形式 $$\mathbf{x}^{(k)}=[\mathbf{D}^{-1}\mathbf{(L+U)]x}^{(k-1)}+[\mathbf{D}^{-1}\mathbf{b}].$$

即 $$x_i^{(k)}=\frac{-\sum_{j=1,j\neq i}^n(a_{ij}x^{(k-1)}_j)+b_i}{a_{ii}},i=1,2,\dots,n.$$

$Jacobi$ 迭代可由以下代码实现：
```c
int k = 1;
while(k<=n)
{
    for(int i = 1; i <= n; i++)
    {
        x[i] = (-sum__x+b[i])/a[i][i];
    }
    if(||x-_x||<TOL) return;
    k++;
    _x=x;
}
```

其中 `sum__x` = $\sum_{j=1,j\neq i}^n(a_{ij}x^\prime_j)$. $Jacobi$ 迭代 要求 $a_{ii}\neq 0,$ 如果存在 $a_{ii}= 0,$ 可以通过重新排列来解决.

$Gauss-Seidal$ 迭代：由于在计算 $x^{(k)}_i$ 时，前 $i-1$ 项已经算出来了，并且比 $x^{(k-1)}_1,\dots,x^{(k-1)}_{i-1}$ 更接近实际解，因此可以进行改进，使用： 
$$x_i^{(k)}=\frac{-\sum_{j=1}^{i-1}(a_{ij}x^{(k)}_j)-\sum_{j=i+1}^{n}(a_{ij}x^{(k-1)}_j)+b_i}{a_{ii}},i=1,2,\dots,n.$$  

即
$$\mathbf{x}^{(k)}=[\mathbf{(D-L)}^{-1}\mathbf{U]x}^{(k-1)}+[\mathbf{(D-L)}^{-1}\mathbf{b}].$$

$Gauss-Seidal$ 方法在大多数情况下优于 $Jacobi$ 方法.

考虑一般迭代方法的收敛性，公式 
$$\mathbf{x}^{(k)}=\mathbf{Tx}^{(k-1)}+\mathbf{c}.$$

如果谱半径 $\rho(\mathbf{T})<1,$ 那么 $(\mathbf{I-T})^{-1}$ 存在，且
$$(\mathbf{I-T})^{-1}=\mathbf{I+T+T^2+\dots}=\sum_{j=0}^\infty \mathbf{T}^j.$$

对于任意的 $\mathbf{x}^{(0)}\in\mathbf{R}^n$, 由
$$\mathbf{x}^{(k)}=\mathbf{Tx}^{(k-1)}+\mathbf{c}.$$

定义的序列收敛到 $\mathbf{x}=\mathbf{Tx}+\mathbf{c}$ 的唯一解 $\leftrightarrow\rho(\mathbf{T})<1.$
> 证明：假设 $\rho(\mathbf{T})<1$, 那么，$$\mathbf{x}^{(k)}=\mathbf{Tx}^{(k-1)}+\mathbf{c}=\mathbf{T^2x}^{(k-2)}+\mathbf{(T+I)c}=\dots=\\\mathbf{T^kx}^{(0)}+\mathbf{(\mathbf{T}^{k-1}+\dots+T+I)c}$$
>
>根据前面的定理，$k\to\infty$ 时 $\mathbf{T}^{k-1}+\dots+\mathbf{T+I} = (\mathbf{I-T})^{-1}.$ 又有，$\rho(\mathbf{T})<1$, $\mathbf{T}$ 收敛， $\lim_{k\to\infty}\mathbf{T^kx}^{(0)}=0$.
>
>则有 $\mathbf{x} = (\mathbf{I-T})^{-1}\mathbf{c}.$ 作为 $\mathbf{x}$ 的唯一解.

如果对任意自然矩阵范数 $||\mathbf{T}||<1$, 那么由 $$\mathbf{x}^{(k)}=\mathbf{Tx}^{(k-1)}+\mathbf{c}.$$

定义的序列满足以下误差界：
1. $||\mathbf{x-x}^{(k)}||\le ||\mathbf{T}||^k||\mathbf{x^{(0)}-x}||.$
2. $||\mathbf{x-x}^{(k)}||\le \frac{||\mathbf{T}||^k}{1-||\mathbf{T}||}||\mathbf{x^{(1)}-x^{(0)}}||.$

如果 $\mathbf{A}$ 是严格对角占优阵，那么对于任选的 $\mathbf{x}^{(0)}$, $Jacobi$ 和 $Gauss-Seidal$ 迭代法给出的序列都收敛于方程的唯一解.

可以得出
$$||\mathbf{x^{(k)}-x}||\approx\rho(\mathbf{T}^k)||\mathbf{x^{(0)}-x}||.$$

因此最好选择谱半径小于 1 的迭代法.

**剩余向量**:若 $\widetilde{x}\in\mathbf{R}^n$ 是线性方程组 $\mathbf{Ax=b}$ 的一个近似解，定义相对于该方程组的 $\widetilde{x}$ 的剩余向量为 $\mathbf{r=b-A\widetilde{x}}.$

$Gauss-Seidal$ 方法可以重新表示为 $$x_i^{(k)}=x_i^{(k-1)}+\frac{r_{ii}^{(k)}}{a_{ii}}.$$

我们可以对 $Gauss-Seidal$ 方法进行修改，添加系数 $\omega$:$$x_i^{(k)}=x_i^{(k-1)}+\omega\frac{r_{ii}^{(k)}}{a_{ii}}.$$

可以减少剩余向量的范数并且明显的加速收敛.
+ $\omega=1,$ $Gauss-Seidal$ 法
+ $0<\omega<1,$ 低松弛法.
+ $1<\omega,$ 超松弛法.
+ 这些方法缩写为 $SOR$ 方法.

$SOR$ 方法可以写作矩阵形式：$$\mathbf{x}^{(k)}=(\mathbf{D}-\omega\mathbf{L})^{-1}[(1-\omega)\mathbf{D}+\omega\mathbf{U}]\mathbf{x}^{(k-1)}+\omega(\mathbf{D}-\omega\mathbf{L})^{-1}\mathbf{b}.$$

$Kahan$ 定理：如果 $a_{ii}\neq0$, 那么 $\rho(\mathbf{T}_\omega)\ge|\omega-1|,$ 这表明 $SOR$ 方法仅当 $0<\omega<2$ 时收敛.

$Ostrowski-Reich$ 定理：若矩阵 $\mathbf{A}$ 正定, 且 $0<\omega<2$ , $SOR$ 方法收敛.

如果矩阵 $\mathbf{A}$ 是正定三对角阵，$SOR$ 方法的最佳 $\omega=\frac2{1+\sqrt{1-[\rho(\mathbf{T}_j)]^2}},$ $\rho(\mathbf{T}_\omega)=\omega-1$

****

## Chap8 : 逼近论
### Sec 1 离散最小二乘逼近
**线性最小二乘逼近**：给定 $n$ 个数据点 $(x_1,y_1),(x_2,y_2),...(x_n,y_n)$ 寻找其最佳线性逼近方程的问题就是找到 $a_0$ 和 $a_1$ 使得
$$E_2(a_0,a_1)=\sum_{i =1}^{n}[y_i-(a_1x_i+a_0)]^2$$

达到最小值，即最小二乘逼近.   

为了确定 $a_0$ 和 $a_1$，解方程：
$$\frac\partial{\partial a_0}\sum_{i =1}^{n}[y_i-(a_1x_i+a_0)]^2=0$$

和
$$\frac\partial{\partial a_1}\sum_{i =1}^{n}[y_i-(a_1x_i+a_0)]^2=0$$

可得解为：
$$a_0=\frac{\sum_{i=1}^nx_i^2\sum_{i=1}^ny_i-\sum_{i=1}^nx_iy_i\sum_{i=1}^nx_i}{n(\sum_{i=1}^nx_i^2)-(\sum_{i=1}^nx_i)^2}$$

和
$$a_1=\frac{n\sum_{i=1}^nx_iy_i-\sum_{i=1}^nx_i\sum_{i=1}^ny_i}{n(\sum_{i=1}^nx_i^2)-(\sum_{i=1}^nx_i)^2}$$

**更高阶的最小二乘逼近**：考虑 $n$ 阶多项式 $P_n(x)=a_nx^n+a_{n-1}x^{n-1}+...+a_1x+a_0$,有 
$$E_2=\sum_{i=1}^m(y_i-P_n(x_i))^2$$

类似于求解线性最小二乘逼近的过程，我们可以得到 $n+1$ 个方程，可以解出唯一的一组 $\{a_0,a_1,...a_n\}$：
$$\sum_{k=0}^na_k\sum_{i=1}^mx_i^{j+k}=\sum_{i=1}^my_ix_i^j,j=0,1,...n$$

可以写成以下矩阵形式：
$$\begin{bmatrix}
    m    & \sum_{i=1}^mx_i & \sum_{i=1}^mx_i^2 & \dots & \sum_{i=1}^mx_i^n \\
    \sum_{i=1}^mx_i    & \sum_{i=1}^mx_i^2 & \sum_{i=1}^mx_i^3 & \dots & \sum_{i=1}^mx_i^{n+1} \\
    \dots & \dots & \dots & \dots & \dots    \\
    \sum_{i=1}^mx_i^n  & \sum_{i=1}^mx_i^{n+1} & \sum_{i=1}^mx_i^{n+2} & \dots & \sum_{i=1}^mx_i^{2n}
\end{bmatrix}
\begin{bmatrix}
    a_0\\
    a_1\\
    \dots\\
    a_n 
\end{bmatrix}=\\
\begin{bmatrix}
    \sum_{i=1}^my_i\\
    \sum_{i=1}^my_ix_i\\
    \dots\\
    \sum_{i=1}^my_ix_i^n
\end{bmatrix}$$

**指数的最小二乘逼近**：
考虑逼近函数的以下形式
$$y=be^{ax},y=bx^a$$

此时可以对方程两边求对数，得到
$$\ln{y}=\ln{b}+ax,\ln y=\ln b + a\ln x$$

从而将其转化为线性的最小二乘逼近.

### Sec 2 正交多项式和(连续的)最小二乘逼近
考虑区间 $[a,b]$ 上函数 $f$ ，需要一个多项式 $P_n(x)$ 来使得误差
$$E=\int_a^b[f(x)-P_n(x)]^2dx$$

达到最小值，类似于 C8P1 中对高阶多项式最小二乘逼近的解，我们有
$$\sum_{k=0}^na_k\int^b_ax^{j+k}dx=\sum_a^bx^jf(x)dx$$

例如，对于 $f(x)=\sin {\pi x}$ 在区间 $[0,1]$ 上的最小二乘逼近多项式，有
$$a_0\int_0^11dx+a_1\int^1_0xdx+a_2\int^1_0x^2dx=\int^1_0\sin {\pi x}dx$$

$$a_0\int_0^x1dx+a_1\int^1_0x^2dx+a_2\int^1_0x^3dx=\int^1_0x\sin {\pi x}dx$$

$$a_0\int_0^1x^2dx+a_1\int^1_0x^3dx+a_2\int^1_0x^4dx=\int^1_0x^2\sin {\pi x}dx$$

注意到
$$\int_a^bx^{j+k}dx=\frac{b^{j+k+1}-a^{j+k+1}}{j+k+1}$$

这一线性方程组的数值解难以求出，因为其是 $Hilbert$ 矩阵. 一种改进的方法是利用正交多项式。
> 对于所有的 $j \ge 0$, 如果 $\phi _j(x)$ 为一个 $j$ 阶多项式，那么 $\{\phi_0,\phi_1,...,\phi_n\}$ 在任意区间上**线性无关**。

**权函数**：若可积函数对区间 $I$ 中所有的 $x$ 都有 $w(x)\ge 0$ 且 $\exists u\in I, w(u)\neq0$ 那么 $w(x)$ 是区间 $I$ 上的一个权函数。  

假设 $\{\phi_0,\phi_1,...,\phi_n\}$ 是某区间 $[a,b]$ 上一组线性无关的函数集合，$w$ 是区间 $[a,b]$ 上的一个权函数，对某函数 $f(x)$ 的一个逼近定义为：
$$P(x)=\sum_{k=0}^na_k\phi_k(x)$$

来使得误差
$$E=\int_a^bw(x)[f(x)-\sum_{k=0}^na_k\phi_k(x)]^2dx$$

达到最小值. 对每一个 $a_j$ 求偏导有：
$$\int_a^bw(x)[f(x)-\sum_{k=0}^na_k\phi_k(x)]\phi_j(x)dx = 0$$

移项可以得到：
$$\int_a^bw(x)f(x)\phi_j(x)dx=\sum_{k=0}^{n}a_k\int_a^bw(x)\phi_k(x)\phi_j(x)dx$$

若选择好的 $\{\phi_0,\phi_1,...,\phi_n\}$ ，使得 $\phi_k(x)$ 与 $\phi_j(x)$ 对于权函数 $w(x)$ **标准正交**，即
$$\int_a^bw(x)\phi_k(x)\phi_j(x)dx=\left\{
\begin{aligned}
0,&  j\neq k\\
C>0,  &j=k
\end{aligned}
\right.
$$

求解 $a_j$ 变得容易：
$$a_j=\frac{\int^b_aw(x)f(x)\phi_j(x)dx}{\int^b_aw(x)[\phi_j(x)]^2dx}$$

为了得到标准正交的多项式集合，引入 $Gram-Schmidt$ 方法。
令：
$$\phi_0(x)\equiv1,\phi_1(x)=x-B_1,\phi_2(x)=(x-B_2)\phi_1(x)-C_2\phi_0(x),\dots\phi_n(x)=(x-B_n)\phi_{n-1}(x)-C_n\phi_{n-2}(x),$$

其中,
$$B_n=\frac{\int^b_axw(x)[\phi_{n-1}(x)]^2dx}{\int^b_aw(x)[\phi_{n-1}(x)]^2dx},C_n=\frac{\int^b_axw(x)\phi_{n-1}(x)\phi_{n-2}(x)dx}{\int^b_aw(x)[\phi_{n-2}(x)]^2dx}.$$


### Sec 3 $Chebyshev$ 多项式和幂级数的缩减
$Chebyshev$ 多项式由以下定义给出：
$$T_n(x)=\cos {(n\arccos{x})}.$$

注意到 $T_0(x)=1, T_1(x)=x.$  

做变量替换 $\theta=\arccos x$ 可得
$$T_{n+1}(\theta)=\cos{(n\theta)}\cos\theta-\sin{(n\theta)}\sin\theta,$$

$$T_{n-1}(\theta)=\cos{(n\theta)}\cos\theta+\sin{(n\theta)}\sin\theta.$$

进而：
$$T_{n+1}(x)=2xT_n(x)-T_{n-1}(x)$$

因此，$T_n(x)$ 为一个首项系数为 $2^{n-1}$ 的 $n$ 阶多项式。  

$Chebyshev$ 多项式在区间 $(-1,1)$ 上关于权函数 $w(x) = \frac1{\sqrt{1-x^2}}$ 正交，且：
$$\int^1_{-1}\frac{[T_n(x)]^2}{\sqrt{1-x^2}}dx=\frac\pi2$$

$n$ 阶 $Chebyshev$ 多项式 $T_n(x)$ 在区间 $[-1,1]$ 上有 $n$ 个零点
$$x_k = cos(\frac{2k-1}{2n}\pi).$$

且，$T_n(x)$ 的绝对极点值在
$$x_k^{'}=cos(\frac{k\pi}n), T_n(x_k^{'})=(-1)^k.$$

首1 $Chebyshev$ 多项式 $\widetilde{T}_n(x)$ 由 $Chebyshev$ 多项式除以 $2^{n-1}$ 得到. 其最高项系数为1.  

假设 $\widetilde{\prod}_n$ 为所有 $n$ 阶首1多项式的集合，$P_n(x)\in\widetilde{\prod}_n$ 那么有：
$$\frac1{2^{n-1}}=\max_{x\in[-1,1]}{|\widetilde{T}_n(x)|}\le\max_{x\in[-1,1]}{|P_n(x)|}$$

等号当且仅当 $P_n(x)\equiv\widetilde{T}_n$ 时成立.  

假设 $P(x)$ 为区间 $[-1,1]$ 上的一个 $Lagrange$ 插值,注意到
$$f(x)-P(x)=\frac{f^{(n+1)}(\xi(x))}{(n+1)!}(x-x_0)(x-x_1)\dots(x-x_n).$$

$Chebyshev$ 多项式可以使近似误差达到最小。  

我们可以通过插值点的选择来优化 $\prod_{i=0}^{n}(x-x_i)$ 使误差达到最小，又由于首1 $Chebyshev$ 多项式的性质，当
$$\prod_{i=0}^{n}(x-x_i)=\widetilde{T}_{n+1}(x).$$

时，该项达到最小。  

因此，当 $x_k$ 选择为 $\widetilde{T}_{n+1}$ 的第 $k+1$ 个零点时，$\prod_{i=0}^{n}(x-x_i)$ 构成首1 $Chebyshev$ 多项式，逼近误差达到最小. 即：
$$\max_{x\in[-1,1]}|f(x)-P(x)|\le\frac1{2^n(n+1)!}\max_{x\in[-1,1]}|f^{(n+1)}(x)|$$

由于变量替换 $\widetilde{x}=\frac12[(b-a)x+a+b]$ , $Chebyshev$ 多项式可以扩展到任意的区间 $[a,b]$ 上。  

另外，$Chebyshev$ 多项式可以用来在精度损失最小的情况下降低逼近多项式的阶。  

考虑在区间 $[-1,1]$ 上的某 $n$ 阶多项式 $P_n(x)$ 作为逼近，目标是在 $\prod_{n-1}$ 中选取 $P_{n-1}(x)$ 使得：
$$\max_{x\in[-1,1]}|P_n(x)-P_{n-1}(x)|.$$

尽量小.  

由上一部分给出的定理可以知道：
$$\max_{x\in[-1,1]}|\frac{1}{a_n}(P_n(x)-P_{n-1}(x))|\ge \frac1{2^{n-1}}.$$

当且仅当：
$$\frac{1}{a_n}(P_n(x)-P_{n-1}(x))=\widetilde{T}_n(x).$$

那么：
$$P_{n-1}(x)=P_{n}(x)-a_n\widetilde{T}_n(x).$$

最小值为 $\frac{|a_n|}{2^{n-1}}.$
****


## Chap 9 : 逼近特征值
### Sec 2 幂法
**幂法**是用来确定矩阵的主特征值的一种迭代方法。他可以生成绝对值最大的特征值及其特征向量.

假设 $n\times n$ 矩阵 $\mathbf{A}$ 有 $n$ 个特征值 $\lambda_1,\lambda_2,\dots,\lambda_n$ 与相应的线性无关的特征向量集合 $\{\mathbf{v}^{(1)},\mathbf{v}^{(2)},\dots,\mathbf{v}^{(n)}\}$，假设 $\mathbf{A}$ 的最大绝对值的特征值为 $\lambda_1$. 

假设 $\mathbf{x}$ 为某向量， $\{\mathbf{v}^{(1)},\mathbf{v}^{(2)},\dots,\mathbf{v}^{(n)}\}$ 线性无关，即有常数 $\beta_1,\beta_2,\dots,\beta_n$ 满足 $$\mathbf{x}=\sum_{j=1}^n\beta_j\mathbf{v}^{(j)}.$$

两边同乘 $\mathbf{A},\mathbf{A}^2,\dots,\mathbf{A}^k$ 可得：
$$\mathbf{Ax}=\sum_{j=1}^n\beta_j\mathbf{Av}^{(j)}=\sum_{j=1}^n\beta_j\lambda_j\mathbf{v}^{(j)},$$

$$\mathbf{A^2x}=\sum_{j=1}^n\beta_j\mathbf{A^2v}^{(j)}=\sum_{j=1}^n\beta_j\lambda_j^2\mathbf{v}^{(j)},$$

$$\dots$$

$$\mathbf{A^kx}=\sum_{j=1}^n\beta_j\lambda_j^k\mathbf{v}^{(j)}.$$

$$\mathbf{A^kx}=\lambda_1^k\sum_{j=1}^n\beta_j(\frac{\lambda_j}{\lambda_1})^k\mathbf{v}^{(j)}.$$

当 $k\to\infty, \lim_{k\to\infty}\mathbf{A^kx}=\lim_{k\to\infty}\lambda_1^k\beta_1\mathbf{v}^{(1)}$
... 然后就没写了
