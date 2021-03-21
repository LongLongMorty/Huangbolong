---
title: P2916 [USACO08NOV]安慰奶牛Cheering up the Cow
date: 2019-03-24 09:30:00
categories: 最小生成树
urlname: 36
tags:
---
<!--markdown--># P2916 [USACO08NOV]安慰奶牛Cheering up the Cow

## 题目描述

约翰有N个牧场，编号依次为1到N。每个牧场里住着一头奶牛。连接这些牧场的有P条道路，每条道路都是双向的。第j条道路连接的是牧场Sj和Ej，通行需要Lj的时间。两牧场之间最多只有一条道路。约翰打算在保持各牧场连通的情况下去掉尽量多的道路。 

约翰知道，在道路被强拆后，奶牛会非常伤心，所以他计划拆除道路之后就去忽悠她们。约翰可以选择从任意一个牧场出发开始他维稳工作。当他走访完所有的奶牛之后，还要回到他的出发地。每次路过牧场i的时候，他必须花Ci的时间和奶牛交谈，即使之前已经做过工作了，也要留下来再谈一次。注意约翰在出发和回去的时候，都要和出发地的奶牛谈一次话。请你计算一下，约翰要拆除哪些道路，才能让忽悠奶牛的时间变得最少？

## 输入输出格式

输入格式：

\* Line 1: Two space-separated integers: N and P

\* Lines 2..N+1: Line i+1 contains a single integer: C_i

\* Lines N+2..N+P+1: Line N+j+1 contains three space-separated

integers: S_j, E_j, and L_j



输出格式：

\* Line 1: A single integer, the total time it takes to visit all the cows (including the two visits to the cow in your

sleeping-pasture)

## 输入输出样例

输入样例#1：

```
5 7 
10 
10 
20 
6 
30 
1 2 5 
2 3 5 
2 4 12 
3 4 17 
2 5 15 
3 5 6 
4 5 12 
```

输出样例#1：

```
176 
```

## 说明

```cpp
   +-(15)-+ 
  /        \ 
 /          \ 
1-(5)-2-(5)-3-(6)--5 
   \   /(17)  / 
(12)\ /      /(12) 
     4------+ 

Keep these paths: 
1-(5)-2-(5)-3      5 
       \          / 
    (12)\        /(12) 
        *4------+ 
```

Wake up in pasture 4 and visit pastures in the order 4, 5, 4, 2, 3,  2, 1, 2, 4 yielding a total time of 176 before going back to sleep.

## 思路

我发现把边权设为走路的时间加上两端谈话的时间，排序之后求最小生成树的选边结果（样例）跟样例答案一样。

所以打算按这样求出选哪些边之后dfs求总时间。

告诉[hqx](https://cnblogs.com/perisino)大佬，大佬经过严密的推论，发现只要把边权设为两倍路上时间加一倍的两端时间，最小生成树的和加上最少的一个点的谈话时间，就是最短总时间。

理由是来回每条边都会经过两次（观察样例/造数据），分别消耗的是两个端点的时间。出发点会多算一次。

HQX大佬TQL！

## CODE

```cpp
#include<cstdio>
#include<iostream>
#include<algorithm>
#define max(a,b) ((a)>(b)?(a):(b))
#define min(a,b) ((a)<(b)?(a):(b))
using namespace std;

const int MAXN=1000000+5,INF=0x3f3f3f3f;

int n,p;
int c[10005];

struct tu
{	
	struct edge
	{
		int p1,p2,w,a;
		friend bool operator <(edge x,edge y)
		{
			return x.a<y.a;
		}
	} b[MAXN];
	
	
	int newpe;
	int head[MAXN];
	int fa[MAXN];
	bool v[MAXN];
	
	void insert(int p1,int p2,int w)
	{
		b[++newpe].p1=p1;
		b[newpe].p2=p2;
		b[newpe].w=w;
		b[newpe].a=2*b[newpe].w+c[p1]+c[p2];
	}
	
	int zxscs(void)//最小生成树（原谅我不会打克鲁斯卡尔）
	{
		sort(b+1,b+1+newpe);
		for(int i=1;i<=n;++i)
		{
			fa[i]=i;
		}
		int cnt=0;
		int tot=0;
		for(int i=1;i<=newpe;++i)
		{
			if(doFind(b[i].p1)!=doFind(b[i].p2))
			{
				++cnt;
				tot+=b[i].a;
				doUnion(b[i].p1,b[i].p2);
			}
			if(cnt==n-1)
			{
				return tot;
			}
		}
		return 0;
	}
	
	void doUnion(int x,int y)
	{
		x=doFind(x);
		y=doFind(y);
		fa[x]=y;
	}
	
	int doFind(int x)
	{
		if(fa[x]==x)return x;
		else return fa[x]=doFind(fa[x]);
	}
	
} mp;
int main(void)
{
	scanf("%d%d",&n,&p);
	int minc=INF;
	for(int i=1;i<=n;++i)
	{
		scanf("%d",c+i);
		minc=min(c[i],minc);
	}
	
	for(int i=1;i<=p;++i)
	{
		int p1,p2,w;
		scanf("%d%d%d",&p1,&p2,&w);
		mp.insert(p1,p2,w);
	}
	int ans;
	ans=mp.zxscs();
	ans+=minc;
	printf("%d\n",ans);
	return 0;
}
```