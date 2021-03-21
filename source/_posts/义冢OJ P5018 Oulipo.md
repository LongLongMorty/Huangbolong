---
title: 义冢OJ P5018 Oulipo
date: 2019-03-18 09:48:00
categories: KMP
urlname: 35
tags:
---
<!--markdown--># 义冢OJ P5018 Oulipo

## 描述

给出两个字符串W和T，求T中有几个W子串



## 輸入

第一行为数据组数.

每组数据有两行W和T，表示模式串和原始串.



## 輸出

对每组数据，每行一个数，表示匹配数.



## 輸入範例 1                 

```
3
BAPC
BAPC
AZA
AZAZAZA
VERDI
AVERDXIVYERDIAN
```

## 輸出範例 1

```
1
3
0
```

## 提示

1 ≤ |W| ≤ 10,000 (here |W| denotes the length of the string W). |W| ≤ |T| ≤ 1,000,000.

## 做

KMP板子题

只能写写板子来水水博客了。。。。

## Code

```cpp
#include<cstdio>
#include<cstring>
#include<iostream>
using namespace std;

const int MAXN=1e6+5;

int nxt[MAXN]={0};

void setNext(string s);
int kmp(string s1,string s2);

int n;

int main()
{
	cin>>n;
	string s1,s2;
	for(int i=1;i<=n;++i)
	{
		cin>>s1>>s2;
		setNext(s1);
		cout<<kmp(s2,s1)<<endl;
	}
	
	return 0;
}
void setNext(string s)
{
	memset(nxt,0,sizeof(nxt));
	nxt[0]=-1;
	int sl=s.size();
	for(int i=1;i<sl;++i)
	{
		int t=nxt[i-1];
		while(s[t+1]!=s[i]&&t>=0)
		{
			t=nxt[t];
		}
		if(s[t+1]==s[i])
		{
			nxt[i]=t+1;
		}
		else
		{
			nxt[i]=-1;
		}
	}
}
int kmp(string s1,string s2)
{
	int i=0,j=0;
	int sl1=s1.size(),sl2=s2.size();
	int cnt=0;
	while(i<sl1)
	{
		if(s1[i]==s2[j])
		{
			++i;
			++j;
			if(j==sl2)
			{
				++cnt;
				j=nxt[j-1]+1;
			}
		}
		else
		{
			if(j==0)++i;
			else j=nxt[j-1]+1;
		}
	}
	return cnt;
}
```

