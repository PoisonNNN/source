---
layout: post
title: "「Summary」NOI Online 2022 提高组 瞎搞游记"
date: 2022-03-26
tags: [游记]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

真·乱搞   
<!-- more -->

## $\mathbb{丹钓战}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P8251)
### $\mathcal{Sol}$
没啥好说的。   
先按照题意模拟一遍《丹钓战》，然后你可记录在未插入当前点时栈顶点的下标，作为当前点的权值。   
对于查询 `[l, r]` ，就是找 `[l, r]` 内权值 `<l` 的点的个数。
### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 5e5 + 5;
const int MAXLOG = 55;

inline void read(int& x) {
	x = 0; int f = 1;
	char c = getchar();
	while (c < '0' || c > '9') {
		if (c == '-') f = -f;
		c = getchar();
	}
	while (c >= '0' && c <= '9') {
		x = (x << 3) + (x << 1) + (c ^ 48);
		c = getchar();
	}
	x *= f;
}

inline void write(int x) {
	if (x < 0) {
		putchar('-');
		x = -x;
	}
	if (x > 9)
		write(x / 10);
	putchar(x % 10 + '0');
}

int n, m, tp, pre[MAXN];
struct Position { int a, b, ind; } pos[MAXN], st[MAXN];

struct PresidentTree {
	int tot, root[MAXN];
	struct Node { int lch, rch, dat; } s[MAXN * MAXLOG];
	void push_up(int p) { s[p].dat = s[s[p].lch].dat + s[s[p].rch].dat; }
	void update(int& p, int l, int r, int pos, int val) {
		s[++tot] = s[p], p = tot;
		if (l == r) {
			s[p].dat += val;
			return;
		}
		int mid = (l + r) >> 1;
		if (pos <= mid) update(s[p].lch, l, mid, pos, val);
		else update(s[p].rch, mid + 1, r, pos, val);
		push_up(p);
	}
	int query(int p, int l, int r, int ql, int qr) {
		if (l >= ql && r <= qr) return s[p].dat;
		int mid = (l + r) >> 1, res = 0;
		if (ql <= mid) res += query(s[p].lch, l, mid, ql, qr);
		if (qr > mid) res += query(s[p].rch, mid + 1, r, ql, qr);
		return res;
	}
} pret;

int main() {
	
	freopen("stack.in", "r", stdin);
	freopen("stack.out", "w", stdout);

	// scanf("%d %d", &n, &m);
	read(n), read(m);
	for (int i = 1; i <= n; i++) {
		// scanf("%d", &pos[i].a);
		read(pos[i].a);
	}
	for (int i = 1; i <= n; i++) {
		// scanf("%d", &pos[i].b);
		read(pos[i].b);
	}

	// int tot = 0;
	for (int i = 1; i <= n; i++) {
		pos[i].ind = i;
		while (tp && (st[tp].a == pos[i].a || st[tp].b <= pos[i].b)) tp--;
		pre[i] = st[tp].ind;
		st[++tp] = pos[i];
		// if (tp == 1) {
		// 	tot++;
		// }
		// printf("%d ", tot);
	}

	// for (int i = 1; i <= n; i++) {
	// 	printf("%d ", pre[i]);
	// }

	for (int i = 1; i <= n; i++) pret.update(pret.root[i] = pret.root[i - 1], 0, n, pre[i], 1);
	for (int i = 1, l, r; i <= m; i++) {
		// scanf("%d %d", &l, &r);
		read(l), read(r);
		int tmp = pret.query(pret.root[r], 0, n, 0, l - 1) - pret.query(pret.root[l - 1], 0, n, 0, l - 1);
		// printf("%d\n", tmp);
		write(tmp), putchar('\n');
	}

	return 0;
}
```

## $\mathbb{讨论}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P8252)
### $\mathcal{Sol}$
写了个神奇暴力在洛谷过了。。。   
大约的确出题人应该不会想到我这个做法，但愿卡不掉吧。。。   

---

思路很简单，对于每个人和他会做的题目连边，依次遍历每个人和他会做的每一道题，找到另外会做这道题的人，把他的贡献值加一， 在一个人遍历完之后查看有贡献的人，如果他的贡献值比双方会做的题数都要少，那么两个人就可以讨论。   
如果，这个人找不到讨论对象，就可以把连向他的所有边删掉。   
找到就直接输出。   

---

其实可以把这个做法卡到 $\mathcal{O(n^2)}$ 。   
但是我觉得出题人不会这样造数据。 /xyx

---

update: 嘤嘤嘤，被卡成了 $70pts$ (2022-03-30)

### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <vector>
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 5e6 + 5;

inline void read(int& x) {
	x = 0; int f = 1;
	char c = getchar();
	while (c < '0' || c > '9') {
		if (c == '-') f = -f;
		c = getchar();
	}
	while (c >= '0' && c <= '9') {
		x = (x << 3) + (x << 1) + (c ^ 48);
		c = getchar();
	}
	x *= f;
}

inline void write(int x) {
	if (x < 0) {
		putchar('-');
		x = -x;
	}
	if (x > 9)
		write(x / 10);
	putchar(x % 10 + '0');
}

int t, n, tot[MAXN], cnt[MAXN], ans1, ans2;
vector<int> G[MAXN], pos;

int main() {

	freopen("discuss.in", "r", stdin);
	freopen("discuss.out", "w", stdout);

	// scanf("%d", &t);
	read(t);

	while (t--) {

		// scanf("%d", &n);
		read(n);
		// printf("!!%d!!\n", n);
		ans1 = ans2 = 0;
		for (int i = 1; i <= 2 * n; i++) G[i].clear();
		for (int i = 1, k; i <= n; i++) {
			// scanf("%d", &k);
			read(k);
			cnt[i] = k;
			for (int j = 1, x; j <= k; j++) {
				// scanf("%d", &x);
				read(x);
				G[i].push_back(x + n);
				G[x + n].push_back(i);
			}
		}

		bool flag = false;
		for (int i = n; i >= 1; i--) {
			pos.clear();

			for (int j = 0; j < G[i].size(); j++) {
				int u = G[i][j];
				G[u].pop_back();
				for (int k = 0; k < G[u].size(); k++) {
					int v = G[u][k];
					tot[v]++;
					if (tot[v] == 1) pos.push_back(v);
				}
			}

			for (int j = 0; j < pos.size(); j++) {
				if (tot[pos[j]] < cnt[pos[j]] && tot[pos[j]] < cnt[i]) {
					// printf("%d %d %d\n", tot[pos[j]], cnt[pos[j]], cnt[i]);
					ans1 = i, ans2 = pos[j];
					flag = true;
					break;
				}
			}
			for (int j = 0; j < pos.size(); j++) tot[pos[j]] = 0;

			if (flag) {
				break;
			}

		}
		if (flag) {
			printf("YES\n%d %d\n", ans1, ans2);
		} else {
			printf("NO\n");
		}

	}

	return 0;
}
```

## $\mathbb{如何正确地排序}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P8253)
### $\mathcal{Sol}$
不会了。
### $\mathcal{Code}$

