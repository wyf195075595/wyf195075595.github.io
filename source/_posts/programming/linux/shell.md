---
title: shell 脚本
date: 2023-04-23 15:23:10
tags: shell
categories: other
---

> shell 脚本开头的两个字符叫做 shebang "#!"。在unix系统中，程序会分析 shebang 后面内容，作为解析器的指令。相当于告诉系统用那种解析器去解读脚本。yarn 就是python 开发的

- 以 #!/bin/sh 开头的文件，程序在执行的时候会调用 /bin/sh，也就是bash解释器
- 以 #!/usr/bin/python 开头的文件，代表指定python解释器去执行
- 以 #!/usr/bin/env 解释器名称，是一种在不同平台上都能正确找到解释器的办法

<!--more-->

### 运行

#### 新建一个shell脚本, test.sh

```shell
#!/bin/sh
echo '我是一行shell 脚本文件'
```

#### 运行

```shell
test.sh # 找不到目录

./test.sh # 权限不够
# 添加权限,就可以运行上边代码
chmod +x hello.sh

# 指定解析器运行
/bin/sh ./test.sh

. ./test.sh # . 等于 source 
source ./test.sh
```

#### 查看当前解析器

```shell
echo $SHELL
```



### 脚本注释

> 为了方便阅读，要有写注释的习惯。
>
> 当行注释： #

### 常用命令



- history 

	查看历史命名，echo $HISTSIZE 命令历史条数

	打开会话历史文件 ~/.bash_history

	```shell
	history #命令以及参数
	-c:清空内存中命令历史;
	-r:从文件中恢复历史命令
	数字 :显示最近n条命令 history 10
	
	执行指定历史命令 !id：	!502
	执行上一条命令： !!
	```

- echo

	```shell
	-n 不换行输出
	-e 解析字符串特殊符号
	
	\r 回车
	\t 制表
	\n 换行
	\b 退格
	
	```

	

- eval

	执行多个命令

	```shell
	eval ls; cd /tmp
	```

	

- exec

	不创建子进程，执行后续命令，执行完毕自动 exit

	```
	exec date
	```

	

- export

- read

- shift



### 快捷键

```shell
ctrl + l # 清屏
ctrl + a # 把光标移动到命令行开头 
ctrl + e # 把光标移动到命令行结尾
ctrl + u # 删除或剪切光标之前的命令
ctrl + k # 删除或剪贴光标之后的内容
ctrl + R # 搜索历史 
ctrl + S # 暂停屏幕输出
ctrl + D # 退出当前终端
tab 补全
```



### 变量

> 临时存放，shell 进程是一个作用域，弱类型跟js一样。
>
> 变量与赋值之间不能空格
>
> 单引号不识别特殊语法，双引号识别

```shell
name='aaa'

echo $name
```

#### 特殊变量

> 

```shell
$0  获取she11脚本文件名，以及脚本路

$n  获取shel1脚本的第n个参数,n在1~9之间，如$1,$2，..$9 ，大于9则需要写，$10，参数空格隔开

$#  获取执行的she11脚本后面的参数总个数

$*  获取shel1脚本所有参数，不加引号等同于$@作用，加上引号"$*"作用是 接收所有参数为单个字符串"$1 $2...

$@  不加引号，效果同上，加引号，是接收所有参数为独立字符串，如”$1”“$2” “$3”...，空格保留
```

**$@ 与 $* 的区别**

```shell
#!/bin/bash
echo "print each param fromfor var in \"\$*\""
for var in "$*"
do
	echo "$var" # 输出一行
done

echo "print each param fromfor var in \"\$@\""
for var in "$@"
do
	echo "$var" # 输出多行
done
```

####  特殊状态变量

```shell
$? 上一次命令执行状态返回值，0正确，非0失败
$$ 当前she11脚本的进程号
$! 上一次后台进程的PID
$_ 上次之前执行的命令，最后一个参数
```

**source 是在当前shell环境加载脚本，因此保留当前变量。bash都会开启一个子shell,不会保留当前shell变量**



**脚本控制返回值玩法**

test.sh

```shell
#!/bin/bash
# $#获取参数个数-ne 不等于的情况 && 并且
[$# -ne 2] && {
	echo "must be two args'
	exit 119 #终止程序运行，且返回119状态码，提供给当前shelL的S?变量，若是在函数里 可以return 119用法
}

# 输出当前进程pid
echo $$

echo ok
```

执行完上边脚本，$? 会保存 119

```shell
# 此时会提示 must be two args
bash test.sh params1 params2 params3

# 119
echao $?
```

**让程序后台执行**

```shell
# 后台执行 ping baidu.com 并将日志输出到黑洞文件中.命令产生一个pid
nohup ping baidu.com & 1> /dev/null

# 输出前面pid
echo $!

```



#### 环境变量

> 个人变量优先于全局
>
> 用户个人配置文件 ~/.bash_profile
>
> 远程用户特有文件 ~/.bashrc
>
> 全局配置文件  /etc/profile, /etc/bashrc. 系统建议不要直接修改主文件而是创建 /etc/profile.d/

set : 检索变量 name开头

```shell
set |grep ^name
```

env : 只显示全局变量

```shell
env |wc -l # 显示输出条数
```

declare: 输出所有的变量，如同set

export: 显示和设置环境变量值

unset 变量名: 撤销环境变量

readonly：只有shell结束，只读便变量失效。只对当前shell生效

![image-20230424102750297](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230424102750297.png)

#### 子串玩法

```
${变量)	返回变量值
${#变量)	返回变量长度，字符长度
${变量:start}	返回变量start数值之后的字符
${变量:start:length}	提取start之后的length限制的字符
${变量#word}	从变量开头删除最短匹配的word子串
${变量##word)	从变量开头，删除最长匹配的word
${变量%word}	从变量结尾删除最短的word
${变量%%word}	从变量结尾开始删除最长匹配的word
${变量/pattern/string}	用string代替第一个匹配的pattern
${变量//pattern/string}	用string代替所有的pattern
```

统计字符串长度的其他方法

```shell
expr length "${name}"

# 返回行数
cat test.txt| wc -l
# 返回最长行数的字符数量
cat test.txt| wc -L

echo "${name}" | awk '{print length($0)}'
```

子串拓展

```
如果parameter变量值为空，返回word字符串${parameter:-word}

如果para变量为空，则word替代变量值，且返回其值${parameter:=word}

如果para变量为空，word当作stderr输出，否则输出变量值用于设置变量为空导致错误时，返回的错误信息${parameter:?word}

如果para变量为空，什么都不做，否则word返回${parameter:+word}
```

删除7天以上的过期数据

```shell
find xargs 搜索，且删除

find 需要搜索的目录-name 你要搜索的文件名字-type 文件类型-mtime +7 xargs rm -f

# 删除指定目录，超过7天的.tar.gz 压缩包 
find ${dir_path:=/data/mysql_back_data/} -name '*.tar.gz' -type f -mtime +7 xargs rm 
```

#### linux 特殊符号

```shell
${vars}	取出变量结果
$()		再括号中执行命令，且拿到命令的执行结果
``		再括号中执行命令，且拿到命令的执行结果
()		开启子she11执行命令结果
$vars	取出变量结果

```



### 父子shell

> 开启子shell去做其他事情不会耽误我去做其他事情，类似于于多进程

![image-20230424143608565](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230424143608565.png)

> -
>
> 1. source和点执行脚本，只在当前的shell环境中执行生效
> 2. 指定bash sh解释器运行脚本，是开启subshell ,开启shell运行脚本命令
> 3. ./script,都会指定shebang, 通过解释器运行，也是开启subshell 运行命令

#### 父子 shell 查看方式

```shell
pstree
# 查看 sshd 项

ps -ef --forest
```

#### 创建进程列表

> 使用小括号

```shell
(cd ~; pwd;ls; cd /tmp/; pwd; ls)
```

#### 判断父子shell

> 通过内置变量 $BASH_SUBSHELL, 值为0 则是当前shell,否者就是可开启了子shell



### 内置命令，外置命令

> 内置命令：在系统启动时就在载入内存，常驻内存，执行效率高但是占用资源。如 cd,
>
> 外置命令：用户需要从硬盘中读取，在写入内存加载.如 nginx

判断命令是否内置，外置命令

- 通过Linux的 【type 命令 】验证（输出此命令是shell内嵌），不会开启子进程
- 外置命令执行肯定会开启子进程
- 查询所有内置命令：compgen -b

### 循环

```
for n in {1..100}
do
	echo $n
done
```

```shell
# 统计循环时间
time for n in {1..10000}
do
  # 生成1-100数字中间用 change分割的字符串
  char= `seq -s "change" 100`
  # 输出打印到黑洞文件，不在控制台打印
  echo ${#char} &>/dev/null
done

# 结果
real    0m43.783s	实际时间
user    0m8.716s	用户时间
sys     0m32.445s	内核时间

```

批量修改文件名

将所欲后缀 _finished.jpg 图片名称去除 _finished

```shell
# 找出所有图片名包含 xxx_finished.jpg
ls *fin*.jpg

for file in `ls *fin*.jpg`
do
	mv $file `echo ${file//_finished/}`
done
```



### 数值运算

> linux 大多运算不持支小数。 大多数运算符与js,java 类似

#### 双小括号

```shell
# 这总反式不能取值
((i + 1))
# 可以取值
echo $((i + 1))
# 2的3次方
((2**3))
# 计算 1-10 相加
echo $((`seq -s + 10`))
```

比较运算中 真为1 ，假为0



**运算脚本**

```shell
#!/bin/bash
print_usage(){
  printf "请输入一个数字\n"
  exit 1
}

# 接收用户输入

read -p "请输入数字：" firstnum

# 判断
# -n 判断字符串是否为空，空-不成立，反之-成立
# sed 作用，将 输入的值，把所有数字替换为空，剩下的就是非数字
if [ -n "`echo $firstnum|sed 's/[0-9]//g'`" ]
  then
    print_usage
fi

read -p "输入 运算符" operator

# 判断
# 限制在 +-*/

if [ -n "`echo $operator|sed 's/[+-\*\/]//g'`" ]
  then
    echo '只允许输入 +,-,*,/'
    exit
fi

# 输入第二个数字
read -p "请输入数字：" secondnum
if [ -n "`echo $secondnum|sed 's/[0-9]//g'`" ]
  then
    print_usage
fi

# 最后计算

echo '运算结果是：'$((${firstnum}${operator}${secondnum}))
```



#### let 命令

> let 命令等同于双括号计算，((赋值表达式))。但是双括号效率更高

```shell
rs=5

let rs=rs*2

echo $rs # 10
```



### expr 命令

> expr 命令允许在命令处理数学表达式。

```shell
expr 1 + 1
expr 1 - 1
# 需要转义字符
expr 1 \* 1
# 分母大于分子则结果为0
expr 1 / 1
# 计算 1-10 相加
expr `seq -s ' + ' 10`
# xargs 管道命令
seq -s ' + ' 10| xargs expr
```

**模式匹配**

1. :， 计算字符串中的字符数
2. .*，任意字符串重复0次或多次

```shell

expr yc.jpg ":" ".*" # 6

expr yc.jpg234 ":" ".*\.jpg" # 6

expr yc.jpeg234 ":" ".*\.jpg" # 0

```

**应用**

> 通过是否匹配的上来判断文件格式，返回长度0 不能匹配，长度>0匹配

### bc 管道命令

> 整数计算，双小括号，let,expr。带小数计算使用 bc

```shell
echo 4*4|bc
# 计算 1-10 相加
echo {1..10}|tr " " "+" |bc

```



### 中括号运算[]

> $[表达式]语法如此，加减乘除

```shell
rs=10
rs=$[rs+8]
echo $rs # 18


```

### awk计算

> 支持小数，if 条件判断

```shell
echo "3.2 3.2" | awk '{print ($1+4*$2)}'
```





### 用户输入

> read 内置命令
>
> -p	设置提示信息
>
> -t	等待用户输入超时, timeout

```shell
read -p "给你5s中，时间输入密码：" pwd
echo $pwd
# 输入多个变量时，注意空格
read -p "请输入账户和密码" zh mm
echo $zh $mm
```

### if-then 语句

> 编程语言中 if 条件语句要求 结果是 false,true.而shell中并不是，bash 的if 语句会直接运行后边的命令，如果其执行正确则进入 then 否则不进入

```shell
if command
then
	command
fi
--------
if pwd
then
	echo 'i am ok'
fi
```



### if-then-else 语句

> 增加了一个判断条件

```shell
if command
then
	command
else
	command
fi
------------------
 if [ 1 -eq 1 ]
 then
 	echo ok
 else
 	echo nono
 fi

```

### elif

```shell
if command
then
	command
elif command
then
	command
else
	command
fi
```



### test

> 用于评估一个表达式；如果条件为真，则返回一个0， 非0则为假。判断结果通过 $? 访问

1. 文件类型侦测

	```shell
	test -e filename
	```

	-e 该文件名] 是否存在?(常用)

	-f 该文件名是否为文件(file)?(常用)

	-d 该文件名 是否为目录(directory)? (常用)

	-b 该文件名是否为一个 block device 装置?

	-c 该[文件名是否为一个 character device 装置?

	-s 该文件名是否为一个 Socket 文件?

	-p 该文件名是否为一个 FIFO (pipe) 文件?

	-L 该文件名是否为一个连结档?

2. 文件权限侦测

	```shell
	test -r filename
	```

	-r 侦测该文件名是否具有可读。的属性?

	-w 侦测该文件名是否具有可写。的属性?

	-x 侦测该文件名是否具有 可执行， 的属性?

	-u 测该文件名是否具有 SUID 的属性?

	-g 侦测该文件名是否具有 SGID 的属性?

	-k 侦测该文件名是否具有 sticky bit 的属性?

	-s 测该文件名是否为非空白文件。?

3. 两个文件之间比较

	```shell
	test file -nt file2
	```

	-nt (newer than)判断 file1 是否比 file2 新

	-ot (older than)判断 file1 是否比 file2 旧

	-ef 判断 file2与 file2是否为同一文件，可用在判断 hard link 的判定

4. 两整数之间判断

	```shell
	test n1 -eq n2
	```

	-eq 两数值相等(equal)

	-ne 两数值不等 (not equal)

	-gt n1 大于 n2 (greater than)

	-lt n1 小于 n2 (less than)

	-ge n1 大于等于 n2 (greater than or equal)

	-le n1 小于等于 n2 (less than or equal)

5. 判断字符串的数据

	```shell
	test -z string # 字符串为0？true: false
	test -n string # 判断字符串是否为0？false: true, -n 可省略
	
	test str1 = str2 # 相等 true, 反之 false
	test str1 != str2 # 相等 返回false, 反之 true
	
	```

6. 多重判断

	```shell
	test -r filename -a -x filename
	```

	-a 两种状况同时成立

	-o 两种状况任意成立

	!  反相状态



#### **简洁的测试方式**

```shell
# 注意空格
if [ 条件 ]
then
	cammands
fi

--------判断目录是否存在---------
#!/bin/bash
target_dir=/home/ccc
if [ -d $target_dir ]
then
	echo "这个目录 $target_dir exists"
	cd $target_dir
	ls
else
	echo "这个目录 $target_dir 不存在"
	
fi
```

#### Linux 内存监控

```shell
FreeMem=`free -m |awk 'NR==2' {print $NF}`
CHARS="Current memory is $FreeMem"

if [ "$FreeMem" -lt "2000" ]
	then
		echo $CHARS|tee /tmp/messages.txt
		# mail -s "主题" 收件人 <
		mail -s "`date +%F-%T$CHARS`" xxx.qq.com < /tmp/messages.txt
		echo "内存不足，抓紧维护服务器！"
```



#### **符合条件测试**

> [ 条件1 ] && [ 条件2 ]
>
> [ 条件1 ] || [ 条件2 ]
>
> 布尔运算符

```shell
#!/bin/bash
target_dir=/home/ccc
if [ -d $target_dir ] && [ -w $target_dir/testing ]
then
	echo "文件不存在，不能写入"
	cd $target_dir
	ls
else
	echo "文件存在，可以写入"
fi
```

测试 nginx 服务是否存在

```shell
#!/bin/bash

# 版本变量
CheckUrl() {
  timeout=5
  # 相当于定义一个计数器
  fails=0
  success=0
  
  # 循环检测，循环执行一些命令
  while true
    do
      wget --timeout=${timeout} --tries=1 https://wyf195075595.github.io -q -O /dev/null
      
      # 命令执行结果 $?
      if [ $? -ne 0 ]
        then
          let fails=fails+1 # 失败次数加一
        else
          let success+=1 # 成功次数加一
      fi
      
      # 判断成功次数大于等于1，可以访问
      if [ $success -ge 1 ]
        then
          echo "该网站运行健康"
          # 返回状态码
          exit 0
      fi
      
      # 当错误次数大于等于2，警告
      if [ ${fails} -ge 2 ]
        then
          echo "网站挂掉了,尽快检查"
          exit 2
      fi
    done
}

CheckUrl
```



### 双小括号

> 前面计算时就用到了这个语法，它支持数值运算，逻辑运算。还能在if 条件中使用

```shell
val=10
if (( $val ** 2 > 90 ))
  then
    echo "xxx"
fi
```

### 双方括号

> 双方括号提供了针对字符串的高级特性，模式匹配，正则表达式的匹配。双括号中字符不需要转义。一般不用，特殊场景使用
>
> 双括号中可以使用 &&,||,!

```shell
# 在双中括号里，进行了 == 双等号，进行字符串匹配 r*，也就找到了 root 。
if [[ $USER == r* ]]
then
  echo "xxx"
else
  echo "xxx"
fi


---------测试变量在【1，3】之间--------------

[[ $num =~ [1-3] ]]
```

### 中括号

> 也可以做一些数值字符比较，但是中括号中 字符需要 添加转义符，很常用
>
> 中括号中不能使用逻辑运算符 ||，&&, 要使用 -a ,-o 代替

![image-20230504103754885](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230504103754885.png)

### case 语句

```sheLl
# 语法
case "变量" in
值1)
  command1
  ;;
值2)
  command2
  ;;
*)
  command3
esac
-------------------
case $car_type in
自行车)
  echo "自行车也不错哦!"
  ;;
摩托车)
  echo "摩托车泰库拉！"
  ;;
*)
  echo "我是谁？我在哪儿？我在干什么？"
esac
```



### while 循环

```shell
while test command
do
  other command
done
```

