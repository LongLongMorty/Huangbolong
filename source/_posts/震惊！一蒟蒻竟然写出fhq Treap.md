---
title: 震惊！一蒟蒻竟然写出fhq Treap
date: 2019-05-12 15:31:00
categories: 平衡树
urlname: 40
tags:
---
<!--markdown-->
# ~~震惊，我竟然写出了fhq Treap~~

> 先%fhq大佬
>
> 然后%zxy大佬

## 节点定义

```cpp
struct node
{
    int w,siz,rdm;//权值，大小（包括自己），随机数
    int l,r;//左右儿子
} nd[MAXN];
```



## 特有操作

fhq Treap也被叫做无旋Treap，它通过分裂与合并来维持平衡和堆的性质。

### 按值分裂

将树分成x,y两颗树，其中x中的元素都小于等于w，y中的元素都大于w。

按地址传参，调用后x,y为新树的根。

~~开始写的传指针，但我太弱了一直没写对~~

```cpp
void splitV(int p,int w,int &x,int &y)//p为当前更新到的节点
{
    if(p==0)//边界
    {
        x=y=0;
    }
    else
    {
        if(nd[p].w<=w)//当前节点比w小，那它和它的左子树都属于x
        {
            x=p;
            splitV(nd[p].r,w,nd[p].r,y);//把x替换为nd[p].r，可以让在右子树中出现的比v小的节点接到x的右边
        }
        else
        {
            y=p;
            splitV(nd[p].l,w,x,nd[p].l);
        }
        update(p);//更新自身的size
    }
}
```



### 按节点数量分裂

众所周知，中序遍历一棵BST的结果是排好序的（~~然而我一开始并没有想到这一点~~）。所以可以将节点分为前k大的和剩下的两部分。

```cpp
void splitS(int p,int num,int &x,int &y)
    //把中序遍历的前k个数单独搞成一棵树，剩下的是另一棵树
    //中序遍历得到的是按大小顺序排出的节点。
    //因为会先访问最小的。（先左然后中后右）
{
    if(p==0)
    {
        x=y=0;
    }
    else
    {
        if(nd[nd[p].l].siz>=num)//左边比要的大了
        {
            y=p;
            splitS(nd[p].l,num,x,nd[p].l);//p的左儿子中有比p小，但比第num大大的。
        }
        else
        {
            x=p;
            splitS(nd[p].r,num-nd[nd[p].l].siz-1,nd[p].r,y);//左边小于num，找右边第(num-nd[nd[p].l].siz-1)大的
        }
        update(p);//更新自身大小
    }
}
```

### 合并

已知x中所有元素小于等于y中所有元素

合并这两棵树并返回新根。

这里体现了堆的性质

```cpp
int merge(int x,int y)
		{
			if(x==0||y==0)
			{
				return x+y;//如果其中一个有值，会返回那个值
			}
			else
			{
				int tmp=0;
				if(nd[x].rdm>=nd[y].rdm)//我猜这里可以改成其他的不等关系
				{
                    //x作为根
					tmp=x;
					nd[x].r=merge(nd[x].r,y);//合并右儿子与y，然后接到x右边
				}
				else
				{
					tmp=y;
					nd[y].l=merge(x,nd[y].l);
				}
				update(tmp);
				return tmp;
			}
		}
```

## 平衡树基操

### 插入

先新建一个节点

```cpp
int newNd(int x)
{
    ++newp;
    nd[newp].w=x;
    nd[newp].rdm=rand();
    nd[newp].siz=1;
    return newp;
}
```

按权值分裂，再把新的节点和分开的两棵树按大小合并

```cpp
void insert(int w)//
{
    int x=0,y=0;
    int p=newNd(w);
    splitV(root,w,x,y);
    x=merge(x,p);
    root=merge(x,y);
}
```

### 删除

按w-1,w分成三棵

中间那棵所有节点权值都为w

合并它的左右儿子，丢掉根，就删除了一个节点

记得合回去

```cpp
void remove(int w)
{
    int x=0,y=0,z=0;
    splitV(root,w,x,y);
    splitV(x,w-1,x,z);
    z=merge(nd[z].l,nd[z].r);
    root=merge(merge(x,z),y); //z 的左右子树合并即可删掉一个v
}
```

### 求排名

按值裂开，返回左儿子大小+1

```cpp
int rank(int w)
{
    int x=0,y=0,ret=0;
    splitV(root,w-1,x,y);
    ret=nd[x].siz+1;
    root=merge(x,y);
    return ret;
}
```

### 求第k大

按排名裂开。。。。。

```cpp
int kth(int k)
{
    int x=0,y=0,z=0;
    splitS(root,k,x,y);
    splitS(x,k-1,x,z);
    int ret=nd[z].w;
    root=merge(merge(x,z),y);
    return ret;
}
```

### 前驱

按`w-1`裂开，找小的那棵树里最大的

```cpp
int front(int w)
{
    int x=0,y=0;
    splitV(root,w-1,x,y);
    int p=x;
    while(nd[p].r)
    {
        p=nd[p].r;
    }
    root=merge(x,y);
    return nd[p].w;
}
```

### 后继

按`w`裂开，找大的那棵树里最小的

```cpp
int back(int w)
{
    int x=0,y=0;
    splitV(root,w,x,y);
    int p=y;
    while(nd[p].l)
    {
        p=nd[p].l;
    }
    root=merge(x,y);
    return nd[p].w;
}
```

## 例题：洛谷 P3369 普通平衡树

就是上面那六个操作

[代码](https://paste.ubuntu.com/p/7KZybPKzb4/)

## 区间翻转（延迟标记）

洛谷 P3835 文艺平衡树

翻的时候打lazytag

```cpp
void fan(int x,int y)
{
    int l,p,r;
    split(root,y,l,r);
    split(l,x-1,l,p);
    nd[p].laz^=1;
    root=merge(merge(l,p),r);
}
```

split,merge和输出时处理lazytag

```cpp
void split(int p,int siz,int &x,int &y)
{
    if(p==0)
    {
        x=y=0;
        return;
    }
    if(nd[p].laz)
    {
        push_down(p);
    }
    if(nd[nd[p].l].siz>=siz)
    {
        y=p;
        split(nd[p].l,siz,x,nd[p].l);
    }
    else
    {
        x=p;
        split(nd[p].r,siz-nd[nd[p].l].siz-1,nd[p].r,y);
    }
    update(p);
}

int merge(int x,int y)
{
    if(x==0||y==0)
    {
        return x+y;
    }

    if(nd[x].rdm>nd[y].rdm)
    {
        if(nd[x].laz)push_down(x);
        nd[x].r=merge(nd[x].r,y);
        update(x);
        return x;
    }
    else
    {
        if(nd[y].laz)push_down(y);
        nd[y].l=merge(x,nd[y].l);
        update(y);
        return y;
    }
}

void out(int p)
{
    if(nd[p].laz)
    {
        push_down(p);
    }

    if(nd[p].l)
    {
        out(nd[p].l);
    }
    printf("%d ",nd[p].w);
    if(nd[p].r)
    {
        out(nd[p].r);
    }
}
```

下传

```cpp
void push_down(int p)
{
    swap(nd[p].l,nd[p].r);
    nd[p].laz=0;
    nd[nd[p].l].laz^=1;
    nd[nd[p].r].laz^=1;
}
```

[代码](https://paste.ubuntu.com/p/g7CCQdvH9D/)

## 可持久化
非常简单，只要在split和merge时为修改添加节点然后用root数组记录版本就行了  
然而。。。 
![提交记录](https://pic.yupoo.com/zhufn/da7e961c/e1bd23c6.jpg)

我要疯了。。。  
以下是split和merge。  
```cpp
void split(int p,int w,int &x,int &y)
{ 
    if(p==0) 
    { 
        x=y=0; 
    } 
    else 
    { 
        int newn=++newp; 
        nd[newn]=nd[p]; 
        if(nd[p].w<=w) 
        { 
            x=newn; 
            split(nd[x].r,w,nd[x].r,y);
        } 
        else 
        { 
            y=newn;             
            split(nd[y].l,w,x,nd[y].l);
        } 
        update(newn); 
    } 
}  

int merge(int x,int y)
{ 
    if(x==0||y==0) 
    { return x+y; } 
    int newn=++newp; 
    if(nd[x].rdm>nd[y].rdm)
    { 
        nd[newn]=nd[x];
        nd[newn].r=merge(nd[newn].r,y); 
    } 
    else 
    { 
        nd[newn]=nd[y]; 
        nd[newn].l=merge(x,nd[newn].l);
    } 
    update(newn); 
    return newn; 
}
```
[完整代码](https://paste.ubuntu.com/p/rvgbHDTTTJ/)
