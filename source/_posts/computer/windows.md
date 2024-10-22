---
title: windows 系统相关知识
date: 2024-10-08 09:23:10
tags: windows
categories: windows
---

收集在使用windows系统中遇到的问题及相关知识汇总

<!--more-->

### 创建软连接

使用软连接来解决软件使用时在C盘（系统盘）产生的大数据转移到其他盘符中。例如：vsCode 软件在安装插件时会默认缓存在 C:\Users\用户名\.vscode\extensions 文件夹中，从而导致C盘爆满，需要通过软连接形式来解决这个问题

```shell
cmd /c mklink /D 目标  源
```

```shell
cmd /c mklink /D "C:\Users\19507\.vscode\extensions" "D:\vsCode\.vscode\extensions"
```

我把C盘.vsCode 拷贝到 Vscode 安装的 D 盘目录中，在将 C盘中的 extensions 目录连接到D盘的 extensions ，cmd /c 是解决命令权限问题所需。

创建软连接时需将 目标链接存在的文件夹删除，**不然会报错 链接目标文件已存在 类似错误提示**

**删除软连接** 可以直接通过 直接删除 **目标** 实现，删除软连接不会影响 **源** 文件

