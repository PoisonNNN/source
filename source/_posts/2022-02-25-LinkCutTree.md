---
layout: post
title: "「Note」LinkCutTree"
date: 2022-02-25
tags: [数据结构]
image: http://gastonsanchez.com/images/blog/mathjax_logo.png

---

我不理解?
<!-- more -->

## $\mathbb{Problem}$
简单的来说，就是在树剖的基础上加一个操作，支持树的**裂开**与**合并**。   
## $\mathbb{Editorial}$
如果做一遍重链剖分操作，操作完之后会发现，树上形成了很多以链为单位的连续区间，那么这时就能用 `SegmentTree` 做一些操作。   

本质上， `LinkCutTree` 维护的是动态的重链。   
搞清楚维护的内容或许就来了一大半了。   

然后说一下具体的实现。   
如果我们想操作某个区间，对于树剖来说，就是两个点反复上跳到 `lca` 并在中途维护题目要求的信息，会发现走的都是重链，如果我维护的是一个动态的树，并且期望达到树剖一样的效果，那我该怎么搞呢？   

把重链抽象成结构体 : `struct maxchain{ chain, fa }` 。   
这提示我们可以单独对于每条链维护一个结构体，其中包含本条链的信息和这条链的顶端的点的父节点（方便往上跳）。   

#### $\mathcal{Struct}$
```cpp
struct Splay { int fa, ch[2], dat, ind, val; bool rev; } s[MAXN];
```

如果把 `link` 和 `cut` 搬到链上表示，即是拆掉某条链的部分，接在另外一条链的某个位置。就是个序列操作，那么就可以用 `Splay` 作为内层数据结构实现这个操作。   

注：`Splay` 以动态的深度作为关键字建树。父节点的指针是单向的，仅是 `son -> fa`   

大致的思想如上，讲讲核心的操作。   

### $\mathcal{Opter}$
我自己造的操作。。。   
`opter(x, y)` 表示把 `x` 和 `y` 之间的路径抠出来，进行操作。   
如果 `x` 和 `y` 在一个 `splay` 里面，并且 `x` 为原树的根，这个就是 `Splay` 的基操，直接把 `y` 转到 `Splay` 的根，然后 `x -> y` 的路径就以 `y` 为根，取出 `y` 不就好了嘛。   
然而这个美好的想法肯定不是一直成立。   
那我们就强行让它成立。   
因为他是 **动态树** /xyx。

### $\mathcal{Makeroot}$
~~顾名思义，造一个根~~，额，`makeroot(x)` 实际上是把 `x` 变成原树的根。   
如果 `x` 和原树的根再一个 `Splay` 里面，那这个只需要把 `x` 旋到 `Splay` 的根就行了。   
你发现我又开始设想某种简单舒适的 `case` 了。   
然而我还是可以强行办到。   
只要我把 `x` 到原树根路径上的点塞到一个 `Splay` 里面就好了。   

### $\mathcal{Accecss}$
##### $\mathbb{Editorial}$
通俗的讲， `access(x)` 就是把 `x` 和当前的根（原树）之间的路径变成一条重链，或者是说，把 `x` 和当前的根（原树）之间的路径上的点塞到一个 `Splay` 里面。   
所以现在你沿着当前点往上跳就行了。。。

步骤如下：

- 首先把 `x` 旋到他所在的 `Splay` 的根。
- 找到这条 `Splay` 的顶端节点的父指针，直接将单向的父指针改成双向的，两条链就拼在了一起，即是 `s[x].ch[1] = i` 。直接连右儿子就行了，因为 `i` 的深度一定大于 `x` 。发现这里不需要去双向断边。
- 更新 `x` 。

##### $\mathcal{Code}$
```cpp
void access(int x) {
	for (int i = 0; x; i = x, x = s[x].fa) {
		splay(x);
		s[x].ch[1] = i;
		push_up(x);
	}
}
```
返回去， `makeroot` 和 `opter` 的代码就也能写了。

```cpp
void makeroot(int x) {
	access(x);
	splay(x);
	push_rev(x); // 反转两个儿子
}
void opter(int x, int y) {
	makeroot(x);
	access(y);
	splay(y);
}
```

### $\mathcal{Link}$
回到本职操作。   
链接两个点，实际上是把两棵树拼到一起，首先把其中一个点旋到根，这个时候处于根的点可以随便搞，直接把 `fa` 指针指到另一个节点即可。

```cpp
void link(int x, int y) {
	makeroot(x);
	if (findroot(y) == x) return; // 不对同连通块里的两点加边
	s[x].fa = y;
}
```

### $\mathcal{Cut}$
很(h&emacr;i)清晰，先把一个点旋到根，把另外一点的父节点置空，把当前节点的右儿子置空。

```cpp
void cut(int x, int y) {
	makeroot(x);
	if (findroot(y) != x || s[y].ch[0] || s[y].fa != x) return; // 判 <x,y> 是否存在
	s[y].fa = 0, s[x].ch[1] = 0;
	push_up(x);
}
```

---

### $\mathcal{Changes \ about \ Splay}$

突然发现 `Splay` 部分忘了写了。。。   

观察可知，当 `makeroot` 之后父子关系、深度会发生变化，以前的 `Splay` 并不适用了，这个时候应该把 `Splay` 以 `newroot` 为根反转过来。   

```cpp
void push_rev(int p) { s[p].rev ^= 1, swap(s[p].ch[0], s[p].ch[1]); }
void push_down(int p) {
	if (!s[p].rev) return;
	push_rev(s[p].ch[0]), push_rev(s[p].ch[1]);
	s[p].rev = 0;
}

bool check(int x) { return ((s[s[x].fa].ch[0] != x) && (s[s[x].fa].ch[1] != x)); }
void push_all(int x) {
	if (!check(x)) push_all(s[x].fa);
	push_down(x);
}
void splay(int x) {
	push_all(x);
	while (!check(x)) {
		int y = s[x].fa, z = s[y].fa;
		if (!check(y)) rotate((ident(x) == ident(y)) ? y : x);
		rotate(x);
	}
	push_up(x);
}

```

## $\mathcal{Code}$

以 [「NOI2014」魔法森林](https://www.luogu.com.cn/problem/P2387) 中维护联通块内最大边权及其编号的代码为例。

<details class="code">
<summary>LinkCutTree.cpp</summary>

```cpp
struct LinkCutTree {
	struct Splay { int fa, ch[2], dat, ind, val; bool rev; } s[MAXN];
	void push_up(int p) {
		s[p].dat = max(s[s[p].ch[0]].dat, max(s[p].val, s[s[p].ch[1]].dat));
		if (s[s[p].ch[0]].dat == s[p].dat) {
			s[p].ind = s[s[p].ch[0]].ind;
		} else if (s[s[p].ch[1]].dat == s[p].dat) {
			s[p].ind = s[s[p].ch[1]].ind;
		} else s[p].ind = p;
	}
	void push_rev(int p) { s[p].rev ^= 1, swap(s[p].ch[0], s[p].ch[1]); }
	void push_down(int p) {
		if (!s[p].rev) return;
		push_rev(s[p].ch[0]), push_rev(s[p].ch[1]);
		s[p].rev = 0;
	}
	bool check(int x) { return ((s[s[x].fa].ch[0] != x) && (s[s[x].fa].ch[1] != x)); }
	int ident(int x) { return x == s[s[x].fa].ch[1]; }
	void connect (int f, int p, int k) { s[f].ch[k] = p, s[p].fa = f; }
	void rotate (int x) {
		int y = s[x].fa, z = s[y].fa, k = ident (x), k2 = ident (y);
		if (!check(y)) s[z].ch[k2] = x;
		s[x].fa = z;
		connect(y, s[x].ch[k ^ 1], k);
		connect(x, y, k ^ 1);
		push_up (y), push_up (x);
	}
	void push_all(int x) {
		if (!check(x)) push_all(s[x].fa);
		push_down(x);
	}
	void splay(int x) {
		push_all(x);
		while (!check(x)) {
			int y = s[x].fa, z = s[y].fa;
			if (!check(y)) rotate((ident(x) == ident(y)) ? y : x);
			rotate(x);
		}
		push_up(x);
	}
	void access(int x) { for (int i = 0; x; i = x, x = s[x].fa) splay(x), s[x].ch[1] = i, push_up(x); }
	void makeroot(int x) { access(x), splay(x), push_rev(x); }
	int findroot(int x) {
		access(x), splay(x), push_down(x);
		while (s[x].ch[0]) x = s[x].ch[0], push_down(x);
		splay(x);
		return x;
	}
	void opter(int x, int y) {
		makeroot(x), access(y), splay(y);
	}
	void link(int x, int y) {
		makeroot(x);
		if (findroot(y) == x) return;
		s[x].fa = y;
	}
	void cut(int x, int y) {
		makeroot(x);
		if (findroot(y) != x || s[y].ch[0] || s[y].fa != x) return;
		s[y].fa = 0, s[x].ch[1] = 0;
		push_up(x);
	}
} lct;

```

</details>