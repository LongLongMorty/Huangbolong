---
title: 用acme.sh给ssl证书续期
date: 2020-02-03 08:19:00
categories: 服务器维护
urlname: 86
tags:
---
<!--markdown-->
关于`acme.sh`的详细说明可以看[这里][1]  
因为我的nginx的配置文件非常凌乱，所以我采用的是关掉nginx，让acme.sh自己作为服务器的方式来验证

首先需要安装socat  
`apt install socat`

然后
`acme.sh  --issue -d zhufn.fun --standalone`  

生成好的证书会自动放到nginx的文件夹里，好神奇  
![ ][2]
后来发现一个证书可以对应多个域名  
于是  
`acme.sh  --issue -d zhufn.fun -d rss.zhufn.fun -d tb.zhufn.fun -d i.zhufn.fun --standalone`

然后改改nginx里的路径，就可以共用一个证书啦
  [1]: https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E
  [2]: https://s2.ax1x.com/2020/02/03/1aCQkF.png