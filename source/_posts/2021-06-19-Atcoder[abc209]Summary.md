---
layout: post
title: "「Solution」ABC209"
date: 2021-06-19 15:50
categories: 题解
tags: [总结, 考试]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---


> 赛后总结

<!-- more -->

其实题目都不算难，但是比赛的时候都只想了一半的正解。考虑也不周全，T3还把数组开小 `Re` 了一发。	
要多见见题目，思维要开阔一点。	

### A Counting

#### 题意
给出`A`, `B`, 求 $[A, B]$ 中整数元素个数。
#### 题解

```cpp
cout << ((b - a + 1 < 0) ? 0 : (b - a + 1));
```

### B Can you buy them all?

#### 题意
给出一堆数，下标为偶数的数的数值均减一，询问总和与`x`的大小关系。
#### 题解
按题意模拟即可。	

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;

int x, n, sum;

int main () {
	cin >> n >> x;
	for (int i = 1, y; i <= n; i++) {
		cin >> y;
		sum += y;
		if (i % 2 == 0) sum--;
	}
	if (sum <= x) {
		printf ("Yes\n");
	} else printf ("No\n");
	return 0;
}
```

### C Not Equal

#### 题意
给出`C`数列，构造`A`数列，满足`A`中元素互不相同，且`a[i] <= c[i]`。

求`A`数列的个数。		

#### 题解
针对于每个`a[i]`，若不考虑相同元素，有`a[i]`种选择，考虑相同元素，即只有`a[i] - k`种选择，`k`是比`c[i]`小的元素个数。	

```cpp
#include <map>
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int Mod = 1e9 + 7;
const int MAXN = 2e5 + 5;

int n, c[MAXN];
long long ans;

int main () {
	cin >> n;
	for (int i = 1; i <= n; i++) {
		cin >> c[i];
	}
	ans = 1;
	sort (c + 1, c + 1 + n);
	for (int i = 1; i <= n; i++) {
		ans = (ans % Mod * (c[i] - i + 1) % Mod) % Mod; 
	}
	cout << (ans % Mod) << endl;
	return 0;
}
```

### D Collision

#### 题意
两个人在树上走，询问距离是奇数是偶数。	
#### 题解
lca板题。		