---
title: 二项式反演
date: 2018-02-22 20:11:35
tags:
  - 数学
  - 二项式反演
category:
  - Mathematics
---

# 反演概念

对于序列$\{f_n\},\{g_n\}$，我们可以找到一组关系，可以将$\{f_n\}$与$\{g_n\}$变换，这样的操作称为反演。

即$\exists~\{a_{n,i}\},\{b_{n,i}\},s.t.$
$$
f_n=\sum^n_{i=0}{a_{n,i}g_i}
$$

$$
g_n=\sum^n_{i=0}b_{n,i}f_i
$$

两者等价。

<!--MORE-->

这里的$\{a_{n,i}\},\{b_{n,i}\}$我们是可以求出来的。

首先将$f_n$带入$g_n$就有：
$$
g_n=\sum^n_{i=0}b_{n,i}\sum^i_{j=0}a_{i,j}g_j
$$
非常熟练地交换求和号后：


$$
g_n=\sum^n_{j=0}g_j\sum^n_{i=j}b_{n,i}a_{i,j}
$$
对于这个形式，我们非常想猜想：
$$
\sum^n_{i=j}b_{n,i}a_{i,j}=[n==j]
$$
实际上，这也是$\{a_{n,i}\},\{b_{n,i}\}$能构成反演的充要条件。

证明暂略（<del>又在挖坑.jpg</del>）。

# 常用的反演举例

* **Möbius 反演**
  $$
  \begin{eqnarray}
  a_{n,d} &=& [d|n]\\
  b_{n,d} &=& [d|n]\mu(\frac{n}{d})\\
  f(n) &=&\sum_{d|n}g(d)\\
  g(n) &=& \sum_{d|n}\mu(\frac{n}{d})f(d)
  \end{eqnarray}
  $$

* **二项式反演**

  $$
  \begin{eqnarray}
  a_{n,i}&=&(-1)^i\binom{n}{i}\\
  b_{n,i}&=&(-1)^i\binom{n}{i}\\
  f(n)&=&\sum_{i=0}(-1)^i\binom{n}{i}g(i)\\
  g(n)&=&\sum_{i=0}(-1)^i\binom{n}{i}f(i)
  \end{eqnarray}
  $$









是不是很优美？

但我们平时用的多为另一种形式。

$$
\begin{eqnarray}
a_{n,i}&=&\binom{n}{i}\\
b_{n,i}&=&(-1)^{n-i}\binom{n}{i}\\
f(n)&=&\sum_{i=0}\binom{n}{i}g(i)\\
g(n)&=&\sum_{i=0}(-1)^{n-i}\binom{n}{i}f(i)
\end{eqnarray}
$$

# 二项式应用举例

* **全错排问题**

  我们设$n$个元素的全错排有$f(n)$种。

  直接求全错排方案数不好求，我们考虑任意排列的方案数，显然有$n!$种。

  枚举每种排列中的不动点数量，有
  $$
  n!=\sum^n_{i=0}\binom{n}{i}f(i)
  $$
  反演一下就可以得到
  $$
  f(n)=\sum^n_{i=0}(-1)^{n-i}\binom{n}{i}i!
  $$
  就可以啦。

* **恰用$k$种颜色染色问题**

  $n$个格子排成一排，恰用$k$种颜色给这$n$个格子染色，相邻两个格子不共色，求方案数。

  设答案为$f(n,k)$，先考虑放松条件，设不管“恰”的方案数为$g(n,k)$，易知$g(n,k)=k(k-1)^{n-1}$。

  枚举每种方案中“恰”用的颜色数，有
  $$
  g(n,k)=\sum^k_{i=0}\binom{k}{i}f(n,i)
  $$
  反演一下就可以得到
  $$
  f(n,k)=\sum^k_{i=0}(-1)^{k-i}\binom{k}{i}i(i-1)^{n-1}
  $$
  Over~

# 一个Excited例子

  假如你有一个数列$\{a_n\}$，它的通项是多项式，其次数最高项为$n^d$，现在给你$\{a_0,\ldots,a_d\}$，询问$a_m$的值。

  这题用二项式反演的话，是可以做到$O(d)$的！

  首先，我们可以发现，任意$d$阶多项式都可以表示成$\sum^d_{i=0}{\binom{n}{i}b_i}$的形式。

  这个结论想想是很显然的，$\binom{n}{i}$是$i$次多项式，如果按$i$从$d,d-1,\ldots,0$的方式来确定$b_i$，每个$b_i$都是能唯一确定的。

  有了这个结论，我们就可以考虑将$a_n$表示成如下形式：
$$
  a_n=\sum^n_{i=0}\binom{n}{i}b_i
$$
  其中高次项($b_k,k>d$)补零。

  运用二项式反演，我们可以得到：
$$
  b_n=\sum^n_{i=0}(-1)^{n-i}\binom{n}{i}a_i
$$
  我们需要知道$\{b_k\},k\in\{0,1,\ldots,d\}$，于是求上式只须求$\{a_k\},k\in\{0,1,\ldots,d\}$的值，而这是已知的（或很好求得的。

  当然，直接这样预处理算出$b_n$的复杂度是$O(d^2)$的，我们还可以优化。

  考虑直接将$b_n$带入$a_n$，有
$$
  a_n=\sum^d_{i=0}\binom{n}{i}b_i=\sum^d_{i=0}\binom{n}{i}\sum^i_{j=0}(-1)^{i-j}\binom{i}{j}a_j
$$
  再次熟练地交换求和号
$$
  a_n=\sum^d_{j=0}a_j\sum^d_{i=j}\binom{n}{i}\binom{i}{j}(-1)^{i-j}
$$
  用一个很简单的技巧
$$
  \binom{n}{i}\binom{i}{j}=\binom{n}{j}\binom{n-j}{i-j}\\
  a_n=\sum^d_{j=0}a_j\sum^d_{i=j}\binom{n}{j}\binom{n-j}{i-j}(-1)^{i-j}
$$
  证明很简单，可以打开用公式，也可以考虑其组合意义。

  将$\binom{n}{j}$提出来，变换一下下标
$$
  a_n=\sum^d_{j=0}a_j\binom{n}{j}\sum^{d-j}_{i=0}\binom{n-j}{i}(-1)^{i}
$$
  ​

  后面那一坨求个和就可以得到
$$
  a_n=\sum^d_{j=0}a_j\binom{n}{j}(-1)^{d-j}\binom{n-j-1}{d-j}
$$
  (用$\binom{i}{j}=\binom{i-1}{j}+\binom{i-1}{j-1}$带入即可)

  我们还可以把它变换一下，但实际上已经差不多了。

  我们要求的两个组合数数都可以递推求得。

  但也可以再转换一下，有
$$
  \begin{eqnarray}
  a_n&=&\sum^d_{j=0}a_j(-1)^{d-j}\frac{n!(n-j-1)!}{j!(n-j)!(d-j)!(n-1-d)!}\\
  &=&\sum^d_{j=0}a_j(-1)^{d-j}\frac{n(n-1)\cdots(n-d)}{j!(d-j)!(n-j)}
  \end{eqnarray}
$$
  预处理一下阶乘逆元和前后缀$\Pi (n-i)$就可达到$O(d)$（模数是质数的话用线性递推，不用快速幂）。

  ​

# 一个更Excited例子

给定$A,B,C,p$，求
$$
\sum^A_{i=1}i^BC^i~(mod~p)
$$
回忆一下我们等比数列求和的公式
$$
1+q+q^2+\cdots+q^n=\frac{1-q^{n+1}}{1-q}
$$
实际上我们可以看出它是一个$\alpha+\beta q^n$的形式，其中$\alpha,\beta$是系数。

对于我们要求的和式，我们觉得结果肯定和$P(n),C^n$有关，其中$P(n)$为关于$n$的多项式。

比如$Sum(n)=P(n)*C^n$，我们就可以设数列$x_n=\frac{Sum(n)}{C^n}$，用之前讲的方法来求。

但很不幸，当我用一个[神奇的网站(Wolframalpha)](https://www.wolframalpha.com/)找规律时，发现实际上$Sum(n)$的形式是这样的
$$
Sum(n)=P(n)*C^n+Q_B(C)
$$
其中$Q_B(x)$是一个各项系数与$B$有关的多项式，长这样
$$
\begin{eqnarray}
Q_1(x)&=&\frac{x}{(1-x)^2}\\
Q_2(x)&=&\frac{x(1+x)}{(1-x)^3}\\
Q_3(x)&=&\frac{x(1+4x+x^2)}{(1-x)^4}\\
Q_4(x)&=&\frac{x(1+11x+11x^2+x^3)}{(1-x)^5}\\
Q_3(x)&=&\frac{x(1+26x+66x^2+26x^3+x^4)}{(-1+x)^6}\\
\end{eqnarray}
$$


感觉很有规律对不对？

那一坨多项式的系数对称的很好看有木有？

但是该怎么算呢？

机智的我上[OEIS](https://oeis.org)搜了一波，发现它居然叫[$eulerian~numbers$](https://oeis.org/A173018)！

欧拉数，很强！而且它可以$O(B^2)$递推！

于是这题就完了！

很神奇对不对？

更神奇的是，对$\{1,2,\ldots,n\}$的一个排列$p$，记$f(p)=\sum^{n-1}_{i=1}[p_i<p_{i+1}]$，则$E(n,k)$就等于满足$f(p)=k$的排列数。是不是很Excited！（<del>有同学想证明吗？欢迎填坑~</del>）

---

完结散花~~

