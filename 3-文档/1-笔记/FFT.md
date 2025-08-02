---
tags:
  - 笔记
aliases:
---
# 前置知识

> [!note] 点值表示
> 一个 $n$ 次多项式可以用 $n+1$ 个点表示

点值表示下 $n$ 次多项式的乘积是 $O(n)$ 的

> [!note] 复数的指数形式
> $re^{i\theta}=a+bi$，其中 $r=\sqrt{a^2+b^2}$，$\tan\theta=\dfrac{b}{a}$

> [!note] 单位根
>  $x^n=1$ 在复数域上的根称为 $n$ 次单位根，$\omega_n^k=e^{i2k\pi/n}$
> $\omega_n^k=\omega_{2n}^{2k}$
> $\omega_{2n}^{k+n}=-\omega_{2n}^k$（$\omega_{2n}^n=-1$）

# DFT

> [!note] DFT（discrete Fourier transform）：离散傅里叶变换
> 将多项式 $A(x)=\sum_{i=0}^na_ix^i$ 转化为点值形式 $(\omega_n^k,A(\omega_n^k)),k=0,1,\ldots,n-1$

考虑 $n=2^l$ 的情况。
设 $B(x)=\displaystyle\sum_{i=0}^{n/2-1}a_{2i}x^i$，$C(x)=\displaystyle\sum_{i=0}^{n/2-1}a_{2i+1}x^i$，则 $A(x)=B(x^2)+xC(x^2)$
设 $0\le k< \dfrac{n}{2}$，则有
$$
\begin{align}
&A(\omega_n^k)=B(\omega_{n/2}^k)+\omega_n^kC(\omega_{n/2}^k)\\
&A(\omega_n^{k+n/2})=B(\omega_{n/2} ^k)-\omega_n^kC(\omega_{n/2}^k)
\end{align}
$$
任意一个多项式 $A$ 的点值形式都能由两个子多项式得到，于是得到一个分治做法。
$$
T(n)=2T(\frac{n}{2})+O(n)
$$
时间复杂度 $O(n\log n)$
用蝴蝶变换（bit-reversal permutation）直接得到递归树最后一层的系数后上推，可以非递归实现 DFT

# IDFT

> [!note] IDFT（inverse DFT）：逆离散傅里叶变换
> 将多项式的点值表示 $(\omega_n^k,b_k),k=0,1,\ldots,n-1$ 转化为其系数表示 $A(x)=\sum_{i=0}^{n-1}a_ix^i$

设 $n\times n$ 的矩阵 $\Omega$，其中 $\Omega_{i,j}=\omega_n^{ij}$，设向量 $\mathbf{a}=(a_0,a_1,\ldots,a_{n-1})$ 和 $\mathbf{b}=(b_0,b_1,\ldots,b_{n-1})$
IDFT 即解线性方程组
$$
\begin{pmatrix}
(\omega_n^0)^0 & (\omega_n^0)^1 & \ldots & (\omega_n^0)^{n-1} \\
(\omega_n^1)^0 & (\omega_n^1)^1 & \ldots & (\omega_n^1)^{n-1} \\
\vdots & \vdots & \ddots & \vdots \\
(\omega_n^{n-1})^0 & (\omega_n^{n-1})^1 & \ldots & (\omega_n^{n-1})^{n-1}
\end{pmatrix}
\begin{pmatrix}
a_0 \\
a_1 \\
\vdots \\
a_{n-1}
\end{pmatrix}
=
\begin{pmatrix}
b_0 \\
b_1 \\
\vdots \\
b_{n-1}
\end{pmatrix}
$$
则有 $\mathbf{a}=\Omega^{-1}\mathbf{b}$
取 $\Omega$ 的共轭矩阵 $\bar{\Omega}$，其中 $\bar{\Omega}_{i,j}=\omega_n^{-ij}$，则有
$$M_{i,j}=(\bar{\Omega}\cdot\Omega)_{i,j}=\displaystyle\sum_{k=0}^{n-1}\omega_n^{-i\cdot k}\cdot\omega_n^{k,j}= \sum_{k=0}^{n-1}\omega_n^{(j-i)\cdot k}$$
这是一个等比数列求和，设 $q=\omega_n^{j-i}$，注意到 $q^n=(\omega_n^n)^{j-i}=1$，套用等比数列求和公式
$$
M_{i,j}=
\begin{cases}
n & q=0\Leftrightarrow j=i \\
\dfrac{1-q^n}{1-q}=0 & q\ne 0\Leftrightarrow j\ne i
\end{cases}
$$
因此有 $M=nI$，则 $\Omega^{-1}=\dfrac{1}{n}\bar{\Omega}$，解得
$$
\mathbf{a}=\dfrac{1}{n}\bar{\Omega}\mathbf{b}
$$
相当于给定 $B(x)=\displaystyle\sum_{i=0}^{n-1}b_ix^{i-1}$，求点值 $B(\omega_n^{-k})$，这个过程相当于把 DFT 中的 $\omega_n^1$ 换成 $\omega_n^{-1}$
