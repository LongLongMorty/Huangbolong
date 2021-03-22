---
title: hdu 2196 Computer
date: 2019-11-04 13:53:44
categories: 动态规划
urlname: 77
tags:
---
<!--markdown-->
# hdu 2196 Computer

树形dp，~~贼鸡儿难~~

## 题目

[传送门](http://acm.hdu.edu.cn/showproblem.php?pid=2196)

给你一棵有边权的树，求每个点到离它最远的点的距离

输入：

?	一个`n`

?	接下来`n`行每行两个整数`j`,`w`，其中第`i`行表示从`(i+1)`到`j`有一条权值为`w`的边

~~为什么我要把输入放在这里，因为我最开始看错了。。。~~

## 解法

首先把`1`（或者其他点）作为根。

一个节点的最远距离就可以从它的儿子里面或者父亲里面找

所以只要取他“儿子里的最远距离”和“他父亲的/不经过他的/儿子里的最远距离，加上他父亲到他的边权”中的最大值

两遍`dfs`，第一遍求每个节点儿子理的最远距离，和经过另外一个儿子的最远距离（不是第二远距离）。

第二遍判断一下能走哪边，然后取`max`

```cpp
if (path[p] == y) {//他爸的最远距离经过了他自己
    f[y][2] = max(f[p][2], f[p][1]) + e[i].w;//能走父亲除了自己外最长的儿子
}
else {
    f[y][2] = max(f[p][2], f[p][0]) + e[i].w;//能走父亲最长的儿子
}
```

## Code

```cpp
#include <cstdio>
#define max(a, b) ((a) > (b) ? (a) : (b))

const int MAXN = 1e4 + 5;

struct ed {
	int to, nex, w;
	ed (void) {
		to = nex = w = 0;
	}
} e[MAXN << 1];

int head[MAXN], f[MAXN][3], path[MAXN];
int newp, n;

void insert (int p1, int p2, int w) {
	++newp;
	e[newp].w = w;
	e[newp].to = p2;
	e[newp].nex = head[p1];
	head[p1] = newp;
}

void dfs1 (int p, int fa) {
	for (int i = head[p]; i; i = e[i].nex) {
		int y = e[i].to;
		if (y != fa) {
			dfs1(y, p);
			if (f[p][0] < f[y][0] + e[i].w) {
				f[p][1] = f[p][0];
				f[p][0] = f[y][0] + e[i].w;
				path[p] = y;//p最长的儿子经过了y
			}
			else if (f[p][1] < f[y][0] + e[i].w) {
				f[p][1] = f[y][0] + e[i].w;
			}
		}
	}
}

void dfs2 (int p, int fa) {
	for (int i = head[p]; i; i = e[i].nex) {
		int y = e[i].to;
		if (y != fa) {
		if (path[p] == y) {//他爸的最远距离经过了他自己
		    f[y][2] = max(f[p][2], f[p][1]) + e[i].w;//能走父亲除了自己外最长的儿子
			}
			else {
			    f[y][2] = max(f[p][2], f[p][0]) + e[i].w;//能走父亲最长的儿子
			}
		}
}

int main (void) {
	while (scanf("%d", &n) != EOF) {
		newp = 0;
		for (int i = 1; i <= (n << 1); ++i) {
			e[i] = ed();
		}
		for (int i = 1; i <= n; ++i) {
			head[i] = 0;
			path[i] = 0;
			f[i][0] = f[i][1] = f[i][2] = 0;
		}
		for (int i = 2; i <= n; ++i) {
			int p2, w;
			scanf("%d%d", &p2, &w);
			insert(i, p2, w);
			insert(p2, i, w);
		}
		dfs1(1, 0);
		dfs2(1, 0);
		for (int i = 1; i <= n; ++i) {
			printf("%d\n", max(f[i][0], f[i][2]));
		}
	}
	return 0;
}
```

