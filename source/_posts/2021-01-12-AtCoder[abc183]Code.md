---
layout: post
title: "「Solution」AtCoder[abc183]Code"
date: 2021-01-12
categories: AtCoder
tags: AtCoder
---
打了一下abc的题，~~好简单~~。      
留档存个代码吧。        
<!-- more -->
## A
有手就行。       
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;

long long x;

int main() {
	scanf ("%lld", &x);
	if (x >= 0) {
		printf ("%lld\n", x);
	} else {
		printf ("0\n");
	}
	return 0;
}
```
## B
相似...       
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define db double
using namespace std;

db sx, sy, tx, ty;
db t, ans;

int main() {
	scanf ("%lf %lf %lf %lf", &sx, &sy, &tx, &ty);
	t = (tx - sx) / (sy + ty) * sy;
	ans = sx + t;
	printf ("%.10lf\n", ans);
	return 0;
}
```

## C
无脑爆搜即可。
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 15;

int n, k;
int dis[MAXN][MAXN], ans;
bool vis[MAXN];

void dfs(int now, int tot, int sum) {
	if (tot == n) {
		sum += dis[now][1];
		if (sum == k) {
			ans++;
		}
		return;
	}
	for (int i = 2; i <= n; i++) {
		if (i == now || vis[i] == 1) continue;
		vis[i] = 1;
		dfs(i, tot + 1, sum + dis[now][i]);
		vis[i] = 0;
	}
}

int main() {
	scanf ("%d %d", &n, &k);
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= n; j++) {
			scanf ("%d", &dis[i][j]);
		}
	}
	dfs(1, 1, 0);
	printf ("%d\n", ans);
	return 0;
}
```
## D
开始还把题看错了。       
直接差分搞。
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define LL long long
using namespace std;
const int MAXN = 2 * 1e5 + 5;

int n, w, m;
LL a[MAXN];

int main() {
	scanf ("%d %d", &n, &w);
	for (int i = 1, s, t, q; i <= n; i++) {
		scanf ("%d %d %d", &s, &t, &q);
		a[s] += q, a[t] -= q;
		m = max(m, t);
	}
	for (int i = 0; i < m; i++) {
		a[i] += a[i - 1];
		if (a[i] > w) {
			printf ("No\n");
			return 0;
		}
	}
	printf ("Yes\n");
	return 0;
}
```
## E
前缀和优化DP
```cpp
#include <queue>
#include <cstdio>
#include <iostream>
#include <algorithm>
#define LL long long
using namespace std;
const int MAXN = 2005;
const int mod = 1e9 + 7;

int n, m;
LL dp[MAXN][MAXN], sum[MAXN][MAXN][5];
bool vis[MAXN][MAXN];

int main() {
	scanf ("%d %d", &n, &m);
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= m; j++) {
			char x;
			cin >> x;
			if (x == '.') {
				vis[i][j] = 1;
			}
		}
	}
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= m; j++) {
			if (vis[i][j] == 1) {
				dp[i][j] = (sum[i - 1][j][1] + sum[i][j - 1][2] + sum[i - 1][j - 1][3]) % mod; 
				if (i == 1 && j == 1) dp[i][j]++;
				sum[i][j][1] = (sum[i - 1][j][1] + dp[i][j]) % mod;
				sum[i][j][2] = (sum[i][j - 1][2] + dp[i][j]) % mod;
				sum[i][j][3] = (sum[i - 1][j - 1][3] + dp[i][j]) % mod;
			} 
		}
	}
	printf ("%lld\n", dp[n][m]);
	return 0;
}
```

## F 
改良版的并查集。        
要用$map$离散化。
```cpp
#include <map>
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 2 * 1e5 + 5;

struct node {
	map<int, int> tot;
	int f, id, c;
} fa[MAXN];

int FindSet(int v) {
	if (fa[v].f == fa[v].id) {
		return fa[v].f;
	} else return fa[v].f = FindSet(fa[v].f);
}

void UnionSet(int u, int v) {
	int x = FindSet(u);
	int y = FindSet(v);
	if (fa[x].f == fa[y].f) return;
	map<int, int>::iterator it;
	for (it = fa[x].tot.begin(); it != fa[x].tot.end(); it++) {
		if (fa[y].tot.find(it -> first) == fa[y].tot.end()) {
			fa[y].tot[it -> first]++;
		}
	}
	fa[x].f = fa[y].f;
}

int n, q;

int main() {
	scanf ("%d %d", &n, &q);
	for (int i = 1; i <= n; i++) {
		scanf ("%d", fa[i].c);
		fa[i].f = i, fa[i].id = i, fa[i].tot[fa[i].c]++;
	}
	while (q--) {
		int opt, x, y;
		scanf ("%d %d %d", &opt, &x, &y);
		if (opt == 1) {
			UnionSet(x, y);
		} else {
			printf ("%d\n", fa[x].tot[y]);
		}
	}
	return 0;
}
```
## END