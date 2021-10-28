---
title: 贴个哈希表代码
date: 2021-10-28 23:19:00
categories: 哈希表
urlname: haxibiao
tags:
---
题目链接：  
<https://www.luogu.com.cn/problem/P4305>  

```c
#include <stdio.h>
#include <stdlib.h>
#define MAXN 50005//数据范围
#define MOD 400000//取模，不要太大，否则会超内存
int a[MAXN];//用来保存结果

struct node
{
    struct node* next;//指向下一项的指针
    int value;//这个节点保存的值
};

struct node* head[MOD];//哈希表，由一堆链表组成，存的是链表头指针

void insert(struct node* p, int v)//在节点p后面插入v
{
    if (p == NULL) return;//不能对NULL操作，保险
    struct node *t = malloc(sizeof(struct node));//为新的节点分配内存
    t -> value = v;//设置值
    //一个插入过程
    t -> next = p -> next;//这一项的下一项设置为前一项的下一项
    p -> next = t;//前一项的下一项设置为这一项
}

int find(struct node*p, int v)//从节点p开始向后查找，直到找到或到链表末尾
{
    while(p != NULL)//跳到NULL停止，最后一项的next默认为NULL
    {
        if (p -> value == v)//查到v了
        {
            return 1;//找到了就直接退出循环
        }
        p = p -> next;//往前跳
    }
    return 0;//没找到才有机会结束循环
}

int main() {
    int t;//数据组数
    scanf("%d", &t);
    while (t--)//只循环t次
    {
        for (int i = 0; i < MOD; ++i)
        {
            head[i] = NULL;
        }
        int n;
        scanf("%d", &n);
        int newp = 0;//记录a中最新元素的下标，由于个人习惯我们将1作为第一个元素的下标
        for (int i = 1; i <= n; ++i)
        {
            int x;
            scanf("%d", &x);//输入一个数
            int flag = 0;//0表示没有相同数据，1表示有
            int m = x % MOD;//取模后的结果，用于在head中索引
            if (m < 0)//负数取模得到负数，导致下标越
                m = -m;
            //查找时分三种情况
            if (head[m] == NULL)//head[m]指向的链表不存在
            {//手动初始化第一个元素
                head[m] = malloc(sizeof(struct node));//分配内存
                head[m] -> value = x;//设置值
                head[m] -> next = NULL;//下一项还没有
            } else
            {//链表存在
                if (find(head[m], x)/*查找x*/) flag = 1;//找到了
                else
                {//没找到
                    struct node* p = head[m];//得到链表头指针
                    while (p -> next != NULL) p = p -> next;//直达尾部
                    insert(p, x);//插入
                }
            }
            if (!flag) a[++newp] = x;//好的我们没找到x，可以往a里插新元素了
        }

        for (int i = 1; i <= newp; ++i)//输出。。
        {
            printf("%d ", a[i]);
        }
        putchar('\n');
    }
    return 0;
}
```