---
title: 文件系统
tags: os
---

## inode

每个文件都有一个 `inode`，其中包含着该文件的元数据 `metadata`。
例如文件的所有者 `user id`，文件类型 `file type`等等。
具体可查看 `Linux` 手册，`man inode`。

当我们需要删除一个文件时，
只有在文件的链接计数 `link count` 为零，或者没有文件标识符 `fd` 引用时，
系统才会释放文件的 inode 和文件在磁盘中的空间。
参考 `xv6-book`。
