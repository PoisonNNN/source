---
layout: post
title: "「Solution」CF149D Coloring Brackets"
date: 2021-01-11
tags: [题解, 区间DP, DP]
---

或许是一道区间 dp 好题吧

<!-- more -->


## Problem

[Link](https://www.luogu.com.cn/problem/CF149D)

题意很简单，就是给你一串合法的括号序列（如 $()(())$ ），给你三个染色的条件：
1. 一个括号可以染红色、蓝色或不染色；
2. 一对匹配的括号需要且只能将其中一个染色 ；
3. 相邻两个括号颜色不能相同（但可以都不染色）；

求符合条件的染色方案总数。

## Solution

我们定义括号序列为 $s$ , $dp[i][j][u][v]$ 表示 $s[i]$ 用颜色 $u$，$s[j]$ 用颜色 $v$，$s$ 在 $[i,j]$ 上的方案总数。其中 $u,v=0/1/2$ （$0$ 表示不染色，$1$ 表示染红色，$2$表示染蓝色）。$p[i]$ 表示与 $s[i]$ （$s[i]$为正括号）匹配的反括号的下标。

我们用 $l,r$ 表示一段区间的左端点和右端点
1. 当 $l+1=r$。

	因为 $s$ 是合法的序列，所以经过递归的操作后也是合法的括号序列，即为 $()$，而这种情况，根据条件一和二，只有 $(1,0) (2,0) (0,1) (0,2)$ 的情况。这也是递归的边界，已经无法再拆分下去了。

	```cpp
	if (l + 1 == r) {
		dp[l][r][1][0] = dp[l][r][2][0] = 1;
		dp[l][r][0][1] = dp[l][r][0][2] = 1;
		return ;
	}
	```
2. 当 $p[l]=r$，即 $s[i],s[j]$ 为一组匹配的括号。

	要求 $dp[l][r][u][v]$ ，肯定会由 $dp[l+1][r-1][u][v]$ 得到，因为 $[l,r]$ 的最优解是建立在 $[l+1,r-1]$ 之上的。
	所以可以用四重循环来枚举 $s[l],s[r],s[l+1],s[r-1]$ 染的颜色，设染的颜色分别为 $i,j,u,v$

	```cpp
	index : l  l + 1 …… r - 1  r
	color : i    u   ……   v    j
	```
	枚举出来的情况肯定有不符合条件的，所以可以根据条件进行筛选：

	```cpp
	1.(i + j) && !(i * j) //s[l],s[r]有且仅有一个不染色（为0）
	2.(i != u || !(i + u)) //s[l],s[l+1]要不都为0，要不都不为0且互不相等
	3.(j != v || !(j + v) //s[r],s[r-1]同理
	```
	在枚举之前，显然还要先拆分 $[l+1,r-1]$ ，也就是先求解子问题。

	```cpp
	dfs(l + 1, r - 1);
        for (int i = 0; i <= 2; i++) {
            for (int j = 0; j <= 2; j++) {
                for (int u = 0; u <= 2; u++) {
                    for (int v = 0; v <= 2; v++) {
                        if ((i + j) && !(i * j) && (i != u || !(i + u)) && (j != v || !(j + v))) {
                            dp[l][r][i][j] += dp[l + 1][r - 1][u][v];
                            dp[l][r][i][j] %= MOD;
                        }
                    }
                }
            }
        }
	```
3. $s[l],s[r]$ 匹配不上。

	所以 $s[l]$ 只能与 $s[p[l]]$，$s[p[l]+1]$ 与 $s[r]$ 进行匹配
	设$s[l],s[p[l]],s[p[l]+1],s[r]$ 染的颜色分别为 $i,j,u,v$
	```cpp
	index : l  p[l] …… p[l] + 1  r
	color : i   j   ……     u     v
	```
	这里只用去考虑 $i,u;v,j$ 之间是否满足，因为 $s[l],s[p[l]]$，$s[p[l]+1],s[r]$ 是否匹配会在递归求解子问题时计算，不成立的话是 $0$，并不影响结果。

	```cpp
	    dfs(l, p[l]);
        dfs(p[l] + 1, r);
        for (int i = 0; i <= 2; i++) {
            for (int j = 0; j <= 2; j++) {
                for (int u = 0; u <= 2; u++) {
                    for (int v = 0; v <= 2; v++) {
                        if ((j != u || !(j + u))) {
                            dp[l][r][i][v] += dp[l][p[l]][i][j] * dp[p[l] + 1][r][u][v] % MOD;
                            dp[l][r][i][v] %= MOD;
                        }
                    }
                }
            }
        }
	```

## Code
```cpp
#include <stack>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define LL long long
using namespace std;

const int MAXN = 705;
const int MOD = 1e9 + 7;
int n;
char s[MAXN];
int p[MAXN];
LL dp[MAXN][MAXN][3][3], ans;

stack<int> st;

void dfs(int l, int r) {
    if (l + 1 == r) {
        dp[l][r][1][0] = dp[l][r][2][0] = 1;
        dp[l][r][0][1] = dp[l][r][0][2] = 1;
        return;
    }
    if (p[l] == r) {
        dfs(l + 1, r - 1);
        for (int i = 0; i <= 2; i++) {
            for (int j = 0; j <= 2; j++) {
                for (int u = 0; u <= 2; u++) {
                    for (int v = 0; v <= 2; v++) {
                        if ((i + j) && !(i * j) && (i != u || !(i + u)) && (j != v || !(j + v))) {
                            dp[l][r][i][j] += dp[l + 1][r - 1][u][v];
                            dp[l][r][i][j] %= MOD;
                        }
                    }
                }
            }
        }
    } 
    else {
        dfs(l, p[l]);
        dfs(p[l] + 1, r);
        for (int i = 0; i <= 2; i++) {
            for (int j = 0; j <= 2; j++) {
                for (int u = 0; u <= 2; u++) {
                    for (int v = 0; v <= 2; v++) {
                        if ((j != u || !(j + u))) {
                            dp[l][r][i][v] += dp[l][p[l]][i][j] * dp[p[l] + 1][r][u][v] % MOD;
                            dp[l][r][i][v] %= MOD;
                        }
                    }
                }
            }
        }
    }
}

int main() {
    scanf("%s", s + 1);
    n = strlen(s + 1);
    for (int i = 1; i <= n; i++) {
        if (s[i] == '(')
            st.push(i);
        else
            p[st.top()] = i, st.pop(); //处理出对应下标
    }
    dfs(1, n);
    for (int i = 0; i <= 2; i++) {
        for (int j = 0; j <= 2; j++) {
            ans += dp[1][n][i][j];
            ans %= MOD;
        }
    }
    printf("%lld", ans);
    //用 long long 怕计算过程中爆掉 
    return 0;
}
```
