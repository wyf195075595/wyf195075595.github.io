---
title: bat 批量化处理命令
date: 2022-06-17 08:23:10
tags: 科普
categories: computer
---
### bat

```js
batchfile
批量化处理命令，集成在windows系统中。
dos下的批处理文件，可以极大的简化日常电脑处理中的一些重复性的任务，在我们电脑的使用过程中起着重要的作用。

-> power shall -> 可执行程序

命令查询
https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands

git仓库
	考虑了批处理脚本的收集、示例以及各种命令的一些细节。课程包括系统管理、文件管理、计算机维护、互联网工具和网络管理。
github.com/happy05dz/Batch-Script-Collection
```

<!--more-->

test.bat

```js
@echo off // 关闭命令输出，不然执行时都会先输入一遍命令，在执行结果

echo 我输出了

net user

::被注释内容

set name=blob // 设置变量 name，=两边尽量不加空格

echo %name% // 打印变量
```

