---
title: Dijkstra的船新写法
date: 2019-08-18 13:43:00
categories: 最短路
urlname: 49
tags:
---
<!--markdown-->Dijkstra嘛，就是每次从最短路未固定的点中找到已知最短路最短的点，然后将它固定，并更新这个点连接的其他点的最短路。最开始时，源点到源点的最短路为0。
所以，复习了一遍`Dijkstra`然后发现了几个函数

+ `make_heap (first, last, comp)` : 把一个数组搞成一个堆
+ `push_heap (first, last, comp)` : 让数组末尾的数浮到堆中正确的位置
+ `pop_heap (first, last, comp)` :把堆~~gui~~头丢到数组末尾

那么简单地封装一下就是
```cpp
void push (int x) {
	heap[++hsize] = x;
	std::push_heap(heap + 1, heap + 1 + hsize, cmp1);
}
void pop (void) {
	std::pop_heap(heap + 1,heap + 1 + hsize--, cmp1);
}
```
用数组实现的堆跑得~~比香港记者还~~快，~~比`vector`实现的优先队列不知道高到哪里去了。~~
![如图](https://yanxuan.nosdn.127.net/51fdeaf44252691970ea1c9398392ee9.png)

模板题代码如下
```cpp
#include <cstdio>
#include <algorithm>

const int MAXM = 500000 + 5, MAXN = 100000 + 5, INF = 2147483647;

int n, m, s;

struct ed {
	int to, nex, w;
} e[MAXM];

int head[MAXN], dis[MAXN], hsize;
bool v[MAXN];
int newp;

struct node {
	int id, v;
} heap[MAXN];

void insert (int p1, int p2, int w) {
	++newp;
	e[newp].to = p2;
	e[newp].w = w;
	e[newp].nex = head[p1];
	head[p1] = newp;
}

bool cmp1 (node x, node y) {
	return x.v > y.v;
}

void push (node x) {
	heap[++hsize] = x;
	std::push_heap(heap + 1, heap + 1 + hsize, cmp1);
}

void pop (void) {
	std::pop_heap(heap + 1,heap + 1 + hsize--, cmp1);
}

void dij (int s) {
	for (int i = 1; i <= n; ++i) {
		dis[i] = INF;
		v[i] = 0;
	}
	dis[s] = 0;
	hsize = 0;
	push((node){s, 0});
	
	while (hsize) {
		node u = heap[1];
		pop();
		if (v[u.id]) continue;//已固定的点 
		v[u.id] = 1;
		for (int i = head[u.id]; i; i = e[i].nex) {
			int y = e[i].to;
			if (dis[y] > u.v + e[i].w) {
				dis[y] = u.v + e[i].w;
				push((node){y, dis[y]});
			}
		}
	}
}

int main (void) {
	scanf("%d%d%d", &n, &m, &s);
	
	for (int i = 1; i <= n; ++i) {
		head[i] = 0;
		heap[i] = (node){0, 0};
	}
	
	for (int i = 1; i <= m; ++i) {
		e[i] = (ed){0, 0, 0};
	}
	
	{
		int p1, p2, w;
		for (int i = 1; i <= m; ++i) {
			scanf("%d%d%d", &p1, &p2, &w);
			insert(p1, p2, w);
		}
	}
	
	dij(s);
	
	for (int i = 1; i <= n; ++i) {
		printf("%d ", dis[i]);
	}
	putchar('\n');
	
	return 0;
}
```