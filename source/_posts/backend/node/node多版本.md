---
title: Nodejs 多版本管理
date: 2022-06-23 11:01:10
tags: nvm
categories: node
---

> 当运行其他项目需要不同版本的node环境时所需，切换高版本或低版本

### NVM

> nvm 就是用来做这件事的
>
> GitHub地址： https://github.com/coreybutler/nvm-windows/releases
>
> GitHub映射地址： https://hub.nuaa.cf/coreybutler/nvm-windows/releases

<!--more-->

1. 下载

	下载地址： https://github.com/coreybutler/nvm-windows/releases

	访问不了可以用上边的映射地址

2. 安装

	下载完成后，安装。点点点选择文件夹目录时注意。

	选择第二个文件目录时注意：默认路径包含了空格。这里需要修改成没有空格的路径名称。否则后边使用命令会报错

	> 如果之前有安装过node.会提示是否允许控制此版本。记住这里有坑。建议卸载之前node版本再重新安装nvm

	

3. 配置settings.txt, 环境变量

  我的配置

  ```
  # 路径不能出现中文、不能有空格
  # nvm 安装路径
  root: C:\Users\19507\AppData\Roaming\nvm
  # 创建快捷方式
  path: C:\Users\19507\AppData\Roaming\nvm\nodejs
  # 镜像地址 提高下载速度
  node_mirror: https://npm.taobao.org/mirrors/node/ 
  npm_mirror: https://npm.taobao.org/mirrors/npm/
  ```

  ```js
  # 安装时会自动设置 环境变量
  NVM HOME	C:/Users/19507/AppData/Roaming/nvm
  NVM SYMLINK	C:/Users/19507/AppData/Roaming/nvm/nodejs
  ```

  

4. 常用命令

	```
	nvm list 查看已经安装的版本
	nvm list installed 查看已经安装的版本
	nvm list available 查看网络可以安装的版本
	nvm version 查看当前的版本
	nvm install 安装最新版本nvm
	nvm use <version> ## 切换使用指定的版本node
	nvm ls 列出所有版本
	nvm current显示当前版本
	nvm alias <name> <version> ## 给不同的版本号添加别名
	nvm unalias <name> ## 删除已定义的别名
	nvm reinstall-packages <version> ## 在当前版本node环境下，重新全局安装指定版本号的npm包
	nvm on 打开nodejs控制
	nvm off 关闭nodejs控制
	nvm proxy 查看设置与代理
	nvm node_mirror [url] 设置或者查看setting.txt中的node_mirror，如果不设置的默认是 https://nodejs.org/dist/
	nvm npm_mirror [url] 设置或者查看setting.txt中的npm_mirror,如果不设置的话默认的是： https://github.com/npm/npm/archive/.
	nvm uninstall <version> 卸载制定的版本
	nvm use [version] [arch] 切换制定的node版本和位数
	nvm root [path] 设置和查看root路径
	```

	

5. 使用

	- 获取可以安装的node 版本

		使用cmd命令时，请使用 管理员权限，在使用切换版本命令时必须【nvm use 版本号】

		```
		nvm list available
		```

		这里的版本号不是完整版，下面地址可以 获取完整列表信息。

		![image-20220623103119537](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220623103119537.png)

		LTS 指最新稳定版本，CURRENT 指最新版本，一般安装 LTS

	- 指定版本安装

		```
		nvm install 16.15.1
		```

		安装完成后查看列表

		![image-20220623103553171](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220623103553171.png)

		

		\*为当前使用的node版本，未使用 nvm use 命名，则不会有\*

		

	- 切换版本

		出现bug了，这里卡了我几下，😠

		![image-20220623103841133](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220623103841133.png)

		> 这里列出出现这种报错的原因
		>
		> 1、安装路径是否包含中文
		>
		> 2、安装路径有空格
		>
		> 3、cmd使用是否是管理员方式打开
		>
		> ​	这里 不是用 windows PowerShell,。 去C：\Windows\System32目录，找到cmd.exe 右键以管理员方式打开
		>
		> 4、配置文件setting.txt中配置的路径和实际安装路径是否相符
		>
		> 以上几点是网上查的，我都没问题还是有报这个错。我猜测是我之前安装过node起了啥冲突😳，与是乎我卸载了原来node 重新安装nvm 🤨，结果成功了😁

		相似性

		

	- 使用

	  到了这里于是我兴冲冲的去打开那个需要 10.版本node 的项目，直接 npm run dev😊

	  结果： NPM 命令不存在 🤮。下载低版本可能会出现这个问题。两个高版本自带了npm

	  解决：网上一查需要手动下载npm,太复杂了，我直接到两个高版本把命令和module依赖考进来就好了，机智如我🤤

	  ![image-20220623105452303](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220623105452303.png)

	  ​				这是 v14.17.3 文件夹

	  node_modules 里面的 npm 文件夹也要拷贝过去

	  

	  如果使用的是 yarn 可以全局安装就是了

	  ```
    npm install -g yarn
	  ```
	
	  
	
	- 结语
	
		今天碰见的一个问题，需要用到 node版本切换，于是记录了下来。虽然没有解决那个问题😩
	
	

> 拓展：n 模块管理 node版本（后面发现的）。但 `n` 不适用于 Microsoft Windows，在 macOS、Linux（包括 Windows Subsystem for Linux）和其他各种类 unix 系统上受支持。
>
> ```js
> # 安装
> npm install -g n
> 
> # 变更 node 版本
> n 14.17.0
> ```
>
> 

### 拓展

### [fnm](https://github.com/Schniz/fnm)

> - **速度快**：`fnm` 采用 Rust 语言编写，相较于其他 Node.js 版本管理工具（如 `nvm`），它在速度上有显著提升，尤其是在切换 Node.js 版本时，能快速完成操作，节省开发时间。
> - **轻量级**：占用资源少，安装和使用过程简单，不会给系统带来过多负担。
> - **跨平台支持**：支持多种操作系统，包括 macOS、Linux 和 Windows，方便不同系统的开发者使用。
> - **简单易用**：提供简洁的命令行接口，易于上手，即使是新手也能快速掌握。
