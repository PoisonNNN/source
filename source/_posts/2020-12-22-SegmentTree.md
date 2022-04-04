---
layout: post
title: "「Note」SegmentTree"
date: 2020-12-22
categories: 线段树
tags: [数据结构, 线段树]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png
---
线段树模板<!-- more -->
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 1e6 * 4 + 5;

int n, m, a[MAXN];

struct SegmentTree {
	int l, r;
	int dat;
} s[MAXN];

void build(int p, int l, int r) {
	s[p].l = l, s[p].r = r;
	if (l == r) {
		s[p].dat = a[l];
		return;
	}
	int mid = (l + r) / 2;
	build(p * 2, l, mid); 
	build(p * 2 + 1, mid + 1, r);
	s[p].dat = max(s[p * 2].dat, s[p * 2 + 1].dat);
}

void update(int p, int x, int val) {
	if (s[p].l == s[p].r) {
		s[p].dat = val;
		return;
	}
	int mid = (s[p].l + s[p].r) / 2;
	if (x <= mid)
		update(p * 2, x, val);
	else 
		update(p * 2 + 1, x, val);
	s[p].dat = max(s[p * 2].dat, s[p * 2 + 1].dat);
}

int query(int p, int l, int r) {
	if (s[p].l >= l && s[p].r <= r) {
		return s[p].dat;
	}
	int mid = (s[p].l + s[p].r) / 2;
	int val = (-(1 << 30));
	if (l <= mid)
		val = max(val, query(p * 2, l, r));
	if (r > mid)
		val = max(val, query(p * 2 + 1, l, r));
	return val;
}

int main() {
	scanf ("%d", &n);
	for (int i = 1; i <= n; i++) {
		scanf ("%d", &a[i]);
	}
	build(1, 1, n); 
	scanf ("%d", &m);
	while (m--) {
		int opt;
		scanf ("%d", &opt);
		if (opt == 1) {
			int x, val;
			scanf ("%d %d", &x, &val);
			update(1, x, val);
		} else {
			int l, r;
			scanf ("%d %d", &l, &r);
			printf ("%d\n", query(1, l, r));
		}
	}
	return 0;
}
```