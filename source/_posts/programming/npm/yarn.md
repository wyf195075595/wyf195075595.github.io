---
title:  npm常用命令
date: 2022-06-17 18:23:10
tags: yarn
categories: npm
---
# yarn

```
//vecode中yarn命令报
yarn : 无法加载文件 C:\Users\Admin\AppData\Roaming\npm\yarn.ps1,因为在此系统因为在此系统上禁止运行脚本。

解决方法：
以管理员身份运行vscode;
执行：get-ExecutionPolicy，显示Restricted，表示状态是禁止的;
执行：set-ExecutionPolicy RemoteSigned;
这时再执行get-ExecutionPolicy，就显示RemoteSigned;

```

