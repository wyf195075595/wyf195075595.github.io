---
title: typescript 语法基础
date: 2022-07-17 23:17:10
tags: typescript
categories: js
---

## 基本数据类型

> number`，`string`，`boolean`，`symbol`，`null`或`undefined

基本类型

```typescript
const str: string = 'hello there'

const num: number = 123

const bool: boolean = false

let n: null = null

let u: undefined = undefined
```

联合类型

```typescript
let age: string|number = 15
```

数组，元组

```typescript
// 数组, 数组中元素是number类型的数组
const arr: number[] = [1,2,3]

// 数组，数组内元素是任意类型
const arr1: any[] = [1, {a: 2}, '3']

// 元组类型顺序数量都要保持一致
const tulpe: [stirng, number] = ['zzz', 123]
```

枚举

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

<!--more-->

## 接口

> 用来描述对象形状的 interface

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

接口拓展

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
```

类型断言

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



## 函数

> 函数主要关系其参数与返回值

函数类型声明

```typescript
// 基本写法
function sum (a: number, b: number): number {
	return a + b
}

// 声明类型:

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

多个泛型， 元组交换

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









> [ts中文文档](https://www.tslang.cn/docs/handbook/interfaces.html)