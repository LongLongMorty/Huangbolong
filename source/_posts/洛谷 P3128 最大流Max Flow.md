---
title: 洛谷 P3128 最大流Max Flow
date: 2019-05-03 09:13:00
categories: 树上差分
urlname: 39
tags:
---
<!--markdown-->
# P3128 [USACO15DEC]最大流Max Flow

## 题面

### 题目描述

FJ给他的牛棚的N(2≤N≤50,000)个隔间之间安装了N-1根管道，隔间编号从1到N。所有隔间都被管道连通了。

FJ有K(1≤K≤100,000)条运输牛奶的路线，第i条路线从隔间si运输到隔间ti。一条运输路线会给它的两个端点处的隔间以及中间途径的所有隔间带来一个单位的运输压力，你需要计算压力最大的隔间的压力是多少。

### 输入输出格式

#### 输入格式：

The first line of the input contains NNN and KKK.

The next N?1N-1N?1 lines each contain two integers xxx and yyy (x≠yx \ne yx≠y) describing a pipe

between stalls xxx and yyy.

The next KKK lines each contain two integers sss and ttt describing the endpoint

stalls of a path through which milk is being pumped.

#### 输出格式：

An integer specifying the maximum amount of milk pumped through any stall in the barn.

### 输入输出样例

输入样例#1：

```cpp
5 10
3 4
1 5
4 2
5 4
5 4
5 4
3 5
4 3
4 3
1 3
3 5
5 4
1 5
3 4
```

输出样例#1：

```cpp
9
```

## Solve

[树上差分](https://buringstraw.win/index.php/archives/38/)模板题。

~~好难，做了一下午~~

~~（树咋存来着？）~~

一开始觉得前向星来存树不好找爸爸。折腾了半天还是用了前向星。

然后LCA也忘了。我还非常沙雕地用tarjan求出每两个点之间的LCA，还开小了数组。结果TLE+MLE。

![TLE+MLE](http://pic.yupoo.com/zhufn/2dd4100f/7f8e4f7f.png)

改用倍增求一直不过样例，后来才发现我一直用的存父亲的数组是放在tarjan LCA里求的。。。

接着只过了一两个点的原因竟然是：

```cpp
int getLca(int p1,int p2) {
    if(depth[p1]<depth[p2]){p1^=p2;p2^=p1;p1^=p2;}
    for(int i=H;i>=0;--i){
        if(depth[f[p1][i]]>=depth[p2])p1=f[p1][i];}
    if(p1==p2)return p1;
    for(int i=H;i>=0;--i)
        if(f[p1][i]!=f[p2][i]){
            p1=f[p1][i];
            p2=f[p2][i];}
    /*没错就是这里！！！！！*/
    return p1;
    //应该是return f[p1][0];
}
```

差错的时候写了dfs和bfs两种初始化方法，现在都能AC。

完整代码如下

```cpp
#include<cstdio> 
#include<iostream>
#include<utility>
#include<map>
#include<cstring>
#include<cmath>
using namespace std;

const int MAXN=2e5+5;

struct ed
{
	int to,nex,w;
} e[MAXN];

int head[MAXN];
int dad[MAXN];//树的 
bool v[MAXN];
int yaLi[MAXN];

int depth[MAXN];//深度
int f[MAXN][16];//for 倍增 
int H;

int maxYaLi=0;
int root=1,newp;

map<pair<int,int>,int> zuzong;

int n,k;

void vAdd(int x,int y);
void doUnion(int x,int y);
void doInit(void);
void lca(int p);
void getMax(int p);
void bfs(int root);
void dfs(int p,int fat);
int doFind(int x);
int readLca(int p1,int p2);
int getLca(int p1,int p2);

int main(void)
{
	scanf("%d%d",&n,&k);
	H=1.0*log(n)/log(2);
	for(int i=1;i<n;++i)
	{
		int x,y;
		scanf("%d%d",&x,&y);
		vAdd(x,y);
		vAdd(y,x);
	}
	bfs(1);
	//或dfs(1,0); 
	for(int i=1;i<=k;++i)
	{
		int x,y;
		scanf("%d%d",&x,&y);
		int lca=getLca(x,y);
		++yaLi[x];
		++yaLi[y];
		--yaLi[lca];
		--yaLi[f[lca][0]];
	}
	memset(v,0,sizeof(v));
	getMax(1);
	printf("%d\n",maxYaLi);
	return 0;
}

void vAdd(int x,int y)
{
	++newp;
	e[newp].to=y;
	e[newp].nex=head[x];
	head[x]=newp;
}


void getMax(int p)
{
	v[p]=1;
	for(int i=head[p];i;i=e[i].nex)
	{
		int y=e[i].to;
		if(v[y])continue;
		else
		{
			getMax(y);
			yaLi[p]+=yaLi[y];
		}
	}
	maxYaLi=max(maxYaLi,yaLi[p]);
}

int getLca(int p1,int p2) 
{
	if(depth[p1]<depth[p2])
	{
		p1^=p2;//交换值 
		p2^=p1;
		p1^=p2;
	}
	for(int i=H;i>=0;--i)
	{
		if(depth[f[p1][i]]>=depth[p2])
		{
			p1=f[p1][i];
		}
	}
	if(p1==p2)return p1;
	for(int i=H;i>=0;--i)
	{
		if(f[p1][i]!=f[p2][i])
		{
			p1=f[p1][i];
			p2=f[p2][i];
		}
	}
	return f[p1][0];
}

#include<queue>
void bfs(int root)
{
	queue<int> q;
	q.push(root);
	depth[root]=1;
	v[root]=1;
	while(!q.empty())
	{
		int u=q.front();
		q.pop(); 
		for(int i=head[u];i;i=e[i].nex)
		{
			int y=e[i].to;
			if(v[y])continue;
			else
			{
				v[y]=1;
				depth[y]=depth[u]+1;
				f[y][0]=u;
				for(int j=1;j<=H;++j)
				{
					f[y][j]=f[f[y][j-1]][j-1];
				}
				q.push(y);
			}
		}
	}
}

void dfs(int p,int fat)
{
	depth[p]=depth[fat]+1;
	f[p][0]=fat;
	for(int i=1;i<=H;++i)
	{
		f[p][i]=f[f[p][i-1]][i-1];
	}
	for(int i=head[p];i;i=e[i].nex)
	{
		int y=e[i].to;
		if(y==fat)continue;
		else dfs(y,p);
	}
}
```

还有一份41分代码（Tarjan）

```cpp
#include<cstdio> 
#include<iostream>
#include<utility>
#include<map>
#include<cstring>
using namespace std;

const int MAXN=5e4+5;

struct ed
{
    int to,nex,w;
} e[MAXN];

int head[MAXN];
int fa[MAXN];//并查集的 
int dad[MAXN];//树的 
bool v[MAXN];
int yaLi[MAXN];
int maxYaLi=0;
int root=1,newp;

map<pair<int,int>,int> zuzong;

int n,k;

void vAdd(int x,int y);
void doUnion(int x,int y);
void doInit(void);
int doFind(int x);
void lca(int p);
int readLca(int p1,int p2);
void getMax(int p);

int main(void)
{
    scanf("%d%d",&n,&k);
    doInit();
    for(int i=1;i<n;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        vAdd(x,y);
        vAdd(y,x);
    }
    lca(1);
    for(int i=1;i<=k;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        int lca=readLca(x,y);
        ++yaLi[x];
        ++yaLi[y];
        --yaLi[lca];
        --yaLi[dad[lca]];
    }
    memset(v,0,sizeof(v));
    getMax(1);
    printf("%d\n",maxYaLi);
    return 0;
}

void vAdd(int x,int y)
{
    ++newp;
    e[newp].to=y;
    e[newp].nex=head[x];
    head[x]=newp;
}

void doInit(void)
{
    for(int i=1;i<=n;++i)
    {
        fa[i]=i;
    }
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

void lca(int u)
{
    v[u]=1;
    for(int i=head[u];i;i=e[i].nex)
    {
        int y=e[i].to;
        if(v[y])continue;
        else
        {
            dad[y]=u;
            lca(y);
            doUnion(y,u);
        }
    }
    for(int i=1;i<=n;++i)
    {
        if(i==u)continue;
        else
        {
            if(v[i])
            {
                zuzong[make_pair(u,i)]=doFind(i);
            }
        }
    }
}

int readLca(int p1,int p2)
{
    pair<int,int> pa=make_pair(p1,p2),pb=make_pair(p2,p1);
    int z1=zuzong[pa];
    if(z1)return z1;
    else return zuzong[pb];
}

void getMax(int p)
{
    v[p]=1;
    for(int i=head[p];i;i=e[i].nex)
    {
        int y=e[i].to;
        if(v[y])continue;
        else
        {
            getMax(y);
            yaLi[p]+=yaLi[y];
        }
    }
    maxYaLi=max(maxYaLi,yaLi[p]);
}
```

