---
title: typescript 语法基础
date: 2022-07-17 23:17:10
tags: typescript
categories: js
---

## 基本数据类型

> number`，`string`，`boolean`，`symbol`，`null`或`undefined

#### 基本类型

```typescript
const str: string = 'hello there'

const num: number = 123

const bool: boolean = false

let n: null = null

let u: undefined = undefined
```

<!--more-->

#### 类型注解

```js
# 声明变量类型
let count : number;
```

#### 类型推断

```js
# 简单类型不需要写类型注释, 不能自动推断时需要类型注释
let one = 1;
let two = 2;
let three = one + two;
```

#### 联合类型

```typescript
let age: string|number = 15
```

#### 数组，元组

```typescript
// 数组, 数组中元素是number类型的数组
const arr: number[] = [1,2,3]

// 数组，数组内元素是任意类型
const arr1: any[] = [1, {a: 2}, '3']

// 数组中有数字，字符串
const arr: (number | string)[] = [1,"2",3]

// 数组中是特定对象类型
type girl = {
    name: string,
    age: number,
    sex?: string
}

const xjj: girl[] = [
    {name: "如花", age: 18}
]

// 元组类型顺序数量都要保持一致
const tulpe: [stirng, number] = ['zzz', 123]
```

#### 枚举

```typescript
// 默认值是下标数字
enum USER_ROLE {
    USER,
    MANAGER,
    ADMIN
}
console.log(USER_ROLE.MANAGER) // 1

// 赋值
enum USER_ROLE {
    USER = 'USER',
    MANAGER = 'MANAGER',
    ADMIN = 'ADMIN'
}
console.log(USER_ROLE.MANAGER) // MANAGER

```



## 接口

> 用来描述对象形状的 interface，值必须是对象

```typescript
// 定义
interface ISchool {
    readonly name: string, // 设置属性只读
    age: number,
    address?: string // ?标识 此属性可填可不填
}

// 使用, 属性不能多，也不能少，类型需保持一致
let school: ISchool = {
    name: 'xxx学校',
    age: 3,
    address: 'xxx'
}

school.name = 'ccc学校' // 报错

```

#### 接口拓展

```typescript
// 拓展继承
interface IZHU extends ISchool {
	type: string,
    [key: string]: any // 可拓展任意类型, 其中 key 不是特定写法，可任意
}

let zhuf:IZHU = {
    ...school,
    type: 'web',
    a: 1,
    b: 2
}
// 接口中的扩展

interface Teacher {
    say(): string;
}
```

#### 类型断言

> 表示这个对象就是这样一个类型

```typescript
// ISchool 接口没有拓展时，新增 lessons 属性会报错， 这时可以使用断言 人工判断他就是 ISchool 类型
let school2: ISchool = ({
	name: 'xxx学校',
    age: 3,
    address: 'xxx',
    lessons: ['1', '2']
}) as ISchool

// 将 number 强行断言成 string
let a: number = 2

// 直接断言成 string 会报错
a as any as string
```

#### 类型守护

> 通过类型判断返回正确的类型, 可以使用断言，in , instanceof，属性访问等方式

```js
function addObj (first: object | NumberObj, second: object | Number) {
    if(first instanceof NumberObj && second instanceof NumberObj) {
        return first.count + second.count;
    }
    return 0
}
```



## 函数

> 函数主要关系其参数与返回值

#### 函数类型声明

```typescript
// 基本写法， 没有返回值 声明返回值 void
function sum (a: number, b: number): number {
	return a + b
}

const getStr :()=>string = ()=> {
    return "str"
}

// 声明类型: type 可以声明基本类型别名，联合类型，元组等类型，interface不行

1、type

type Sum = (a: number,b: number)=> number | string

2、interface

interface Sum {
    (a: number, b: number): number
}

// 区别: interface 可以继承，被类实现。 type 仅仅是个别名

let sum2: Sum = (a: number, b: number): number => a + b

sum2 = 'type 声明 联合类型'
```



## 泛型



```typescript
// js
function createArray (len, value) {
    let rs = []
    for (let i = 0; i< len ; i++) {
        rs.push(value)
    }
    return rs
}

let arr = createArray(3, 'zf')

// ts
function createArray<T> (len: number, value: <T>): T[] {
    let rs = []
    for (let i = 0; i< len ; i++) {
        rs.push(value)
    }
    return rs
}

```

#### 多个泛型， 元组交换

```typescript
// 原, 返回值 any
const swap = (tuple: [string, number]): [number,string] => {
	return [tuple[1], tuple[0]]
}
swap('1', 2)

// 改 返回值 随 传参类型
const swap = <S,N>(tuple: [S, N]): [N,S] => {
	return [tuple[1], tuple[0]]
}
swap<strig,number>('1', 2)
```

#### 类中使用泛型

```js
class SelectGirl<T> {
    constructor(private girls: T[]) {}
	getGirl(index: number): T {
        return this.girls[index];
    }
}

const selectGirl = new SelectGirl<string>(["girl1","girl2","girl3"])
console.log(selectGirl.getGirl(1))// girl2
```

#### 泛型约束

```js
interface Girl {
    name: string
}
class SelectGirl<T extends Girl> {
    constructor(private girls: T[]) {}
}

class SelectGirl<T extends string| number> {
    constructor(private girls: T[]) {}
}
```



## 类

> 大部分跟js原生类差不多，但是也加入了一些新的东西

```js
class Teacher {
    public name = "老师A" // 都能使用
	protected major = "数学"// 本类，子类中使用
	private skill ="教学" // 只在本类中使用,一般配合set,get 使用
    teacher() {
        return this.name + this.skill + this.major;
    }
	static do() {} // 静态方法，通过类名访问，没有this
	set skill(val) {this.skill =  val}
	get skill() {return this.skill}
}
class Student extends Teacher {
	name = "学生A"
	major = "英语"
    skill = "学习"
    teacher() {
       return super.teacher() + " 辛苦了！" 
    }
    learn() {
        console.log(this.name + this.skill + this.major);
    }
}
let stu = new Student();
console.log(stu.teacher());

stu.learn();
```

### 抽象类

> 抽象类中有抽象方法，继承抽象类必须实现抽象方法

```js

abstract class Girl {
	abstract skill();
}

class BaseTacher extends Girl {
    skill() {
        
    }
}
```



## 命名空间

> 避免全局污染

```js
namespace Home {
    class Header{
        constructor() {
            let dom = document.createElement("div")
            dom.innerText = "Header"
            document.body.appendChild(dom)
        }
    }
    class Content {
        constructor() {
            let dom = document.createElement("div")
            dom.innerText = "Content"
            document.body.appendChild(dom)
        }
    }
    class Footer {
        constructor() {
            let dom = document.createElement("div")
            dom.innerText = "Footer"
            document.body.appendChild(dom)
        }
    }
    
    export class Page {
        constructor() {
            new Header();
            new Content();
            new Footer();
        }
    }
    
    // 子命命名空间
    export namespace subComponents {
        export class Test {
            constructor() {
                console.log("new Test");
            }
            
        }
    }
}

// 使用
Home.Page();
Home.subComponents.Test();
```



## ts.config.js

> 
>
> [中文文档](https://www.tslang.cn/docs/handbook/compiler-options.html)

> [ts中文文档](https://www.tslang.cn/docs/handbook/interfaces.html)