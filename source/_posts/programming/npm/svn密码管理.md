---
title: SVN 密码管理
date: 2022-06-23 15:23:10
tags: svn
categories: svn
---

> 小海龟svn 没有账号密码管理，每次切换用户baidu告诉我说，清除用户信息🙄，
>
> 这也太烦了把。话说上次输入密码还是前一年的事，密码账户都忘了。于是乎想了个感觉还不错的💡

<!--more-->

1. 百度大法查询资料

	Windows

	​	用户名密码储存位置：

	```
	C:\Users\sxy\Application Data\Roaming\Subversion\auth
	```

	- 删除账户

		-右键出TortoiseSVN菜单，Settings->Saved Data->Authentication data,Clear

		-删除 auth 文件夹

	

	Linux

	用户名密码储存位置：

	```
	~/.subversion/auth/svn.simple目录下
	```

	

2. 备份auth文件的方式实现账户储存

	当要切换 svn 账户时，将auth 文件夹拷贝一份备注好信息，然后清除账户信息。

	下次操作时即可输入新的账户。

	切换账户直接重命名回来就可了



### 找回账户密码

> 前提，本机已经登陆过账户密码。只是后边忘了。
>
> [参考文章](https://blog.csdn.net/linmengmeng_1314/article/details/88352543)

1. 找账户

	找到 svn 默认密码存储位置：C:\Users\19507\AppData\Roaming\Subversion\auth\svn.simple，此文件夹下存储所有svn记录，记事本打开可以看见用户名和加密密码

2. 查看密码

	[下载 Download.exe](http://www.leapbeyond.com/ric/TSvnPD/) ， 双击运行就可以展示出所有用户名和密码信息