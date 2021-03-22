---
title: Tarjan模板小合集
date: 2019-07-24 08:56:39
categories: 割点，割边
urlname: 47
tags:
---
<!--markdown-->
## 强连通分量
染色为搜索树根节点编号：
```cpp
void tarjan (int p) {
    dfn[p] = low[p] = ++tim;
    v[p] = 1;
    s.push(p);
    for (int i = head[p]; i; i = e[i].nex) {
        int y = e[i].to;
        if (!dfn[y]) {
            tarjan(y);
            low[p] = min(low[p], low[y]);
        }
        else if(v[y]){
            low[p] = min(low[p], dfn[y]);
        }
    }
    if (dfn[p] == low[p]) {
        color[p] = p;
        v[p] = 0;
        while (s.top() != p) {
            color[s.top()] = p;
            v[s.top()] = 0;
            s.pop();
        }
        s.pop();
    }
}
```

## 缩点
没有板子。。。
情况很多。有时可以只统计出入度
有时可以建新图\(namespace namespace namespace\)

## 割点
```cpp
void tarjan (int p){
    dfn[p] = low[p] = ++tim;
    int kidsCnt = 0;
    for (int i = head[p]; i; i = e[i].nex) {
        int y = e[i].to;
        if (!dfn[y]) {
            ++kidsCnt;
            tarjan(y);
            low[p] = min(low[p], low[y]);
            if ((p == root && kidsCnt >= 2) || (p != root && dfn[p] <= low[y])) {
                cut[p] = 1;
                //++cutsCnt;会重复统计数量
            }
        }
        else {
            low[p] = min(low[p], dfn[y]);
        }
    }
}
```
调用：
```cpp
for (int i = 1; i <= n; ++i) {
    if (!dfn[i]) {
        root = i;
        tarjan(i);
    }
}
```
## 点双联通分量
没有模板题我也不知道对没对。。。
```cpp
void tarjan (int p, int fa) {
    dfn[p] = low[p] = ++tim;
    for (int i = head[p]; i; i = e[i].nex) {
        int y = e[i].to;
        if (!dfn[y]) {
            st[++top] = y;
            tarjan(y, p);
            low[p] = min(low[p], low[y]);
            if (low[y] >= dfn[u]) {
                ++cnt;
                while (st[top] != y) {
                    bcc[cnt].push_back(st[top--]);
                }
                bcc[cnt].push_back(stack[top--]);
                bcc[cnt].push_back(p);
            }
        }
        else if (y != fa) {
            low[p] = min(low[p], dfn[y]);
        }
    }
}
```


## 割边 （桥）
`newp`的初值要设为`1`
```cpp
void tarjan (int p, int ed) {//ed为来的边
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
```
## 边双联通分量
先来一遍割边的`tarjan`
然后有个`dfs`
```cpp
void dfs (int p) {
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
这个`dfs`要这样用
```cpp
for (int i = 1; i <= n; ++i) {
    if (!dcolor[i]) {
        ++dcnt;
        dfs(i);
    }
}
```

### 联通图变成边双联通图要加的边数
```cpp
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
```
