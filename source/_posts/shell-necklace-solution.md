---
title: NKOJ 3933 【贝壳串】 题解
tags:
  - Solution
  - FFT
  - CDQ
  - 多项式求逆
category:
  - Solution
mathjax: true
date: 2018-01-13 14:46:20
---


题目[在此](http://oi.nks.edu.cn/zh/Problem/Details/3933)

给定每种长度的贝壳数量$A(i)$，求长度为$n$的链的种数。

设长度为$n$的链种数为$f(n)$，容易写出DP方程:

$f(n)=\sum_{i=1}^n{A(i)*f(n-i)}$

转移$O(n)$，直接计算复杂度$(n^2)$，显然不可取。

注意到方程的卷积形式。设

$\tilde{f}=\sum{f(i)x^i}$

$\tilde{A}=\sum{A(i)x^i}$

显然有$\tilde{f}*\tilde{A}+1=\tilde{f}(mod~x^{n+1})$

解得$\tilde{f}=\frac{1}{1-\tilde{A}}(mod~x^n)$

求逆即可。

这题的模数为313，于是巨坑。。

注意到多项式求时的迭代式。

$B_n(x)=B_m(x)*(2-B_m(x)*A_n(x))$

$B_n(x)$中的系数可能达到$50000*313^3$的级别，会爆精度。。。

于是只好把$B_m(x)*A_n(x)$先算出来，再算$B_n(x)$，两次$FFT$以保证精度。

假如结果中的系数再鬼畜一点的话，就只能用[拆系数$FFT$](http://blog.csdn.net/samjia2000/article/details/65661468)，这种黑科技了。

(然而没人写系列)

Code:
``` c++
#include<stdio.h>
#include<algorithm>
#include<complex>
using namespace std;
const int mod=313;
const int N=5e5+5;
typedef complex<double> C;
const double Pi=3.1415926535897932384626433832795;
C fft_wi[N],inv_wi[N];
int max_exp;
void init_wi(int n) {
	int i,j,k;
	C t0=exp(C(0,Pi/n)),
		t1=exp(C(0,-Pi/n));
	max_exp=n*2;
	fft_wi[0]=inv_wi[0]=1;
	for(i=1;i<n;i++) {
		fft_wi[i]=fft_wi[i-1]*t0;
		inv_wi[i]=inv_wi[i-1]*t1;
	}
}
void FFT(C A[],int n,int ty) {
	int i,j,k,m,t,l;
	C *wi=ty==1?fft_wi:inv_wi;
	C t0,t1;
	for(j=i=0;i<n;i++) {
		if(i<j) swap(A[i],A[j]);
		for(l=n>>1;(j^=l)<l;l>>=1);
	}
	for(m=1;m<n;m<<=1) {
		t=max_exp/(m<<1);
		for(k=0;k<n;k+=m<<1) {
			for(i=k,j=0;i<k+m;i++,j+=t) {
				t0=A[i];
				t1=A[i+m]*wi[j];
				A[i]=t0+t1;
				A[i+m]=t0-t1;
			}
		}
	}
	if(ty==1) return;
	t0=1.0/n;
	for(i=0;i<n;i++) A[i]*=t0;
}
C S[N],T[N];
void polynomial_inverse(int A[],int B[],int len) {
	if(len==1) {B[0]=1;return;}
	polynomial_inverse(A,B,len>>1);
	int i,j,k,M=len<<1;
	for(i=0;i<len;i++) S[i]=A[i];
	fill(S+len,S+M,0);
	for(i=0;i<(len>>1);i++) T[i]=B[i];
	fill(T+(len>>1),T+M,0);
	FFT(S,M,1);FFT(T,M,1);
	for(i=0;i<M;i++)
		S[i]=S[i]*T[i];
	FFT(S,M,-1);
	for(i=0;i<M-(len>>1);i++)
		S[i].real(-((long long)floor(S[i].real()+0.5))%mod),S[i].imag(0);
	S[0].real((long long)floor(2-S[i].real()+0.5)%mod);
	fill(S+M-(len>>1),S+M,0);
	FFT(S,M,1);
	for(i=0;i<M;i++)
		T[i]=T[i]*S[i];
	FFT(T,M,-1);
	for(i=len>>1;i<len;i++)
		B[i]=((long long)floor(T[i].real()+0.5))%mod;
	return;
}
int n,M=1,A[N],B[N];
int main() {
	int i,j,k;
	scanf("%d",&n);
	for(i=1;i<=n;i++) scanf("%d",&A[i]),A[i]%=mod,A[i]=A[i]?mod-A[i]:0;
	A[0]=1;while(M<=n) M<<=1;init_wi(M);
	polynomial_inverse(A,B,M);
	printf("%d\n",(B[n]+mod)%mod);
}
```