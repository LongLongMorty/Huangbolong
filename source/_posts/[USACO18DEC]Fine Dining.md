---
title: 【USACO18DEC】Fine Dining
date: 2019-07-15 08:51:16
categories: 最短路
urlname: 44
tags:
---
<!--markdown-->
# [USACO18DEC]Fine Dining
咕咕咕。。。

什么？前几次考试？

。。。有空就补（放心你没空的）
## 题目

漫长的一天结束了，饥困交加的奶牛们准备返回牛棚。农场由N片牧场组成（2≤N≤50,000），方便起见编号为1…N。所有奶牛都要前往位于牧场N的牛棚。其他N?1片牧场中每片有一头奶牛。奶牛们可以通过M条无向的小路在牧场之间移动（1≤M≤100,000）。第i条小路连接牧场ai和bi，通过需要时间ti。每头奶牛都可以经过一些小路回到牛棚。

由于饥饿，奶牛们很乐于在他们回家的路上停留一段时间觅食。农场里有K（1<=K<=N）个有美味的干草捆，第i个干草捆的美味值为yi。每头奶牛都想要在她回牛棚的路上在某一个干草捆处停留，但是她这样做仅当经过这个干草捆使她回牛棚的时间增加不超过这个干草捆的美味值。注意一头奶牛仅仅“正式地”在一个干草捆处因进食而停留，即使她的路径上经过其他放有干草捆的牧场；她会简单地无视其他的干草捆。

### 输入输出格式

**输入格式：**
输入的第一行包含三个空格分隔的整数N，M和K。以下M行每行包含三个整数ai，bi和ti，表示牧场ai和bi之间有一条需要ti时间通过的小路（ai不等于bi，ti为不超过10^4的正整数）。
以下K行，每行以两个整数描述了一个干草捆：该干草捆所在牧场的编号，以及它的美味值（一个不超过10^9的正整数）。同一片牧场中可能会有多个干草捆。

**输出格式：**
输出包含N?1行。如果牧场i里的奶牛可以在回牛棚的路上前往某一个干草捆并且在此进食，则第i行为一个整数1，否则为一个整数0。

### 输入输出样例

**输入样例#1：**

```
4 5 1
1 4 10
2 1 20
4 2 3
2 3 5
4 3 2
2 7
```

**输出样例#1：**

```
1
1
1
```

### 说明
在这个例子中，牧场3里的奶牛可以停留进食，因为她回去的时间仅会增加6（从2增加到8），而这个增加量并没有超过干草捆的美味值7。牧场2里的奶牛显然可以吃掉牧场2里的干草，因为这并不会导致她的最佳路径发生变化。对于牧场1里的奶牛是一个有趣的情况，因为看起来她的最佳路径（10）可能会因为前去进食而有过大的增加量。然而，确实存在一条路径使得她可以前去干草捆处停留：先到牧场4，然后去牧场2（吃草），然后回到牧场4。

## 思路
大佬告诉我们，先再原图上跑一遍SPFA，得出每个点到$n$的距离，存进$d2$。
把美味度存进数组$a$
再从$n + 1$连出一条指向有草的点的单向边，边权为$d2[i] - a[i]$
然后以$n + 1$为源点再跑一遍SPFA，存进d1。
最后判断$d1[i]$是否小于等于$d2i[]$。
原理呢。。。
一头牛走到一个干草堆，之后肯定要走它到n的最短路，那么直接把$d2[i] - a[i]$连到$n + 1$上，跑最短路，就能比较$d[i]$与绕路走的路程之差是否小于a[i]

## 代码
```cpp
#include<cstdio>
#include<iostream>
#include<cstring>
using namespace std;
const int MAXN = 100005;
struct ed {
    int to, nex, w;
} e[MAXN * 3];;
int head[MAXN], a[MAXN], plc[MAXN], d1[MAXN], d2[MAXN];
int newp;
bool v[MAXN];
int n, m, k;

void insert(int p1, int p2, int w) {
    ++newp;
    e[newp].w = w;
    e[newp].to = p2;
    e[newp].nex = head[p1];
    head[p1] = newp;
}
#include<queue>
void spfa(int p) {
    queue<int> q;
    q.push(p);
    memset(v, 0, sizeof(v));
    memset(d1, 0x3f, sizeof(d1));
    //v[p] = 1;
    d1[p] = 0;
    while(!q.empty()) {
        int u = q.front();
        q.pop();
        v[u] = 0;
        for (int i = head[u]; i; i = e[i].nex) {
            int y = e[i].to;
            if (d1[y] > d1[u] + e[i].w) {
                d1[y] = d1[u] + e[i].w;
                if (!v[y]) {
                    v[y] = 1;
                    q.push(y);
                }
            }
        }
    }
}

int main (void) {
//    freopen("dining.in", "r", stdin);
//    freopen("dining.out", "w", stdout);

    scanf("%d%d%d", &n, &m, &k);

    for (int i = 1; i <= m; ++i) {
        int p1, p2, w;
        scanf("%d%d%d", &p1, &p2, &w);
        insert(p1, p2, w);
        insert(p2, p1, w);
    }

    spfa(n);
    for (int i = 1; i <= n; ++i) {
        d2[i] = d1[i];
    }

    for (int i = 1; i <= k; ++i) {
        scanf("%d", plc + i);
        scanf("%d", a + i);
        insert(n + 1, plc[i], d2[plc[i]] - a[i]);
    }
    spfa(n + 1);

    for (int i = 1; i < n; ++i) {
        if (d1[i] <= d2[i]) {
            printf("1\n");
        }
        else printf("0\n");
    }

    fclose(stdin);
    fclose(stdout);
    return 0;
}

```