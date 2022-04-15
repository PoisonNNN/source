---
layout: post
title: "「Note」树状数组"
date: 2021-01-09
tags: [学习笔记, 数据结构, 树状数组]
---

<!-- more -->

## 概念

树状数组 ($Binary$ $Indexed$ $Tree$) 是一个区间查询和单点修改复杂度都为      $log(n)$ 的数据结构。主要用于维护序列的前缀和。

长。。。这样
![](https://img-blog.csdnimg.cn/20200726213856539.png)
其中 $c$ 数组为树状数组。
容易发现：
$c[1]=a[1]$
$c[2]=a[1]+a[2]$
$c[3]=a[3]$
$……$

那 $c[i]$ 到底是多少个 $a[j]$ 相加呢？答案是 $lowbit(i)$ 个。

### lowbit

在这之前，我们需要了解一下树状数组里的**关键操作**—— $lowbit$

$lowbit(x)$ : 将 $x$ 转化为二进制后，取出其最低位的 $1$ (结果即为只保留最低位的 $1$ 及其后面的 $0$，并将其转化为十进制后的值)。

#### 写法

关于 $lowbit$ 的写法有很多种，这里给出两种方法：

  1. $lowbit(x)=x-(x$ & $(x-1))$ 
     解释一下：设 $x$ = $(A1B)_2$ ( $A$ 为 $x$ 最低位的 $1$ 之前的部分，$B$ 为之后的部分，全部为 $0$ )，$x-1=(A0C)_2$ （ $B,C$ 长度一致，$C$ 全为 $1$），则 $x$ and $x-1=(11…11000…0)_2$ （ $len_A$ 个 $1$ ，$len_{B/C}+1$ 个 $0$），所以 $x$ 再减去这部分，就得到了 $(00…00100…00)$（ $1$ 前面 $len_A$ 个 $0$ ，$1$ 后面 $len_{B/C}+1$ 个 $0$），即取出了最低位的 $1$ 。
  2. $lowbit(x)=x$ & $-x$ 
      	这也是最常用的写法。
      	$-x=$~$x+1$，即先将 $x$ 在二进制下取反（ $0 \rightarrow 1$，$1 \rightarrow 0$），再加上 $1$ ，最低位的 $1$ 在先前的取反后变成 $0$ ，其右边的 $0$ ，全部变为 $1$ ，所以加的 $1$ ，让其右边的 $1$ 全部变回了 $0$ ，它本身加上了进位的 $1$ ，也变回了 $1$ ，$x$ 再与上这一部分，只有最低位的 $1$ 的位置，在 $x,-x$ 上都为 $1$ ，所以也能取出了最低位的 $1$ 。

#### 作用

  1. 对原数组（设为 $a$）进行更新（$update$）操作，同样在初始化，建树状数组的时候，每次输入 $a[i]$ ，可以通过更新达到初始化的效果。

```cpp
void Update(int x, int y) {
	for (int i = x; i <= n; i += lowbit(i)) BIT[i] += y;
}

for (int i = 1; i <= n; i++) {
	scanf("%d", &a[i]);
	Update(i, a[i]);
} 
```

  2. 求得前缀和

```cpp
int Sum(int x) {
	int ans = 0;
	for (int i = x; i; i -= lowbit(i)) ans += BIT[i];
	return ans;
}
```

## 具体操作

### 一维

#### 单点修改+区间查询

[Link](https://loj.ac/problem/130)

这里两个操作，可以通过更新和查询前缀和实现。

 - 给定 $i,x$，将 $a[i]$ 加上 $x$。
   即 $update(i,x)$。
 - 给定 $l,r$ ，求 $\sum_{i=l}^ra[i]$ 的值（换言之，求 $a[l]+a[l+1]+\dots+a[r]$ 的值）。
   意思是求 $[l,r]$ 的区间和，可以通过 $sum[r]-sum[l-1]$ 求得。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1e6 + 5;
int n, m;
int a[MAXN];
ll BIT[MAXN]; 

int lowbit(int x) { return x & (-x); }

void Update(int x, int y) {
	for (int i = x; i <= n; i += lowbit(i)) BIT[i] += y;
}

ll Sum(int x) {
	ll ans = 0;
	for (int i = x; i; i -= lowbit(i)) ans += BIT[i];
	return ans;
}

int main() {
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++) {
		scanf("%d", &a[i]);
		Update(i, a[i]);
	} 
	for (int i = 1; i <= m; i++) {
		int type, l, r;
		scanf("%d %d %d", &type, &l, &r);
		if (type == 1) {
			Update(l, r);
		}
		else {
			printf("%lld\n", Sum(r) - Sum(l - 1));
		}
	}
	return 0;
}

```

#### 区间修改+单点查询

[Link](https://loj.ac/problem/131)
这里需要用到差分，一个数组的差分数组和它的前缀和数组是互逆的。

```cpp
a[6] = {0, 1, 2, 3, 4, 5};
cf[6] = {0, 1, 1, 1, 1, 1}; //cf[i] = a[i] - a[i - 1] 差分
qzh[6] = {0, 1, 3, 6, 10, 15}; //qzh[i] = qzh[i - 1] + a[i] 前缀和

cf_qzh[6] = {0, 1, 2, 3, 4, 5}; //差分数组的前缀和数组
qzh_cf[6] = {0, 1, 2, 3, 4, 5}; //前缀和数组的差分数组
```

总的来说就是：差分数组的前缀和数组就是原数组，前缀和数组的差分数组也是原数组。
我们要用到的就是，差分数组的前缀和数组就是原数组这一特性。

而差分数组的区间修改是将 $cf[l]+k,cf[r+1]-k$ （设让 $[l,r]$ 里的每个数加上 $k$，$cf$ 为原数组的差分数组）

对于这道题，我们不再在原数组上建树状数组了，改在差分数组上建树状数组。

每次区间修改，就对 $cf[l]+k,cf[r+1]-k$ ，查询每个数，即求 $[1,x]$ 的前缀和。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1e6 + 5;
int n, m;
int a[MAXN];
ll BIT[MAXN];

int lowbit(int x) { return x & (-x); }

void Update(int x, int y) {
	for (int i = x; i <= n; i += lowbit(i)) BIT[i] += y;
}

ll Sum(int x) {
	ll ans = 0;
	for (int i = x; i; i -= lowbit(i)) ans += BIT[i];
	return ans;
}

int main() {
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++) {
		scanf("%d", &a[i]);
		Update(i, a[i] - a[i - 1]);
	}
	for (int i = 1; i <= m; i++) {
		int type, l, r, x;
		scanf("%d", &type);
		if (type == 1) {
			scanf("%d %d %d", &l, &r, &x);
			Update(l, x);
			Update(r + 1, -x); 
		}
		else {
			scanf("%d", &x);
			printf("%lld\n", Sum(x));
		} 
	}
	return 0;
}

```

#### 区间修改+区间查询

[Link](https://loj.ac/problem/132)

区间修改依然像上一问那么做，树状数组依然建在差分数组上。

区间查询则需要稍微推一下：(设求 $[1,p]$ 的前缀和，$a$ 为差分数组)

$\sum_{i=1}^p\sum_{j=1}^ia[j]$ 展开一下

$a[1]+(a[1]+a[2])+(a[1]+a[2]+a[3])+\dots+(a[1]+a[2]+a[3]+\dots+a[p])$

$p*a[1]+(p-1)*a[2]+(p-2)*a[3]+\dots+2*a[p-1]+a[p]$

因为树状数组最主要的作用就是求前缀和，所以要把式子落到前缀和上。

$p*(a[1]+a[2]+\dots+a[p])-0*a[1]-1*a[2]-\dots-(p-1)*a[p]$

$\sum_{i=1}^pa[i]*p-\sum_{j=1}^pa[j]*(j-1)$

所以只需要用两个树状数组，一个维护 $a[i]$ 的前缀和，一个维护 $a[i]*(i-1)$ 的前缀和。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1e6 + 5;
int n, m;
int a[MAXN];
ll BIT1[MAXN], BIT2[MAXN];

int lowbit(int x) {
	return x & (-x);
}

void Update(int x, int y) {
	for (int i = x; i <= n; i += lowbit(i))
		BIT1[i] += y, BIT2[i] += (ll)(x - 1) * y;
}

ll Sum(int x) {
	ll ans = 0;
	for (int i = x; i; i -= lowbit(i))
		ans += BIT1[i] * x - BIT2[i];
	return ans;
}

int main() {
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++) {
		scanf("%d", &a[i]);
		Update(i, a[i] - a[i - 1]);
	}
	for (int i = 1; i <= m; i++) {
		int type, l, r, x;
		scanf("%d %d %d", &type, &l, &r);
		if (type == 1) {
			scanf("%d", &x);
			Update(l, x);
			Update(r + 1, -x);
		}
		else {
			printf("%lld\n", Sum(r) - Sum(l - 1));
		}
	}
	return 0;
}
```

### 求逆序对

树状数组还可以求得逆序对，很巧妙。

首先你需要将输入进来的序列 $a$ [离散化](https://baike.baidu.com/item/%E7%A6%BB%E6%95%A3%E5%8C%96/10501557?fr=aladdin)（当数值范围较小可以免去离散化）

离散化是什么呢？当数据只与它们之间的相对大小有关，而与具体是多少无关时，可以进行离散化。

$e.g.$

```cpp
原数组： 	10000  1  8  99  500
离散化数组：   5  1  2  3  4
```

实现方法主要有两种，这里只介绍用 $STL$ 实现的

```cpp
#include <algorithm>
using namespace std;

int query(int x) {
	return lower_bound(a + 1, a + 1 + m, x) - a; //二分查找 log(m)
}

for (int i = 1; i <= n; i++) {
	scanf("%d", &a[i]);
	b[i] = a[i];
}
sort(a + 1, a + 1 + n);
m = unique(a + 1, a + 1 + n) - (a + 1); //去重
for (int i = 1; i <= n; i++) {
	b[i] = query(b[i]);
}
```

言归正传，在离散化之后，从 $1$~$n$ 循环，$i$ 可以表示进了树状数组的数的个数，每次将 $update(b[i],1)$ 表示又进了一个第 $b[i]$ 小的数，这时候的 $Sum(b[i])$ ，表示的就是进入了树状数组中的数中 $\leq$ 当前数的数的总数， 则 $i-Sum(b[i])$ 就表示 $>$ 当前数且在当前数之前的数，因为此时在树状数组中的数都是在当前数之前的，还有它自己，不 $\leq$ 它那就是 $>$ 它，所以 $i-Sum(b[i])$ 即为当前数的逆序对数，累加即可。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1005;
int n, m, ans;
int a[MAXN], b[MAXN], BIT[MAXN];

int lowbit(int x) {
	return x & (-x);
}

void Update(int x, int y) {
	for (int i = x; i <= n; i += lowbit(i)) BIT[i] += y;
}

int Sum(int x) {
	int ans = 0;
	for (int i = x; i; i -= lowbit(i)) ans += BIT[i];
	return ans;
}

int query(int x) {
	return lower_bound(a + 1, a + 1 + m, x) - a;
}

int main() {
	scanf("%d", &n);
	for (int i = 1; i <= n; i++) {
		scanf("%d", &a[i]);
		b[i] = a[i];
	}
	sort(a + 1, a + 1 + n);
	m = unique(a + 1, a + 1 + n) - (a + 1);
	for (int i = 1; i <= n; i++) {
		b[i] = query(b[i]);
	}
	for (int i = 1; i <= n; i++) {
		Update(b[i], 1);
		ans += i - Sum(b[i]);
	}
	printf("%d", ans);
	return 0;
}
```

### 二维

#### 单点修改+区间查询

[Link](https://loj.ac/problem/133)

这是二维树状数组里最基本的操作，其实和一维的大同小异，定义是介样的（$baidu$）

$BIT[x][y]=a[i][j](x-lowbit(x) + 1 \leq i \leq x,y-lowbit(y) + 1 \leq j \leq y)$

有了定义，实现就很简单了，跟一维没什么两样。

```cpp
void Update(int x, int y, int z) { //单点修改
    for (int i = x; i <= n; i += lowbit(i))
        for (int j = y; j <= m; j += lowbit(j)) BIT[i][j] += z;
}

int Sum(int x, int y) { //查询左上角(1,1)右下角(x,y)的矩阵的元素之和
    int ans = 0;
    for (int i = x; i; i -= lowbit(i))
        for (int j = y; j; j -= lowbit(j)) ans += BIT[i][j];
    return ans;
}
```

只不过在求和的时候，像二维前缀和一样，需要用到容斥，结合图来说：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729213805405.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0MyMDIyWXVSTA==,size_16,color_FFFFFF,t_70#pic_center)
我们要求的是红色矩阵中元素的和，设红色矩阵左上角坐标 $(a, b)$，右下角 $(c,d)$

所以红色矩阵可以看作，左上角为 $(1,1)$ 右下角为 $(c,d)$ 的矩阵减去左上角为 $(1,1)$ 右下角为 $(c,b)$ 的矩阵和左上角为 $(1,1)$ 右下角为 $(a,d)$ 的矩阵（即减去两个蓝色矩阵），这样就只剩下了红色矩阵，但由于绿色矩阵被减了两次，再加上即可。

$\sum_{i=a}^c \sum_{j=b}^da[i]=Sum(c, d) - Sum(a - 1, d) - Sum(c, b - 1) + Sum(a - 1, b - 1)$

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1 << 12;
int n, m, type;
ll BIT[MAXN + 5][MAXN + 5];

int lowbit(int x) { return x & (-x); }

void Update(int x, int y, int z) {
    for (int i = x; i <= n; i += lowbit(i))
        for (int j = y; j <= m; j += lowbit(j)) BIT[i][j] += z;
}

ll Sum(int x, int y) {
    ll ans = 0;
    for (int i = x; i; i -= lowbit(i))
        for (int j = y; j; j -= lowbit(j)) ans += BIT[i][j];
    return ans;
}

int main() {
    scanf("%d %d", &n, &m);
    while (~scanf("%d", &type)) {
        int a, b, c, d;
        if (type == 1) {
            scanf("%d %d %d", &a, &b, &c);
            Update(a, b, c);
        } else {
            scanf("%d %d %d %d", &a, &b, &c, &d);
            printf("%lld\n", Sum(c, d) - Sum(a - 1, d) - Sum(c, b - 1) + Sum(a - 1, b - 1));
        }
    }
    return 0;
}
```

#### 区间修改+单点查询

[Link](https://loj.ac/problem/134)

二维采用的方法依然是差分，但由于初始时是一个零矩阵（所有元素均为 $0$），所以只用进行相应的修改。举个例子

```cpp
0  0  0  0  0
0  0  0  0  0
0  0  0  0  0
0  0  0  0  0
0  0  0  0  0 
```

想要到达这样的效果：将左上角为 $(a,b)$ 右下角为 $(c,d)$ 的矩阵内的元素所有加上 $x$，即：

```cpp
0  0  0  0  0
0  x  x  x  0
0  x  x  x  0
0  x  x  x  0
0  0  0  0  0
```

我们可以将 $(a,b)$ 加上 $x$，$(c+1,d+1)$ 加上 $x$，$(a,d+1)$，$(c+1,b)$ 减去 $x$。

```cpp
0  0  0  0  0
0 +x  0  0 -x
0  0  0  0  0
0  0  0  0  0
0 -x  0  0 +x
```

由于在差分上建树状数组，所以单点查询还是求其前缀和。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1 << 12;
int n, m, type;
ll BIT[MAXN + 5][MAXN + 5];

int lowbit(int x) { return x & (-x); }

void Update(int x, int y, int z) {
    for (int i = x; i <= n; i += lowbit(i))
        for (int j = y; j <= m; j += lowbit(j)) {
            BIT[i][j] += z;
        }
}

ll Sum(int x, int y) {
    ll ans = 0;
    for (int i = x; i; i -= lowbit(i))
        for (int j = y; j; j -= lowbit(j)) {
            ans += BIT[i][j];
        }
    return ans;
}

int main() {
    scanf("%d %d", &n, &m);
    while (~scanf("%d", &type)) {
        if (type == 1) {
            int a, b, c, d, x;
            scanf("%d %d %d %d %d", &a, &b, &c, &d, &x);
            Update(a, b, x);
            Update(c + 1, d + 1, x);
            Update(a, d + 1, -x);
            Update(c + 1, b, -x);
        } else {
            int a, b;
            scanf("%d %d", &a, &b);
            printf("%lld\n", Sum(a, b));
        }
    }
    return 0;
}
```

#### 区间修改+区间查询

[Link](https://loj.ac/problem/135)

首先还是在二维查分数组上建树状数组（设求左上角 $(1,1)$ ，右下角 $(a,b)$ 的矩阵的元素和，$a$ 为差分数组）

则 $Sum=\sum_{i=1}^x\sum_{j=1}^y\sum_{x=1}^i\sum_{y=1}^ja[x][y]$

类比一维时的做法，我们看看每个 $a[x][y]$ 出现了多少次：

$a[1][1]$ 出现了 $x * y$ 次；
$a[1][2]$ 出现了 $x * (y-1)$ 次；
$a[2][1]$ 出现了 $(x-1) * y$ 次；
$a[2][2]$ 出现了 $(x - 1) * (y - 1)$ 次；
$\dots$

~~找找规律~~发现 $a[x][y]$ 出现 $(x-i+1) * (y-j+1)$ 次

即：

$=\sum_{i=1}^x\sum_{j=1}^ya[i][j] * (x-i+1) * (y-j+1)$

$=\sum_{i=1}^x\sum_{j=1}^ya[i][j] * [(x+1)*(y+1)-(x+1)*j-(y+1)*i+i*j]$

$=\sum_{i=1}^x\sum_{j=1}^ya[i][j]*(x+1)*(y+1)-\sum_{i=1}^x\sum_{j=1}^ya[i][j]*(x+1)*j-\sum_{i=1}^x\sum_{j=1}^ya[i][j]*(y+1)*i+\sum_{i=1}^x\sum_{j=1}^ya[i][j] * i*j$

由这个式子不难看出我们需要四个树状数组分别维护

$a[i][j]$、$a[i][j]*j$、$a[i][j]*i$、$a[i][j] * i*j$

的前缀和。。。~~加个快读更快~~

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 1 << 11;
int n, m, type;
ll BIT1[MAXN + 5][MAXN + 5], BIT2[MAXN + 5][MAXN + 5], BIT3[MAXN + 5][MAXN + 5], BIT4[MAXN + 5][MAXN + 5];

int lowbit(int x) { return x & (-x); }

void Update(int x, int y, int z) {
    for (int i = x; i <= n; i += lowbit(i))
        for (int j = y; j <= m; j += lowbit(j)) {
        	BIT1[i][j] += z;
        	BIT2[i][j] += (ll)x * z;
        	BIT3[i][j] += (ll)y * z;
        	BIT4[i][j] += (ll)x * y * z;
		}
}

ll Sum(int x, int y) {
    ll ans = 0;
    for (int i = x; i; i -= lowbit(i))
        for (int j = y; j; j -= lowbit(j)) {
			ans += BIT1[i][j] * (x + 1) * (y + 1) - BIT2[i][j] * (y + 1) - BIT3[i][j] * (x + 1) + BIT4[i][j];
		}
    return ans;
}

void read(int &x) {
	x = 0; 
	int f = 1;
	char s = getchar(); 
	while (s > '9' || s < '0') { 
		if (s == '-') f = -1; 
		s = getchar(); 
	}
	while (s >= '0' && s <= '9') { 
		x = (x << 3) + (x << 1) + (s - '0');
		s = getchar(); 
	}
	x *= f;
}

int main() {
    read(n); read(m);
    while (~scanf("%d", &type)) {
        if (type == 1) {
        	int a, b, c, d, x;
        	read(a); read(b); read(c); read(d); read(x);
            Update(a, b, x);
            Update(c + 1, d + 1, x);
            Update(a, d + 1, -x);
            Update(c + 1, b, -x);
        } 
		else {
			int a, b, c, d;
			read(a); read(b); read(c); read(d);
            printf("%lld\n", Sum(c, d) - Sum(a - 1, d) - Sum(c, b - 1) + Sum(a - 1, b - 1));
        }
    }
    return 0;
}
```

### 习题

[Problem](https://loj.ac/problem/10114) 数星星

读题时还想这道题挺复杂，直到我看到了输入格式。~~真香~~

把输入的点放在坐标系上，相当于每次从下往上，从左往右这样输入。

我们分别看看输入的点的横纵坐标， $y$ 坐标没什么价值，因为在它之前输入的一定 $\leq$ 当前 $y$ 坐标（~~显然的啊~~），$x$ 坐标则很有价值，因为它的等级是看星星的左下方（包含正左和正下）的星星总和，当前输入的星星的等级需要考虑的星星已经囊括在了已经输入的星星之中，它的等级即为已输入的星星中 $x$ 坐标为 $\leq$ 当前 $x$ 坐标的星星个数。

```cpp
5  o  o  o  o
4  o  *  !  o
3  o  *  *  o
2  *  *  o  *
1  2  3  4  5
```

举个例子，设当前输入到了 $!$ 这个位置的星星，它的等级即为左下角为 $(1,1)$，右上角为 $(4,4)$ 这个矩阵中的星星个数（不包括它自己），这些星星已在输入当前星星前被输入了。

所以我们可以在 $x$ 轴上建树状数组统计每个 $x$ 坐标上的星星个数，每次输入直接可以得到该星星的等级 $Sum(x)$ ，并更新当前 $x$ 坐标上的星星数量（加上一）。

**注意，这里输入的坐标有$0$，而树状数组下标不能为$0$，所以要加$1$处理！**

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ll long long
using namespace std;

const int MAXN = 32005;
const int MAXM = 15005;
int n;
int BIT[MAXN], lev[MAXM];

struct node {
    int x;
    int y;
} a[MAXN];

int lowbit(int x) { return x & (-x); }

void update(int x, int y) {
    for (; x <= 32001; x += lowbit(x)) BIT[x] += y; //因为加 1了，所以要遍历到32001
}

int query(int x) {
    int ans = 0;
    for (; x; x -= lowbit(x)) ans += BIT[x];
    return ans;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d %d", &a[i].x, &a[i].y);
        int sum = query(a[i].x + 1);
        update(a[i].x + 1, 1); //+1
        lev[sum]++;
    }
    for (int i = 0; i < n; i++) {
        printf("%d\n", lev[i]);
    }
    return 0;
}
```