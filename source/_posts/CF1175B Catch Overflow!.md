---
title: CF1175B Catch Overflow!
date: 2019-06-24 09:54:00
categories: 栈
urlname: 42
tags:
- CodeForces
---
<!--markdown--># 题目
给一个函数 f，f 在一开始会传入 x 的初始值。f 有一些指令，指令分三种：

    for n - 循环

    end - 每个循环的终止符。每个配对的 for n 和 end 之间的代码都要被运行 n 次。保证每个 for n 指令都能与一个 end 指令配对。

    add - 将 x 增加 1。

做完所有操作后 x 被当做返回值返回。

在中途的运算中，x 可能会大于 $2^{32}?1$，此时你要输出 OVERFLOW!!!

现在请输出 f(0) 的值。

# 思路
表面上这道题要用栈，但我决定用数组来做。
`f[i]`表示当前处于第`i`层循环，循环体内执行的次数，
`f[i] = f[i - 1] * n`
会溢出的点有循环层数过深时和加得过多时，但很深的循环里不一定有加法语句，所以这里立个flag来判。
感觉自己是不是搞得太复杂了。。。。。。

# Code
```cpp
#include<iostream>
#include<cstdio>
#include<vector>
#include<cstdlib>
using namespace std;

const long long over = ((long long)1 << 32) - 1;

int n, h;
long long x;
long long cnt = 0;
vector<long long> f;

int main (void) {
    f.push_back(1);
    scanf("%d", &n);
    char t[4];
    bool flag = 0;
    for (int i = 1; i <= n; ++i) {
        scanf("%s", t);
        if (t[0] == 'f') {
            ++cnt;
            f.resize(cnt + 1);
            scanf("%d", &h);
			if (over / h < f[cnt - 1]) {
				flag = 1;
			}
            f[cnt] = f[cnt - 1] * h;
        }
        else if (t[0] == 'a') {
            if (flag) {
                printf("OVERFLOW!!!\n");
                exit(0);
            }
            else {
                int tmp = x;
                x += f[cnt];
                if (x > over || x < tmp) {
                    printf("OVERFLOW!!!\n");
                    exit(0);
                }
            }
        }
        else {
        	flag = 0;
            --cnt;
        }
    }
    printf("%I64d\n", x);
    return 0;
}

```
