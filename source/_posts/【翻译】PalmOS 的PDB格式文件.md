---
title: 【翻译】PalmOS 的PDB格式文件
date: 2020-02-12 02:50:00
categories: YouKnow
urlname: 87
tags:
---
<!--markdown-->研究MOBI要从研究PDB开始。
~~但我还是不知道MOBI跟PalmOS有什么关系~~
原文链接：<https://wiki.mobileread.com/wiki/PDB#Palm_Database_File_Code>
PalmOS将所有RAM存储保存为数据库格式。RAM中没有文件系统。当同步到PC上时，这些数据库将被保存在单独的文件中，这就是添加扩展名的地方。设备RAM中的所有文件都必须是Palm数据库格式，无论是程序还是数据。为外部存储创建的PDB文件是数据文件，其中可以包含任意内容。
表格显示有问题，看图吧  
![1bSXLD.png](https://s2.ax1x.com/2020/02/12/1bSXLD.png)

| 偏移地址 | 字节数               | 内容                 | 注释                                                         |
| -------- | -------------------- | -------------------- | ------------------------------------------------------------ |
| 0        | 32                   | 数据库名             | 数据库名，以0结尾。PalmOS上作为文件名。对于电子书一般为书名，长度够的话包含作者名 |
| 32       | 2                    | 属性                 | bit field.（不重要，不翻了）<br>0x0002 Read-Only<br>0x0004 Dirty AppInfoArea<br>0x0008 Backup this database (i.e. no conduit exists)<br>0x0010 (16 decimal) Okay to install newer over existing copy, if present on PalmPilot<br>0x0020 (32 decimal) Force the PalmPilot to reset after this database is installed<br>0x0040 (64 decimal) Don't allow copy of file to be beamed to other Pilot. |
| 34       | 2                    | 版本                 |                                                              |
| 36       | 4                    | 创建时间             | Unix时间戳                                                   |
| 40       | 4                    | 修改时间             |                                                              |
| 44       | 4                    | 最后备份时间         |                                                              |
| 48       | 4                    | 修改编号？           |                                                              |
| 52       | 4                    | 软件信息ID？         | 软件信息的偏移地址（如果有）或者null                         |
| 56       | 4                    | 分类ID？             | 分类信息的偏移地址（如果有）或者null                         |
| 60       | 4                    | 文件类型             | [见该表](https://wiki.mobileread.com/wiki/PDB#Palm_Database_File_Code) |
| 64       | 4                    | 创建者（软件）       | [见该表](https://wiki.mobileread.com/wiki/PDB#Palm_Database_File_Code) |
| 68       | 4                    | 唯一的ID种子？       | 内部使用                                                     |
| 72       | 4                    | 下一个记录表ID       | 在 PalmOS 的内存中使用                                       |
| 76       | 2                    | 记录数               | 该文件中有N条记录                                            |
| 78       | 8N                   | 列表，每条记录的信息 |                                                              |
|          | 以下是每条记录的信息 | 重复N次              |                                                              |
|          | 4                    | 记录数据的偏移地址   | 从文件头开始                                                 |
|          | 1                    | 记录属性             | bit field. The least significant four bits are used to represent the category values. These are the categories used to split the databases for viewing on the screen. A few of the 16 categories are pre-defined but the user can add their own. There is an undefined category for use if the user or programmer hasn't set this.<br>0x10 (16 decimal) Secret record bit.<br>0x20 (32 decimal) Record in use (busy bit).<br>0x40 (64 decimal) Dirty record bit.<br>0x80 (128, unsigned decimal) Delete record on next HotSync. |
|          | 3                    | 唯一ID               | 一般从0开始数                                                |
|          | 记录的信息结束       |                      |                                                              |
|          | 2?                   | 分隔                 | 一般是两个全0字节                                            |
|          | ?                    | 记录                 | 记录的数据。。。。。                                         |
|          |                      |                      |                                                              |

