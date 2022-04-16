---
layout: post
title: "「Solution」[COCI2019-2020#1] Zoo"
date: 2021-02-20
tags: [题解, 树形结构]
---

挺妙的

<!-- more -->

## Problem
[Link](https://www.luogu.com.cn/problem/P7208)

## Solution
首先考虑 $\le100$ 的 $45$ 分。

显然的是，$(1,1)$ 和 $(n,m)$ 的位置上的动物一定是最后逃跑的那只动物。
由于是求至少逃走的动物数量，所以我们希望每只动物覆盖的区域尽可能的广。

因为同一只动物可以在同一位置停留数次，所以我们可以先把包含 $(1,1)$ 的连通块从 B -> T 或 T -> B。
即这个连通块可能覆盖了上一只不同的动物的脚印。再继续改变，直到每个脚印相同，改变的次数即为所求。

但这样的复杂度显然过不了 $\le1000$ 的。

转化一下，将每一个连通块当成一个点，我们可以将相邻两个连通块连上边，而这棵树的深度就是答案。

## Code
```cpp
#include <cstdio>
#include <cstring>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

typedef long long LL;

const int Maxn = 1e3;

int n, m, tot;
char G[Maxn + 5][Maxn + 5];
int ct[Maxn + 5][Maxn + 5], vis[Maxn + 5][Maxn + 5], dep[Maxn * Maxn + 5];
int dir[4][2] = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};

vector < int > edge[Maxn * Maxn + 5];

void Fill (int x, int y, int k, char tar) {
    if (x < 1 || x > n || y < 1 || y > m) return;
	
    vis[x][y] = 1; ct[x][y] = k;
    for (int i = 0; i < 4; i ++) {
        int xx = x + dir[i][0];
        int yy = y + dir[i][1];
        if (!vis[xx][yy] && G[xx][yy] == tar) Fill (xx, yy, k, tar);
    }
}

void Link (int u, int v) {
    edge[u].push_back (v);
}

int Max (int a, int b) {
    return a > b ? a : b;
}

int Search (int u) {
    queue < int > que;
    que.push (u); dep[u] = 1;
	
    int depth = 0;
    while (que.size ()) {
        int v = que.front ();
        que.pop ();
        depth = Max (depth, dep[v]);
	for (unsigned int i = 0, k; i < edge[v].size (); i ++) {
	    k = edge[v][i];
	    if (dep[k]) continue;
	    dep[k] = dep[v] + 1;
	    que.push (k); 
	}
    }
	
    return depth;
}

int main () {
    scanf ("%d %d", &n, &m);
	
    for (int i = 1; i <= n; i ++) {
	scanf ("%s", G[i] + 1);
    }
	
    for (int i = 1; i <= n; i ++) {
	for (int j = 1; j <= m; j ++) {
            if (ct[i][j] || G[i][j] == '*') continue;
		Fill (i, j, ++ tot, G[i][j]);
        }
    }

    for (int i = 1; i <= n; i ++) {
	for (int j = 1; j <= m; j ++) {
            if (G[i][j] == '*') continue;
	    for (int k = 0; k < 4; k ++) {
	        int x = i + dir[k][0];
		int y = j + dir[k][1];
		if (x < 1 || x > n || y < 1 || y > m) continue;
		if (G[x][y] == G[i][j] || G[x][y] == '*') continue;
		Link (ct[i][j], ct[x][y]);
            }
	}
    }
	
    for (int i = 1; i <= tot; i ++) {
	sort (edge[i].begin (), edge[i].end ());
        unique (edge[i].begin (), edge[i].end ());
    }
	
    printf ("%d", Search (1));
    return 0;
}

```