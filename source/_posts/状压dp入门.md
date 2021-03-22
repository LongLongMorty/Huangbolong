---
title: 状压dp入门
date: 2019-10-24 00:03:00
categories: 动态规划
urlname: 66
tags:
---
<!--markdown-->
# 状压dp入门

状态压缩嘛，就是把连续的一坨可以用01表示的状态，搞进个整数里，然后用位运算来进行检查、转移等操作。

## 例题

[\[SCOI2005\]互不侵犯](https://www.luogu.org/problem/P1896)

每行国王分布的情况可以用01表示，这样就可以把每一行的状态用一个整数表示。

先预处理出一行里面没有会打架的的所有情况，和该情况对应的国王数量

`f[i][j][k]`为第`i`行的状态为第`j`种时，一共用了`k`个国王的方案数

那么`f[i][j][k]=f[i - 1][l][k - kingCnt[l]]`

具体看代码。

这次用了`nodejs`来写，写完之后顿时感觉`c++`是最好的语言

### Code

```js
process.stdin.resume();
process.stdin.setEncoding('utf8');
process.stdin.on('readable', () => {
  let line  = process.stdin.read()
  if (line == null) {
    return
  }
  line = line.split(' ')
  let n = parseInt(line[0]), k = parseInt(line[1])
  let ans = 0
  let maxI = (1 << n) - 1 //数值最大的状态
  let goodI = new Array(513).fill(0), kingCnt = new Array(513).fill(0) //能用的状态和它对应的国王数量
  let newg = 0
  for (let i = 0; i <= maxI; ++i) {
      if (i & (i << 1)) continue //左移或右移一位都能看有没有相邻的两个1，但是右移最低位的1就没了
      ++newg;
      goodI[newg] = i
      for (let j = 0; j < n; ++j) {
          if (i & (1 << j)) {//看看那些位上有1
            ++kingCnt[newg]
          }
      }
  }

  //我淦，为什么初始化数组这么麻烦？
  let f = new Array(10);
  for (i = 0; i < 11; ++i) {
    f[i] = new Array(513)
    for (j = 0; j < 160; ++j) {
      f[i][j] = new Array(82).fill(0)
    }
  }

  //初始化，题解里面我看得懂的一个
  for (let i = 1; i <= newg; ++i) {
    if (kingCnt[i] <= k) {//如果这种状态要的国王数量小于k，那他就是一种解
      f[1][i][kingCnt[i]] = 1
    }
  }
  for (let i = 1; i <= n; ++i) {//第i行
    for (let j = 1; j <= newg; ++j) {//本行状态
        for (let l = 0; l <= k; ++l) {//本行为止总共用到的国王
            if (l >= kingCnt[j]) {
                for (let m = 1; m <= newg; ++m) {//上一行状态
                    if (!(goodI[m] & goodI[j]) && 
                    !(goodI[m] & (goodI[j] << 1)) &&
                    !(goodI[m] & (goodI[j] >> 1))) {//判断
                        f[i][j][l] = f[i - 1][m][l - kingCnt[j]] + f[i][j][l]//转移
                    }
                }
            }
        }
    }
  }
  for (let i = 1; i <= newg; ++i) {//可能在任意一行结尾
      ans = f[n][i][k] + ans
  }
  process.stdout.write(`${ans}\n`)
  process.exit(0)
});
```

