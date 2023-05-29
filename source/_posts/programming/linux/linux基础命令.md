---
title: linux命令
date: 2023-05-04 10:57:10
tags: linux
categories: linux
---





> linux 由 unix 演化而来
>
> 基于Linux 开源的内核，不同厂商开发出了不同名称的系统: 如 Redhat,CentOSE, Ubuntu,Suse,红旗等。

**虚拟机的网络连接形式**

- 桥连接

	Linux 可以和其他的通信，但是可能造成IP冲突

- NAT

	网络地址转换方式，linux 可以访问外网，不会造成IP冲突

- 主机模式

	你的Linux是一个独立的主机，不能访问外网

**Vmtools**

> 此工具可以让 centOS 与主机共享文件，剪切板

<!--more-->

#### Linux 目录结构

> 采用层级式树状目录结构。Linux中一切皆文件，usb,摄像头，网卡等硬件，都会映射成一个文件

```shell
-/	# 根目录
-bin	#指令
-dev	#设备管理,硬件的映射文件
-home	#创建用户会生成对应文件
-lib64	#静态库
-mnt	#挂载目录
-proc	#内核相关，虚拟目录，内存映射
-run
-srv	#存放服务启动之后所需数据
-tmp	#临时文件
-var	#变量
-boot	#启动Linux的核心文件
-etc	#配置文件
-lib	#动态连接共享库
-media	#如dvd
-opt	#要安装的软件
-root	#root相关
-sbin	#高级权限指令
-sys	#该目录安装了linux2.6内核中新出现的文件系统ysfs
-usr	#用户，用户很多应用程序和文件都放在这个目录
--local	#给主机额外安装软件所安装的目录
-selinux	#安全子系统，控制程序只能访问特定文件
```



#### Linux 编辑器 Vi、Vim

三种模式：

1. 正常模式

	只能查看

2. 插入编辑模式

	按下 i,I,o,O,a,A,r，R 任意字母进入

3. 命令行模式

	通过上面方式切换VI到VIM 模式， 命令 :wq, :q, :q! 

	查找命令：/关键字，回车查找, 按n查找下一个

	拷贝当前行：yy

	拷贝当前行向下5行：5yy

	粘贴：p, 拷贝后使用

	删除当前行：dd

	删除当前行向下5行：5dd

	光标回到顶部：gg

	光标回到底部：G

	撤销操作：u

	

#### 开/关/重启

```shell
# 立马关机
shutdowm -h now 
# 定时1分钟后关机
shoutdown -h 1
# 立即重启
shoutdown -r now

# 直接使用 ，等价于关机
half

# 重启
reboot

# 把内存数据写入到磁盘，重启关机前建议执行一次
sync
	
```



#### 用户的登录和注销

> 由于管理员账户权限较高，为避免操作失误。一般采用普通用户登录，在切换管理员权限。
>
> 注销命令 logout ，在图形界面级别无效。在级别3下有效

```shell
# 退出远程连接,注销登陆
logout

# 切换用户
su -用户名
```



### 用户管理

> 用户，用户组（可理解为角色）概念。用户被分配给某个组。组不同权限不同。
>
> /home/ ： 目录下有各个创建的用户对于家目录，当用户登陆时，会自动进入到自己的家目录

#### **创建用户**

> Linux用户需要向管理员申请，并被分配到某个组

```shell
# 语法
useradd [选项] 用户名

# 创建用户小明，默认会创建一个组 xm,并把小明放进去
# 创建用户后，会自动创建和用户同名的家目录，也可以通过 `useradd -d 指定目录名称 新用户名` ,为用户指定家目录
useradd xm

# 给新用户设置密码， 注意要在root权限下
passwd 用户名
```

#### 删除用户

```shell
# 删除用户，保留家目录
userdel 用户名

# 删除用户及家目录
userdel -r 用户名
```

#### 查询用户信息

```shell
# 语法， 没有信息返回 提示
id 用户名
```

#### 切换用户

```shell
# 切换用户， 高权限切换低权限不需要密码
su - 切换的用户名

# 返回到切换之前用户
exit
```

#### 查看当前用户/登录用户

```shell
whoami/ who am i
```

#### 用户组

> 每个用户都有一个组，通过组来控制用户权限管理

```shell
# 增加组
groupadd 组名

# 删除组
groupdel 组名

# 增加用户时直接加上组
useradd -g 用户组 用户名

# 将用户 zwj 改到 wudang
usermod -g wudang zwj

# 改变该用户初始的登录目录
usermod -d 目录名 用户名
```

#### 用户和组的相关文件

- /etc/passwd

	用户user配置文件，记录用户的各种信息

- /etc/shadow

	口令（密码）配置文件【信息会加密】，每行的意义

	登录名:加密口令:最后修改时间:最小使劲啊间隔:最大时间间隔:告警时间:不活动时间:失效时间:标志

- /etc/group

	组配置文件，每行含义

	组名:口令:组标识号:组内用户列表



### 组管理、权限管理

> 在Linux中每个用户必须属于一个组，不能独立于组外。
>
> Linux中的文件：
>
> - 所有者
> - 所在组
> - 其他组
> - 改变用户所在组

#### 查看文件所有者，目录所在组

> 当前用户创建文件时，这个文件所有者是当前用户，所在组为当前用户所在组

```shell
ls -ahl
```

#### 修改文件所有者

改变所有者不会修改文件所有组

```shell
chown 用户名 文件名

chowm root test.txt

# 改变用户所有者和所有组
chowm newowner:newgroup file
-R	如果是目录，则使其下所有子文件或目录递归生效
```



#### 文件目录所在组

> 当某个用户创建一个文件内后，这个文件的所在组就是该用户的所在组

##### 修改文件所在组

改变文件所在组不会影响文件所有者

```shell
chgrp 组名 文件名
# 将文件 test.txt 所有组改为 class1
chgrp class1 test.txt
-R	如果是目录，则使其下所有子文件或目录递归生效
```



#### 权限基本介绍

> 文件的类型
>
> **对于文件来说**：
>
> r：读 cat
>
> w：写 vim echo
>
> x：执行 运行命名或者脚本
>
> **对于目录来说**：
>
> r：读（看到目录里面有什么） ls
>
> w：在目录里面新建文件，删除，移动 touch mkdir rm mv cp
>
> x：目录是不能直接运行的，对目录赋予 x 权限，代表用户可以进入目录，也就是说，赋予 x 权限的用户或群组可以使用 cd 命令。



```shell
ls -l
drwxr-xr-x.  2 jysp class1    6 6月  24 2020 音乐


# 第一个字母判断文件的类型
-:普通文件
d:目录
l: 软链接 
c: 字符设备[键盘，鼠标]
b:块文件，硬盘

# rwx,2-4字母为一组：表示文件所有者拥有的权限

# r-x,5-7字母为一组：文件所在组的用户的权限

# r-x，8-10字母为一组：表示文件其他组的用户的权限

# 2， 这个数字。如果是文件-表示硬链接数量，如果是目录-表示子目录数量

# jysp, 用户名

# class1，用户所在组名

# 6，表示文件大小，如果是目录则是4096

# 6月  24 2020，问件最后修改时间
```



##### 修改权限

> 通过chmod命令，可以修改文件或者目录的权限

**方式1：+，-，=**

​	u：所有者

​	g：所有组

​	o：其他人

​	a：所有人（u,g,o的总和）

```shell
# 全部重新复制
chmod u=rwx,g=rx,o=x
# 基础上加
chmod a+w
# 基础上减
chmod a-x
```

**方式2：**

```shell
例如 将文件 /home/abc.txt 赋值权限：rwxr-xr-x
rwx = 4 + 2 + 1 = 7
r-x = 4 + 1 = 5
r-x = 4 + 1 = 5

chmod 755 /home/abc.txt
```



### 实用指令

> 一定情况下可以找回丢失root密码：
>
> 重启进入按 enter键（回车）（6及一下 enter, 7版本按e键）
>
> 进入单用户模式以root用户（root不需要密码
>
> 改密码

系统运行级别 配置文件： /etc/inittab

| 级别 | 环境                     |
| ---- | ------------------------ |
| 0    | 关机                     |
| 1    | 单用户（找回丢失root密码 |
| 2    | 多用户无网络服务         |
| 3    | 多用户有网络服务         |
| 4    | 保留                     |
| 5    | 图形界面                 |
| 6    | 重启                     |

切换运行级别

```shell
init [0/1/2/3/4/5/6]
```



### 指令帮助

> 都是英文，看不明白就百度

```shell
man 命令

help 命令
```



### 文件目录类

#### pwd

> 显示当前工作目录绝对路径

#### ls

```shell
# 基本语法：
ls [选项] [目录或是文件]

# 常用选项
-a: 显示所有文件及目录，包括隐藏
-l: 以列表方式展示
```

#### cd

```shell
# 回到自己的家目录
cd ~ / cd :

# 返回上一级
cd ..
```

#### mkdir

```shell
# mkdir 指令用于创建目录

mkdir [选项] 要创建的目录
-p: 创建多级目录

mkdir -p /home/www/html
```

#### rmdir

```shell
# 删除空目录， 非空目录无法删除，需要用到 rm -rf 要删除的目录
rmdir [选项] 要删除的空白目录
```

#### touch

```shell
# 创建空文件
touch 文件名称
```

#### cp

```shell
# 拷贝文件到指定目录
cp [选项] source dest

# 常用选项
-r: 递归复制整个文件夹

# 细节： 强制覆盖不提示
\cp

---------------
cp a.text bbb/
```



#### rm

```shell
# 移出文件或目录
rm [选项] 要删除的文件或目录

常用指令
-r: 递归整个文件夹
-f: 强制删除不提示

# 删库跑路
rm -rf *
```



#### mv

```shell
# 移动文件或重命名

mv oldFilaName newFileName
mv /temp/moveFile /targetFloder
```



#### cat

```shell
# 查看文件内容，
cat [选项] 要查看的文件

常用命令
-n: 显示行号

# 方便查看都会带上管道符| more

```

#### more 

```shell
# more指令是一个基于VI编辑器的文本过滤器，它以全屏的方式按页显示文本文件内容。more指令中内置了若干快捷键
more 吞噬星空.txt
```

| 操作   | 功能说明                 |
| ------ | ------------------------ |
| 空格   | 下一页                   |
| enter  | 下一行                   |
| q      | 立刻离开，退出预览       |
| ctrl+F | 滚下一屏                 |
| ctrl+B | 返回上一屏               |
| =      | 输出当前行号             |
| :f     | 输出文件名和当前行的行号 |

#### less

```shell
# less 指令用来显示分屏查看文件内容，与more类似，按需加载显示内容，效率高
less 吞噬星空.txt
```

| 操作     | 功能说明                           |
| -------- | ---------------------------------- |
| 空格     | 向下翻一页                         |
| pagedown | 下一页                             |
| pageup   | 上一页                             |
| /字串    | 向下搜索【字串】，n：下找，N：上找 |
| ?字串    | 向上搜索【字串】，n：下找，N：上找 |
| q        | 离开查看                           |

#### 输出重定向（>）和追加(>>)

> 重定向会覆盖原先内容，追加不会

```shell
# 列表中的内容写入文件a.txt
ls -l >文件
# 列表内容追加到文件末尾
ls -al >>文件
# 将文件1覆盖到文件2
cat 文件1>文件2
# 
echo '内容'>>文件
```

#### echo

> 输出内容到控制台

```shell
# 基本语法
echo [选项] [输出内容]

# 输出当前环境变量
echo $PATH
```



#### head

> head用于显示文件的开头部分内容，默认显示文件前10行内容

```shell
# 基本语法

# 查看文件前10行
head 文件
# 查看文件前5行
head -n 5 文件
```

#### tail

> 用于输出文件中尾部的内容，默认情况下tail指令显示文件的后10行内容

```shell
# 基本语法

# 查看文件后10行
tail 文件
# 查看文件后5行
tail -n 5 文件
# 实时追踪改文档所有更新，工作中常用于监听日志文件
tail -f 文件
```

#### ln

> 软李艾i你姐也叫符号连接，类似于windows里的快捷方式，主要是存放了链接其他文件的路径

```shell
#　基本语法
ln -s [源文件或目录] [软链接名]

# 创建软连接
ln -s /home/jysp/test/hello.txt test_link
# 删除软连接,路径不要带/
rm -rm test_link
```

#### history

> 查看已执行过的历史命令，也可以执行历史指令

```shell
# 基本语法
history

# 显示所有历史命令
history
# 显示最近使用过的10个指令
history 10
# 执行历史编号为5的指令
!5
```



### 时间日期类的指令

> date 指令-显示当前日期

```shell
# 基础语法

# 显示当前时间
date
# 显示当前年份
date +%Y
# 显示当前月份
date +%m
# 显示当前哪一天
date +%d
# 显示年月日时分秒
date "+%Y-%m-%d %H:%M:%S"
```

#### 设置时间

```shell
# 基本语法
date -s 字符串时间
```

#### cal

> 查看日历指令

```shell
# 基本语法
cal [选项]
-h  查看帮助
```



### 搜索查找类



#### find

> 将指令从指定目录向下递归遍历其个个子目录，将满足条件的文件或者目录 显示在终端

```shell
# 基本语法
find [搜索范围] [选项]
# 选项
-name	按照文件名查找模式
-user	查找指定用户所有文件
-size	按照指定文件大小查找文件

------------
# 搜索 /home/jysp 目录下 login.html 文件
find /home/jysp/ -name login.html
# 搜索 /home/jysp 目录下 gif 文件
find /home/jysp/ -name *.gif

# 搜索 / 目录下 大小超过20M的 文件
# -20M	小于20M
# 20M	等于20M
# 单位 k,M,G,T...

find / -size +20M
```

#### locate

> locaate指令可以**快速定位文件路径**。locate指令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位给定的文件。Locate指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确度，管理员必须定期更新locate时刻。
> 

```shell
# 基本语法
locate 搜索文件

# 应用实例
案例1:请使用locate 指令快速定位 hello.txt 文件所在目录

```

> 特别说明
> **由于locate指今基于数据库进行查询，所以第一次运行前，必须使用updatedb指令创建locate数据库。**

#### grep 和 |

> grep 过滤查找，管道符 "|", 表示将前一个命令的处理结果输出传递给后面的命令处理

```shell
# 基本语法
grep [选项] 查找内容 源文件

# 选项
-n	显示匹配及行号
-i	忽略字母大小

----------------------
请在hello.txt 文件中，查找 31，并显示行号
grep -ni 30 hello.txt

cat hello.txt |grep -n 30
```



### 压缩和解压类

#### gzip/gunzip 

> gzip 用于压缩文件，gunzip 用于解压

```shell
# 基本语法

# 压缩，只能将文件压缩为 *.gz 文件，支持多文件，空格分开
gzip 文件

# 解压文件命令
gunzip 文件.gz
```

#### zip/unzip

> zip 用于压缩文件，unzip用于解压文件。项目发包中常用

```shell
# 基本语法
zip [选项] xxx.zip 要压缩的内容
-r:	递归压缩，即压缩目录

unzip [选项] xxx.zip
-d<目录>：指定压缩后文件存放目录

------------------------------
1、将/test 下所有文件压缩成 test.zip
zip -r test.zip test
2、将 test.zip 解压到 test/zip 目录下
unzip -d /test/zip test.zip 


```



#### tar

> tar 是打包指令，最后打包文件是 .tar.gz的文件

```shell
# 基本语法

tar [选项] XXX.tar.gz 打包内容

# 选项
-x：解压.tar文件
-z：打包同时压缩
-v：显示所有过程
-f：指定压缩文件名
-j：有bz2属性的
-c：产生.tar打包文件，用于压缩文件
```

1、压缩多个文件

```shell
tar -zcvf a.tar.gz a1.txt a2.txt
```

2、压缩目录

```shell
tar -zcvf myhome.tar.gz /home/
```

3、解压到当前目录

```shell
tar -zxvf a.tar.gz
```

4、解压到指定目录

```shell
# 指定目录必须已存在
tar -zxvf myhome.tar,gz -C /test/gz
```



### 定时任务调度

> crontab 进行定时任务的设置。
>
> 系统在某个时间执行的特定的命令或程序。
>
> 分类：
>
> 1、系统工作，如病毒扫描 
>
> 2、个别用户工作，比如 MySQL数据库备份

```shell
# 基本语法
contab [选项]

# 常用选项
-e	编辑crontab定时任务
-l	查询crontab任务
-r	删除当前用户所有的crontab任务
```

#### 快速入门

设置任务调度文件： /etc/crontab

设置个人任务调度： crontab -e

输入任务：

```shell
# 每小时的每分钟执行 ls-l /etc/>> /tmp/to.txt
*/1**** ls -l  /etc/> /tmp/to.txt
```

| 项目   | 含义               | 范围                   |
| ------ | ------------------ | ---------------------- |
| 第1个* | 1h中的第几分钟     | 0-59                   |
| 第2个* | 一天中的第几个小时 | 0-23                   |
| 第3个* | 一个月当中的第几天 | 1-31                   |
| 第4个* | 一年中的地几个月   | 1-12                   |
| 第5个* | 一周当中的星期几   | 0-7（0,7都代表星期日） |

**特殊参数说明**

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 任意时间                                                     |
| ,        | 代表不连续时间。如"0 6,8 * * * 命令",每天6:00,8:00执行一次   |
| -        | 代表连续时间范围。如"0 5 * * 1-6 命令",代表周一到周6凌晨5点0分执行 |
| */n      | 代表隔多久执行一次。如"*/10 * * * * 命令"，每隔10分钟执行一次 |

#### **调度任务实例**

> 每个一分钟，就将当前日期信息，追加到 /xxx/mydate 文件中

1. 编写一个文件 mytask1.sh

	```shell
	date >> /xxx/mydate
	```

2. 给脚本可执行权限

3. crontab -e

4. */1 * * * *   /xxx/mytask1.sh

#### 相关指令

1. contab -r  终止任务调度
2. contab -l  列出当前有哪些任务调度
3. service contab restart  重启任务调度



### 磁盘分区、挂载

> 分区方式：
>
> 1. mbr分区
> 	- 最多支持4个主分区
> 	- 系统只能安装在主分区
> 	- 扩展分区要占一个主分区
> 	- MBR最大只支持2TB，但拥有最好兼容性
> 2. gpt分区
> 	1. 支持无限多个分区（操作系统可能会限制，如windows下最多128个）
> 	2. 支持最大18EB的大容量（EB=1024PB,pb=1024TB）
> 	3. windows7 64位以后才支持gpt

硬盘说明：

对于Linux硬盘分为 IDE硬盘和SCSI硬盘，目前基本上是SCSI硬盘

对于IDE硬盘：驱动标识符 "hdx~"

```shell
hd 表名分区类型就是IDE
X 为盘号，a基本盘，b基本从属盘，c为辅助主盘，d为辅助从属盘
~ 代表分区，主分区、拓展分区（1-4），从5开始为逻辑分区

# hda3
IDE硬盘上第三个主分区或拓展分区
```

对于SCSI硬盘使用 "sd"表示分区所在设备的类型，其余跟 IDE硬盘一样

#### 查看系统分区和挂载

```shell
lsblk -f
```

![image-20230508112227249](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230508112227249.png)

#### Linux挂载今典案例

> 在Linux中增加一个新的硬盘 sdb1(2G)，并挂载到/home/newdisk

**如何增加一块硬盘**

1. 虚拟机添加硬盘（需重启）

2. 分区  fdisk /dev/sdb

	输入 n 新增分区，然后输入p,分区类型为主分区,然后输入w,保存退出

3. 格式化  mkfs -t ext4 /dev/sdb1

4. 挂载 先创建一个 /home/newdisk,挂载 mount /dev/sdb1 /home/newdisk

	```shell
	mount 设备名称 挂载名录
	umount 设备名称 挂载名录
	```

	

5. 设置永久挂载（重启后任然生效）

	```shell
	vim /etc/fstab
	
	添加
	/dev/sdb1	/home/newdisk	ext4	defaults	0 0
	```

	

### 磁盘情况查询



#### 查询系统磁盘使用情况

```shell
df -h
df -l
```

#### 查询指定目录的磁盘占用情况

```shell
# 默认当前目录
du -ach /目录
-s 指定目录占用大小汇总
-h	带计量单位
-a	含文件
--max-depth=1	子目录深度
-c	汇总值
```

#### 常用指令

1. 统计/home 文件夹下文件个数

	```shell
	ls -l /home | grep "^-" | wc -l
	```

	

2. 统计/home 文件夹下目录个数

	```shell
	ls -l /home | grep "^d" | wc -l
	```

	

3. 统计/home 文件夹下文件个数，包括子文件

	```shell
	ls -lR /home | grep "^-" | wc -l
	```

	

4. 统计/home 文件夹下目录个数，包括文件夹里的

	```shell
	ls -lR /home | grep "^d" | wc -l
	```

	

5. 以树状显示目录结构

	```shell
	tree
	```

	

### 网络配置



#### 查看虚拟网络IP和网关

VMware 编辑-> 虚拟网络编辑器



为防止IP每次登录动态分配，设置静态IP



修改配置文件来指定IP，并可连接到外网

```shell
# eth0 代表第一块网卡，eth1代表第二块
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```



### 进程管理



#### 显示系统执行的进程

> ps命令是用来查看目前系统中，有哪些正在执行，以及他们执行的情况，可不加任何参数。

```shell
# | grep nginx 过滤查询进程信息
ps -aux | grep nginx
-a	显示当前终端的所有进程信息
-u	以用户的格式显示进程信息
-x	显示后台进程运行的参数
-e	显示所有进程。
-f	全格式
```

| 字段 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| PID  | 进程识别号                                                   |
| TTY  | 终端机号                                                     |
| TIME | 此进程所消CPU时间                                            |
| CMD  | 正在执行的命令或进程号                                       |
| TT   | 终端名称                                                     |
| STAT | S-睡眠，s-先导进程，N-优先级低，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者停止 |
| CPU  | cpu 进程占用                                                 |
| RSS  | 物理内存占用                                                 |
| VSZ  | 虚拟内存占用大小                                             |

![image-20230508152858498](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230508152858498.png)

#### 查询某进程父进程

```shell

ps -ef | grep nginx

# 用户	进程id  父进程id
root       1602   1569  0 12:13 ?        00:00:00 nginx: master process nginx -g daemon off;
101        1690   1602  0 12:13 ?        00:00:00 nginx: worker process
root      40487   2197  0 15:43 pts/0    00:00:00 grep --color=auto nginx

```

#### 终止进程

基本语法

```shell
# 通过进程号杀死进程
kill  [选项] 进程号
# 通过进程名杀死进程，支持通配符
killall 进程名字
-9：强迫进程立即停止
```

**实践案例**

1. 踢掉某个非法登录用户

	```shell
	ps -aux | grep sshd
	kill xxx_id(第二列参数)
	```

	

2. 终止远程登录服务sshd,在适当时候重启sshd服务

	```shell
	ps -aux | grep sshd
	kill xxx_id(第二列参数)
	```

	

3. 终止多个gedit编辑器

	```shell
	killall gedit
	```

	

4. 强制杀掉一个终端

	```shell
	kill -9 gedit
	```

5. 查看进程树

	```shell
	pstree
	-p: 显示进程PID
	-u: 显示进程的所属用户
	```

	



### 服务管理

> 服务本质就是进程，但是运行在后台的，通常都会监听某个端口，等待其他程序知识。比如 mysql,sshd防火墙等，又称为守护进程，是Linux中非常重要的知识点。

#### 管理指令

> service/systemctl 命令设置后立马生效，但是重启后修改会重置。

```shell
service 服务名 start| stop | restart | reload | status
systemctl （>=7.0）
```

#### 查看关闭重启防火墙情况

```shell
systemctl status firewalld
```

1.查看防火墙此时运行状态。方式有两种具体如下:

```text
[root@localhost student]# firewall-cmd --state
[root@localhost student]# systemctl status firewalld
```

2.查看防火墙是否开机自启。

```text
[root@localhost student]# systemctl is-enabled
```

3.查看接口区域，这里以ens33为例。

```text
[root@localhost student]# firewall-cmd --get-zone-of-interface=ens33
public
```

4.查看绑定区域的接口。

```text
[root@localhost student]# firewall-cmd --get-active-zones
docker
  interfaces: docker0
public
  interfaces: ens33
```

5.查看所有开放端口。

```text
root@zq-virtual-machine:/home/zq# netstat -aptn
```



2.1 开启防火墙

1.执行**systemctl start firewalld.service**命令开启防火墙，默认是开启的。

```text
[root@localhost student]# systemctl start firewalld.service
```

2.2 关闭防火墙

1.执行**systemctl stop firewalld.service**命令关闭防火墙。

```text
systemctl stop firewalld.service
```

2.3 重启防火墙

1.执行**systemctl restart firewalld.service**命令重启防火墙。

```text
systemctl restart firewalld.service
```

2.4 开机自启防火墙

1.执行**systemctl enable firewalld.service**命令设置防火墙开机自启。

```text
systemctl enable firewalld.service
```

#### 打开端口/服务

**3.1 打开端口**

**临时打开端口**

1.执行**firewall-cmd --zone=public --add-port=80/tcp**命令打开80/tcp。这里需要注意，这是临时的打开端口，重启后无效。

```text
[root@localhost student]# firewall-cmd --zone=public --add-port=80/tcp
```

2.执行**systemctl restart firewalld.service**命令重启防火墙。

```text
[root@localhost student]# systemctl restart firewalld.service
```

**注意:当开放端口设置完成后必须要重启，否则不生效！！！**

**永久打开端口**

1.执行**firewall-cmd --permanent --zone=public --add-port=80/tcp**命令永久打开80/tcp。

```shell
firewall-cmd --permanent --zone=public --add-port=80/tcp
```

2.执行**systemctl restart firewalld.service**命令重启防火墙。

```shell
systemctl restart firewalld.service
```

**注意:当开放端口设置完成后必须要重启，否则不生效！！！**

**3.2 打开服务**

**临时打开服务**

1.执行**firewall-cmd --zone=public --add-service=http**命令公共区域中临时打开服务HTTP。

```shell
firewall-cmd --zone=public --add-service=http
```

**永久打开服务**

1.执行**firewall-cmd --permanent --zone=public --add-service=http**命令公共区域中临时打开服务HTTP。

```shell
firewall-cmd --permanent --zone=public --add-service=http 
```



#### 查看系统服务

方式1：

```shell
#<7
# 系统服务
setup

# >=7
nmtui
```

方式2：

```shell
# <7
ls -l /etc/init.d/

# >=7
ls -l /usr/lib/systemd/
```



#### 进程监控

> top 命令，与ps相似，不同的是top会隔一段时间跟新正在运行的进程

```shell
top [选项]
-d	指定几秒后更新，默认3s
-i	使top 不显示任何闲置或者僵死进程
-p	通过指定监控进程ID来仅仅监控某个进程的状态
```

快捷键

| 操作 | 功能                    |
| ---- | ----------------------- |
| P    | 以CPU使用率排序，默认值 |
| M    | 以内存使用率排序        |
| N    | 以PID排序               |
| q    | 退出                    |

**终止指定索引**

top指令后，输入k,回车，在输入要结束的进程ID号



#### 监控网络状态

查看系统网络情况 netstat

```shell
#基本用法
netstat [选项]

-an 按一定顺序排列输出
-p	显示哪个进程在调用
```

查看服务名为 sshd 的服务信息

```shell
netstat -anp | grep sshd
```



#### chkconfig 指令

> 通过 chkconfig  命令可以给每个服务器各运行级别设置自启动/关闭

```shell
# 查看服务
chkconfig 服务名 --list|grep xxx

chkconfig 服务名 --list

chkconfig --level 5 服务名 on/off
```



### 软件包管理

RPM和YUM

rpm包的管理

```shell
# 查询已安装包 XX
rpm -qa|grep xx
```

一个rpm包名: firefox-45.0.1-1.el6.centos.x86_64.rpm

名称:firefox版本号: 45.0.1-1

适用操作系统: el6.centos

.x86 64表示centos6.x的64位系统。如果是i686、i386表示32位系统，noarch表示通用。

#### rpm 包管理的其他查询命令

```
rpm-qa :查询所安装的所有rpm软件包

rpm -ql	软件包名：查询软件包中文件

rpm -qf 文件全路径名：查询文件所属软件包

rpm -q 软件包名：查询软件包是否安装

rpm -qi 软件包名：查询软件包信息
```



#### yum 包管理

> Yum 是一个 shell 前端软件包管理器。基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包。

```shell
# 查看yum仓库中所有可以安装的rpm包
yum list|grep '^zip'

# 根据命令查找该命令属于哪个安装包，最好写命令的绝对路径，如果不知道绝对路径可以在命令前用*匹配
yum provides 命令

# yum 本地安装
yum localinstall -y 包名

# yum 源安装
yum install -y 软件名

# 网络安装
yum install -y 网址

# 重新安装该软件，可以用来恢复之前误删除该服务的重要文件，但是恢复的是最初始的配置
yum reinstall -y 包名

# 查看当前系统中，有哪些软件可以进行更新
yum check-update

# 加软件名就是更新指定的软件包，不加软件名就是更新该系统中全部可以更新的软件'(不加软件名的更新很危险，它会将你的内核更新到最新版本，会导致很多命令不同，有些服务可能会失效)'
yum update -y [软件名]

# 卸载指定的软件
yum erase -y 软件名

# 卸载指定的软件
yum remove -y 软件名

# 清除所有的缓存，也可以清除默认路径下yum下载的包
yum clean all(Packages)

# 加载缓存
yum makecache

# 查看系统中还有哪些组可以安装
yum group list

# 安装包组
yum groups install 包组名字

#：卸载包组 
yum groups remove 包组名字
```

包的默认yum下载的路径：

```shell
vim /etc/yum.conf 

...
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
...
# 将keepcache改成1就可以吧rpm包下载到本地

# 也可以用选项来下载rpm包到本地：
--downloadonly：仅下载，不安装
--downloaddir：指定下载的目录
```



### Linux 安装JDK

> 上传解压到 /usr/java/jdk1.8_131。 配置环境变量 

/etc/profile

```js
# jdk
export JAVA_HOME=/usr/java/jdk1.8.0_281
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

# nodejs
export NODE_PATH=/home/jysp/workspace/node-v16.17.0/lib/node_modules

```

配置完成需要注销重新登录