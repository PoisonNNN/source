---
layout: post
title: "「Note」权值线段树"
date: 2021-01-01
categories: 线段树
tags: [线段树, 动态开点]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png
---
权值线段树初步
<!-- more -->
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 1e5 + 5;
const int MAXM = 1e9;

int q, tot = 1;

struct SegmentTree {
	int lc, rc;
	int cnt, del;
} s[MAXN * 30];

void pushup(int p) {
	s[p].cnt = s[s[p].lc].cnt + s[s[p].rc].cnt;
	return;
}

void pushdown(int p) {
	if (s[p].del == 1) {
		s[p].del = 0;
		s[s[p].lc].del = 1;
		s[s[p].rc].del = 1;
		s[s[p].lc].cnt = 0;
		s[s[p].rc].cnt = 0;
	} 
}

void update(int p, int l, int r, int id) {
	if (l == r) {
		s[p].del = 0;
		s[p].cnt++;
		return;
	}
	pushdown(p);
	int mid = (l + r) >> 1;
	if (id <= mid) {
		if (s[p].lc == 0) s[p].lc = ++tot;
		update(s[p].lc, l, mid, id);
	} else {
		if (s[p].rc == 0) s[p].rc = ++tot;
		update(s[p].rc, mid + 1, r, id);
	}
	pushup(p);
}

void clean(int p, int l, int r, int ql, int qr) {
	if (s[p].cnt == 0 || s[p].del == 1 || p == 0) return;
	if (l >= ql && qr >= r) {
		s[p].cnt = 0;
		s[p].del = 1;
		return;
	}
	pushdown(p);
	int mid = (l + r) >> 1;
	if (ql <= mid) {
		clean(s[p].lc, l, mid, ql, qr);
	}
	if (qr > mid) {
		clean(s[p].rc, mid + 1, r, ql, qr);
	}
	pushup(p);
}

int sum(int p, int l, int r, int ql, int qr) {
	if (s[p].cnt == 0 || s[p].del == 1 || p == 0) return 0;
	if (l >= ql && qr >= r) {
		return s[p].cnt;
	}
	pushdown(p);
	int val = 0;
	int mid = (l + r) >> 1;
	if (ql <= mid) {
		val += sum(s[p].lc, l, mid, ql, qr);
	}
	if (qr > mid) {
		val += sum(s[p].rc, mid + 1, r, ql, qr); 
	}
	return val;
}

int querykth(int p, int l, int r, int ql, int qr, int k) {
	if (s[p].cnt == 0 || s[p].del == 1 || p == 0) return -1;
	if (l == r) {
		if (s[p].cnt >= k) return l;
		else return -1;
	}
	pushdown(p);
	int mid = (l + r) >> 1, cnt = 0;
	if (qr > mid) {
		cnt = sum(s[p].rc, mid + 1, r, ql, qr);
		if (cnt >= k) return querykth(s[p].rc, mid + 1, r, ql, qr, k);
	} 
	if (ql <= mid) {
		return querykth(s[p].lc, l, mid, ql, qr, k - cnt);
	}
	return -1;
}

int main() {
	scanf ("%d", &q);
	while (q--) {
		int opt, x, l, r, k;
		scanf ("%d", &opt);
		if (opt == 1) {
			scanf ("%d", &x);
			update(1, 1, 1e9, x);
		}
		if (opt == 2) {
			scanf ("%d %d", &l, &r);
			clean(1, 1, 1e9, l, r);
		}
		if (opt == 3) {
			scanf ("%d %d %d", &l, &r, &k);
			printf ("%d\n", querykth(1, 1, 1e9, l, r, k));
		}
	}
	return 0;
}
```