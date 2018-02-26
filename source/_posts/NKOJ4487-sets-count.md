---
title: NKOJ4487【集合计数】题解
date: 2018-02-25 19:14:07
tags:
  - Solution
  - 二项式反演
category:
  - Solution
---

题面在[这里](http://oi.nks.edu.cn/zh/Problem/Details/4487)

> 一个有$N$个元素的集合有$2^N$个不同子集（包含空集），现在要在这$2^N$个集合中取出若干集合（至少一个），使得它们的交集的元素个数为$K$，求取法的方案数，答案模$1000000007$。（是质数喔~）

<!--MORE-->

题目要求交集元素为$K$，我们可以考虑对我们选的$S_i$取补，有$|\cup \overline S_i|=N-K$。

我们只需要求并起来集合大小为$N-K$的选法有多少种。

我们设**最后并起来得到的集合**大小为$n$的方案数为$f(n)$，$Ans=\binom{N}{N-K}f(N-K)$。

显然选的集合都是都是全集的子集，考虑直接乱选的方案数，有$2^{2^n}$种（每个集合都选或不选）。

考虑最后并起来的结果为$k​$，有
$$
2^{2^n}=\sum^n_{k=0}\binom{n}{k}f(k)
$$
二项式反演一下就有
$$
f(n)=\sum^n_{k=0}(-1)^{n-k}\binom{n}{k}2^{2^n}
$$
预处理一下阶乘及其逆元，$2^{2^n}$直接平方平方就可以了，复杂度$O(N)$。

有个小细节，至少要选一个集合，要把空集的情况去掉，即$Ans-=[N==K]$。



附代码：

```c++
#include<stdio.h>
#include<string.h>
using namespace std;
typedef long long ll;
const int mod=1e9+7;
const int N=1000009;
int add(int a,int b) {a+=b;a-=a>=mod?mod:0;return a;}
int sub(int a,int b) {a-=b;a+=a<0?mod:0;return a;}
int mul(int a,int b) {return 1LL*a*b%mod;}
int inv[N],n,K;
int pows[N],Ans;
int fact[N],finv[N];
int C(int a,int b) {
	return mul(fact[a],mul(finv[b],finv[a-b]));
}
int main() {
	int i;
	scanf("%d%d",&n,&K);
	inv[1]=1;
	for(i=2;i<=n;i++)
		inv[i]=mul(mod-mod/i,inv[mod%i]);
	fact[0]=finv[0]=1;
	pows[0]=2;
	for(i=1;i<=n;i++) {
		fact[i]=mul(fact[i-1],i);
		finv[i]=mul(finv[i-1],inv[i]);
		pows[i]=mul(pows[i-1],pows[i-1]);
	}
	K=n-K;
	for(i=0;i<=K;i++) {
			Ans=((K-i&1)?sub:add)(Ans,mul(C(K,i),pows[i]));
	}
	Ans=mul(Ans,C(n,K));
	printf("%d\n",Ans-(K==0));
}
```

