---
title: 生成函数
date: 2018-03-08 19:45:36
tags:
  - 数学
  - 生成函数
  - 多项式
category:
  - Mathematics
---

###  定义

> 对于数列$\{f_n\}$，我们定义$\tilde F(x)=\sum^\infty_{i=0}f_ix^i$，并称$\tilde F(x)$为数列$\{f_n\}$的生成函数。

定义就这么点。

从这个定义可以看出，生成函数实际上就是在描述一个序列（其实你也可以把它当做集合）。

但实际应用中，生成函数可以比更加灵活，我们可以利用生成函数来表示状态集合，利用生成函数的性质来计数，求解数列等。
<!--MORE-->

---

### 生成函数的运算

对于$\{a_n\}$我们记$\tilde A(x)$为它对应的生成函数。

即用小写字母表示序列，大写字母表示生成函数。

* 加减乘常数
  $$
  \tilde A(x)=\alpha\tilde B(x)\pm \beta\tilde C(x)\\
  \Leftrightarrow a_n=\alpha b_n\pm \beta c_n
  $$

* 偏移
  $$
  \tilde A(x)=x^l\tilde B(x)\\\Leftrightarrow a_n=b_{n-l}
  $$

* 生成函数乘法(卷积)
  $$
  \tilde A(x)=\tilde B(x) * \tilde C(x)\\
  \Leftrightarrow a_n=\sum_{i+j=n}b_i*c_j
  $$
  实际上，这个乘法还可以定义为其他的卷积，比如异或卷积，与卷积，或卷积以及其他，只要能快速计算，在OI中就比较很意义。

  假如我的卷积中有一个组合数系数怎么办呢？

  即
  $$
  c_n=\sum^n_{i=0}\binom{n}{i}a_ib_{n-i}
  $$
  我们就要用到指数型生成函数
  $$
  \tilde C(x)=\sum^\infty_{i=0}\frac{c_i}{i!}x^i
  $$
  你会惊奇地发现，这样卷起来之后刚好满足条件。

* 求导和积分
  $$
  \tilde A(x)=\tilde B'(x)\Leftrightarrow a_n=(n+1)b_{n+1}\\
  \tilde A(x)=\int \tilde B(x)dx\Leftrightarrow a_n=\frac{b_{n-1}}{n}
  $$
  这种操作多用于将你的序列乘或除下标的时候。

* 指数和取对
  $$
  exp(\tilde F(x))=\sum^\infty_{i=0}\frac{\tilde F^{(i)}(x)}{i!}\\
  ln(\tilde F(x))=\sum^\infty_{i=1}\frac{\tilde F^{(i)}(x)}{i}
  $$
  该操作的对序列的意义不大明确，但可以用来求解包含生成函数的方程。

  具体实现时采用 倍增+FFT，暂不进行讨论。

* 开方与求逆

  实际上这也没什么实际意义，通常都是解生成函数的中间操作。

  对形如$\tilde G(x)=(1+\tilde F(x))^\alpha,\alpha\in \mathbb{R}$，我们可以利用广义二项式定理展开成
  $$
  \tilde G(x)=\sum^\infty_{i=0}\frac{\alpha^{\underline{i}}}{i!}\tilde F^{(i)}(x)
  $$
  其中$\alpha^{\underline i}=\alpha(\alpha-1)\cdots(\alpha-i+1)$，

  特别地$\frac{(-n)^{\underline{i}}}{i!}=\binom{n+i-1}{i}(-1)^{i}$

* 取模

  生成函数的推导中实际上很少采用取模操作。

  但由于计算机能处理的范围并不是我们求和的$0\rightarrow\infty$，我们在具体应用中经常使用$(mod~x^n)$这样的操作来便于实现，在这里就不具体讨论了。


总之，生成函数为我们提供了非常**舒服**的运算方式，<del>大部分时候</del>都可以让我们只用关心转移或递推，得到关系式之后扔给计算机求解。

---

### 常见的生成函数

$$
\begin{aligned}
\frac{1}{1-x}&=\sum^\infty_{i=0}x^i\\
\frac{1}{(1-x)^r}&=\sum\binom{r+i-1}{i}x^i,r\in\mathbb{N^*}\\
\frac{1}{(1-x)^2}&=\sum^\infty_{i=0}(i+1)x^i=(\frac{1}{1-x})'\\
ln(\frac{1}{1-x})&=\sum^\infty_{i=1}\frac{1}{i}x^i=\int\frac{1}{1-x}dx\\
exp(x)&=\sum^\infty_{i=0}\frac{x^i}{i!}\\
ln(1+x)&=\sum^\infty_{i=1}\frac{(-1)^{k+1}}{k}x^k
\end{aligned}
$$

但这些有什么用呢？举几个例子。

1. 假如我要求调和级数（$H_n=\sum_{i=1}^n\frac{1}{i}$），我就可以拿出$\{\frac{1}{n}\}$的生成函数$ln(\frac{1}{1-x})$，给它乘上一个$\frac{1}{1-x}$即可。即

$$
[x^n](\frac{1}{1-x}ln(\frac{1}{1-x}))=H_i
$$

2. 假如我要求将某个正整数拆分成若干个正整数之和的方案数，那么我就可以设生成函数$f_n(x)=\sum^\infty_{k=0}x^{kn}=\frac{1}{1-x^n}​$，来表示正整数$n​$选了$k​$个，那答案的生成函数就可以表示为

$$
\begin{aligned}
&F(x)=\prod^\infty_{n=1}f_n(x)=\prod^\infty_{n=1}\frac{1}{1-x^n}\\&
其中[x^n]F(x)为n的划分数。
\end{aligned}
$$

---

### 求解线性递推

设$a_n=b_1a_{n-1}+b_2a_{n-2}+\cdots+b_ka_{n-k}$为$k$阶线性递推数列

那么根据前面的偏移操作和乘常数的操作，有
$$
\begin{equation}
a_k=b_1a_{k-1}+b_2a_{k-2}\cdots+b_ka_0\\
a_{k+1}=b_1xa_k+b_2a_{k-1}\cdots+b_ka_1\\
\end{equation}

\begin{aligned}
\tilde A(x)=&(\tilde A(x)-\sum^{k-2}_{i=0}a_ix^i)b_1x^1\\
+&(\tilde A(x)-\sum^{k-3}_{i=0}a_ix^i)b_2x^2\\
+&\cdots\\
+&\tilde A(x)b_kx^k+\sum^{k-1}_{i=0}a_ix^i
\end{aligned}
$$
将$\tilde A(x)$移到一边，就有

$$
\tilde A(x)(1-\sum^{k}_{i=1}b_ix^i)=\sum^{k-1}_{i=0}a_i(1-\sum_{j=1}^{k-i-1}b_jx^j)
$$
得
$$
\tilde A(x)=\frac{\sum^{k-1}_{i=0}a_i(1-\sum_{j=1}^{k-i-1}b_jx^j)}{1-\sum^{k}_{i=1}b_ix^i}
$$
可使用多项式求逆解决。

> 题外话
>
> 为什么会有特征根呢？
>
> 我们可以发现答案的分母就相当于将特征根方程$x^k=\sum^k_{i=1}b_ix^{k-i}$的系数颠倒，由代数基本定理可知$1-\sum^{k}_{i=1}b_ix^i=\prod_{i=1}^k(1-\lambda_ix)$，其中$\lambda_i$是特征根。可以将答案写为$\tilde A(x)=\sum^k_{i=1}\frac{C_i}{1-\lambda_ix}$的形式，其中$C_i$为常数，由之前的展开就可以知道$[x^n]\tilde A(x)=\sum^k_{i=1}{C_i\lambda_i^n}$。
>
> 此处应有严谨证明，但时间有限，只有先挖坑了。

---

### 求解卷积递推

##### 例  The Child And Binary Tree

>有一个含有 $n$ 个元素的正整数集合 $S={c_1,c_2,⋯,c_n}$，我们称一个节点带权的有根二叉树是**好的**，当且仅当对于每个节点 $v$，$v$ 的权值在 $S$ 内，并且我们称这棵树的权值为所有节点的权值和
>
>现在给出一个正整数 $m$，你要计算出对于所有正整数 $1≤s≤m$，有多少不同的**好的**二叉树满足它的权值是 $s$
>
>答案对 $998244353(7×17×223+1,一个质数)$ 取模，其中 $1≤n,m,c_i≤10^5$
>
>题目链接：[Codeforces-438E](http://codeforces.com/contest/438/problem/E)、[BZOJ-3625](http://www.lydsy.com/JudgeOnline/problem.php?id=3625)、[NKOJ-3218](http://oi.nks.edu.cn/zh/Problem/Details/3218)

这道题就可以体现生成函数的灵活性。

考虑函数$\tilde F(x)$，它表示**所有的二叉树**。$[x^n]\tilde F(x)$表示权值和为$n$的不同的二叉树个数。为了方便，我们定义$[x^0]\tilde F(x)=1$，即存在空树。

我们想一想我们的二叉树是如何生成的。

它有一个根节点，根节点有左右子树，那么递推（递归？）关系就出来了。
$$
\tilde F(x)=\tilde F(x)(\sum x^{s_i})\tilde F(x)+1
$$
左子树的方案卷上根的方案再卷上右子树的方案，再加上空树的方案。

我们生成函数的性质很好，它几乎支持所有运算，所以就可以解得
$$
\tilde S(x)=\sum x^{s_i}\\
\tilde F(x)=\frac{1-\sqrt{1-4\tilde S(x)}}{2\tilde S(x)}
$$
为什么不取+号呢？

由于我们具体操作的时候，多项式取逆必须要满足常数项不为0，但显然我们的$\tilde S(x)$没有常数项，所以必须要求上面的式子没有常数项，以约掉公共的$x^i$，以便求逆。假如对多项式开方熟悉一点就会知道上面**开方**出来的常数项为1，我们要减掉。就是这样。

---

##### 例 括号序列计数(n=1)

> 题面在此：[vijos](https://vijos.org/p/1871),[nkoj](http://oi.nks.edu.cn/zh/Problem/Details/2884),[bzoj](http://www.lydsy.com/JudgeOnline/problem.php?id=3605)
>
> 我们只考虑(n=1)的情况，即告诉你左括号，右括号，空格的种类为$a,b,c$，问你长度为$len$的括号序列的个数。

这个问题显然可以用Catalan数和卷积解决，但我们可以试着用生成函数思考。

> 以下可能会出现生成函数符号与集合混用的情况，请大家感性理解。

我们要求长度为$len$的括号序列的个数，显然我们要求一个生成函数来表示所有的合法括号序列，设它为$\tilde F(x)$,$[x^n]\tilde F(x)$为长度为$n$的合法括号序列种数。但是我们直接这样的话，似乎不是很好转移。

不好转移，那我们就多设几个生成函数呗。

设$A(x)$为所有外面**套了一个括号**的括号序列的生成函数，例如(())$\in A$，但()()$\notin A$。

设$B(x)$为**仅由空格**组成的合法序列，例如{|,#,##,###,...}，|表示空状态。

那么有
$$
B(x)=\frac{1}{1-cx}\\
A(x)=axF(x)bx=abx^2F(x)\\
F(x)=F(x)A(x)B(x)+B(x)\\
F(x)=F^2(x)abx^2B(x)+B(x)\\
F(x)=\frac{1-\sqrt{1-4abx^2B^2(x)}}{2abx^2B(x)}
$$
多项式开方，求逆一股脑弄上去就好了。

> 当n=2时，上面的$F,A,B$就可以变成2x2的矩阵，但是似乎这样并解不出来，尬。

> 实际上这两个递推的解形式都是Catalan-like
>
> Catalan数的生成函数为$C(x)=\frac{1-\sqrt{1-4x}}{2x}$

---

### 关于概率的计算

用生成函数来求解概率问题在有些时候往往有奇效。

设某个问题的状态可以又一些小状态$\{s_i\}$的序列表示，其中出现$s_i$的概率为$p_i$。

记所有当前所有状态的集合（生成函数）为$S$，那么

$S'=S(\sum p_is_i)$

$S'$中每一项的系数都表示从$S$集合中再走一步得到某个状态的概率$p$。

我们将$s_i$变成一些与$x$有关的东西，就可以统计一些信息。

#####  例 一道<del>有趣的</del>硬币游戏

>题目链接：[NKOJ](http://oi.nks.edu.cn/zh/Problem/Details/4087)
>
>给你一些长度为m的HT序列，不断抛硬币，出现上述序列中的一个就停止，求以每种序列停止的概率。

这道题的推导可以是将生成函数和集合结合在一起。

我们设$N$是没有人赢的状态的集合，把他化为生成函数的感觉就是把它的每个状态前面乘上一个概率系数再加起来，但出现HT的概率是已知的，我们可以等会再加进去。

设$A_i$为第$i$个串$S_i$的赢的状态集合。考虑走了一步，就有
$$
|+N(H+T)=N+\sum A_i
$$
其中"|"表示空状态。

但是只有这个关系式还不够，我们要想想他们之间更多的关系。

举个例子，$S_1=$HTHT，$S_2$=HTTT。

我们想一想，直接给$N$接上$S_2$，得到的可能是$A_2$，但也可能先是$A_1$，再加上一段。

e.g. origin=HT，modified=HT**HTTT**

假如我们考虑一下这些前后缀重合的状态，就可以发现如下等式
$$
\begin{aligned}
N\cdot HTTT&=A_1\cdot TT+A_2\\
N\cdot HTHT&=A_1
\end{aligned}
$$
现在我们把$H$和$T$都换成$\frac{x}{2}$，再令$x=1$，根据我们的定义，$A_i(1)$就可以表示出现$A_i$中状态的概率。

我们在$x=1$的情况下解方程，就可以得到每种序列赢的概率。

---

### 一些鬼畜的计数问题

##### 例  n个节点的无标号有根有向树个数(source: TAOCP)

> RT.
>
> 有向有根树的定义为
> $$
> \forall v \in V,\exists one~and~only~one~path~from~v~to~root
> $$
>

设每答案为$a_n$。

当$n=1$时，显然$a_1=1$。

当$n>1$时，显然$root$有一些奇奇怪怪的子树。

我们假设大小为$j$的子树有$c_j$个，这里有$\binom{c_j+a_j-1}{c_j}$种方案（隔板法，将$c_j$划分成$a_j$块，可以为空）。
$$
a_n=\sum_{c_1+2c_2+\cdots=n-1}\binom{c_1+a_1-1}{c_1}\binom{c_2+a_2-1}{c_2}\cdots\binom{c_{n-1}+a_{n-1}+1}{c_{n-1}}
$$
根据我们前面的公式
$$
\sum^\infty_{i=0}{\binom{i+k-1}{i}}x^{ir}=\frac{1}{(1-x^r)^k}
$$
有
$$
A(x)=\frac{x}{(1-x)^{a_1}(1-x^2)^{a_2}\cdots}
$$
如何解？

取对之后，有
$$
\begin{aligned}
ln~A(x)&=-\sum_{i=1}^\infty{a_i}{ln(1-x^i)}+ln(x)\\
&=-\sum_{i=1}^{\infty}a_i\sum_{j=1}^\infty\frac{(-1)^{j+1}(-x^i)^j}{j}+ln(x)\\
&=\sum_{i=1}^\infty a_i\sum^\infty_{j=1}\frac{x^{ij}}{j}+ln(x)\\
&=\sum_{i=1}^\infty \frac{A(x^i)}{i}+ln(x)
\end{aligned}
$$
即
$$
A(x)=x\cdot exp(\sum^\infty_{i=1}\frac{A(x^i)}{i})
$$
两边同时求导，有
$$
\begin{aligned}
A'(x)=\frac{A(x)}{x}+A(x)\cdot\sum^\infty_{i=1}A'(x^i)x^{i-1}\\
x\cdot A'(x)=A(x)(1+\sum_{i=1}^\infty A'(x^i)x^i)
\end{aligned}
$$
比较一波对应系数，我们可以发现
$$
(n-1)a_nx^n=\sum_{i+j*r=n}a_ix^ia_jjx^{jr},i,j,r\ge1
$$
于是乎
$$
(n-1)a_n=\sum_{i=1}^{n-1}ia_is_{n,i}\\
其中s_{n,i}=\sum_{n-i*r>0}{a_{n-i*r}}
$$
我们就得到了一个$O(n^2log~n)$求$a_n$的较为优秀算法。

---

### 当我们把卷积换成异或卷积之后

很多时候，我们用生成函数求解问题，都不是在关注它是函数，而是寻找它表示的状态之间的关系。

生成函数的运算只是转移的另一种表达。

反正我们知道它能算！

##### 例 SDOI 2017 切树游戏

>给出一棵无根树 $T$，节点从 $1$ 到 $n$ 编号，点 $i$ 的权值为 $v_i$。定义一棵树的权值为树中所有点的权值异或起来的结果。
>
>你想要在树上玩一个游戏。你会做 $m$ 次以下两种操作：
>
>* `C x y`：将 $v_x$ 修改为 $y$。
>* `Q k`：询问 $T$ 有多少棵非空的连通子树，满足这棵子树的权值为 $k$。输出模 10007。

如何求权值为$k$的子树个数？

考虑把$T$变成有根树，对于一个联通子树$T'$，我们令$T'$深度最小(在$T$中)的为根，那么我们只需要考虑，某个点，它子树中，包括它，且权值为$k$的子树有多少。

假设我们现在$u$，记它的儿子为$s_i$，点$x$的答案为$F_x(z)$。

则$F_u(z)=z^{v_u}\prod (z^0+F_{s_i}(z))$，在这里，我们就在考虑以每个儿子为根的联通子树是怎么选的。

要解决这道题，我们可以考虑轻重链剖分维护$\sum_{u\in T}F_u(z)$。

由于我们的乘法是异或卷积，实际实现中，我们可以维护$F(z)$FWT后点值(这样叫可能有些不妥)，我们生成函数的加减乘除可以换成对应点值的加减乘除，每次修改，就可以在每条重链的线段树上进行区间乘法/除法，注意模数较小，除数可能为0，要记下能被$mod$除几次。

但对于类似的问题，%%%[ImmortalCO](http://immortalco.blog.uoj.ac/blog/2625)有一种动态DP的黑科技。

对每个点 $u$ 定义一个向量 $(F_u(z),H_u(z),z^0)$ 。

$H_u(z)$是子树中的答案和。

对于每条重链上的，维护一下它对它对其重链上的父节点的贡献，这应该是一个和它的虚儿子有关的矩阵，我们只需要一个能支持单点修改，能高效维护矩阵积的数据结构，可以用线段树解决。

详情请见[ImmortalCO's Blog](http://immortalco.blog.uoj.ac/blog/2625)。

---

### Reference

* [ImmortalCO's Blog](http://immortalco.blog.uoj.ac/blog/2625)
* [Miskcoo's Blog](http://blog.miskcoo.com/2015/06/polynomial-with-newton-method)
* TAOCP
* Concrete Mathematics
* 《组合数学》

