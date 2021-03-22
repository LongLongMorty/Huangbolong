---
title: CDQ分治总结
date: 2019-08-27 13:14:00
categories: 分治
urlname: 50
tags:
---
<!--markdown-->
经过了一周~~的划水~~，我终于搞懂了cdq分治。

总的来说，cdq分治处理偏序问题就是

- 先把左边和右边当成一个完整的问题处理
- 然后把左边对右边的影响合并到右边

## 例题

### 园丁的烦恼

[传送门](https://www.luogu.org/problem/P2163)

求静态区域内的点数，二维偏序模板题。

```cpp
#include<cstdio> 
#include<algorithm>

const int MAXN = 500000 * 5 + 5;

//x,y:横纵坐标
//type:操作类型
//add:求矩形区域面积用几个矩形加加减减，所以add表示一下正负
//id:询问的id，因为一个询问拆成了好几个
//ans:存当前询问对应的答案
struct node {
	int x, y, type, add, id, ans;
} a[MAXN], tmp[MAXN];

int n, m, newp;
int ans[MAXN];

void add (int x, int y, int type, int add, int id, int ans) {
	a[++newp] = (node){x, y, type, add, id, ans};
}

bool cmp1 (node x, node y) {
	if (x.x == y.x) {
		if (x.y == y.y) {//查到比自己y大的就停止
			return x.type < y.type;//修改在询问前
		}
		return x.y < y.y;
	}
	return x.x < y.x;
}

void cdq (int l, int r) {
	if (l == r) {
		return;
	}
	int mid = (l + r) >> 1;
	cdq(l, mid);
	cdq(mid + 1, r);
    //左半边处理好了，把左半边所有修改加到右半边的询问中去
	int newl = l, newr = mid + 1, pos = l, ans = 0;
    //对于这个区间的上层区间，左边的x肯定都小于右边，所以这里按y排序后塞回a
	while (newl <= mid && newr <= r) {//不能越界
		if (a[newl].y <= a[newr].y) {//确保newl的操作在newr的范围内
			if (a[newl].type == 1) {
				++ans;//是点，累加答案
			}
			tmp[pos++] = a[newl++];
		}
		else {
			if (a[newr].type == 2) {
				a[newr].ans += ans;//是询问，把前面统计的点加上
			}
			tmp[pos++] = a[newr++];
		}
	}
    //没处理完的别剩着
    while (newl <= mid) {
        tmp[pos++] = a[newl++];
    }
    while (newr <= r) {
        if (a[newr].type == 2) {
            a[newr].ans += ans;
        }
        tmp[pos++] = a[newr++];
    }
    //按y排好的结果装入a
	for (int i = l; i <= r; ++i) {
		a[i] = tmp[i];
	}
}

int main (void) {
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; ++i) {
		int x, y;
		scanf("%d%d", &x, &y);
		add(x, y, 1, 0, 0, 0);
	}
	for (int i = 1; i <= m; ++i) {
		int x1, y1, x2, y2;
		scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
		add(x2, y2, 2, 1, i, 0);
		add(x1 - 1, y2, 2, -1, i, 0);
		add(x2, y1 - 1, 2, -1, i, 0);
		add(x1 - 1, y1 - 1, 2, 1, i, 0);
	}
	
	std::sort(a + 1, a + 1 + newp, cmp1);
	cdq(1, newp);
	
	for (int i = 1; i <= newp; ++i) {
		if (a[i].type == 2) {
			ans[a[i].id] += a[i].add * a[i].ans;
		}
	}
	
	for (int i = 1; i <= m; ++i) {
		printf("%d\n", ans[i]);
	}
	return 0;
}
```

### 树状数组1

[传送门](https://www.luogu.org/problem/P3374)

把操作出现的时间看做是第一维即可。

对于后面的数修改不会影响到前面的前缀和。

```cpp
#include <cstdio> 

const int MAXN = 500000 + 5;

struct node {
	int x, y, id, type;
	friend bool operator <(node x, node y) {
		return x.x == y.x ? x.type < y.type :x.x < y.x;
	}
} a[MAXN * 3], tmp[MAXN * 3];

int n, m, newp;
int ans[MAXN * 2];

void cdq(int l, int r) {
	if (l == r) {
		return;
	}
	
	int mid = (l + r) >> 1;
	cdq(l, mid);
	cdq(mid + 1, r);
	
	int i = l, j = mid + 1, p = l, sum = 0;
	while (i <= mid && j <= r)	 {
		if (a[i] < a[j]) {
			if (a[i].type == 1) sum += a[i].y;
			tmp[p++] = a[i++];
		}
		else {
			if (a[j].type == 2) {
				ans[a[j].id] += sum;
			}
			tmp[p++] = a[j++];
		}
	}
	while (i <= mid) {
		tmp[p++] = a[i++];
	}
	while (j <= r) {
		if (a[j].type == 2) {
			ans[a[j].id] += sum;
		}
		tmp[p++] = a[j++];
	}
	for (int i = l; i <= r; ++i) {
		a[i] = tmp[i];
	}
}

int main (void) {
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; ++i) {
		scanf("%d", &a[++newp].y);
		a[newp].x = i;
		a[newp].type = 1;
	}
	int opt, x, y, z, anscnt = 0;
	for (int i = 1; i <= m; ++i)  {
		scanf("%d", &opt);
		if (opt == 1) {
			scanf("%d%d", &x, &y);
			a[++newp].x = x;
			a[newp].y = y;
			a[newp].type = 1;
		}
		else {
			++anscnt;
			scanf("%d%d", &x, &y);
			a[++newp].x = x - 1;
			a[newp].id = anscnt * 2 - 1;
			a[newp].type = 2;
			a[++newp].x = y;
			a[newp].id = anscnt * 2;
			a[newp].type = 2;
		}
	}
	cdq(1, newp);
	for (int i = 1; i <= anscnt; ++i) {
		printf("%d\n", ans[i * 2] - ans[i * 2 - 1]);
	}
	return 0;
}
```

### 陌上花开（三维偏序）

~~为什么我要把“陌上花开”写在前面呢？因为这样看起来比较帅~~

第一维排序，第二位像原来那样比大小，然后搞个值域树状数组来统计每个0~x里的第三维

**要去重**

```cpp
#include <cstdio>
#include <algorithm>

const int MAXN = 200000 + 5;
namespace sz {
	int n;
	int lowbit (int x){return x & (-x);}
	int c[MAXN * 2];
	void add (int x, int k) {
	    while (x <= n) {
	        c[x] += k;
	        x += lowbit(x);
	    }
	}
	int query (int x) {
	    int ans = 0;
	    while (x > 0) {
	        ans += c[x];
	        x -= lowbit(x);
	    }
		return ans;
	}
}


struct node {
	int a, b, c, id;
} a[MAXN], tmp[MAXN];

int n, k, newp;
int size[MAXN], ans[MAXN], num[MAXN];

bool cmp1 (node x, node y) {
	return x.a == y.a ? (x.b == y.b ? x.c < y .c : x.b < y.b) : x.a < y.a;
}

void cdq(int l, int r) {
	if (l == r) return;
	int mid = (l + r) >> 1;
	cdq(l, mid);
	cdq(mid + 1, r);
	int i = l, j = mid + 1, p = l;
	while (i <= mid && j <= r) {
		if (a[i].b <= a[j].b) {
			sz::add(a[i].c, size[a[i].id]);
			tmp[p++] = a[i++];
		}
		else {
			ans[a[j].id] += sz::query(a[j].c);
			tmp[p++] = a[j++];
		}
	}
	while (j <= r) {
		ans[a[j].id] += sz::query(a[j].c);
		tmp[p++] = a[j++];
	}
	for (int h = l; h < i; ++h) {
		sz::add(a[h].c, -size[a[h].id]);
	}
	while (i <= mid) {
		tmp[p++] = a[i++];
	}
	for(int i = l; i <= r; ++i) {
		a[i] = tmp[i];
	}
}

int main (void) {
	scanf("%d%d", &n, &k);
	sz::n = k;
	for (int i = 1; i <= n; ++i) {
		scanf("%d%d%d", &a[i].a, &a[i].b, &a[i].c);
	}
	std::sort(a + 1, a + 1 + n, cmp1);
	for (int i = 1; i <= n; ++i) {
		if (a[i].a != a[i - 1].a || a[i].b != a[i - 1].b || a[i].c != a[i - 1].c) {
			tmp[++newp] = a[i];
		}
		++size[newp];
	}
	for (int i =1; i <= newp; ++i) {
		a[i] = tmp[i];
		a[i].id = i;
	}
	cdq(1, newp);
	for (int i = 1; i <=newp; ++i) {
		num[ans[a[i].id] + size[a[i].id] - 1] += size[a[i].id];//除了ans里的数量，还要加上完全相同的数量 
	}
	for (int i = 0; i < n; ++i) {
		printf("%d\n", num[i]);
	}
	return 0;
}
```

### 摩基亚

[传送门](https://www.luogu.org/problem/P4390)

~~其实这是 Nokia 哒~~

本来是个二维，加上时间顺序就是三维了。

> 树状数组下标不能为0

```cpp
#include <cstdio>
#include <algorithm>

const int MAXN = 1700000 + 5;
namespace sz {
	int n;
	int lowbit (int x){return x & (-x);}
	int c[MAXN * 2];
	void add (int x, int k) {
	    while (x <= n) {
	        c[x] += k;
	        x += lowbit(x);
	    }
	}
	int query (int x) {
	    int ans = 0;
	    while (x > 0) {
	        ans += c[x];
	        x -= lowbit(x);
	    }
		return ans;
	}
	void clear (int x) {
	    while (x <= n) {
	        c[x] = 0;
	        x += lowbit(x);
	    }		
	}
}
struct node {
	int x, y, id, type, val;
	friend bool operator <(node x, node y) {
		return x.x == y.x ? x.y == y.y ? x.type < y.type : x.y < y.y : x.x < y.x;
	}
} a[MAXN], tmp[MAXN];

int n, newp, newq;
int ans[MAXN];

void cdq(int l, int r) {
	if (l == r) {
		return;
	}
	
	int mid = (l + r) >> 1;
	cdq(l, mid);
	cdq(mid + 1, r);
	int i = l, j = mid + 1, p = l;
	
	while (i <= mid && j <= r) {
		if (a[i].x <= a[j].x) {
			if(a[i].type == 1)sz::add(a[i].y, a[i].val);	
			tmp[p++] = a[i++];
		}
		else {
			if (a[j].type == 2)ans[a[j].id] += sz::query(a[j].y);
			tmp[p++] = a[j++];
		}
	}
	while (i <= mid) {
		tmp[p++] = a[i++];
	}
	while (j <= r) {
		if (a[j].type == 2) {
			ans[a[j].id] += sz::query(a[j].y);
		}
		tmp[p++] = a[j++];
	}
	for (int k = l; k <= mid; ++k) {
		if (a[k].type == 1) sz::clear(a[k].y);
	}
	for (int i = l; i <= r; ++i) {
		a[i] = tmp[i];
	}
}

void read (int &x) {
	x = 0;
	int k = 1;
	int t = getchar();
	while (t > '9' || t < '0') {
		if (t == '-') k = -1;
		t = getchar();
	}
	while (t >= '0' && t <= '9') {
		x *= 10;
		x += (t - '0');
		t = getchar();
	}
	x *= k;
}

int main (void) {
	read(n);read(n);
	++n;
	sz::n = n;
	int opt;
	read(opt);
	while (opt != 3) {
		if (opt == 1) {
			++newp;
			read(a[newp].x);read(a[newp].y);read(a[newp].val);
			++a[newp].x;++a[newp].y;
			a[newp].type = 1;
		}
		else {
			int x1, x2, y1, y2;
			read(x1);read(y1);read(x2);read(y2);
			++x1;++x2;++y1;++y2;
			a[++newp].x = x2;a[newp].y = y2;a[newp].type = 2;a[newp].id = ++newq;
			a[++newp].x = x1 -1;a[newp].y = y2;a[newp].type = 2;a[newp].id = ++newq;
			a[++newp].x = x2;a[newp].y = y1 - 1;a[newp].type = 2;a[newp].id = ++newq;
			a[++newp].x = x1 - 1;a[newp].y = y1 - 1;a[newp].type = 2;a[newp].id = ++newq;
		}
		read(opt);
	}
	//std::sort(a + 1, a + 1 + newp, cmp1);
	cdq(1, newp);
	for (int i = 1; i <= newq; i += 4) {
		printf("%d\n", ans[i] - ans[i + 1] - ans[i + 2] + ans[i + 3]);
	}
	return 0;
}
```

