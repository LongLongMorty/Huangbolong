---
title: 洛谷P2904 跨河River Crossing
date: 2019-02-22 13:52:01
categories: 动态规划
urlname: 33
tags:
---
<!--markdown-->
## 题目描述

Farmer John以及他的N(1 <= N <= 2,500)头奶牛打算过一条河，但他们所有的渡河工具，仅仅是一个木筏。  由于奶牛不会划船，在整个渡河过程中，FJ必须始终在木筏上。在这个基础上，木筏上的奶牛数目每增加1，FJ把木筏划到对岸就得花更多的时间。  当FJ一个人坐在木筏上，他把木筏划到对岸需要M(1 <= M <=  1000)分钟。当木筏搭载的奶牛数目从i-1增加到i时，FJ得多花M_i(1 <= M_i <=  1000)分钟才能把木筏划过河（也就是说，船上有1头奶牛时，FJ得花M+M_1分钟渡河；船上有2头奶牛时，时间就变成M+M_1+M_2分钟。后面的依此类推）。那么，FJ最少要花多少时间，才能把所有奶牛带到对岸呢？当然，这个时间得包括FJ一个人把木筏从对岸划回来接下一批的奶牛的时间。

## 输入输出格式

输入格式：

\* Line 1: Two space-separated integers: N and M

\* Lines 2..N+1: Line i+1 contains a single integer: M_i

输出格式：

\* Line 1: The minimum time it takes for Farmer John to get all of the cows across the river.

## 输入输出样例

输入样例#1：

```
5 10 
3 
4 
6 
100 
1 
```

输出样例#1：

```
50 
```

## 说明

There are five cows. Farmer John takes 10 minutes to cross the river  alone, 13 with one cow, 17 with two cows, 23 with three, 123 with four,  and 124 with all five.

Farmer John can first cross with three cows (23 minutes), then return  (10 minutes), and then cross with the last two (17 minutes). 23+10+17 =  50 minutes total.

## 我的WA思路

```
f[牛数][返回次数]=时间
初值为无限大
f[0][0]=m;
f[i][j]=min(f[i-1][j]+tz[i],f[i-1][j-1]+m+tz[1])
然后发现不应该+tz[i]，而当前是这条船的第几头牛又不知道
所以搞不定
```

## AC思路

```
f[i]初始化为m[i]的前缀和+m
即为把所有牛装到船上的解
然后遍历每头牛，检查从任意位置断开是否有更优解
```

```cpp
#include<cstdio>
#include<iostream>
using namespace std;

const int MAXN=2505;

int tz[MAXN]={0};
int f[MAXN];
int n,m;

int main(){
	cin>>n>>m;
	f[0]=m;
	for(int i=1;i<=n;++i){
		cin>>tz[i];
		f[i]=tz[i]+f[i-1];
	}
	
	for(int i=2;i<=n;++i){
		for(int j=1;j<=i;++j){
			f[i]=min(f[j]+f[i-j]+m,f[i]);
		}
	}
	
	cout<<f[n]<<endl;
	return 0;
}
```

