---
title: c基础
date: 2023-10-31 14:23:10
tags: c
categories: c
---

> 在vscode 中运行c++

## [环境搭建](https://blog.csdn.net/Winda_shi/article/details/129463163)

<!--more-->

1. **搜索安装 C/C++ 和 code runner 插件**

2. 下载mingb64,解压，配置环境变量

3. 在这个 .vscode 文件夹下新建2个文件作为配置文件（launch.json和tasks.json）

	launch.json

	```json
	{
	    // 使用 IntelliSense 了解相关属性。 
	    // 悬停以查看现有属性的描述。
	    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
	    "version": "0.2.0",
	    "configurations": [
	        
	        {
	            "name": "(gdb) Launch", 
	            "type": "cppdbg",
	            "request": "launch",
	            "program": "${workspaceFolder}/${fileBasenameNoExtension}.exe",
	            "args": [], 
	            "stopAtEntry": false,
	            "cwd": "${workspaceFolder}",
	            "environment": [],
	            "externalConsole": true, 
	            "MIMode": "gdb",
	            "miDebuggerPath": "D:\\Program Files\\mingw64\bin\\gdb.exe",
	            //**********要修改为自己的编译器所在的bin路径，形如 c:\\***\\bin\\gdb.exe
	            "preLaunchTask": "gc", 
	            "setupCommands": [
	                {
	                    "description": "Enable pretty-printing for gdb",
	                    "text": "-enable-pretty-printing",
	                    "ignoreFailures": false
	                }
	            ]
	        
	        }
	
	    ]
	}
	```

	tasks.json

	```json
	{
	    "version": "2.0.0",
	    "command": "gcc", 
	    "args": [
	        "-g",
	        "${file}",
	        "-o",
	        "${fileBasenameNoExtension}.exe"
	    ], 
	    "problemMatcher": {
	        "owner": "cpp",
	        "fileLocation": [
	            "relative",
	            "${workspaceFolder}"
	        ],
	        "pattern": {
	            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
	            "file": 1,
	            "line": 2,
	            "column": 3,
	            "severity": 4,
	            "message": 5
	        }
	    }
	}
	```

4. **按ctrl+shit+p，搜索找到编辑配置（JSON）文件，并打开检查自己的“includePath”**

	搜索 c/c++, 编辑配置文件

	```json
	{
	    "configurations": [
	        {
	            "name": "Win32",
	            "includePath": [
	                "${workspaceFolder}/**",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/x86_64-w64-mingw32",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/backward",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/include",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/include-fixed",
	                "D:/Program Files/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/include"
	            ],
	            "defines": [
	                "_DEBUG",
	                "UNICODE",
	                "_UNICODE"
	            ]
	        }
	    ],
	    "version": 4
	}
	```

5. **g++ -v -E -x c++ -，进行查看路径并复制下来**

	上边里面D盘路径都是这个命令查出来的

6. 重启vscode

7. 编写示例 hello.c

	```c
	#include<stdio.h>
	int main(void){
	    printf("Hello World!");
	    return 0;
	}
	```

8. 运行 runCode

## 常用基本类型

- short,int,long  整形
- float,double 浮点型
- char 字符串
- void

#### 数组

> 有序的元素序列

```c
// 字符数组
char arr[2]
// 整形数组
int array[10]
```



### 结构体

> 类似于对象的概念

```c
struct {
	int a;
	char b;
}
```



### 枚举类型



```c
enum em {
    red_color = 0,
    green_color, // 1
    black_color // 2
};
```



### 指针

储存地址的变量

```c
// 定义指针
type* var
    
// 使用指针
*var
```



### 堆内存的分配于释放

```c
// 分配内存
void* mem = malloc(size)

// 释放内存
free(mem)
```

```c
#include <stdlib.h>
int main(void) {
	char* p = (char*)malloc(10);
    // 给内存位置1赋值
    *p = 'a';
    // 给内存位置2赋值
    *(p+1) = 'b';
    printf('%s', p);
    
    // 释放内存
    free(p);
    // 不设置为 Null, 依然可以通过 p 操作内存。类似于bug一样
    p = NULL;
}
```

### 函数

> 函数定义没有提升，只能写在调用之前

```c
void func (int a) { ...}
```

### 函数指针

> 返回值类型(***指针变量**名)([**形参**列表])

```c
// 声明函数指针
void (*f) (int a) { ...}
// 将函数地址赋值给函数指针，可通过f调用函数
f = func;
```



## 操作文件

> 通过 FILE* file;

打开文件

```c
FILE* fopen(path, mode);
```

关闭文件

```c
fclose(FILE*);
```

