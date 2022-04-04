---
layout: post
title: "「Solution」Devu and Flowers"
date: 2021-11-18
categories: 数学
tags: [数学, 容斥原理]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

> 居然调了半天的组合数？

<!-- more -->    

## Devu and Flowers

[link](https://vjudge.net/contest/469401#problem/C)

## 分析

看到$n$很小，第一反应是要状压。   

如果直接求解不太舒服，考虑求补集。   

在不考虑个数的限制下的总方案数是$\dbinom{n+m-1}{n-1}$。   

考虑选择一个种类的花不合法，设为$S_i$。   


$Ans = \dbinom{n + m - 1}{n - 1} - \sum_\limits{i = 1}^n S_i + \sum_\limits{i,j \in n} (S_i \cup S_j) - \dits$

$S_i = \dbinom{n+m-1-a_i-1}{n-1}$   

$S_i \cup S_j = \dbinom{n + m - 1 - a_i - 1 - a_j - 1}{n - 1}$

----

结果问题是看到数据范围后求不来组合数了，想了要到一个小时怎么对$1e12$范围里的数求组合数。。。   

其实用$\dbinom{n}{k} = \dbinom{n}{n - k}$就行了。

可以看到式子中即使暴力算只会循环不到$20$次。。。   

然后到处都要取模。

## 代码

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define LL long long
using namespace std;
const int MAXN = 25;
const int Mod = 1000000007;

LL n, m, a[MAXN], inv[MAXN], ans;

LL qpow(LL x, LL y) {
	LL res = 1;
	while (y) {
		if (y & 1) res = res * x % Mod;
		x = x * x % Mod;
		y >>= 1;
	}
	return res;
}

LL C(LL x, LL y) {

	if (y > x) return 0;

	y = x - y;
	LL res = 1ll;
	for (LL i = y + 1ll; i <= x; i++) {
		res = (res * (i % Mod)) % Mod;
	}
	for (int i = 1ll; i <= x - y; i++) {
		res = res * inv[i] % Mod;
	}

	return res;
}

int main() {

	inv[0] = 1;
	for (int i = 1; i <= 20; i++) {
		inv[i] = qpow(i, Mod - 2);
	}

	scanf("%lld %lld", &n, &m);
	for (int i = 1; i <= n; i++) {
		scanf("%lld", &a[i]);
	}

	for (int i = 0, sta = 1, cnt = 0; i < (1 << n); i++, sta = 1, cnt = 0) {

		LL tot = 0;
		for (int j = 1; j <= n; j++) if ((1 << j - 1) & i) {
			cnt++;
			tot += a[j] + 1;
		}

		if (cnt & 1) sta = -1;

		ans = (ans + sta * C(n + m - 1 - tot, n - 1) % Mod + Mod) % Mod;

		// printf("%d %lld %lld\n", cnt, ans, sta * C(n + m - 1 - tot, n - 1));

	}

	printf("%lld\n", ans);

	return 0;
}
```

