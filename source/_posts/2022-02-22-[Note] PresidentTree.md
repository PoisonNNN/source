---
layout: post
title: "「Note」PresidentTree"
date: 2022-02-22
tags: [数据结构]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

小清新数据结构
<!-- more -->

## 查询 Kth

```cpp
struct PresidentTree {
	int tot, cnt, root[MAXN];
	struct Node { int l, r, lch, rch; LL dat; } s[MAXN];
	void init() {
		for (int i = 1; i <= tot; i++) s[i] = Node{0, 0, 0, 0, 0}, root[i] = 0;
		tot = cnt = 0;
	}
	int newnode(int val, int l, int r) { s[++tot] = Node{l, r, 0, 0, 0}; return tot; }
	void update(int& p, int x, LL val) {
		s[++tot] = s[p], p = tot, s[p].dat++;
		if (s[p].l == s[p].r) return;
		int mid = (s[p].l + s[p].r) >> 1;
		if (x <= mid) {
			if (!s[p].lch) s[p].lch = newnode(0, s[p].l, mid);
			update(s[p].lch, x, val);
		} else {
			if (!s[p].rch) s[p].rch = newnode(0, mid + 1, s[p].r);
			update(s[p].rch, x, val);
		}
	}
	int query(int rt1, int rt2, int k) {
		if (s[rt1].l == s[rt1].r) return s[rt1].l;
		int mid = (s[rt1].r + s[rt1].l) >> 1;
		int num = s[s[rt2].lch].dat - s[s[rt1].lch].dat;
		if (num >= k) {
			if (!s[rt1].lch) s[rt1].lch = newnode(0, s[rt1].l, mid);
			if (!s[rt2].lch) s[rt2].lch = newnode(0, s[rt2].l, mid);
			return query(s[rt1].lch, s[rt2].lch, k);
		} else {
			if (!s[rt1].rch) s[rt1].rch = newnode(0, mid + 1, s[rt1].r);
			if (!s[rt2].rch) s[rt2].rch = newnode(0, mid + 1, s[rt2].r);
			return query(s[rt1].rch, s[rt2].rch, k - num);
		}
	}
} pret;
```

## 区间修改
```cpp
struct PresidentTree {
	int tot, cnt, root[MAXN];
	struct Node { int l, r, lch, rch; LL dat, tag; } s[MAXN];
	void init() {
		for (int i = 1; i <= tot; i++) s[i] = Node{0, 0, 0, 0, 0, 0}, root[i] = 0;
		tot = cnt = 0;
	}
	void build(int& p, int l, int r) {
		p = ++tot; s[p].l = l, s[p].r = r, s[p].dat = a[s[p].l];
		if (l == r) return;
		int mid = (s[p].l + s[p].r) >> 1;
		build(s[p].lch, l, mid), build(s[p].rch, mid + 1, r);
		s[p].dat = s[s[p].lch].dat + s[s[p].rch].dat;
	}
	void update(int& p, int l, int r, LL val) {
		s[++tot] = s[p], p = tot;
		if (s[p].l >= l && s[p].r <= r) { s[p].tag += val; return; }
		int mid = (s[p].l + s[p].r) >> 1;
		if (l <= mid) update(s[p].lch, l, r, val);
		if (r > mid) update(s[p].rch, l, r, val);
		s[p].dat += val * (min(r, s[p].r) - max(l, s[p].l) + 1);
	}
	LL query(int p, int l, int r) {
		if (s[p].l >= l && s[p].r <= r) { return s[p].dat + s[p].tag * (s[p].r - s[p].l + 1); }
		int mid = (s[p].l + s[p].r) >> 1; LL res = 0;
		if (l <= mid) res += query(s[p].lch, l, r);
		if (r > mid) res += query(s[p].rch, l, r);
		res += s[p].tag * (min(r, s[p].r) - max(l, s[p].l) + 1);
		return res;
	}
} pret;
```