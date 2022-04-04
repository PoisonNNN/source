---
layout: post
title: "「Solution」A Simple Chess"
date: 2021-11-26
categories: 数学
tags: [数学, 容斥原理]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

可能就只有我会把$i,j$写反吧。。。
<!-- more -->

## A Simple Chess
[link](https://vjudge.net/contest/470752#problem/E)

## 分析

首先对于每个读进来的坐标从左上到右下排序。  

总方案数是明确的，考虑不合法的方案数。  

因为给定的棋子走的方式比较奇怪，先考虑如何计算出某两个点之间的到达的方案数。   
假设两个点之间的横距离为$a$，纵距离为$b$，并且走了$x$步**右$2$下$1$**的操作，走了$y$步**右$1$下$2$**的操作。   
可以列出方程   
$\begin {cases} 2x + y = a \\ 2y + x = b \end {cases}$
$\rightarrow \begin {cases} x = \frac{2a-b}{3} \\ y = \frac{2b-a}{3} \end {cases}$   
如果$x,y \notin \mathbb{Z}$表示方案数为$0$，直接返回即可。如果满足要求则方案数为$\dbinom{x+y}{x}$

对于每个点$(x_i, y_i)$，考虑从起点到它且不经过其他点的方案数是$dp_i$。如果单纯计算从$(1,1)$到$(x_i, y_i)$的路径数，其中可能会经过排序在它前面的点，这时需要容斥，容斥的系数为先前一个点$j$到$i$的路径数。    

即是 $dp_i = \sum_{j = 1}^{i - 1} Dist(j, i) \times dp_j$  



最后答案 $Ans = \dbinom{n + m}{n} - \sum_{i = 1} ^ {cnt} Dist(i, (n, m)) \times dp_i$

## 代码

<details>

```cpp
#include <cstdio>
#include <cstring>
#include <cstdlib>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 205;
const int Mod = 110119;

int cnt, cas;
int ans, n, m, sum, fac[Mod + 5], dp[Mod + 5];

struct node {
    int x, y;
} s[MAXN];

int qpow(int x, int y) {
    int res = 1;
    while (y) {
        if (y & 1) res = res * x % Mod;
        x = x * x % Mod;
        y >>= 1;
    }
    return res;
}

int C(int x, int y) {
    if (y > x) return 0;
    if (y == 0 || y == x) return 1;
    return fac[x] * qpow(fac[y], Mod - 2) % Mod * qpow(fac[x - y], Mod - 2) % Mod;
}

int Lucas(int x, int y) {
    if (y == 0) return 1ll;
    return C(x % Mod, y % Mod) * Lucas(x / Mod, y / Mod) % Mod;
}

int Dis(int p, int q) {
    int a = s[q].x - s[p].x, b = s[q].y - s[p].y;
    if ((2 * a - b) % 3 != 0 || (2 * b - a) % 3 != 0) return 0;
    int x = (2 * a - b) / 3;
    int y = (2 * b - a) / 3;
    if (x < 0 || y < 0) return 0;
    return Lucas(x + y, x);
}

bool cmp(node a, node b) {
    return (a.x == b.x) ? a.y < b.y : a.x < b.x;
}

signed main() {

    // freopen("test.in", "r", stdin);

    fac[0] = 1;
    for (int i = 1; i < Mod - 1; i++) {
        fac[i] = fac[i - 1] * i % Mod;
    }

    while (scanf("%lld %lld %lld", &n, &m, &cnt) != EOF) {

        for (int i = 0; i <= cnt + 5; i++) {
            s[i].x = s[i].y = dp[i] = 0;
        }

        for (int i = 1; i <= cnt; i++) {
            scanf("%lld %lld", &s[i].x, &s[i].y);
        }
        s[++cnt].x = 1, s[cnt].y = 1;

        sort(s + 1, s + 1 + cnt, cmp);

        ans = 0;

        dp[1] = 1ll;
        for (int i = 2; i <= cnt; i++) {
            for (int j = 1; j < i; j++) {
                dp[i] = (dp[i] - Dis(j, i * dp[j] % Mod + Mod) % Mod; // 此处的 i, j写反了!!!!!!
            }
        }

        s[++cnt].x = n, s[cnt].y = m;

        for (int i = 1; i < cnt; i++) {
            ans = ((ans + dp[i] * Dis(i, cnt)) % Mod + Mod) % Mod;
        }

        printf("Case #%lld: %lld\n", ++cas, ans);

    }

    return 0;
}
```

</details>