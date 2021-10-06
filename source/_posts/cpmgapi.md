---
title: 拷贝漫画pc web端解密
date: 2021-10-6 20:04:00
categories: web
urlname: cpmgapi
tags:
---
~~（为什么要加个pc呢因为手机版网页好像是没加密的）~~  

首先我们打开一个漫画的目录页面并按下F12，可以看到请求目录的api返回了一坨16进制

![目录](https://pic.yupoo.com/zhufn/c617c1e7/ae6b2836.png)

进入发起该网络请求的js文件，可以看到eval巴拉巴拉

![eval](https://pic.yupoo.com/zhufn/923b97eb/a2a1464d.png)

eval里的函数返回了一个字符串，将它存进js文件中，使用whistle替换请求的文件，在firefox中找到http请求的回调函数，打断点（主要是找到了`'url': _0x1edb91 + _0x2f1f('0x19') + _0x124534 + '/chapters','success': function(){巴拉巴拉}`这里）。

![js](http://pic.yupoo.com/zhufn/5463b083/9a01f15c.png)

![调试](http://pic.yupoo.com/zhufn/8f1e54c0/7a07e735.png)

运行到155行我们发现解密后的json存在了`_0x336148`里。147,148两行是两个函数调用，`a.b.c.d`被写成了`a[b][c][d]`的形式。逐个对中括号里的表达式求值之后，得到这是函数是`xxx.enc.hex.parse`，搜索函数名，找到`CryptoJS`的相关内容得出此处在使用AES解密。密码是144行的`dio`，iv偏移值是146行的`_0x513f33`。这个时候我们直接拿json里的`result`去解密是不行的，比较发现`_0x2bee4f`比`result`少了16位，而这16位是偏移值。去掉后再解密即可得到目录。

![解密](https://pic.yupoo.com/zhufn/a8ad3ac0/2fbc8c13.png)

接下来是漫画阅读页面

F12没发现调用api，但图片是懒加载的。查看源码发现。。。

![阅读页面](https://pic.yupoo.com/zhufn/853a2599/7614c272.png)

此处解密方式同上。

