---
layout: post
title: "「Solution」AtCoder[abc177]Code"
date: 2021-1-16
categories: AtCoder
tags: AtCoder
---

>AtCoder[abc177]练习后留档 2021/1/6
<!-- more -->

## A
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int double
using namespace std;

int d, t, s, tt;

signed main() {
    scanf("%lf %lf %lf", &d, &t, &s);
    tt = d / s;
    if (tt <= t)
        printf("Yes\n");
    else
        printf("No\n");
    return 0;
}
```
## B
注意有坑，下标不要整出边界了。
```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 1005 + 50;

char a[MAXN], b[MAXN];
int lena, lenb, ans;

int Summary(int id) {
    int sum = 0;
    for (int i = 1; i <= lenb; i++) {
        if (b[i] != a[i + id - 1])
            sum++;
    }
    return sum;
}

int main() {
    scanf("%s", a + 1);
    scanf("%s", b + 1);
    lena = strlen(a + 1);
    lenb = strlen(b + 1);
    ans = 0x3f3f3f3f;
    for (int i = 1; i <= lena - lenb + 1; i++) {
        ans = min(ans, Summary(i));
    }
    printf("%d\n", ans);
    return 0;
}
```

## C
给出一个数组，数组元素两两求积，再对积求和，输出该和值。
利用前缀和。	有点像小学奥数里的分组比赛问题。
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int mod = 1e9 + 7;
const int MAXN = 4 * 1e5 + 5;

int a[MAXN], n;
int sum[MAXN], ans;

signed main() {
    scanf("%lld", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%lld", &a[i]);
        sum[i] = (sum[i - 1] % mod + a[i] % mod) % mod;
    }
    for (int i = 1; i <= n; i++) {
        ans = (ans % mod + (a[i] % mod * sum[i - 1] % mod) % mod) % mod;
    }
    printf("%lld\n", ans % mod);
    return 0;
}
```
## D
并查集板题，加一个$rank$值即可。
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 4 * 1e5 + 5;

int n, m, ans;

struct node {
    int rank, f;
} fa[MAXN];

void MakeSet(int n) {
    for (int i = 1; i <= n; i++) {
        fa[i].f = i;
        fa[i].rank = 1;
    }
}

int FindSet(int v) {
    if (fa[v].f == v)
        return v;
    else
        return fa[v].f = FindSet(fa[v].f);
}

void UnionSet(int u, int v) {
    int x = FindSet(u);
    int y = FindSet(v);
    if (x == y)
        return;
    fa[x].f = fa[y].f;
    fa[y].rank += fa[x].rank;
    return;
}

int main() {
    scanf("%d %d", &n, &m);
    MakeSet(n);
    for (int i = 1, u, v; i <= m; i++) {
        scanf("%d %d", &u, &v);
        UnionSet(u, v);
    }
    for (int i = 1; i <= n; i++) {
        ans = max(ans, fa[FindSet(i)].rank);
    }
    printf("%d\n", ans);
    return 0;
}
```
## E
此题一开始我的做法是错的。	
但是数据良心地给了我$80pts$。	
判断最大公约数是否是1，可以用$gcd$直接跑。	
但判断两两互质有点麻烦。如果暴力的话一定会超时，可以考虑含根号的算法，即$\sqrt{n}n$。	
那么将每个数都进行质因数分解，如果一个因子被记录超过1次，则可以判断了。	
细节有点多，还有特判。
```cpp
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <algorithm>
using namespace std;
const int SIZE = 2 * 1e6 + 5;
const int MAXN = 1e6;

int n, a[SIZE], pre[SIZE], cnt, Gcd, tot[MAXN];
int vis[SIZE];
bool flag1;

bool cmp(int x, int y) { return x < y; }

int gcd(int x, int y) {
    if (y == 0)
        return x;
    else
        return gcd(y, x % y);
}

void change(int x) {
    for (int i = 2; i <= sqrt(x); i++) {
        if (x % i == 0) {
            tot[i]++;
            if (tot[i] != 1) {
                printf("setwise coprime\n");
                exit(0);
            }
            while (x % i == 0) x /= i;
        }
    }
    if (x != 1) {
    	tot[x]++;
    	if (tot[x] != 1) {
            printf("setwise coprime\n");
            exit(0);
	}
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        if (a[i] != 1) {
            flag1 = 1;
        }
        if (i == 1)
            Gcd = a[i];
        else
            Gcd = gcd(a[i], Gcd);
    }
    if (flag1 == 0) {
        printf("pairwise coprime\n");
        return 0;
    }
    if (Gcd != 1) {
        printf("not coprime\n");
    } else {
        sort(a + 1, a + 1 + n, cmp);
        for (int i = 1; i <= n; i++) {
        	if (a[i] == 1) continue;
            change(a[i]);
        }
        printf("pairwise coprime\n");
    }
    return 0;
}
```