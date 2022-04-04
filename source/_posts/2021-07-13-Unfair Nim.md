---
layout: post
title: "「Solution」Atcoder ABC Unfair Nim"
date: 2021-07-13 21:20:56
categories: 题解
tags: [位运算, 乱搞]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

题解？
<!-- more -->

### 题意
两个人玩 **石头** 。	
规则自已看。。。	
然后可以知道简化后就是给你两个数的和，与异或和，问拆分方案。	
即是:
已知		
$a + b$ 和 $a \bigoplus b$.求与 小于等于$x_1$的最大的 $a$.	

### 分析
因为已知 $a + b$ 和 $a \bigoplus b$.	
所以可知 $a \& b$	

$a \& b = \frac{a+b-a \bigoplus b}{2}$		

然后就完了。	

记 $x = a \& b, y = a \bigoplus b, tmp = x$

$tmp$ 是$a$最小值。		

那么把$x,y$都转二进制。	

考虑每一位。	

分类讨论一下：	

下面的$x,y$都表示$x,y$的二进制下同一位

$1: x = 1, y = 1$		

这种情况是不合法的，直接判$-1$.	

$2:x = 1, y = 0$		

两个数都为1.

$tmp += (1 << pos)$	

$3:x = 0,y = 0$	

不干啥。	

$4:x = 0,y = 1$

因为要使$a$最大，所以如果$tmp + (1 << pos) \leq x_1$		



就完了。	



### 代码

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 45; 

int n, x, y, all, m, pos, tmp, nx, ny, delta;
int a[MAXN], b[MAXN];
bool satis[MAXN], vis[MAXN];

signed main () {
	scanf ("%lld", &n);
	scanf ("%lld %lld", &x, &y);
	m = x + y;
	for (int i = 3, k; i <= n; i++) {
		scanf ("%lld", &k);
		all ^= k;
	}
	delta = (m - all);
	if (delta < 0 || delta % 2 == 1) {
		printf ("-1\n");
		return 0;
	}
	tmp = delta >> 1;
	delta >>= 1;
	for (int i = 40; i >= 0; i--) {
		int b = (delta >> i) & 1;
		int a = (all >> i) & 1;
		if (b == 1 && a == 1) {
			printf ("-1\n");
			return 0;
		}
		if (a == 1 && (tmp | (1ll << i)) <= x) {
			tmp |= (1ll << i);
		}
	}
	if (x < tmp || tmp == 0) {
		printf ("-1\n");
	} else {
		printf ("%lld\n", x - tmp);
	}
	return 0;
}
```

