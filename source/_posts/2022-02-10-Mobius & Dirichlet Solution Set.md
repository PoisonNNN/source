---
layout: post
title: "「Solution」Mobius & Dirichlet Solution Set"
date: 2022-02-06
categories: 数学
tags: [数学, 莫比乌斯反演, Dirichlet]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

All in one... updating...
<!-- more -->

## $\mathcal{Theorem}$

$$
[\gcd(i, j) = 1] = \sum_{d | \gcd(i, j)} \mu(d)
$$

## $\mathbb{完全平方数}$

### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P4318)
### $\mathcal{Sol}$
二分 + 莫比乌斯函数
$$
Ans = \sum_{i = 1}^{\sqrt{n}} \lfloor \frac{n}{i^2} \rfloor
$$

### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define LL long long
using namespace std;
const int MAXN = 1e5 + 5;

int t, n, cnt, mu[MAXN], pr[MAXN];
LL l, r, mid;
bool vis[MAXN];

void Euler() {
	mu[1] = 1;
	for (int i = 2; i <= 1e5; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 1e5; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) {
				mu[i * pr[j]] = 0;
				break;
			}
			mu[i * pr[j]] = -mu[i];
		}
	}
}

int count(LL x) {
	int sum = 0;
	for (int i = 1; i * i <= x; i++) {
		sum += (x / (i * i)) * mu[i];
	}
	return sum;
}

int main() {

	Euler();

	// for (int i = 2; i <= 20; i++) {
	// 	printf("%d ", mu[i]);
	// }
	// printf("\n");

	scanf("%d", &t);

	// printf("test %d\n", count(19));

	while (t--) {

		scanf("%d", &n);
		l = 1, r = 2e9;
		while (l < r) {
			mid = (l + r) >> 1;
			if (count(mid) >= n) r = mid;
			else l = mid + 1;
		}
		printf("%lld\n", l);

	}

	return 0;
}
```

## $\mathbb{GCD}$
### $\mathcal{Link}$
[link](http://222.180.160.110:1024/contest/2387/problem/2)
### $\mathcal{Sol}$

$$
\begin{aligned}
    &\sum_{i = 1}^{n} \sum_{j = 1}^{m} [\gcd(i, j) = k] \\
    = &\sum_{i = 1}^{\lfloor \frac{n}{k} \rfloor} \sum_{j = 1}^{\lfloor \frac{m}{k} \rfloor} [\gcd(i, j) = 1] \\
    = &\sum_{i = 1}^{\lfloor \frac{n}{k} \rfloor} \sum_{j = 1}^{\lfloor \frac{m}{k} \rfloor} \sum_{d \mid \gcd(i, j)} \mu(d) \\
    = &\sum_{d = 1}^{\min(\lfloor \frac{n}{k} \rfloor, \lfloor \frac{m}{k} \rfloor)} \mu(d) \lfloor \frac{n}{kd} \rfloor \lfloor \frac{m}{kd} \rfloor
\end{aligned}
$$
然后可以直接枚举 $d$ 。   
### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int long long
#define LL long long
using namespace std;
const int MAXN = 1e5 + 5;

int vis[MAXN], pr[MAXN], cnt, mu[MAXN], sum[MAXN];
int a, b, c, d, k, t, cas;

void Euler () {
	
	mu[1] = 1;
	for (int i = 2; i <= 1e5; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 1e5; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) {
				mu[i * pr[j]] = 0;
				break;
			}
			mu[i * pr[j]] = -mu[i];
		}
	}

	for (int i = 1; i < MAXN; i++) sum[i] = sum[i - 1] + mu[i];

}

signed main() {

	Euler ();
	scanf("%lld", &t);

	while(t--) {
		
		scanf("%lld %lld %lld %lld %lld", &a, &b, &c, &d, &k);
		if (k == 0) {
			printf("Case %lld: 0", ++cas); continue;
		}

		b = b / k, d = d / k;
		if (b > d) swap(b, d);

		LL all, del, last, ans;
		all = del = 0;
		for (int i = 1; i <= b; i = last + 1) {
			last = min(b / (b / i), d / (d / i));
			all += (sum[last] - sum[i - 1]) * (b / i) * (d / i);
		}
		for (int i = 1; i <= b; i = last + 1) {
			last = b / (b / i);
			del += (sum[last] - sum[i - 1]) * (b / i) * (b / i);
		}
		ans = all - (del / 2);

		printf("Case %lld: %lld\n", ++cas, ans);

	}

	return 0;
}
```

## $\mathbb{[HAOI2011]Problem \ b}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P2522)
### $\mathcal{Sol}$

$$
\begin{aligned}
    &\sum_{i = 1}^{n} \sum_{j = 1}^{m} [\gcd(i, j) = k] \\
    = &\sum_{i = 1}^{\lfloor \frac{n}{k} \rfloor} \sum_{j = 1}^{\lfloor \frac{m}{k} \rfloor} [\gcd(i, j) = 1] \\
    = &\sum_{i = 1}^{\lfloor \frac{n}{k} \rfloor} \sum_{j = 1}^{\lfloor \frac{m}{k} \rfloor} \sum_{d \mid \gcd(i, j)} \mu(d) \\
    = &\sum_{d = 1}^{\min(\lfloor \frac{n}{k} \rfloor, \lfloor \frac{m}{k} \rfloor)} \mu(d) \lfloor \frac{n}{kd} \rfloor \lfloor \frac{m}{kd} \rfloor
\end{aligned}
$$

然后应该用整除分块，时间复杂度 $\mathcal{O}(n + \sqrt{n})$ 。   

### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 5e4 + 5;

int t, a, b, c, d, k, pr[MAXN], cnt, ans;
int mu[MAXN], sum[MAXN];
bool vis[MAXN];

void read(int& x) {
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

void Euler () {

	mu[1] = 1;
	for (int i = 2; i <= 5e4; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 5e4; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) {
				mu[i * pr[j]] = 0;
				break;
			}
			mu[i * pr[j]] = -mu[i];
		}
	}

	for (int i = 1; i < MAXN; i++) sum[i] = sum[i - 1] + mu[i];

}

int query (int n, int m) {
	if (n > m) swap(n, m);
	int res = 0;
	for (int i = 1, last = 1; i <= n; i = last + 1) {
		last = min(n / (n / i), m / (m / i));
		res += (sum[last] - sum[i - 1]) * (n / (i * k)) * (m / (i * k));
	}
	return res;
}

int main () {

	read(t);
	Euler ();

	while (t--) {
		read(a), read(b), read(c), read(d), read(k);
		ans = query (b, d) - query (a - 1, d) - query (c - 1, b) + query (a - 1, c - 1);
		printf ("%d\n", ans);
	}

	return 0;
}
```

## $\mathbb{YY的GCD}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P2257)
### $\mathcal{Sol}$
令所有素数组成的集合为 $\mathbb{P}$ 。   

$$
\begin{aligned}
    & \sum_{i = 1}^{n} \sum_{j = 1}^{m} [\gcd(i, j) \in \mathbb{P}] \\
    = & \sum_{p \in \mathbb{P}} \sum_{i = 1}^{n} \sum_{j = 1}^{m} [\gcd(i, j) = p] \\
    = & \sum_{p \in \mathbb{P}} \sum_{i = 1}^{\lfloor \frac{n}{p} \rfloor} \sum_{i = 1}^{\lfloor \frac{n}{p} \rfloor} [\gcd(i, j) = 1] \\
    = & \sum_{p \in \mathbb{P}} \sum_{d = 1}^{\min(\lfloor \frac{n}{p} \rfloor, \lfloor \frac{m}{p} \rfloor)} \mu(d) \lfloor \frac{n}{pd} \rfloor \lfloor \frac{m}{pd} \rfloor
\end{aligned}
$$

令 $pd = T$ 。   
$$
\begin{aligned}
    = & \sum_{p \in \mathbb{P}} \sum_{d = 1}^{\min(\lfloor \frac{n}{p} \rfloor, \lfloor \frac{m}{p} \rfloor)} \mu(d) \lfloor \frac{n}{pd} \rfloor \lfloor \frac{m}{pd} \rfloor \\
    = & \sum_{T = 1}^{n} \lfloor \frac{n}{T} \rfloor \lfloor \frac{m}{T} \rfloor \sum_{p \mid T} \mu(\frac{T}{p}) \\
\end{aligned}
$$

后面的 ${p \mid T} \mu(\frac{T}{p})$ 可以于预处理。   
然后又是喜闻乐见的整除分块。   

### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 1e7 + 5;

int t, n, m, pr[MAXN], cnt, ans;
int mu[MAXN], h[MAXN];
bool vis[MAXN];

void Euler () {
	mu[1] = 1;
	for (int i = 2; i <= 1e7; i++) {
		if (!vis[i]) {
			pr[++cnt] = i;
			mu[i] = -1;
			h[i] = 1;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 1e7; j++) {
			vis[i * pr[j]] = 1;
			if (i % pr[j] == 0) {
				h[i * pr[j]] = mu[i];
				mu[i * pr[j]] = 0;
				break;
			} else {
				h[i * pr[j]] = -h[i] + mu[i];
				mu[i * pr[j]] = -mu[i];
			}
		}
		h[i] += h[i - 1];
	}
}

signed main () {

	scanf ("%lld", &t);
	Euler ();

	while (t--) {
		scanf ("%lld %lld", &n, &m);
		if (n > m) swap (n, m);
		ans = 0;
		for (int l = 1, r = 1; l <= n; l = r + 1) {
			r = min (n / (n / l), m / (m / l));
            ans = ans + (h[r] - h[l - 1]) * (n / l) * (m / l);
		}
		printf ("%lld\n", ans);
	}
	
	return 0;
}
```

## $\mathbb{Multiplicative Functions}$
### $\mathcal{Link}$
[link](https://vjudge.net/contest/480220#problem/A)
### $\mathcal{Sol}$

$$
\begin{aligned}
	f * f^{-1} = \epsilon \\
	f^{-1}(1) &= \frac{1}{f(1)} \\
	f^{-1}(n) * f(n) &= \sum_{d \mid n} f^{-1}(d)f(\frac{n}{d}) \\
	& = f^{-1}(n)f(1) + \sum_{d \mid n, d \not = n} f^{-1}(d) f(\frac{n}{d}) \\
	& = \epsilon(n) \\
	& = 0\\
	f^{-1}(n) = -\frac{\sum_{d \mid n, d \not = n} f^{-1}(d) f(\frac{n}{d})}{f(1)}
\end{aligned}
$$

### $\mathcal{Code}$

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int Mod = 2007;
const int MAXN = 1e4 + 5;

int n, f[MAXN], invf[MAXN], pr[MAXN], cnt;
bool vis[MAXN];

void Euler() {
	for (int i = 1; i <= n; i++) {
		invf[i] = (-invf[i] + Mod) % Mod;
		printf("%d ", invf[i]);
		for (int j = 2; j * i <= n; j++) {
			invf[i * j] = (invf[i * j] + f[j] * invf[i]) % Mod;
		}
	}
}

int main() {

	scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%d", &f[i]);
	invf[1] = -1;
	Euler();

	return 0;
}
```

## $\mathbb{Clarke \ and \ math}$
### $\mathcal{Link}$
[link](http://222.180.160.110:1024/contest/2387/problem/5)
### $\mathcal{Sol}$
$$
\begin{aligned}
	1(x) = 1 \\
	g_i&=\sum_{i_1\mid i}\sum_{i_2 \mid i_1}\sum_{i_3 \mid i_2}⋯\sum_{i_k\mid i_{k−1}}f(i_k) \\
	   &=\sum_{i_1\mid i}\sum_{i_2 \mid i_1}\sum_{i_3 \mid i_2}⋯\sum_{i_k\mid i_{k−1}}f(i_k) \times 1(\frac{i_{k - 1}}{i_{k}}) \\
       &=\sum_{j \mid i} f(j) * 1^k \\
\end{aligned}
$$
### $\mathcal{Code}$
```cpp
#include <cmath>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 1e5 + 5;
const int Mod = 1e9 + 7;

int t, n, k, f[MAXN], ans[MAXN], tmp[MAXN], x[MAXN];

void dirichlet(int* ans, int* x) {
	memset(tmp, 0, sizeof(tmp));
	int up = sqrt(n);
	for (int i = 1; i <= up; i++) {
		tmp[i * i] = (tmp[i * i] + ans[i] * x[i] % Mod) % Mod;
		for (int j = i + 1; j <= n / i; j++) {
			tmp[i * j] = (tmp[i * j] + ans[i] * x[j] % Mod) % Mod;
			tmp[i * j] = (tmp[i * j] + ans[j] * x[i] % Mod) % Mod;
		}
	}
	for (int i = 1; i <= n; i++) ans[i] = tmp[i];
}

signed main() {

	scanf("%lld", &t);
	while (t--) {
		scanf("%lld %lld", &n, &k);
		for (int i = 1; i <= n; i++) scanf("%lld", &f[i]);
		for (int i = 1; i <= n; i++) ans[i] = 0, x[i] = 1;
		ans[1] = 1;

		while (k) {
			if (k & 1) dirichlet(ans, x);
			dirichlet(x, x);
			k >>= 1;
		}
		dirichlet(ans, f);

		for (int i = 1; i <= n; i++) {
			printf("%lld ", ans[i]);
		}
		printf("\n");

	}

	return 0;
}
```

## $\mathbb{BZOJ4407 \ 于神之怒加强版}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P4449)
### $\mathcal{Sol}$
$$
\begin{aligned}
	  & \sum_{i=1}^n \sum_{j=1}^m \gcd(i,j)^k \\
	= & \sum_{g = 1}^{\min(n, m)} g^k \sum_{i=1}^{\lfloor \frac{n}{g} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{g} \rfloor} [\gcd(i, j) = 1] \\
	= & \sum_{g}^{\min(n, m)} g^k \sum_{i=1}^{\lfloor \frac{n}{g} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{g} \rfloor} \sum_{d \mid \gcd(i, j)} \mu(d) \\
	= & \sum_{d=1}^{\min(n, m)} \sum_{t = 1}^{\lfloor \frac{n}{d} \rfloor} dt^k \sum_{i=1}^{\lfloor \frac{n}{dt} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{dt} \rfloor} \sum_{d \mid \gcd(i, j)} \mu(d) \\
\end{aligned}
$$

令 $T = dt$ 。   

$$
\begin{aligned}
	& = \sum_{T = 1}^{\min(n, m)} \lfloor \frac{n}{T} \rfloor \lfloor \frac{m}{T} \rfloor \sum_{d \mid T} d^k \mu(\frac{T}{d}) \\
	& = \sum_{T = 1}^{\min(n, m)} \lfloor \frac{n}{T} \rfloor \lfloor \frac{m}{T} \rfloor \sum_{d \mid T} id_k(d) \mu(\frac{T}{d}) \\
\end{aligned}
$$   
令 $f(T) = \sum_{d \mid T} id_k(d) \mu(\frac{T}{d})$。   
$$
\begin{aligned}
	f(n) & = f(\prod p_i ^ {x_i}) \\
		 & = \prod f(p_i^{x_i}) \\
	f(p_i^{x_i}) &= \sum_{j = 1}^{x_i} id_k(p_i^j) \times \mu(p_i^{x_i - j}) \\
	&= f(p_i^{x_i})\times \mu(1) + f(p_i^{x_i - 1})\times\mu(p_i) \\
	&= (p_i^k - 1)\times p_i^{k\times (x_i - 1)}
\end{aligned}
$$   
然后《发现》递推关系: $f(p_i^{x_i}) = f(p_i^{x_i - 1}) \times p_i^k$ 。   
当 $x_i = 1$ 时，$f(p_i) = p_i^k - 1$ 。   

在线性筛的时候，首先得到 $f(i)$ ，目前要求 $f(i \times p_j)$ 。   

$$
f(i \times p_j) = 
\begin{cases}
	f(i) \times (p_j^k - 1) \ , \ p_j \nmid i \\
	f(i) \times p_j^k \ , \ p_j \mid i
\end{cases}
$$

### $\mathcal{Code}$
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define LL long long
#define int long long
using namespace std;
const int MAXN = 5e6 + 5;
const int Mod = 1e9 + 7;

int t, k, n, m, cnt, mu[MAXN], pr[MAXN];
bool vis[MAXN];
LL f[MAXN], ans;

LL qpow(LL x, int y) {
	LL res = 1;
	while (y) {
		if (y & 1) res = res * x % Mod;
		x = x * x % Mod;
		y >>= 1;
	}
	return res;
}


void Euler() {
	mu[1] = 1, f[1] = 1;
	for (int i = 2; i <= 5e6; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1, f[i] = (qpow(i, k) - 1 + Mod) % Mod;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 5e6; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) { 
				f[i * pr[j]] = (f[i] * qpow(pr[j], k)) % Mod;
				mu[i * pr[j]] = 0;
				break;
			} else {
				f[i * pr[j]] = (f[i] * (qpow(pr[j], k) - 1 + Mod)) % Mod;
				mu[i * pr[j]] = -mu[i];
			}
		}
	}
	for (int i = 2; i <= 5e6; i++) f[i] = (f[i] + f[i - 1] + Mod) % Mod;
}

signed main() {

	scanf("%lld %lld", &t, &k);
	Euler();

	while (t--) {
		scanf("%lld %lld", &n, &m);

		int up = min(n, m);
		ans = 0;
		for (int i = 1, last = 1; i <= up; i = last + 1) {
			last = min(n / (n / i), m / (m / i));
			ans = (ans + ((n / i) * (m / i) % Mod) * ((f[last] - f[i - 1] + Mod) % Mod)) % Mod;
		}
		printf("%lld\n", ans);

	}

	return 0;
}
```

## $\mathbb{Crash的数字表格}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P1829)
### $\mathcal{Sol}$
$$
\begin{aligned}
	&\sum \limits _{i = 1} ^{n} \sum \limits _{j = 1} ^{m} \mathrm{lcm}( i,j)\\
	=&\sum \limits _{i = 1} ^{n} \sum \limits _{j = 1} ^{m} \frac{i\times j}{\gcd(i,j)} \\
	=&\sum \limits _{i = 1} ^{n} \sum \limits _{j = 1} ^{m} \sum_{d \mid i, d\mid j, \gcd(\frac{i}{d}, \frac{j}{d}) = 1} \frac{i\times j}{d} \\
	=&\sum_{d=1}^{n} \sum_{i=1}^{n} \sum_{j=1}^{m} [\gcd(\frac{i}{d}, \frac{j}{d}) = 1] \frac{i \times j}{d} \\
	=&\sum_{d=1}^{n} \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} [\gcd(i, j) = 1] i\times j \times d \\
	=&\sum_{d=1}^{n} d \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} [\gcd(i, j) = 1] i\times j\\
	=&\sum_{d=1}^{n} d \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} \sum_{g \mid \gcd(i, j)} \mu(g) i\times j\\
	=&\sum_{d=1}^n d \sum_{g \mid d} \mu(g) \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} [g \mid i] \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} [g \mid j] i\times j \\
	=&\sum_{d=1}^n d \sum_{g = 1}^n \mu(g) g^2 \sum_{i=1}^{\lfloor \frac{n}{dg} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{dg} \rfloor} i\times j\\
	=&\sum_{d=1}^n d \sum_{g = 1}^n \mu(g) g^2 \frac{(\lfloor \frac{n}{dg} \rfloor + 1) \times \lfloor \frac{n}{dg} \rfloor}{2} \frac{(\lfloor \frac{m}{dg} \rfloor + 1) \times \lfloor \frac{m}{dg} \rfloor}{2} \\
\end{aligned}
$$   
整除分块可以 $\mathcal{O}(n \log n)$ 处理掉。   
普通版本就可以解决了。   
对于多组数据的情况还要优化。   
$$
\begin{aligned}
	=&\sum_{d=1}^n d \sum_{g = 1}^n \mu(g) g^2 \frac{(\lfloor \frac{n}{dg} \rfloor + 1) \times \lfloor \frac{n}{dg} \rfloor}{2} \frac{(\lfloor \frac{m}{dg} \rfloor + 1) \times \lfloor \frac{m}{dg} \rfloor}{2} \\
	=&\sum_{T=1}^{n} \sum_{d \mid T} d \times \mu(\frac{T}{d}) \times (\frac{T}{d})^2 \frac{(\lfloor \frac{n}{T} \rfloor + 1) \times \lfloor \frac{n}{T} \rfloor}{2} \frac{(\lfloor \frac{m}{T} \rfloor + 1) \times \lfloor \frac{m}{T} \rfloor}{2} \\
	=&\sum_{T=1}^{n} T \frac{(\lfloor \frac{n}{T} \rfloor + 1) \times \lfloor \frac{n}{T} \rfloor}{2} \frac{(\lfloor \frac{m}{T} \rfloor + 1) \times \lfloor \frac{m}{T} \rfloor}{2} \sum_{g \mid T} \mu(g) \times g\\
	=&\sum_{T=1}^{n} T\sum_{g \mid T} \mu(g) \times g \frac{(\lfloor \frac{n}{T} \rfloor + 1) \times \lfloor \frac{n}{T} \rfloor}{2} \frac{(\lfloor \frac{m}{T} \rfloor + 1) \times \lfloor \frac{m}{T} \rfloor}{2} \\
\end{aligned}
$$   
令 $f(x) = \sum_{d \mid x} \mu(d) \times d$ 。   
$$
f(i \times p_j) = 
\begin{cases}
	f(i) , p_j \mid i \\
	f(i) \times f(p_j) , p_j \nmid i
\end{cases}
$$

### $\mathcal{Code}$
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 1e7 + 5;
const int Mod = 100000009;

void read(int& x) {
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

int t, n, m, ans, mu[MAXN], f[MAXN], pr[MAXN], sum[MAXN], inv, cnt;
bool vis[MAXN];

void Euler() {
	inv = (Mod * 3 + 1) / 4;
	f[1] = 1, mu[1] = 1;
	for (int i = 2; i <= 1e7; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1, f[i] = (1 - i + Mod) % Mod;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 1e7; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) {
				mu[i * pr[j]] = 0;
				f[i * pr[j]] = f[i];
				break;
			}
			mu[i * pr[j]] = -mu[i];
			f[i * pr[j]] = (f[i] * f[pr[j]] % Mod + Mod) % Mod;
		}
	}
	for (int i = 1; i <= 1e7; i++) sum[i] = (sum[i - 1] + f[i] * i % Mod) % Mod;
	
}

signed main() {

	Euler();

	read(t);
	while (t--) {
		read(n), read(m);
		int up = min(n, m);
		ans = 0;
		for (int i = 1, last = 1; i <= up; i = last + 1) {
			last = min(min(n / (n / i), m / (m / i)), up);
			ans = (ans + (sum[last] - sum[i - 1] + Mod) % Mod * ((n / i + 1) * (n / i) % Mod) % Mod * ((m / i + 1) * (m / i) % Mod) % Mod) % Mod;
		}
		printf("%lld\n", ans * inv % Mod);
	}

	return 0;
}
```

## $\mathbb{数表}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P3312)
### $\mathcal{Sol}$
$$
\begin{aligned}
	 &\sum_{i = 1}^n \sum_{j = 1}^m [f(\gcd(i, j)) \le a] f(\gcd(i, j)) \\
	=&\sum_{d, f(d) \le a} f(d) \sum_{i=1}^n \sum_{j=1}^{n} \gcd(i, j) = 1; \\
	=&\sum_{d, f(d) \le a} f(d) \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} [\gcd(i, j) = 1] \\
	=&\sum_{d, f(d) \le a} f(d) \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} \sum_{j=1}^{\lfloor \frac{m}{d} \rfloor} \sum_{g}^{\min(\lfloor \frac{n}{d} \rfloor, \lfloor \frac{m}{d} \rfloor)} \\
	=&\sum_{d, f(d) \le a} f(d) \sum_{g}^{\min(\lfloor \frac{n}{d} \rfloor, \lfloor \frac{m}{d} \rfloor)} \mu(g) \lfloor \frac{n}{dg} \rfloor \lfloor \frac{m}{dg} \rfloor \\
	=&\sum_{T = 1}^n \lfloor \frac{n}{T} \rfloor \lfloor \frac{m}{T} \rfloor \sum_{d \mid T} [f(d) \le a] f(d) \mu(\frac{T}{d})
\end{aligned}
$$   
然后可以把后面的一块抠出来。   
令 $h(T) = \sum_{d \mid T} [f(d) \le a] f(d) \mu(\frac{T}{d})$ 。然后随着 $a$ 的递增，可以动态维护，用 `bit` 即可。   
### $\mathcal{Code}$
```cpp
#include <map>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
#define int long long
using namespace std;
const int MAXN = 1e5 + 5;
const int Mod = (1ll << 31);

int q, cnt, lev, pr[MAXN], mu[MAXN], f[MAXN], g[MAXN], sum[MAXN], h[MAXN], bit[MAXN], ans[MAXN];
bool vis[MAXN];
map<int, int> re;

int lowbit(int x) { return x & -x; }
void update(int p, int val) { for (int i = p; i <= 1e5; i += lowbit(i)) bit[i] = (bit[i] + val) % Mod; }
int query(int p) { int res = 0; for (int i = p; i; i -= lowbit(i)) res += bit[i]; return res; }

void Euler() {
	g[1] = f[1] = 1; mu[1] = sum[1] = 1;
	for (int i = 2; i <= 1e5; i++) {
		if (!vis[i]) {
			pr[++cnt] = i, mu[i] = -1, f[i] = g[i] = i + 1;
		}
		for (int j = 1; j <= cnt && pr[j] * i <= 1e5; j++) {
			vis[pr[j] * i] = 1;
			if (i % pr[j] == 0) {
				mu[i * pr[j]] = 0;
				g[i * pr[j]] = g[i] * pr[j] + 1;
				f[i * pr[j]] = f[i] / g[i] * g[i * pr[j]];
				break;
			} else {
				mu[i * pr[j]] = -mu[i];
				f[i * pr[j]] = f[i] * f[pr[j]];
				g[i * pr[j]] = 1 + pr[j];
			}
		}
		sum[i] = sum[i - 1] + mu[i];
	}
}

struct Questions { int a, n, m, ind; };
Questions que[MAXN];

struct Num { int val, ind; } tmp[MAXN];
vector<int> add[MAXN];

void read(int& x) {
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

signed main() {

	Euler();
	for (int i = 1; i <= 1e5; i++) tmp[i].val = f[i], tmp[i].ind = i;
	sort(tmp + 1, tmp + MAXN - 4, [](const Num& x, const Num& y) { return x.val < y.val; });
	for (int i = 1; i <= 1e5; i++) {
		if (tmp[i].val != tmp[i - 1].val) re[++lev] = tmp[i].val;
		add[lev].push_back(tmp[i].ind);
		// printf("%d %d %d\n", tmp[i].val, tmp[i].ind, re[tmp[i].val]);
	}
	// printf("\n");

	read(q);
	for (int i = 1; i <= q; i++) read(que[i].n), read(que[i].m), read(que[i].a), que[i].ind = i;
	sort(que + 1, que + 1 + q, [](const Questions& x, const Questions& y) { return x.a < y.a; });

	for (int i = 1, last = 0; i <= q; i++) {

		if (que[i].a != que[i - 1].a) {
			int pos = 0;
			for (pos = last + 1; re[pos] <= que[i].a && pos <= lev; pos++) {
				for (int k = 0; k < add[pos].size(); k++) {
					int x = add[pos][k];
					// printf("%d %d %d-\n", x, f[x], re[x]);
					for (int t = 1; t * x <= 1e5; t++) {
						h[t * x] = (h[t * x] + f[x] * mu[t] + Mod) % Mod;
						update(t * x, (f[x] * mu[t] + Mod) % Mod);
					}
				}
			}
			last = pos - 1;
			// printf("\n");
		}

		int res = 0, up = min(que[i].n, que[i].m);
		for (int l = 1, r = 1; l <= up; l = r + 1) {
			r = min(que[i].n / (que[i].n / l), que[i].m / (que[i].m / l));
			r = min(r, up);
			res = (res + (query(r) - query(l - 1)) * (que[i].n / l) % Mod * (que[i].m / l) % Mod + Mod) % Mod;
		}
		ans[que[i].ind] = res;

	}
	for (int i = 1; i <= q; i++) {
		printf("%lld\n", ans[i]);
	}

	return 0;
}
```

## $\mathbb{DZY \ Loves \ Math}$
### $\mathcal{Link}$
[link](http://222.180.160.110:1024/contest/2387/problem/10)
### $\mathcal{Sol}$
定义 $f(n)$ 为 $n$ 所含质因子的最大幂指数。   
$$
\begin{aligned}
	&\sum_{i = 1}^{a} \sum_{j = 1}^b f(\gcd(i, j)) \\
	=&\sum_{T = 1}^n \lfloor \frac{n}{T} \rfloor \lfloor \frac{m}{T} \rfloor \sum_{d \mid T}  f(d) \mu(\frac{T}{d})
\end{aligned}
$$   
~~把 $f$ 预处理出来，用埃筛算贡献即可。~~   
$1e7$ 的数据规模跑的过去个鬼啊。。。   
这个玩意儿没有积性没办法搞啊。。。   

果断咕掉。

### $\mathcal{Code}$
```cpp
#include <gu>
using namespace gu;

int Gu() {
	return Gu();
}
```

## $\mathbb{约数个数和}$
### $\mathcal{Link}$
[link](https://www.luogu.com.cn/problem/P3327)
### $\mathcal{Sol}$
$$
\begin{aligned}
	 &\sum_{i=1}^n\sum_{j=1}^m d(ij) \\
	=&\sum_{i=1}^n\sum_{j=1}^m \frac{d(ij)}{\gcd(i, j)} \times (\gcd(i, j) + 1) \\
	=&
\end{aligned}
$$