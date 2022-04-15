---
layout: post
title: "「Solution」棘手的操作 题解"
date: 2022-03-29
tags: [题解, 线段树, 并查集]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

~~关于我不会可并堆这个事。~~   

<!-- more -->

## $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P3273)
## $\mathcal{Sol}$

两个 $log$ 的线段树合并应该都会，我就不说了。   
这个做法的瓶颈在于合并过慢，考虑把它办了。   
希望是不合并，把每个连通块搞到一段连续的区间里，以降低复杂度。   
发现只有合并操作，不用考虑分割，貌似这个思路是对的。   
继续想，把所有的联通块都可以挤扁成链，如图：   
![](https://chenjg41.gitee.io/assets/images/T2.png)   
当两个联通块中有两点相连时，可以等价地直接把他们首尾相接。   
那么可以先把操作离线下来，把所有的合并操作先做一遍，并记录每个操作后，被操作的连通块的大小变化，同时把连出来的图存好。   
跑 `dfs` ，标上 `dfn` ，现在图上只有多条单链，可以直接映射到线段树上，就可以把联通块的操作转到区间上。   

---

大约的确是没写的太清楚，可以看看加了注释的代码

## $\mathcal{Code}$

```cpp
// Maybe it is a LinkQueryTree...
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 3e5 + 5;
const int INF = 0x3f3f3f3f;

int n, m, a[MAXN], dfn[MAXN], redfn[MAXN], tot;
struct Question { int opt, u, v; } que[MAXN];
vector<int> G[MAXN];
vector<int> len[MAXN];

struct UnionFindSet {
	int fa[MAXN], siz[MAXN], tail[MAXN];
	void init() {
		for (int i = 1; i <= n; i++) {
			len[i].clear();
			G[i].clear();
			fa[i] = tail[i] = i, siz[i] = 1, len[i].push_back(1);
		}
	}
	int FindSet(int v) {
		if (fa[v] == v) return v;
		else return fa[v] = FindSet(fa[v]);
	}
	void UnionSet(int u, int v) {
		int x = FindSet(u);
		int y = FindSet(v);
		if (x == y) return;
		if (siz[x] < siz[y]) swap(x, y); // 启发式
		fa[y] = tail[x]; // 将小块的头接到大块的尾
		G[tail[x]].push_back(y); // 大块的尾箱小块的头连边，把两条链拼一起
		tail[x] = tail[y]; // 更新链尾
		siz[x] += siz[y]; // 更新大小
		len[x].push_back(siz[x]); // 记录每个版本的链长
	}
} dsu;

void dfs(int u) {
	dfn[u] = ++tot, redfn[tot] = u; // 双向映射
	for (int i = 0; i < G[u].size(); i++) {
		int v = G[u][i];
		dfs(v);
	}
}

struct SegmentTree { // 这个就是一个区修区查线段树
	struct Node { int l, r, dat, tag; } s[MAXN << 2];
	void push_up(int p) { s[p].dat = max(s[p << 1].dat, s[p << 1 | 1].dat); }
	void push_down(int p) {
		if (!s[p].tag) return;
		s[p << 1].tag += s[p].tag;
		s[p << 1 | 1].tag += s[p].tag;
		s[p << 1].dat += s[p].tag;
		s[p << 1 | 1].dat += s[p].tag;
		s[p].tag = 0;
	}
	void build(int p, int l, int r) {
		s[p].l = l, s[p].r = r;
		if (l == r) {
			s[p].dat = a[redfn[l]];
			return;
		}
		int mid = (l + r) >> 1;
		build(p << 1, l, mid), build(p << 1 | 1, mid + 1, r);
		push_up(p);
	}
	void update(int p, int l, int r, int val) {
		if (s[p].l >= l && s[p].r <= r) {
			s[p].dat += val;
			s[p].tag += val;
			return;
		}
		push_down(p);
		int mid = (s[p].l + s[p].r) >> 1;
		if (l <= mid) update(p << 1, l, r, val);
		if (r > mid) update(p << 1 | 1, l, r, val);
		push_up(p);
	}
	int query(int p, int l, int r) {
		if (s[p].l >= l && s[p].r <= r) return s[p].dat;
		push_down(p);
		int mid = (s[p].l + s[p].r) >> 1, res = -INF;
		if (l <= mid) res = max(res, query(p << 1, l, r));
		if (r > mid) res = max(res, query(p << 1 | 1, l, r));
		return res;
	}
} seg;

int main() {

	// freopen("kittle.in", "r", stdin);
	// freopen("kittle.out", "w", stdout);

	scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
	scanf("%d", &m);
	for (int i = 1, u, v; i <= m; i++) {
		char opt[3]; scanf("%s", opt);
		if (opt[0] == 'U') {
			scanf("%d %d", &u, &v), que[i] = Question{0, u, v};
		} else if (opt[0] == 'A') {
			if (opt[1] == '1') scanf("%d %d", &u, &v), que[i] = Question{1, u, v};
			else if (opt[1] == '2') scanf("%d %d", &u, &v), que[i] = Question{2, u, v};
			else scanf("%d", &v), que[i] = Question{3, 0, v};
		} else {
			if (opt[1] == '1') scanf("%d", &u), que[i] = Question{4, u, 0};
			else if (opt[1] == '2') scanf("%d", &u), que[i] = Question{5, u, 0};
			else que[i] = Question{6, 0, 0};
		}
	}

	// 离线下来，连一遍图
	dsu.init();
	for (int i = 1; i <= m; i++) if (que[i].opt == 0) {
		dsu.UnionSet(que[i].u, que[i].v);
	}
	// 建立映射
	for (int i = 1; i <= n; i++) if (dsu.FindSet(i) == i) { // 对于每条链，从链头开始标 dfn
		dfs(i);
	}

	// for (int i = 1; i <= n; i++) {
	// 	printf("%d ", dfn[i]);
	// }
	// printf("\n");
	// for (int i = 1; i <= n; i++) {
	// 	printf("%d ", redfn[i]);
	// }
	// printf("\n");

	seg.build(1, 1, n), dsu.init(); // 这里又把 dsu 清空一遍，边操作边更新，保证 dsu 和当前图的一致性
	for (int i = 1, res; i <= m; i++) {
		if (que[i].opt == 0) {
			dsu.UnionSet(que[i].u, que[i].v);
		} else if (que[i].opt == 1) {
			seg.update(1, dfn[que[i].u], dfn[que[i].u], que[i].v);
		} else if (que[i].opt == 2) { // 现在这个联通块对应的区间就是 [dfn[now], dfn[now] + len - 1]
			int now = dsu.FindSet(que[i].u);
			seg.update(1, dfn[now], dfn[now] + len[now][len[now].size() - 1] - 1, que[i].v);
		} else if (que[i].opt == 3) {
			seg.update(1, 1, n, que[i].v);
		} else if (que[i].opt == 4) {
			res = seg.query(1, dfn[que[i].u], dfn[que[i].u]);
			printf("%d\n", res);
		} else if (que[i].opt == 5) {
			int now = dsu.FindSet(que[i].u);
			res = seg.query(1, dfn[now], dfn[now] + len[now][len[now].size() - 1] - 1);
			printf("%d\n", res);
		} else {
			res = seg.query(1, 1, n);
			printf("%d\n", res);
		}

		/*
		for (int j = 1; j <= n; j++) {
			printf("%d ", seg.query(1, dfn[j], dfn[j]));
		}
		printf("\n");
		*/

	}

	return 0;
}
```
