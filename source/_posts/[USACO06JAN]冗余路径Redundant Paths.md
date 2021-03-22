---
title: 【USACO06JAN】冗余路径Redundant Paths
date: 2019-07-23 12:12:00
categories: 割点，割边
urlname: 46
tags:
---
<!--markdown-->
## 题目
为了从F(1≤F≤5000)个草场中的一个走到另一个，贝茜和她的同伴们有时不得不路过一些她们讨厌的可怕的树．奶牛们已经厌倦了被迫走某一条路，所以她们想建一些新路，使每一对草场之间都会至少有两条相互分离的路径，这样她们就有多一些选择．

每对草场之间已经有至少一条路径．给出所有R(F-1≤R≤10000)条双向路的描述，每条路连接了两个不同的草场，请计算最少的新建道路的数量, 路径由若干道路首尾相连而成．两条路径相互分离，是指两条路径没有一条重合的道路．但是，两条分离的路径上可以有一些相同的草场． 对于同一对草场之间，可能已经有两条不同的道路，你也可以在它们之间再建一条道路，作为另一条不同的道路．

**输入格式**：
Line 1: Two space-separated integers: F and R

Lines 2..R+1: Each line contains two space-separated integers which are the fields at the endpoints of some path.

**输出格式**：
Line 1: A single integer that is the number of new paths that must be built.

**输入输出样例**
输入样例#1：
7 7
1 2
2 3
3 4
2 5
4 5
5 6
5 7

输出样例#1： 
2

## 思路
这是一道一本通上的模板题。求一个有桥的联通图通过加边变成边双联通图，需要加的边数，
先删除所有的桥，剩下的是一些边双联通分量。把把每个边双联通分量缩成一个顶点，加回桥，最后剩下的会是一棵树。
设这棵树叶节点的数量为leaf，则需要加的边数为`leaf == 1 ? 0 : (leaf + 1) / 2`。这里的除号是整除。
这是经过测试的！！
![UTOOLS1563883675341.png](https://yanxuan.nosdn.127.net/f6606434df15e5145a457356d4e4e125.png)

![UTOOLS1563883793008.png](https://yanxuan.nosdn.127.net/ebaf19d4d1650efe0d10a3fb38addd60.png)

## 代码
```cpp
#include <cstdio>
#include <iostream>
#include <stack>
#include <cmath>
using std::stack;
using std::min;

const int MAXN = 40000 + 5;

namespace m1 {
    struct ed {
        int to, nex, frm;
    } e[MAXN];
    int head[MAXN];
    int newp = 1;
    int low[MAXN], dfn[MAXN], out[MAXN];
    int tim;
    int dcnt;//双联通分量编号
    int dcolor[MAXN];//双联通分量染色
    bool bridge[MAXN];
    void insert (int p1, int p2);
    void tarjan (int p, int ed);
    void dfs (int p);
}

int n, m;

int main (void) {
    {
        using namespace m1;
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= m; ++i) {
            int x, y;
            scanf("%d%d", &x, &y);
            insert(x, y);
            insert(y, x);
        }
        tarjan(1, 0);
        for (int i = 1; i <= n; ++i) {
            if (!dcolor[i]) {
                ++dcnt;
                dfs(i);
            }
        }
        for (int i = 1; i <= m; ++i) {
            if (dcolor[e[i * 2].frm] != dcolor[e[i * 2].to]) {
                ++out[dcolor[e[i * 2].frm]];
                ++out[dcolor[e[i * 2].to]];
            }
        }
        int leaf = 0;
        for (int i = 1; i <= dcnt; ++i) {
            if (out[i] == 1) {
                ++leaf;
            }
        }
        printf("%d\n", leaf ==  1 ? 0 : (leaf + 1) / 2);
    }
    return 0;
}

void m1::insert (int p1, int p2) {
    ++newp;
    e[newp].frm = p1;
    e[newp].to = p2;
    e[newp].nex = head[p1];
    head[p1] = newp;
}

void m1::tarjan (int p, int ed) {
    dfn[p] = low[p] = ++tim;
    for (int i = head[p]; i; i = e[i].nex) {
        int y = e[i].to;
        if (!dfn[y]) {
            tarjan(y, i);
            low[p] = min(low[p], low[y]);
            if (dfn[p] < low[y]) {
                bridge[i] = bridge[i ^ 1] = 1;
            }
        }
        else if (i != (ed ^ 1)) {
            low[p] = min(low[p], dfn[y]);
        }
    }
}

void m1::dfs (int p) {
    using namespace m1;
    dcolor[p] = dcnt;
    for (int i = head[p]; i; i = e[i].nex) {
        int y = e[i].to;
        if (dcolor[y] != 0 || bridge[i] == 1) {
            continue;
        }
        dfs(y);
    }
}
```