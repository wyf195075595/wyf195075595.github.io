---
title:  npm常用命令
date: 2022-06-17 18:23:10
tags: npm
categories: npm
---

## 1、常用命令

> npm -v      #显示版本，检查npm 是否正确安装。
>
> npm i express  #安装express模块
>
> npm i -g express  #i全局安装express模块
>
> npm list     #列出已安装模块
>
> npm show express   #显示模块详情
>
> npm update     #升级当前目录下的项目的所有模块
>
> npm update express   #升级当前目录下的项目的指定模块
>
> npm update -g express  #升级全局安装的express模块
>
> npm uninstall express  #删除指定的模块

<!--more-->

```js
设置镜像

  npm config set registry https://registry.npm.taobao.org
  
取消淘宝镜像
 
  npm config delete registry

清除 npx 缓存

	npx clear-npx-cache
```



### npm 全局安装 某 依赖包 但是 本地require 却不生效

在 node 环境 执行 require('xxx') ,报错   Cannot find module xxx

- 查看全局包路径

	```
	npm root -g
	```

- 在 node 环境中 查看依赖的路径列表

	```
	module.paths
	```

	> 如果列表i中不包括 全局路径则 require 就不会去全局路径寻找依赖

- 解决方法【添加环境依赖】

	NODE_PATH 设置为 全局包路径

- 重启 编辑器或黑窗口 生效

