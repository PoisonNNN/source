---
layout: post
title: "「Solution」BalticOI 2014 Day 1 三个朋友"
date: 2021-01-09
tags: [题解, 字符串, Hash]
---

为数不多的小常数又好写的数据结构丫

<!-- more -->

## Problem
**题目描述**
给定一个字符串 $S$ ，先将字符串 $S$ 复制一次(~~变成双倍快乐~~)，得到字符串 $T$ ，然后在 $T$ 中插入一个字符，得到字符串 $U$ 。

给出字符串 $U$ ，重新构造出字符串 $S$ 。

**输入格式**
第一行一个整数 $N$ ，表示字符串 $U$ 的长度。

第二行一个长度为 $N$ 的字符串，表示字符串 $U$ 。

**输出格式**
一行一个字符串，表示字符串 $S$ 。

特别地:
如果字符串无法按照上述方法构造出来，输出 **NOT POSSIBLE**；
如果字符串  不唯一，输出 **NOT UNIQUE**。

## Solution
### 前置知识
这道题用到的方法是进制 $hash$ ，顾名思义将字符串转化为 $k$ 进制数，具体的方法如下：

```cpp
const int MAXN = 1005;
char s[MAXN];
int hash[MAXN], k;

scanf("%s", s + 1);
for (int i = 1; i <= strlen(s + 1); i++) {
	hash[i] = hash[i - 1] * k + s[i]; //将字符串转化为 k 进制数
}
```

当我们要求一段子串（或者说是一段区间）的 $hash$ 值时又该怎么办呢？

设这段子串在 $[l,r]$ ，而 
$hash[l] = hash[l-1]*k+s[l]$

$hash[l+1] = hash[l]*k+s[l+1] = (hash[l-1]*k+s[l])*k+s[l+1]=hash[l-1]*k^2+s[l]*k+s[l+1]$

$…$

容易知道 $hash[l+1]$ 中包含了 $hash[l-1]*k^{l+1-l+1}$ 和 $[l,l+1]$ 的 $hash$ 值。同理 $hash[r]$ 中包含了 $hash[l-1]*k^{r-l+1}$ 和 $[l,r]$ 的 $hash$ 值，所以求$[l,r]$ 的 $hash$ 值可以用 $hash[r]$ 减去 $hash[l-1]*k^{r-l+1}$。

```cpp
int Get_Hash(int l, int r) {
	return hash[r] - hash[l - 1] * pre[r - l + 1]; //pre[i]=k^i
}
```

从这个问题可以扩展到另一个问题，求 $[l,r]$ 删除下标位 $x$ 的字符后的 $hash$ 值。
对于这个问题我们可以先 $[l,x-1],[x+1,r]$ 的 $hash$ 值，然后将 $hash(l,k-1)*k^{r-(x+1)+1}$ 与 $hash(x+1,r)$ 相加即可。这个的道理和求 $hash$，减去 $hash[l-1]*k^{r-l+1}$ 是一样的。

```cpp
int Get_Sum(int l, int r, int k) {
	return Get_Hash(l, k - 1) * pre[r - k] + Get_Hash(k + 1, r);
}
```

知道了这两个问题如何求解就可以轻松解决本题了。

### 思路
很容易知道 $N$ 为偶数时是一定不行的

首先按照字符的位置可以分为两种个情况：

1. 在 $[1,(N+1)/2]$
	若在这之间，则 $S$ 就是 $s[(N+1)/2+1——N]$
	我们可以 $[1,(N+1)/2]$ 做循环，枚举字符的位置，然后根据 $hash$ 值来判断是否相等
```cpp
string str1, ans1;

int mid = (N + 1) / 2;
for (int i = mid + 1; i <= n; i++) {
		str1.push_back(s[i]);
	}
for (int i = 1; i <= mid; i++) {
		if (Get_Sum(1, mid, i) == Get_Hash(mid + 1, n)) {
			ans++;
			ans1 = str1;
			break;
		} 
	}
```
	
2. 在 $[(N+1)/2,N]$
	若在这之间，则 $S$ 就是 $s[1——(N+1)/2-1]$
	同理。
```cpp
string ans2, str2;

int mid = (N + 1) / 2;
for (int i = 1; i < mid; i++) {
		str2.push_back(s[i]);
	}
for (int i = mid; i <= n; i++) {
		if (Get_Sum(mid, n, i) == Get_Hash(1, mid - 1)) {
			ans++;
			ans2 = str2;
			break;
		}
	}
```
在这之后，需要分出三种情况：无解？唯一解？多解？

1.当 $ans == 0$ 一定无解，直接输出**NOT POSSIBLE**
2.当 $ans==1$ 或 $ans1 == ans2$， 即两种情况下只有一种或者两种情况得到的是相同的解都是输出 $ans1/ans2$.
3.上述两种情况都不满足，就肯定是多解了，输出**NOT UNIQUE**

## Code
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;

const int MAXN = 2e6 + 5;
#define base 131
#define ull unsigned long long

int n, mid, ans;
string str1, str2, ans1, ans2;
char s[MAXN];
ull tmpl, tmpr;
ull pre[MAXN], hash[MAXN];

ull Get_Hash(int l, int r) {
	return hash[r] - hash[l - 1] * pre[r - l + 1];
}

ull Get_Sum(int l, int r, int k) {
	return Get_Hash(l, k - 1) * pre[r - k] + Get_Hash(k + 1, r);
}

int main() {
	scanf("%d %s", &n, s + 1);
	mid = (n + 1) >> 1;
	if (!(n & 1)) {
		return printf("NOT POSSIBLE"), 0;
	}
	pre[0] = 1;
	for (int i = 1; i <= n; i++) {
		pre[i] = pre[i - 1] * base;
		hash[i] = hash[i - 1] * base + (s[i] - 'A' + 1);
	}
	
	tmpr = Get_Hash(mid + 1, n);
	for (int i = mid + 1; i <= n; i++) {
		str1.push_back(s[i]);
	}
	for (int i = 1; i <= mid; i++) {
		tmpl = Get_Sum(1, mid, i);
		if (tmpl == tmpr) {
			ans++;
			ans1 = str1;
			break;
		} 
	}
	
	tmpl = Get_Hash(1, mid - 1);
	for (int i = 1; i < mid; i++) {
		str2.push_back(s[i]);
	}
	for (int i = mid; i <= n; i++) {
		tmpr = Get_Sum(mid, n, i);
		if (tmpl == tmpr) {
			ans++;
			ans2 = str2;
			break;
		}
	}
	
	if (!ans) printf("NOT POSSIBLE");
	else if (ans == 1 || ans1 == ans2) cout << (ans1.size() ? ans1 : ans2);
	else printf("NOT UNIQUE");
	return 0;
}
```
