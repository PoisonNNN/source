---
layout: post
title: "「Solution」CF507D The Maths Lecture"
date: 2021-01-10
tags: [题解, DP, 数位DP]
---

??

<!-- more -->

## Problem

[Link](https://www.luogu.com.cn/problem/CF507D)

求有多少个 $n$ 位十进制数 (没有前导 $0$ ) ，满足至少有一个非零后缀是 $k$ 的倍数，并对 $m$ 取模。

~~因为是对数的处理，又是一道 DP ，所以是一道数位 DP。~~

这道题的提问就属于数位 DP 的一般提问方式：在某个范围内求满足要求的数的个数。

---

## Solution

定义 $dp[i][j][p]$ 为填了 $i$ 位，该 $i$ 位十进制数模 $k$ 余数为 $j$ ，若存在是 $k$ 的倍数的后缀，$p=1$，否则 $p=0$ 。

因为涉及到后缀，状态就可以从后往前枚举填到哪一位了，以及填了 $i -1$ 位之后模 $k$ 的余数，决策就是枚举第 $i$ 位填哪个数。

那什么时候是可行的呢？

1. 在 $i-1$ 位时是可行的。

   很显然不管你第 $i$ 位填啥（首位 $0$ 除外）都是可行的，因为必然存在满足的后缀。

   所以直接转移：$dp[i][q][1]+=dp[i -1][j][1]$，其中 $q$ 为填上第 $i$ 为后该数模 $k$ 的余数。

2. 在 $i-1$ 位时不可行。

   1. 填上第 $i$ 为后该数模 $k$ 的余数 $q = 0$。

      这样就可以转移到 $p=1$ 上，但这里有个细节：填的数不能为 $0$，因为没有这种情况 ヾ(≧∇≦*)ゝ。（因为填的数为 $0$ 而 $q=0$ 只会是 $p=1$ 时才会有的情况）

   2. $q\neq0$ 。

      就只能继续转移到 $p=1$ 上。

   综上，转移为：
   $$ 
   \begin{cases} 
   dp[i][0][1]+=dp[i-1][j][0] & q = 0 \\ 
   dp[i][q][0]+=dp[i-1][j][0] & q\neq0 \\ 
   \end{cases}
   $$

在 DP 前我们可以预处理出 $10^{i-1}$ ($1<= i <= n$) 模  $k$ 的余数，方便于计算 $q$ 。

---

## Code

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

typedef long long LL;

const int Maxn = 1e3;
const int Maxk = 1e2;

int n, m, k, base;
int dp[Maxn + 5][Maxk + 5][2], mod[Maxn + 5];

int main () {
	scanf ("%d %d %d", &n, &k, &m);
	
	dp[0][0][0] = 1, base = 1;
	for (int i = 1; i <= n; i ++) {
		mod[i] = base % k;
		base = base * 10 % k;
	} //预处理 10^(i-1) % k 
	
	for (int i = 1; i <= n; i ++) {
		for (int j = 0; j < k; j ++) {
			for (int p = (i == n); p <= 9; p ++) { //首位不能填 0 
				int rem = (j + mod[i] * p) % k; //计算填数后的余数 
				dp[i][rem][!rem && p] = (dp[i][rem][!rem && p] + dp[i - 1][j][0]) % m;	
				dp[i][rem][1] = (dp[i][rem][1] + dp[i - 1][j][1]) % m;
				//转移 
			}
		}
	}
	
	int ans = 0;
	for (int i = 0; i < k; i ++) {
		ans = ((LL) ans + dp[n][i][1]) % m;
	}
	
	printf ("%d", ans);
	return 0;
}

```