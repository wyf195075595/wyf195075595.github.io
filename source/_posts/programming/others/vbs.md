---
title: vebscript
date: 2022-06-20 18:23:10
tags: vbs
categories: other
---

Vbscript 脚本语言，运行在 windos 的脚本语言。

<!--more-->

## 变量

```vbscript
语法：
	dim 变量
	
// 定义变量
dim a,b
a=10
b=20
msgbox a+b

// 定义数组
dim names(2)// 创建了一个包含三个元素的数组

names(0)="George"
names(1)="John"
names(2)="Thomas"

// 定义二维数组
声明了一个包含 5 行 7 列的 2 维数组：

dim table(4, 6)
```

<!--more-->

## 子程序与函数

- 子程序：
	•是一系列的语句，被封装在 Sub 和 End Sub 语句内。
	•可执行某些操作，但不会返回值。
	•可带有通过程序调用来向子程序传递参数。
	•如果没有，必须带有空的圆括号

```vbscript

Sub mysub()
 some statements
End Sub
Sub mysub(argument1,argument2)
 some statements
End Sub

```

- 函数程序：
	•是一系列的语句，被封装在 Function 和 End Function 语句内。
	•可执行某些操作并返回值。
	•可带有通过程序调用来向其传递参数。
	•如果没有，必须带有空的圆括号
	•通过向函数程序名赋值的方式，可使其返回值。

	```vbscript
	Function myfunction()
	 some statements
	 myfunction=some value
	End Function
	Function myfunction(argument1,argument2)
	 some statements
	 myfunction=some value
	End Function
	
	调用函数
	name = findname()
	
	可以这样调用子程序：
	Call MyProc(argument)
	
	或者，也可以省略 Call 语句：
	MyProc argument
	```

	


## 输入输出

```
输入框语法：
	imputbox(提示信息，弹框标题，输入框默认值)

输入：
	dim str
	str = imputbox("请输入你的名字","title", "hhhhhhh")
	msgbox str

输出：
	弹窗: msgbox "文字内容",窗口模式,"标题栏"

具体的弹窗配置如下：
效果						常量				值
只显示“确定”按钮			vbOKOnly			0
显示“确定“，“取消”			vbOKCancel			1
显示“终止”、“重试”、“忽略”   vbAbortRetrylgnore	2
显示“是”、“否”、“取消”		vbYesNoCancel		3
显示“是”、“否”			  vbYesNo			4
显示重试、取消				vbRetryCancel		5
使第1个按钮为默认按钮		vbDefaultButton1		0
使第2个按钮为默认按钮		vbDefaultButton2		256
使第3个按钮为默认按钮		vbDefaultButton3		512
使第4个按钮为默认按钮		vbDefaultButton4		768
显示“严重错误”			vbCritical			16
显示“惊叹号”				vbExclamation		48
显示“关键信息”			vbInformation		64
显示“疑问号”				vbQuestion			32


例子
//例如你需要显示一个警告窗口加上一个“是否”的按钮，就这样写：

MsgBox "信息",48+4,"标题"

//或者写成常量的形式：

MsgBox "信息",vbExclamation+vbYesNo,"标题"

//如果你需要显示一个“错误”符号，就这样：
MsgBox "信息",vbCritical,"标题"

```



## 判断语句

```vbscript
1、if else, elseif

dim str
// vbYesNo 这个是说，显示两个可供选择的选项框啦
str = msgbox"你爱我吗？", vbYesNo, "爱"
if str=vbYes then
	msgbox "我也爱你！！！"
elseif str=vbYes then
	msgbox "凑数的"
else
	msgbox "我要关你的电脑！！！"
	set sh=wscript.createobject("wscript.shell")
	sh.run "shutdown -s -t 300"

end if

终止关机

Windows+R打开cmd，然后，输入：

shutdown -a

这样就会终止掉自动关机啦。


2、Select Case

假如你希望选择多套代码之一来执行，可以使用 SELECT 语句：
select case payment
 case "Cash"
   msgbox "You are going to pay cash"
 case "Visa"
   msgbox "You are going to pay with visa"
 case "AmEx"
   msgbox "You are going to pay with American Express"
 case Else
   msgbox "Unknown method of payment"
end select

```

## 逻辑运算符

```vbscript
逻辑运算符
只列举特殊的

相等 = 注意！ 其他语言大多都是 == 表示相等，= 表示赋值
赋值 =
不相等 <>
非 n o t notnot
与 a n d andand
或 o r oror
异或 x o r xorxor
```



## 循环语句

```vbscript
1、do--loop

do
	msgbox "死循环"
loop

关闭死循环
Ctrl + Alt + Del，然后点击任务管理器，找到对应的进程

推出循环的方式
-until

dim str
do unit str= "爱"
	msgbox "你到底爱不爱我？"
	str=inputbox("爱或不爱","答案","爱")
loop

-、while

dim str
do
	msgbox "你爱我吗？"
	str=inputbox("爱火不爱", "回答我", "yes")
loop while str<>"yes" // <> 的意思是不等于


-、exit do

dim str
do
	msgbox "你爱我吗？"
	str=inputbox("爱不爱？","回答我", "爱")
	if str="爱" then
		msgbox "我也爱你"
		exit
	else
		msgbox "请说你爱我！！"
		msgbox "请你在回答一次"
	end if
loop


2、for--next
类似于for循环

msgbox "显示一些数字"
for i=0 to 3
	msgbox "这个数字是："&i // & 来进行连接
next


3、Step 关键词

通过使用 Step 关键词，我们可以规定计数变量递增或递减的步进值。

在下面的例子中，计数变量i每次循环的递增步进值为 2。
For i=2 To 10 Step 2
  some code
Next

如果要递减计数变量，就必须负的步进值。并且需要规定小于开始值的结束值。

在下面的例子中，计数变量i每次循环的递减步进值为 2。
For i=10 To 2 Step -2
  some code
Next

退出 For...Next

如需退出 For...Next 语句，可以使用 Exit 关键词。

```

```
set ws=createobject("wscript.shell")
road = ws.CurrentDirectory
aaa = wscript.ScriptFullName
```

