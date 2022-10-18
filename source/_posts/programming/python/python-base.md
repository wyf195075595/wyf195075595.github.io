---
title: python基础
date: 2022-06-17 18:23:10
tags: 基础语法
categories: python
---

> 通用语言，脚本语言，开源语言，跨平台语言，多模型语言
>
> 2000年 1.0 版本
>
> 2008年 2.0 版本
>
> 3.0系列 不兼容早期的2.0

<!--more-->

## 开发环境配置

```python
#官网下载
www.python.org/download/

#vsCode运行
	下载过 runcode 插件可以直接选中python 代码片段运行
	
#从终端运行Python程序
    - 在python文件目录下运行
	python xxx.py

#VSCode中运行Python，print中文时出现乱码
增加环境变量：

key: PYTHONIOENCODING
value: UTF8
```

## 程序的基本编写方法

```
IPO模式
-I：Input 输入，程序的输入
-P：Process 处理，程序的主要逻辑
-O：Output 输出，程序的输出
```

## 语法

```js
◆单行注释： #开头

◆多行注释： ''' 开头和结尾

◆命名
	-保证名字的唯一性
	-命名规则：使用大小写字母，数字，下划线组合。但首字母只能大小写字母或◆下划线，不能使用空格

◆空格的使用
	-表示缩进关系的空格不能改变
	-空格不能将一个命名分割
	-除上述两条外，程序中可以任意使用空格增加程序可读性

◆变量
	变量常被描述为可用于存储值的盒子。在你刚接触变量时，这种定义可能很有帮助，但它并没有准确描述Python内部表示变量的方式。一种好得多的定义是，变量是可以赋给值的标签，也可以说变量指向特定的值。
    
◆常量 使用全大写字母与下划线组成， 但是修改常量不报错
◆字符串模板拼接
	f 为format 简写
	-f"任意字符 {字符变量} 任意字符"
	-"{}, {}".format(变量1， 变量2)

◆使用制表符或换行符来添加空白
	在编程中，空白泛指任何非打印字符，如空格、制表符和换行符。在字符串中添◆加制表符，可使用字符组合\t.换行符 \n
    
◆同时给多个变量复制
	x,y,z = 1,2,3
◆两个变量交换赋值
	x, y = y, x
◆for 循环
	Python根据缩进来判断代码行与前一个代码行的关系
    for item in arr:
    	语句1
    	语句2
        ...
    循环外

◆if语句
    if 条件1:
        ...
    elif 条件2：
    	...
    else:
    	...
        
◆多条件检查
    -and  对应js 中的 &&
    -or	  对应js 中的 ||
    -in    判断特定的值是否已包含在列表中
	-not in  确定特定的值未包含在列表中

◆input()
    函数input()让程序暂停运行，等待用户输入一些文本。获取用户输入后，Python将其赋给一个变量，以方便你使用。
    
◆类型转换
	转整型: init()

◆while
	while 条件:
        if true:
        	break
		elif 条件2:
			continue
		else:
        	...
            
◆模块之间的引用
	-相同模块间引用
		直接 import xxx 后缀.py不需要
	-不同模块间引用
        import sys
        sys.path.append('目标模块文件夹相对路劲')
        import 模块名
        模块名.函数名()
     -引入时设置别名
		from 模块名 import 函数名 as 别名
     -引入模块所有函数，可直接使用函数名调用
		import 模块名 as *
```

## 字符串

```python
方法
    capitalize()	把首字符转换为大写。
    casefold()	把字符串转换为小写。
    center()	返回居中的字符串。
    count()	返回指定值在字符串中出现的次数。
    encode()	返回字符串的编码版本。
    endswith()	如果字符串以指定值结尾，则返回 true。
    expandtabs()	设置字符串的 tab 尺寸。
    find()	在字符串中搜索指定的值并返回它被找到的位置。
    format()	格式化字符串中的指定值。
    format_map()	格式化字符串中的指定值。
    index()	在字符串中搜索指定的值并返回它被找到的位置。
    isalnum()	如果字符串中的所有字符都是字母数字，则返回 True。
    isalpha()	如果字符串中的所有字符都在字母表中，则返回 True。
    isdecimal()	如果字符串中的所有字符都是小数，则返回 True。
    isdigit()	如果字符串中的所有字符都是数字，则返回 True。
    isidentifier()	如果字符串是标识符，则返回 True。
    islower()	如果字符串中的所有字符都是小写，则返回 True。
    isnumeric()	如果字符串中的所有字符都是数，则返回 True。
    isprintable()	如果字符串中的所有字符都是可打印的，则返回 True。
    isspace()	如果字符串中的所有字符都是空白字符，则返回 True。
    istitle()	如果字符串遵循标题规则，则返回 True。
    isupper()	如果字符串中的所有字符都是大写，则返回 True。
    join()	把可迭代对象的元素连接到字符串的末尾。
    ljust()	返回字符串的左对齐版本。
    lower()	把字符串转换为小写。
    lstrip()	返回字符串的左修剪版本。
    maketrans()	返回在转换中使用的转换表。
    partition()	返回元组，其中的字符串被分为三部分。
    replace()	返回字符串，其中指定的值被替换为指定的值。
    rfind()	在字符串中搜索指定的值，并返回它被找到的最后位置。
    rindex()	在字符串中搜索指定的值，并返回它被找到的最后位置。
    rjust()	返回字符串的右对齐版本。
    rpartition()	返回元组，其中字符串分为三部分。
    rsplit()	在指定的分隔符处拆分字符串，并返回列表。
    rstrip()	返回字符串的右边修剪版本。
    split()	在指定的分隔符处拆分字符串，并返回列表。
    splitlines()	在换行符处拆分字符串并返回列表。
    startswith()	如果以指定值开头的字符串，则返回 true。
    strip()	返回字符串的剪裁版本。
    swapcase()	切换大小写，小写成为大写，反之亦然。
    title()	把每个单词的首字符转换为大写。
    translate()	返回被转换的字符串。
    upper()	把字符串转换为大写。
    zfill()	在字符串的开头填充指定数量的 0 值。
	

```

## number

```
在Python中，可对整数执行加（+）减（-）乘（*）除（/）运算。

+ ，-， *， /， **(乘方)

数字下划线 3.6以上支持
	10_000 === 1_0000 = 10000
```

## 列表

```python
类似于js中的数组
	list = [1,2,3,4,5]
	list[1] // 1 使用下标访问列表元素
    list[-1] // 5 下标为负数时，列表从右至左读取
    
◆方法
append()	在列表的末尾添加一个元素
clear()	删除列表中的所有元素
copy()	返回列表的副本
count()	返回具有指定值的元素数量。
extend()	将列表元素（或任何可迭代的元素）添加到当前列表的末尾
index()	返回具有指定值的第一个元素的索引
insert()	在指定位置添加元素
pop()	删除指定位置的元素，不传下标默认删除列表最后一个
remove()	删除具有指定值的项目
reverse()	颠倒列表的顺序
sort()	对列表进行排序，影响原列表
	-arr.sort(reverse=True) 实现反转
sorted() 与sort功能一致，不过是临时的，不改变原列表
len(list) 获取列表的长度
min(list) 找出列表中最小的值
max(list) 找出列表中最大的值
sum(list) 列表中所有值的和



◆删除对象属性的方式删除
	del	list[index]
    
◆列表解析 
	-直接获取1-11 乘方的集合，【后边没有 :】
	[value**2 for value in range(1, 11)]
    
◆切片
	返回部分列表数据，省略第一个值则 从头开始，省略第二个值则直到最后，两个都不写则相当于复制整个列表
    list[0:3] #返回索引[0-3)的元素集合 
```

## 元组

```python
    组看起来很像列表，但使用圆括号而非中括号来标识。定义元组后，就可使用索引来访问其元素，就像访问列表元素一样。
    元组不能修改
    dimensions(200, 50)
    
    严格地说，元组是由逗号标识的，圆括号只是让元组看起来更整洁、更清晰。如果你要定义只包含一个元素的元组，必须在这个元素后面加上逗号：my_t = (3,)
创建只包含一个元素的元组通常没有意义，但自动生成的元组有可能只有一个元素。

◆方法
count()	返回元组中指定值出现的次数。
index()	在元组中搜索指定的值并返回它被找到的位置。
```

## 字典

```python

	Python中，字典是一系列键值对。每个键都与一个值相关联，你可使用键来访问相关联的值。与键相关联的值可以是数、字符串、列表乃至字典。事实上，可将任何Python对象用作字典中的值
    字典类似于js中的对象，但是访问属性只能通过 obj[attr],不能使用点访问
    
◆普通的遍历
	for key,val in obj.items():
        print(key, val)
    
◆方法
clear()	删除字典中的所有元素
copy()	返回字典的副本
fromkeys()	返回拥有指定键和值的新字典
get()	返回指定键的值
items()	返回包含每个键值对的 元组的列表
keys()	返回包含字典键的列表
pop()	删除拥有指定键的元素
popitem()	删除最后插入的键值对
setdefault()	返回指定键的值。如果该键不存在，则插入具有指定值的键。
update()	使用指定的键值对字典进行更新
values()	返回字典中所有值的列表

```

## 集合

```
集合中的每个元素都必须是独一无二的,集合是无序的，因此您无法确定项目的显示顺序。

thisset = {"apple", "banana", "cherry"}

◆方法
add()	向集合添加元素。
clear()	删除集合中的所有元素。
copy()	返回集合的副本。
difference()	返回包含两个或更多集合之间差异的集合。
difference_update()	删除此集合中也包含在另一个指定集合中的项目。
discard()	删除指定项目。
intersection()	返回为两个其他集合的交集的集合。
intersection_update()	删除此集合中不存在于其他指定集合中的项目。
isdisjoint()	返回两个集合是否有交集。
issubset()	返回另一个集合是否包含此集合。
issuperset()	返回此集合是否包含另一个集合。
pop()	从集合中删除一个元素。
remove()	删除指定元素。
symmetric_difference()	返回具有两组集合的对称差集的集合。
symmetric_difference_update()	插入此集合和另一个集合的对称差集。
union()	返回包含集合并集的集合。
update()	用此集合和其他集合的并集来更新集合。
```

## 函数

```python
	函数是带名字的代码块，用于完成具体的工作。要执行函数定义的特定任务，可调用该函数。需要在程序中多次执行同一项任务时，无须反复编写完成该任务的代码，只需要调用执行该任务的函数，让Python运行其中的代码即可
    
    定义函数
        def test ():
            ...
         
        test()
        
    多个参数可以用 *，类似于js 扩展运算符
    def make_pizza (*options):
    def make_pizza (size, *options):
        ...
        
    // **表示将参数赋值为 字典形式
    def get_options (one, two, **options):
        print(options) # {'name': 'www', 'age': '15'}
    get_options(1,2,name="www",age="15")
    
    // 默认参数
    #给形参指定默认值时，等号两边不要有空格：
    def get_options (one="111", two="222", **options):
    
```

## 类

```python
    根据约定，在Python中，首字母大写的名称指的是类。这个类定义中没有圆括号，因为要从空白创建这个类。

类名应采用大驼峰命名法
实例名和模块名都采用小写格式，并在单词之间加上下划线
#创建类
class Dog:
    #__init__()是一个特殊方法，每当你根据Dog类创建新实例时，Python都会自动运行它,类似于js的construct
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def sit(self):
        print(f"{self.name} is now sitting")
#实例化
my_dog = Dog('Willie', 6)
my_dog1 = Dog('tim', 3)

#调用方法
my_dog.sit()

#继承
class Adog(Car):
    def __init__(self, name, age):
        #super()是一个特殊函数，让你能够调用父类的方法
        super().__init__(name, age):
            
#类的导入
-导入整个模块
    import Car
    #Car.ElectricCar
    #Car.Car
-导入多个类
	from 模块名 import Car,ElectricCar
-导入所有类, 但是不推荐这种导入方式。不能很清楚知道导入了哪些类
	from 模块名 import *
```

## python 标准库

```python
	Python标准库是一组模块，我们安装的Python都包含它。可以使用标准库中的任何函数和类，只需在程序开头包含一条简单的import语句即可
	
random 库
	方法
	- randint() 将两个整数作为参数，返回位于这两个整数之间的随机整数
	-choice() 将一个列表或元组作为参数，并随机返回其中一个元素
        from random import choice
        players = ['charles', 'martina', 'michael', 'florence', 'eli']
        first_up = choice(players)
        first_up #'florence'
        
        
Python Module of the Week　
	要了解Python标准库，一个很不错的资源是网站Python Module of the Week。请访问该网站并查看其中的目录，找一个你感兴趣的模块进行探索。从模块random开始可能是个不错的选择。
```

## 文件和异常

```python

◆文件读取
	#关键字with在不再需要访问文件后将其关闭,当然也可以通过手动close去关闭，但是若代码出现error 导致close 未执行，文件将不会关闭。
	with open('test.txt') as file_object:	
    	contents = file_object.read()
     print(contents)
    
◆逐行读取
	with open('test.txt') as file_object:
        for line in file_object:
            print(line)
            
    每行的末尾都有一个看不见的换行符，而函数调用print()也会加上一个换行符，因此每行末尾都有两个换行符
    
◆创建一个包含文件各行内容的列表
filename = 'test.txt`'

with open(filename) as file_object:
  lines = file_object.readlines()

for line in lines:
  print(line.rstrip())

    方法readlines()从文件中读取每一行，并将其存储在一个列表中。接下来，该列表被赋给变量lines。在with代码块外，依然可使用这个变量。在❷处，使用一个简单的for循环来打印lines中的各行。因为列表lines的每个元素都对应于文件中的一行，所以输出与文件内容完全一致。
    读取文本文件时，Python将其中的所有文本都解读为字符串
    
◆写入文件
filename = 'programming.txt'
#可指定读取模式（'r'）、写入模式（'w'）、附加模式（'a'）或读写模式（'r+'）。如果省略了模式实参，Python将以默认的只读模式打开文件。
with open(filename, 'w') as file_object:
    file_object.write("I love programming.")
    
-写入多行
	需要手动在行尾加入 \n

    
    
◆异常
	Python使用称为异常的特殊对象来管理程序执行期间发生的错误。每当发生让Python不知所措的错误时，它都会创建一个异常对象。如果你编写了处理该异常的代码，程序将继续运行；如果未对异常进行处理，程序将停止并显示traceback，其中包含有关异常的报告。
    
    -处理ZeroDivisionError异常
    try:
        print(5/0)
    except ZeroDivisionError: #这里只捕捉 ZeroDivisionError 错误
        print('被除数不能为0')
        
    else:
        print('没有发生错误')

eg:
    def count_words(filename):
      """计算一个文件大致包含多少个单词。"""
      try:
          with open(filename, encoding='utf-8') as f:
              contents = f.read()
      except FileNotFoundError:
          print(f"Sorry, the file {filename} does not exist.")
      else:
          words = contents.split()
          num_words = len(words)
          print(f"The file {filename} has about {num_words} words.")

      filename = 'alice.txt'
      count_words(filename)
        
        
 pass语句
	可用于让Python在代码块中什么都不要做
    
    eg:
        try:
            print(5/0)
        except ZeroDivisionError:
            #pass语句还充当了占位符，提醒你在程序的某个地方什么都没有做，并且以后也许要在这里做些什么
            pass
        else:
            ...
```

## 储存数据

```python
    很多程序都要求用户输入某种信息，如让用户存储游戏首选项或提供要可视化的数据
    
 　使用json.dump()和json.load()

1、json.dump()
	import json
    numbers = [2,3,4,5,6,7,8,9,10]

    filename = 'number.json'
    with open(filename, 'w') as f:
        #使用函数 json.dump() 将数字列表存储到文件 numbers.json 中。
        json.dump(numbers, f)
2、json.load()
	import json

    filename = 'numbers.json'
    with open(filename) as f:
       numbers = json.load(f)

    print(numbers)
    
    
    
eg:
    import json
  #如果以前存储了用户名，就加载它。
  #否则，提示用户输入用户名并存储它。
  filename = 'username.json'
  try:
    with open(filename) as f:
       username = json.load(f)
  except FileNotFoundError:
    username = input("What is your name? ")
    with open(filename, 'w') as f:
      json.dump(username, f)
      print(f"We'll remember you when you come back, {username}!")
  else:
      print(f"Welcome back, {username}!")
        
        
重构
 import json

  def get_stored_username():
     """如果存储了用户名，就获取它。"""
      filename = 'username.json'
      try:
          with open(filename) as f:
              username = json.load(f)
      except FileNotFoundError:
         return None
      else:
          return username

  def greet_user():
      """问候用户，并指出其名字。"""
      username = get_stored_username()
     if username:
          print(f"Welcome back, {username}!")
      else:
          username = input("What is your name? ")
          filename = 'username.json'
          with open(filename, 'w') as f:
              json.dump(username, f)
              print(f"We'll remember you when you come back, {username}!")

  greet_user()
```

## 单元测试

```python
Python标准库中的模块unittest提供了代码测试工具。单元测试用于核实函数的某个方面没有问题

#unittest模块中的断言方法
方法
-用途		
assertEqual(a, b) 		-核实a == b	
assertNotEqual(a, b)	-核实a != b	
assertTrue(x)			-核实x为True	
assertFalse(x)			-核实x为False	
assertIn(item, list)	-核实item在list中	
assertNotIn(item, list)	-核实item不在list中

def get_formatted_name(first_name, last_name):
    if middle_name:
        return f"{first_name} {middle_name} {last_name}".title()
    else:
        return f"{first_name} {last_name}".title()

import unittest

class NamesTestCase(unittest.TestCase):
    """测试name_function.py。"""
	#方法名必须以test_打头，这样它才会在我们运行主文件时自动运行
    def test_first_last_name(self):
        """能够正确地处理像Janis Joplin这样的姓名吗？"""
        formatted_name = get_formatted_name('janis','', 'joplin')
        #比较结果是否相等
        self.assertEqual(formatted_name, 'Janis Joplin')
    def test_first_middle_last_name(self):
        """能够正确地处理像Janis Joplin这样的姓名吗？"""
        formatted_name = get_formatted_name('janis', 'and', 'joplin')
        #比较结果是否相等
        self.assertEqual(formatted_name, 'Janis And Joplin')

if __name__ == '__main__':
    unittest.main()


# if代码块检查特殊变量__name__，这个变量是在程序执行时设置的。如果这个文件作为主程序执行，变量__name__将被设置为'__main__'。在这里，调用unittest.main()来运行测试用例。如果这个文件被测试框架导入，变量__name__的值将不是'__main__'，因此不会调用unittest.main()。


SetUp 写法

import unittest
from survey import AnonymousSurvey

class TestAnonymousSurvey(unittest.TestCase):
  """针对AnonymousSurvey类的测试。"""

  def setUp(self):
      """
     创建一个调查对象和一组答案，供使用的测试方法使用。
      """
     question = "What language did you first learn to speak?"
     self.my_survey = AnonymousSurvey(question)
     self.responses = ['English', 'Spanish', 'Mandarin']

  def test_store_single_response(self):
      """测试单个答案会被妥善地存储。"""
      self.my_survey.store_response(self.responses[0])
      self.assertIn(self.responses[0], self.my_survey.responses)

  def test_store_three_responses(self):
      """测试三个答案会被妥善地存储。"""
      for response in self.responses:
          self.my_survey.store_response(response)
      for response in self.responses:
          self.assertIn(response, self.my_survey.responses)

if __name__ == '__main__':
  unittest.main()
```

