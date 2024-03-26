---
title: typescript 语法基础
date: 2022-07-17 23:17:10
tags: typescript
categories: js
---

## 基本数据类型

> number`，`string`，`boolean`，`symbol、bigint、object`，`null`或`undefined

#### 基本类型

- any 类型

	any 类型可以赋值任何类型，同样 any 类型可以赋值给其他类型造成污染

- unkonwn  为避免any类型污染问题，unknown类型作用与any一致，但是它只能赋值到unknown/any类型的变量上

	要想使用 unknown 类型，必须缩小范围，否则无法使用

	- uknouwn运算有限， 只能进行比较运算（运算符`==`、`===`、`!=`、`!==`、`||`、`&&`、`?`）、取反运算（运算符`!`）、`typeof`运算符和`instanceof`运算符这几种

		```js
		let a:unknown = 1;
		
		if (typeof a === 'number') {
		  let r = a + 10; // 正确
		}
		```

	```js
	let a:unknown = 1;
	
	if (typeof a === 'number') {
	  let r = a + 10; // 正确
	}
	```

- never

	为了保持与集合论的对应关系，以及类型运算的完整性，TypeScript 还引入了“空类型”的概念，即该类型为空，不包含任何值。

	由于不存在任何属于“空类型”的值，所以该类型被称为`never`，即不可能有这样的值。

	```js
	1、函数抛出错误，不可能有返回值
	function f():never {
	  throw new Error('Error');
	}
	2、剩余的情况就属于never类型
	function fn(x:string|number) {
	  if (typeof x === 'string') {
	    // ...
	  } else if (typeof x === 'number') {
	    // ...
	  } else {
	    x; // never 类型
	  }
	}
	3、变量x的类型是never，就不可能赋给它任何值，否则都会报错。
	let x:never;
	
	```

- object 类型

	大写的`Object`类型代表 JavaScript 语言里面的广义对象。所有可以转成对象的值，都是`Object`类型，这囊括了几乎所有的值。除了 Null / undefined

	```js
	let obj:Object;
	 
	obj = true;
	obj = 'hi';
	obj = 1;
	obj = { foo: 123 };
	obj = [1, 2];
	obj = (a:number) => a + 1;
	```

- undefined/null 

	既是值，又是类型。

	作为值，它们有一个特殊的地方：任何其他类型的变量都可以赋值为`undefined`或`null`。

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

- 简单类型不需要写类型注释, 不能自动推断时需要类型注释

- 当自动类型无法推断出来时，会默认为 any 类型
- 

```js
let one = 1;
let two = 2;
let three = one + two;
```

#### 值类型

TypeScript 规定，单个值也是一种类型，称为“值类型”。

```js
let x:'hello';

x = 'hello'; // 正确
x = 'world'; // 报错
```

上面示例中，变量`x`的类型是字符串`hello`，导致它只能赋值为这个字符串，赋值为其他字符串就会报错。

TypeScript 推断类型时，遇到`const`命令声明的变量，如果代码里面没有注明类型，就会推断该变量是值类型。（如果赋值为对象，并不会推断为值类型。）

```js
// x 的类型是 "https"
const x = 'https';

// y 的类型是 string
const y:string = 'https';
```

1. 父类型不能赋值给子类型

	5 是 number 子类型，4+1 是 number 类型，赋值错误

	```js
	const x:5 = 4 + 1; // 报错
	```

	反之则可以

	as 断言，将 4+1 断言为 5 类型。

	```js
	const x:5 = (4 + 1) as 5; // 正确
	```

#### type 命令

`type`命令用来定义一个类型的别名。

```typescript
type Age = number;

let age:Age = 55;
```

上面示例中，`type`命令为`number`类型定义了一个别名`Age`。这样就能像使用`number`一样，使用`Age`作为类型。

#### 交叉类型

交叉类型（intersection types）指的多个类型组成的一个新类型，使用符号`&`表示。

交叉类型常常用来为对象类型添加新属性。

```typescript
type A = { foo: number };

type B = A & { bar: number };
```

上面示例中，类型`B`是一个交叉类型，用来在`A`的基础上增加了属性`bar`。

#### 联合类型

```typescript
let age: string|number = 15
```

#### 数组

- 如果变量的初始值是空数组，那么 TypeScript 会推断数组类型是`any[]`

	```typescript
	// 推断为 any[]
	const arr = [];
	```

	然后随着新成员的加入，TypeScript 会自动修改推断的数组类型。

	**类型推断的自动更新**只发生**初始值为空数组**的情况。如果初始值不是空数组，类型推断就不会更新。

- 只读

	```typescript
	const arr:readonly number = [];
	const arr:ReadonlyArray<number> = [];
	```

- 多维数组

	`T[][]`的形式，表示二维数组，`T`是最底层数组成员的类型

	```typescript
	var multi:number[][] = [[1,2,3], [23,24,25]];
	```

	

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


```

#### 元组

- 数组的成员类型写在方括号外面（`number[]`），元组的成员类型是写在方括号里面（`[number]`）

```typescript
// 元组类型顺序数量都要保持一致
const tulpe: [stirng, number] = ['zzz', 123]
```

- 元组成员的类型可以添加问号后缀（`?`），表示该成员是可选的。

```typescript
let a:[number, number?] = [1];
```

上面示例中，元组`a`的第二个成员是可选的，可以省略。

- 使用扩展运算符（`...`），可以表示不限成员数量的元组

```typescript
type NamedNums = [
  string,
  ...number[]
];

const a:NamedNums = ['A', 1, 2];
const b:NamedNums = ['B', 1, 2, 3];
```

- 如果不确定元组成员的类型和数量，可以写成下面这样。

```typescript
type Tuple = [...any[]];
```

- 只读元组

```typescript
// 写法一
type t = readonly [number, string]

// 写法二
type t = Readonly<[number, string]>
```

- 元组形式传参

如果arr是数组，数组的数量不确定，就导致报错

```typescript
const arr:[number, number] = [1, 2];

function add(x:number, y:number){
  // ...
}

add(...arr) // 正确
```

#### symbol

```typescript
let x:symbol = Symbol();
let y:symbol = Symbol();

x === y // false
```

- unique symbol

unique symbol 使用 const 定义，使用let定义

```typescript
const x:unique symbol = Symbol();
// 等同于
const x = Symbol();
```

​	

#### 枚举

用来将相关常量放在一个容器里面，方便使用。

Enum 成员默认不必赋值，系统会从零开始逐一递增，按照顺序为每个成员赋值，比如0、1、2……

**成员的值可以是任意数值，但不能是大整数（Bigint）**

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

**很大程度上，Enum 结构可以被对象的`as const`断言替代**。

```typescript
enum Foo {
  A,
  B,
  C,
}

const Bar = {
  A: 0,
  B: 1,
  C: 2,
} as const;

if (x === Foo.A) {}
// 等同于
if (x === Bar.A) {}
```

**多个同名的 Enum 结构会自动合并。**

```typescript
enum Foo {
  A,
}

enum Foo {
  B = 1,
}

enum Foo {
  C = 2,
}

// 等同于
enum Foo {
  A,
  B = 1，
  C = 2
}
```

同名 Enum 合并的另一个限制是，所有定义必须同为 const 枚举或者非 const 枚举，不允许混合使用。

**Enum 成员的值除了设为数值，还可以设为字符串。**

```typescript
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT',
}
```

注意，字符串 Enum 的成员值，不能使用表达式赋值。

```typescript
enum MyEnum {
  A = 'one',
  B = ['T', 'w', 'o'].join('') // 报错
}
```

##### keyof 运算符

keyof 运算符可以取出 Enum 结构的所有成员名，作为联合类型返回。

```typescript
enum MyEnum {
  A = 'a',
  B = 'b'
}

// 'A'|'B'
type Foo = keyof typeof MyEnum;
```

上面示例中，`keyof typeof MyEnum`可以取出`MyEnum`的所有成员名，所以类型`Foo`等同于联合类型`'A'|'B'`。

如果要返回 Enum 所有的成员值，可以使用`in`运算符。

```typescript
enum MyEnum {
  A = 'a',
  B = 'b'
}

// { a: any, b: any }
type Foo = { [key in MyEnum]: any };
```

##### 反向映射

```typescript
enum MyEnum {
  A = 1,
  B
}

MyEnum.A // 1
MyEnum[1] // 'A'
```

注意，这种情况只发生在数值 Enum，对于字符串 Enum，不存在反向映射。

```typescript
MyEnum.A // 'a'
MyEnum.a // undefiend
```





#### 对象

声明方式就是使用大括号，并在內部声明属性的方法和类型

```typescript
// 属性类型以分号结尾
type MyObj = {
  x:number;
  y:number;
  add: (x:number, y:number) => number;
};

// 属性类型以逗号结尾
type MyObj = {
  x:number,
  y:number,
  add(x:number, y:number): number
};
```

一旦声明了类型，对象赋值时，就不能缺少指定的属性，也不能有多余的属性。

除了**`type`命令**可以为对象类型声明一个别名，TypeScript 还提供了**`interface`命令**，可以把对象类型提炼为一个接口。

```typescript
interface MyObj {
  x: number;
  y: number;
}

const obj:MyObj = { x: 1, y: 1 };
```

##### 可选属性

如果某个属性时可选的，需要在后面加上一个❓

```typescript
type User = {
  firstName: string;
  lastName?: string;
};
```

##### 只读属性

在声明属性前面加上 readonly

```typescript
interface MyInterface {
  readonly prop: number;
}
```

prop 属性只能在初始化期间赋值，后面再修改就会报错。

还有一种方法，就是在赋值时，在对象后面加上只读断言`as const`。

```typescript
const myUser = {
  name: "Sabrina",
} as const;

myUser.name = "Cynthia"; // 报错
```

如果变量明确地声明了类型，那么 TypeScript 会以声明的类型为准。

```typescript
const myUser:{ name: string } = {
  name: "Sabrina",
} as const;

myUser.name = "Cynthia"; // 正确
```

##### 属性名的索引类型

如果对象的属性非常多，一个个声明类型就很麻烦。下面写法可以随意属性取名

```typescript
type MyObj = {
  // 属性名为任意字符型，属性值为任意字符型
  [property: string]: string
};

const obj:MyObj = {
  foo: 'a',
  bar: 'b',
  baz: 'c',
};
```

> **建议谨慎使用，因为属性名的声明太宽泛，约束太少**。另外，属性名的**数值索引不宜用来声明数组**，因为采用这种方式声明数组，就不能使用各种数组方法以及`length`属性，因为类型里面没有定义这些东西。

##### 解构赋值

解构赋值类型声明写在另一个：{} 里，因为原来{}里的：被js语法占用了

```typescript
const {id, name, price}:{
  id: string;
  name: string;
  price: number
} = product;
```

##### 结构类型原则

只要对象 B 满足 对象 A 的结构特征，TypeScript 就认为对象 B 兼容对象 A 的类型，这称为“结构类型”原则（structural typing）。

```typescript
type A = {
  x: number;
};

type B = {
  x: number;
  y: number;
};
```

上面示例中，对象`A`只有一个属性`x`，类型为`number`。对象`B`满足这个特征，因此**兼容对象`A`，只要可以使用`A`的地方，就可以使用`B`**。

##### 严格字面量检查

​	如果对象使用字面量表示，会触发 TypeScript 的严格字面量检查（strict object literal checking）。如果字面量的结构跟类型定义的不一样（比如多出了未定义的属性），就会报错。

**如果等号右边不是字面量，而是一个变量，根据结构类型原则，是不会报错的**

```typescript
const myPoint = {
  x: 1,
  y: 1,
  z: 1
};

const point:{
  x:number;
  y:number;
} = myPoint; // 正确
```

如果你确认字面量没有错误，也可以使用类型断言规避严格字面量检查。又或者说如果允许字面量有多余属性，可以像下面这样在类型里面定义一个通用属性。

```typescript
//1、 断言
const obj:Options = {
  title: '我的网页',
  darkmode: true,
} as Options;


//2、 添加通用属性
type Options = {
    title: string,
    darkMode: boolean,
    [x: string]: any
}

//3、修改 tsConfig 关闭多余属性检查
{
  "compilerOptions": {
    "suppressExcessPropertyErrors": true
  }
}
```



##### 最小可选属性

​	如果一个对象的所有属性都是可选的，那么其他对象跟它都是结构类似的。为了避免这种情况，TypeScript 2.4 引入了一个“最小可选属性规则”，也称为[“弱类型检测”](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-4.html#weak-type-detection)（weak type detection）。

​	如果某个类型的所有属性都是可选的，那么该类型的对象必须至少存在一个可选属性，不能所有可选属性都不存在。这就叫做“最小可选属性规则”。

```typescript
type Options = {
  a?:number;
  b?:number;
  c?:number;
};

const opts = { d: 123 };

const obj:Options = opts; // 报错
```

##### 空对象

空对象是 TypeScript 的一种特殊值，也是一种特殊类型。

```typescript
const obj = {};

// 上边语法等同于 const obj:{} = {};

// 空对象没有自定义属性，所以对自定义属性赋值就会报错。
obj.prop = 123; // 报错
```

空对象作为类型，其实是`Object`类型的简写形式。

```typescript
let d:{};
// 等同于
// let d:Object;

d = {};
d = { x: 1 };
d = 'hello';
d = 2;
```

各种类型的值（除了`null`和`undefined`）都可以赋值给空对象类型，跟`Object`类型的行为是一样的。

因为`Object`可以接受各种类型的值，而空对象是`Object`类型的简写，所以它不会有严格字面量检查，赋值时总是允许多余的属性，只是不能读取这些属性。

```typescript
interface Empty { }
const b:Empty = {myProp: 1, anotherProp: 2}; // 正确
b.myProp // 报错
```

上面示例中，变量`b`的类型是空对象，视同`Object`类型，不会有严格字面量检查，但是读取多余的属性会报错。

如果想强制使用没有任何属性的对象，可以采用下面的写法。

```typescript
interface WithoutProperties {
  [key: string]: never;
}

// 报错
const a:WithoutProperties = { prop: 1 };
```

## 接口

> 用来描述对象形状的 interface，值必须是对象.**对象的模板**

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

#### 接口对象属性

接口对象的属性与对象一样，属性索引共有`string`、`number`和`symbol`三种类型。

最多只能定义一个**字符串索引**, 并且其它指定的属性值也要遵守规则

```typescript
interface MyObj {
  [prop: string]: number;

  a: boolean;      // 编译错误，a的值必须是 number 类型
}
```

属性的**数值索引**，其实是指定数组的类型。

```typescript
interface A {
  [prop: number]: string;
}

const obj:A = ['a', 'b', 'c'];
```

如果一个 interface 同时定义了字符串索引和数值索引，那么数值索引必须服从于字符串索引。因为**在 JavaScript 中，数值属性名最终是自动转换成字符串属性名**。

```typescript
interface A {
  [prop: string]: number;
  [prop: number]: string; // 报错
}

interface B {
  [prop: string]: number;
  [prop: number]: number; // 正确
}
```

#### 对象的方法

```typescript
// 写法一
interface A {
  f(x: boolean): string;
}

// 写法二
interface B {
  f: (x: boolean) => string;
}

// 写法三
interface C {
  f: { (x: boolean): string };
}
```

##### 属性名表达式写法

```typescript
const f = 'f';
interface A {
  [f](x: boolean): string;
}
```

##### 方法的重载

```typescript
interface A {
  f(): number;
  f(x: boolean): boolean;
  f(x: string, y: string): string;
}


// 实现
function MyFunc(): number;
function MyFunc(x: boolean): boolean;
function MyFunc(x: string, y: string): string;
function MyFunc(
  x?:boolean|string, y?:string
):number|boolean|string {
  if (x === undefined && y === undefined) return 1;
  if (typeof x === 'boolean' && y === undefined) return true;
  if (typeof x === 'string' && typeof y === 'string') return 'hello';
  throw new Error('wrong parameters');  
}

// 需要额外在对象外部给出函数方法的实现
const a:A = {
  f: MyFunc
}
```

接口除了在对象上使用，interface 也可以用来声明独立的函数。

```typescript
interface Add {
  (x:number, y:number): number;
}

const myAdd:Add = (x,y) => x + y;
```

构造函数中也可以使用,详情见 classs 类章节

```typescript
interface ErrorConstructor {
  new (message?: string): Error;
}
```

#### 接口的继承

interface 可以继承其他类型，主要有下面几种情况。

1. interface 继承 interface

  interface 可以使用`extends`关键字，继承其他 interface。

  ```typescript
  interface Shape {
    name: string;
  }
  
  interface Circle extends Shape {
    radius: number;
    name: boolean;// 报错
  }
  ```

  注意，**子接口与父接口的同名属性必须是类型兼容的**，不能有冲突，否则会报错，多重继承时也是如此。重名时子属性会覆盖父属性

2. interface 继承 type

	```typescript
	type Country = {
	  name: string;
	  capital: string;
	}
	
	interface CountryWithPop extends Country {
	  population: number;
	}
	```

	注意，如果`type`命令定义的类型不是对象，interface 就无法继承

3. interface 继承 class

	interface 还可以继承 class，即继承该类的所有成员

	```typescript
	class A {
	  x:string = '';
	
	  y():boolean {
	    return true;
	  }
	}
	
	interface B extends A {
	  z: number
	}
	```

	实现`B`接口的对象就需要实现这些属性。

	```typescript
	const b:B = {
	  x: '',
	  y: function(){ return true },
	  z: 123
	}
	```

	某些类拥有私有成员和保护成员，interface 可以继承这样的类，但是意义不大。

	```typescript
	class A {
	  private x: string = '';
	  protected y: string = '';
	}
	
	interface B extends A {
	  z: number
	}
	
	// 报错
	const b:B = { /* ... */ }
	
	// 报错
	class C implements B {
	  // ...
	}
	```

	上面示例中，`A`有私有成员和保护成员，`B`继承了`A`，但无法用于对象，因为对象不能实现这些成员。这导致`B`只能用于其他 class，而这时其他 class 与`A`之间不构成父类和子类的关系，使得`x`与`y`无法部署。

	

#### 接口合并

多个同名接口会合并成一个接口。

```typescript
interface Box {
  height: number;
  width: number;
}

interface Box {
  length: number;
}
```

这样的设计主要是为了兼容 JavaScript 的行为。JavaScript 开发者常常对全局对象或者外部库，添加自己的属性和方法。那么，**只要使用 interface 给出这些自定义属性和方法的类型，就能自动跟原始的 interface 合并，使得扩展外部类型非常方便**。

示例 如下：扩展window Document 对象

```typescript
interface Document {
  foo: string;
}

document.foo = 'hello';
```

同名接口合并时，同一个属性如果有多个类型声明，彼此不能有类型冲突。与继承情况类似

关于**接口中同名方法，则会发生重载**

```typescript
interface Cloner {
  clone(animal: Animal): Animal;
}

interface Cloner {
  clone(animal: Sheep): Sheep;
}

interface Cloner {
  clone(animal: Dog): Dog;
  clone(animal: Cat): Cat;
}

// 等同于
interface Cloner {
  clone(animal: Dog): Dog;
  clone(animal: Cat): Cat;
  clone(animal: Sheep): Sheep;
  clone(animal: Animal): Animal;
}
```

​	同名接口合并时，如果同名方法有不同的类型声明，那么会发生函数重载。而且，后面的定义比前面的定义具有更高的优先级。

​	这个规则有一个例外。同名方法之中，如果有一个**参数是字面量类型**，**字面量类型有更高的优先级。**

```typescript
interface A {
  f(x:'foo'): boolean;
}

interface A {
  f(x:any): void;
}

// 等同于
interface A {
  f(x:'foo'): boolean;
  f(x:any): void;
}
```

如果两个 interface 组成的联合类型存在同名属性，那么该属性的类型也是联合类型。

```typescript
interface Circle {
  area: bigint;
}

interface Rectangle {
  area: number;
}

declare const s: Circle | Rectangle;

s.area;   // bigint | number
```

上面示例中，接口`Circle`和`Rectangle`组成一个联合类型`Circle | Rectangle`。因此，这个联合类型的同名属性`area`，也是一个联合类型。

#### interface 与 type 的异同

​	很多对象类型既可以用 interface 表示，也可以用 type 表示。而且，两者往往可以换用，**几乎所有的 interface 命令都可以改写为 type 命令。**

##### **区别**

（1）、**`type`能够表示非对象类型，而`interface`只能表示对象类型（包括数组、函数等**

（2）、**interface`可以继承其他类型，`type 不支持继承。**

继承的主要作用是添加属性，`type`定义的对象类型如果想要添加属性，只能使用`&`运算符，重新定义一个类型。

```typescript
type Animal = {
  name: string
}

type Bear = Animal & {
  honey: boolean
}
```

**type 和 interface 可以相互继承**

**interface 继承 type**

```typescript
type Foo = { x: number; };

interface Bar extends Foo {
  y: number;
}
```

**type 也可以继承 interface**

```typescript
interface Foo {
  x: number;
}

type Bar = Foo & { y: number; };
```

（3）、**同名`interface`会自动合并，同名`type`则会报错。也就是说，TypeScript 不允许使用`type`多次定义同一个类型。**

```typescript
type A = { foo:number }; // 报错
type A = { bar:number }; // 报错
```

作为比较，`interface`则会自动合并。

```typescript
interface A { foo:number };
interface A { bar:number };

const obj:A = {
  foo: 1,
  bar: 1
};
```

（4）、**`interface`不能包含属性映射（mapping），`type`可以**

```typescript
interface Point {
  x: number;
  y: number;
}

// 正确
type PointCopy1 = {
  [Key in keyof Point]: Point[Key];
};

// 报错
interface PointCopy2 {
  [Key in keyof Point]: Point[Key];
};
```

（5）、**this`关键字只能用于`interface**

```typescript
// 正确
interface Foo {
  add(num:number): this;
};

// 报错
type Foo = {
  add(num:number): this;
};
```

**（6）、type 可以扩展原始数据类型，interface 不行。**

```typescript
// 正确
type MyStr = string & {
  type: 'new'
};

// 报错
interface MyStr extends string {
  type: 'new'
}
```

上面示例中，type 可以扩展原始数据类型 string，interface 就不行。

**（7）`interface`无法表达某些复杂类型（比如交叉类型和联合类型），但是`type`可以。**

```typescript
type A = { /* ... */ };
type B = { /* ... */ };

type AorB = A | B;
type AorBwithName = AorB & {
  name: string
};
```

上面示例中，类型`AorB`是一个联合类型，`AorBwithName`则是为`AorB`添加一个属性。这两种运算，`interface`都没法表达。

> **综上所述，如果有复杂的类型运算，那么没有其他选择只能使用`type`；一般情况下，`interface`灵活性比较高，便于扩充类型或自动合并，建议优先使用。**



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

#### 类型复用

```typescript
// 箭头函数写法
const sum :(x:number, y:number) => number = function (x, y):number {
    return x + y;
}

// 类型复用
const plus:typeof sum = function plus(x, y) {
    return x - y;
}
```

#### 对象写法

```typescript
// 对象写法
let sum: {
    (x:number, y:number): number
};
sum = function (x, y) {
  return x + y;
};
```

#### function 类型

Function 类型的函数可以接受任意数量的参数，每个参数的类型都是`any`，返回值的类型也是`any`，代表没有任何约束，所以不建议使用这个类型，给出函数详细的类型声明会更好。

```typescript
function doSomething(f:Function) {
  return f(1, 2, 3);
}
```

#### 箭头函数

```typescript
// 1、箭头函数写法1
const sum :(x:number, y:number) => number = function (x, y) {
    return x + y;
}
// 2、箭头函数写法2
const sum = function (x:number, y:number):number {
    return x + y;
}
// 3、函数为参数
function test(fn: (x:number, y:number) => number):number {
   return fn(a, b);
}
```

map()`方法的参数是一个箭头函数`(name):Person => ({name})

```typescript
type Person = { name: string };

const people = ['alice', 'bob', 'jan'].map(
  (name):Person => ({name})
);
```

此时name的类型省略了，应为可以通过 Person中推断出来

#### 函数的可选参数

如果函数的某个参数可以省略，则在参数名后面加问号表示

此时效果相当于 **x:原始类型|undefined**，但是把这种写法当作可选参数来用就不行了

```typescript
function f(x?:number) {
  // ...
}

f(); // OK
f(10); // OK
```

函数的可选参数只能在参数列表的尾部，跟在必选参数的后面

```typescript
et myFunc:
  (a?:number, b:number) => number; // 报错
```

#### 参数解构

参数解构可以结合类型别名（type 命令）一起使用，代码会看起来简洁一些。

```typescript

function sum(
  { a, b, c }: {
     a: number;
     b: number;
     c: number
  }
) {
  console.log(a + b + c);
}
// 简化
type ABC = { a:number; b:number; c:number };

function sum({ a, b, c }:ABC) {
  console.log(a + b + c);
}
```

#### rest

rest 参数表示函数剩余的所有参数，它可以是数组（剩余参数类型相同），也可能是元组（剩余参数类型不同）

```typescript
// rest 参数为数组
function joinNumbers(...nums:number[]) {
  // ...
}

// rest 参数为元组
function f(...args:[boolean, number]) {
  // ...
}
```

#### 只读参数

如果函数内部不能修改某个参数，可以在函数定义时，在参数类型前面加上`readonly`关键字，表示这是只读参数。

```typescript
function arraySum(
  arr:readonly number[]
) {
  // ...
  arr[0] = 0; // 报错
}
```

#### void 类型

void 返回值类型允许返回 undefined / null

#### 局部类型

局部类型只能在函数内使用, 函数外使用会报错

```typescript
function hello(txt:string) {
  type message = string;
  let newTxt:message = 'hello ' + txt;
  return newTxt;
}

const newTxt:message = hello('world'); // 报错
```

#### 高阶函数

一个函数返回值还是一个函数，我们称这个函数为高阶函数

```typescript
(someValue: number) => (multiplier: number) => someValue * multiplier;
```

#### 函数重载

一个函数接收不同类型参数，并根据参数类型不同会有不同函数行为。执行不同逻辑行为，称**函数重载**

```typescript
function reverse(str:string):string;
function reverse(arr:any[]):any[];
function reverse(
  stringOrArray:string|any[]
):string|any[] {
  if (typeof stringOrArray === 'string')
    return stringOrArray.split('').reverse().join('');
  else
    return stringOrArray.slice().reverse();
}
// 调用
function reverse(str:string):string;
function reverse(arr:any[]):any[];
```

对象的方法也可以使用重载。

```typescript
class StringBuilder {
  #data = '';

  add(num:number): this;
  add(bool:boolean): this;
  add(str:string): this;
  add(value:any): this {
    this.#data += String(value);
    return this;
  }

  toString() {
    return this.#data;
  }
}
```

#### 构造函数

构造函数的最大特点就是必须使用new 命令调用

```typescript
class Animal {
  numLegs:number = 4;
}
// 构造函数类型
type AnimalConstructor = new () => Animal;
// 对象写法
type AnimalConstructor = {
  new (s:string): object;
}

function create(c:AnimalConstructor):Animal {
  return new c();
}
// 传入的Animal就相当于一个构造函数，像内置 Date 对象 Date() 就是构造函数，前面加上new 就是实例化的Date对象了
const a = create(Animal);
```



## 泛型

有些时候，函数返回值的类型与参数类型是相关的。

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

#### 泛型的写法

泛型主要用在四个场合：函数、接口、类和别名。

##### 函数中使用泛型

普通函数

```typescript
function id<T>(arg:T):T {
  return arg;
}
```

变量函数

```typescript
// 写法一
let myId:<T>(arg:T) => T = id;

// 写法二
let myId:{ <T>(arg:T): T } = id;
```

##### 接口中使用泛型

```typescript
interface Box<Type> {
  contents: Type;
}

let box:Box<string>;
```

定义泛型接口，然后使用

写法一

```typescript
interface Comparator<T> {
  compareTo(value:T): number;
}

class Rectangle implements Comparator<Rectangle> {

  compareTo(value:Rectangle): number {
    // ...
  }
}
```

写法二

```typescript
interface Fn {
  <Type>(arg:Type): Type;
}

function id<Type>(arg:Type): Type {
  return arg;
}

let myId:Fn = id;
```

##### 类中使用泛型

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

```typescript
class A<T> {
  value: T;
}

class B extends A<any> {
}
```

注意，泛型类描述的是类的实例，不包括**静态属性和静态方法**，因为这两者定义在类的本身。因此，**它们不能引用类型参数**。

##### 类型别名的泛型写法

```typescript
type Container<T> = { value: T };

const a: Container<number> = { value: 0 };
const b: Container<string> = { value: 'b' };
```

下面是定义树形结构的例子。

```typescript
type Tree<T> = {
  value: T;
  left: Tree<T> | null;
  right: Tree<T> | null;
};
```

类型别名`Tree`内部递归引用了`Tree`自身。

#### 类型参数默认值

类型参数可以设置默认值。使用时，如果没有给出类型参数的值，就会使用默认值。

```typescript
function getFirst<T = string>(
  arr:T[]
):T {
  return arr[0];
}

getFirst([1, 2, 3]) // 正确
```

**一旦类型参数有默认值，就表示它是可选参数**。如果有多个类型参数，可选参数必须在必选参数之后。

```typescript
<T = boolean, U> // 错误

<T, U = boolean> // 正确
```

#### 数组的泛型表示

数组类型有一种表示方法是`Array<T>`。这就是泛型的写法

```typescript
let arr:Array<number> = [1, 2, 3];
```

同样的，如果数组成员都是字符串，那么类型就写成`Array<string>`。事实上，在 TypeScript 内部，**数组类型的另一种写法`number[]`、`string[]`，只是`Array<number>`、`Array<string>`的简写形式。**

其他的 TypeScript 内部数据结构，比如`Map`、`Set`和`Promise`，其实也是泛型接口，完整的写法是`Map<K, V>`、`Set<T>`和`Promise<T>`。

TypeScript 默认还提供一个**`ReadonlyArray<T>`接口，表示只读数组。**

#### 泛型约束

很多类型参数并不是无限制的，对于传入的类型存在约束条件。

```js
interface Girl {
    name: string
}
// T 继承 Girl ,T[] 中内容应满足 Girl 约束
class SelectGirl<T extends Girl> {
    constructor(private girls: T[]) {}
}
// T 继承 string| number 特性
class SelectGirl<T extends string| number> {
    constructor(private girls: T[]) {}
}
```

类型参数可以同时设置约束条件和默认值，前提是默认值必须满足约束条件。

```typescript
type Fn<A extends string, B extends string = 'world'>
  =  [A, B];

type Result = Fn<'hello'> // ["hello", "world"]
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

泛型有一些使用注意点。

**（1）尽量少用泛型。**

泛型虽然灵活，但是会加大代码的复杂性，使其变得难读难写。一般来说，只要使用了泛型，类型声明通常都不太易读，容易写得很复杂。因此，可以不用泛型就不要用。

**（2）类型参数越少越好。**

多一个类型参数，多一道替换步骤，加大复杂性。因此，类型参数越少越好。

**（3）类型参数需要出现两次。**

如果类型参数在定义后只出现一次，那么很可能是不必要的。

**（4）泛型可以嵌套。**

类型参数可以是另一个泛型。

## 类

> 大部分跟js原生类差不多，但是也加入了一些新的东西

- 顶层属性声明如果不写类型，会被推断为any。如果赋值就可以自动推断类型

- 如果ts配置了 strictPropertyInitialization， 不赋值初始值就报错，可以这样写

	```typescript
	class Point {
		x!: number;
		y!: number;
	}
	```

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

### 函数的重载

​	另外，构造方法不能声明返回值类型，否则报错，因为它总是返回实例对象。

```typescript
class Point {
  constructor(x:number, y:string);
  constructor(s:string);
  constructor(xs:number|string, y?:string) {
    // ...
  }
}
```

### 存取器方法

（1）如果某个属性只有`get`方法，没有`set`方法，那么该属性自动成为只读属性。

```typescript
class C {
  _name = '';
  get name() {
    return this._name;
  }
  set name(value) {
    this._name = value;
  }
}
```

（2）TypeScript 5.1 版之前，`set`方法的参数类型，必须兼容`get`方法的返回值类型，否则报错。

```typescript
class C {
  _name = '';
  get name():string {
    return this._name;
  }
  set name(value:number|string) {
    this._name = String(value);
  }
}
```



(3）`get`方法与`set`方法的可访问性必须一致，要么都为公开方法，要么都为私有方法。

### 属性索引

类允许定义属性索引。

```typescript
class MyClass {
  [s:string]: boolean |
    ((s:string) => boolean);

  get(s:string) {
    return this[s] as boolean;
  }
}
```

在类中，索性也覆盖了方法。如果一个对象同时定义了属性索引和方法，那么前者必须包含后者的类型。否则会报错

```typescript
class MyClass {
  [s:string]: boolean | (() => boolean);
  f() {
    return true;
  }
  // 属性存取器视同属性。
  get isInstance() {
    return true;
  }
}
```

### 类的接口

#### implements 关键字

```typescript
interface Country {
  name:string;
  capital:string;
}
// 或者
type Country = {
  name:string;
  capital:string;
}

class MyCountry implements Country {
  name = '';
  capital = '';
}
```

上面示例中，`interface`或`type`都可以定义一个对象类型。类`MyCountry`使用`implements`关键字，表示**该类的实例对象满足这个外部类型**。

```typescript
interface A {
  get(name:string): boolean;
}

class B implements A {
  get(s) { // s 的类型是 any
    return true;
  }
}
```

​	interface 只是指定检查条件，如果不满足这些条件就会报错。它并不能代替 class 自身的类型声明。

```typescript
class Car {
  id:number = 1;
  move():void {};
}

class MyCar implements Car {
  id = 2; // 不可省略
  move():void {};   // 不可省略
}
```

`implements`关键字后面，不仅可以是接口，也可以是另一个类。这时，后面的类将被当作接口。

#### 实现多个接口

1. 类的继承，继承一个类，这个类实现多个接口

	```typescript
	class Car implements MotorVehicle {
	}
	
	class SecretCar extends Car implements Flyable, Swimmable {
	}
	```

	

2. 接口继承,实现一个接口，这个接口继承多个接口

	```typescript
	interface MotorVehicle {
	  // ...
	}
	interface Flyable {
	  // ...
	}
	interface Swimmable {
	  // ...
	}
	
	interface SuperCar extends MotoVehicle,Flyable, Swimmable {
	  // ...
	}
	
	class SecretCar implements SuperCar {
	  // ...
	}
	```

注意，发生多重实现时（即一个接口同时实现多个接口），不同接口不能有互相冲突的属性。

#### 类与接口的合并

**TypeScript 不允许两个同名的类，但是如果一个类和一个接口同名，那么接口会被合并进类。**

```typescript
class A {
  x:number = 1;
}

interface A {
  y:number;
}

let a = new A();
a.y = 10;

a.x // 1
a.y // 10
```

上面示例中，类`A`与接口`A`同名，后者会被合并进前者的类型定义



### Class 类型

#### 实例类型

TypeScript 的**类本身就是一种类型**，但是**它代表该类的实例类型**，而不是 class 的自身类型。

```typescript
class Color {
  name:string;

  constructor(name:string) {
    this.name = name;
  }
}

const green:Color = new Color('green');
```

当类继承接口时，实例类型可以是 类|接口

```typescript
interface MotorVehicle {
}

class Car implements MotorVehicle {
}

// 写法一
const c1:Car = new Car();
// 写法二
const c2:MotorVehicle = new Car();
```

#### 类自身的类型

要获得一个类的自身类型，一个简便的方法就是使用 **typeof** 运算符。

```typescript
class Point {
  x:number;
  y:number;

  constructor(x:number, y:number) {
    this.x = x;
    this.y = y;
  }
}

// 错误,Point描述的是实例类型，而不是 Class 的自身类型。
function createPoint(
  PointClass:Point,// 此处的类型应该是  typeof Point
  x: number,
  y: number
) {
  return new PointClass(x, y);
}
```

#### 结构类型原则

​	Class 也遵循“结构类型原则”。一个对象只要满足 Class 的实例结构，就跟该 Class 属于同一个类型。

```typescript
class Foo {
  id!:number;
}

function fn(arg:Foo) {
  // ...
}

const bar = {
  id: 10,
  amount: 100,
};

fn(bar); // 正确
```

对象`bar`满足类`Foo`的实例结构，只是多了一个属性`amount`。所以，它可以当作参数，传入函数`fn()`。

​	总之，只要 A 类具有 B 类的结构，哪怕还有额外的属性和方法，TypeScript 也认为 A 兼容 B 的类型。不仅是类，如果某个对象跟某个 class 的实例结构相同，TypeScript 也认为两者的类型相同。

```typescript
class Person {
  name: string;
}

const obj = { name: 'John' };
const p:Person = obj; // 正确
```

这种情况，运算符`instanceof`不适用于判断某个对象是否跟某个 class 属于同一类型。

```typescript
obj instanceof Person // false
```

**空类**不包含任何成员，任何其他类都可以看作与空类结构相同。因此，凡是类型为空类的地方，所有类（包括对象）都可以使用。

```typescript
class Empty {}

function fn(x:Empty) {
  // ...
}

fn({});
fn(window);
fn(fn);
```

**注意，确定两个类的兼容关系时，只检查实例成员，不考虑静态成员和构造方法。**

```typescript
class Point {
  x: number;
  y: number;
  static t: number;
  constructor(x:number) {}
}

class Position {
  x: number;
  y: number;
  z: number;
  constructor(x:string) {}
}

const point:Point = new Position('');
```

如果**类中存在私有成员（private）或保护成员（protected）**，那么确定兼容关系时，TypeScript **要求私有成员和保护成员来自同一个类，这意味着两个类需要存在继承关系。**

```typescript
// 情况一
class A {
  private name = 'a';
}

class B extends A {
}

const a:A = new B();

// 情况二
class A {
  protected name = 'a';
}

class B extends A {
  protected name = 'b';
}

const a:A = new B();
```

上面示例中，`A`和`B`都有私有成员（或保护成员）`name`，这时只有在`B`继承`A`的情况下（`class B extends A`），`B`才兼容`A`。

#### 类的继承

​	类（这里又称“子类”）可以使用 extends 关键字继承另一个类（这里又称“基类”）的所有属性和方法。

```typescript
class A {
  greet() {
    console.log('Hello, world!');
  }
}

class B extends A {
}

const b = new B();
b.greet() // "Hello, world!"
```

基类（父类）也可以做为 子类的类型

```typescript
const a:A = b;
a.greet()
```

子类可以覆盖基类的同名方法。

```typescript
class B extends A {
  // 子类中的geet参数要跟父类能兼容，这里父类可以不传，子类重写方法要兼容
  greet(name?: string) {
    if (name === undefined) {
      super.greet();
    } else {
      console.log(`Hello, ${name}`);
    }
  }
}
```

类属性未声明初始值，**declare 修饰符**

```typescript
interface Animal {
  animalStuff: any;
}

interface Dog extends Animal {
  dogStuff: any;
}

class AnimalHouse {
  resident: Animal;

  constructor(animal:Animal) {
    this.resident = animal;
  }
}

class DogHouse extends AnimalHouse {
  // 此处resident未设置初始值和赋值操作，会报错。添加declare即可，表示赋值操作在基类（父类中）进行
  declare resident: Dog;

  constructor(dog:Dog) {
    super(dog);
  }
}

const dog = {
  animalStuff: 'animal',
  dogStuff: 'dog'
};

const dogHouse = new DogHouse(dog);

console.log(dogHouse.resident) // undefined
```

#### 可访问修饰符

`public`、`private`和`protected`。

这三个修饰符的位置，都写在属性或方法的最前面。

- public

	表示这是公开成员，**外部可以自由访问**。public 修饰符是默认修饰符，如果省略不写，实际上就带有该修饰符。

- ### protected

	`protected`修饰符表示该成员是保护成员，**只能在类的内部使用该成员**，实例无法使用该成员，但是**子类内部可以使用**。

- ### private

	`private`修饰符表示私有成员，**只能用在当前类的内部**，类的实例和子类都不能使用该成员。

注意，子类不能定义父类私有成员的同名成员。

#### 实例属性简写形式

```typescript
class A {
  constructor(
    public a: number,
    protected b: number,
    private c: number,
    readonly d: number
  ) {}
}

// 编译结果
class A {
    a;
    b;
    c;
    d;
    constructor(a, b, c, d) {
      this.a = a;
      this.b = b;
      this.c = c;
      this.d = d;
    }
}
```

#### 静态成员

类的内部可以使用`static`关键字，定义静态成员。

静态成员是只能通过类本身使用的成员。

```typescript
class MyClass {
  static x = 0;
  static printX() {
    console.log(MyClass.x);
  }
}

MyClass.x // 0
MyClass.printX() // 0
```

静态私有属性也可以用 ES6 语法的`#`前缀表示，`public`和`protected`的静态成员可以被继承。而私有属性成员不会

```typescript
class MyClass {
    private static x = 0;

    static x = 0;
}
```

#### 泛型类

类也可以写成泛型，使用类型参数。

```typescript
class Box<Type> {
  contents: Type;

  constructor(value:Type) {
    this.contents = value;
  }
}
// 左边b的类型可以省略，右边可以推到出来
const b:Box<string> = new Box('hello!');
```

注意，静态成员不能使用泛型的类型参数。



#### 抽象类，抽象成员

> 抽象类只能当作基类使用，用来在它的基础上定义子类
>
> 抽象类中有抽象方法，继承抽象类必须实现抽象方法。

```js

abstract class Girl {
	abstract skill();
}

class BaseTacher extends Girl {
    skill() {
        
    }
}
```

注意

（1）抽象成员只能存在于抽象类，不能存在于普通类。

（2）抽象成员不能有具体实现的代码。也就是说，已经实现好的成员前面不能加`abstract`关键字。

（3）抽象成员前也不能有`private`修饰符，否则无法在子类中实现该成员。

（4）一个子类最多只能继承一个抽象类。

#### this问题

类的方法经常用到`this`关键字，它表示该方法当前所在的对象。

有些场合需要给出`this`类型，但是 JavaScript 函数通常不带有`this`参数，这时 TypeScript 允许函数增加一个名为`this`的参数，放在参数列表的第一位，用来描述函数内部的`this`关键字的类型。

```typescript
// 编译前
function fn(
  this: SomeType,
  x: number
) {
  /* ... */
}

// 编译后
function fn(x) {
  /* ... */
}
```

注意，`this`类型不允许应用于静态成员。

```typescript
class A {
  static a:this; // 报错
}
```

## 断言

对于没有类型声明的值，TypeScript 会进行类型推断，很多时候得到的结果，未必是开发者想要的。

```typescript
type T = 'a'|'b'|'c';
let foo = 'a';

let bar:T = foo; // 报错

let foo = 'a' as 'a'; // 或
let bar:T = foo as 'a'
```

### 类型断言有两种语法。

```typescript
// 语法一：<类型>值
<Type>value

// 语法二：值 as 类型
value as Type
```

上面两种语法是等价的，`value`表示值，`Type`表示类型。早期只有语法一，后来因为 TypeScript 开始支持 React 的 JSX 语法（尖括号表示 HTML 元素），为了避免两者冲突，就引入了语法二。目前，推荐使用语法二。

```typescript
// 语法一
let bar:T = <T>foo;

// 语法二
let bar:T = foo as T;
```

类型断言的一大用处是，指定 unknown 类型的变量的具体类型。

```typescript
const value:unknown = 'Hello World';

const s1:string = value; // 报错
const s2:string = value as string; // 正确
```

另外，类型断言也适合指定联合类型的值的具体类型。

```typescript
const s1:number|string = 'hello';
const s2:number = s1 as number;
```

### 类型断言的条件

类型断言并不意味着，可以把某个值断言为任意类型。

```typescript
const n = 1;
const m:string = n as string; // 报错
```

上面示例中，变量`n`是数值，无法把它断言成字符串，TypeScript 会报错。

类型断言的使用前提是，值的实际类型与断言的类型必须满足一个条件。

```typescript
expr as T
```

上面代码中，`expr`是实际的值，`T`是类型断言，它们必须满足下面的条件：**`expr`是`T`的子类型，或者`T`是`expr`的子类型。**

如果真的要断言成一个完全无关的类型，也是可以做到的。

```typescript
// 或者写成 <T><unknown>expr
expr as unknown as T
```

实例

```typescript
const n = 1;
const m:string = n as unknown as string; // 正确
```

### as const 断言

如果没有声明变量类型，let 命令声明的变量，会被类型推断为 TypeScript 内置的基本类型之一；**const 命令声明的变量，则被推断为值类型常量**。

```typescript
// 类型推断为基本类型 string
let s1 = 'JavaScript';

// 类型推断为字符串 “JavaScript”
const s2 = 'JavaScript';
```

有些时候，let 变量会出现一些意想不到的报错，变更成 const 变量就能消除报错。

```typescript
let s = 'JavaScript';

type Lang =
  |'JavaScript'
  |'TypeScript'
  |'Python';

function setLang(language:Lang) {
  /* ... */
}

setLang(s); // 报错
```

**使用了`as const`断言以后，let 变量就不能再改变值了。**

```typescript
let s = 'JavaScript' as const;
s = 'Python'; // 报错
```

注意，`as const`断言只能用于字面量，不能用于变量。

```typescript
let s = 'JavaScript';
setLang(s as const); // 报错
```

另外，`as const`也不能用于表达式。

```typescript
let s = ('Java' + 'Script') as const; // 报错
```

`as const`断言可以用于整个对象，也可以用于对象的单个属性，这时它的类型缩小效果是不一样的。

```typescript
const v1 = {
  x: 1,
  y: 2,
}; // 类型是 { x: number; y: number; }

const v2 = {
  x: 1 as const,
  y: 2,
}; // 类型是 { x: 1; y: number; }

const v3 = {
  x: 1,
  y: 2,
} as const; // 类型是 { readonly x: 1; readonly y: 2; }
```

由于`as const`会将数组变成只读元组，所以很适合用于函数的 rest 参数。

```typescript
function add(x:number, y:number) {
  return x + y;
}

const nums = [1, 2];
const total = add(...nums); // 报错
```

上面示例中，变量`nums`的类型推断为`number[]`，导致使用扩展运算符`...`传入函数`add()`会报错，因为`add()`只能接受两个参数，而`...nums`并不能保证参数的个数。

解决方法就是使用`as const`断言，将数组变成元组。

```typescript
const nums = [1, 2] as const;
const total = add(...nums); // 正确
```

使用`as const`断言后，变量`nums`的类型会被推断为`readonly [1, 2]`，使用扩展运算符展开后，正好符合函数`add()`的参数类型。

### 非空断言

对于那些可能为空的变量（即可能等于`undefined`或`null`），TypeScript 提供了非空断言，保证这些变量不会为空，写法是在变量名后面加上感叹号`!`。

```typescript
function f(x?:number|null) {
  validateNumber(x); // 自定义函数，确保 x 是数值
  console.log(x!.toFixed());
}

function validateNumber(e?:number|null) {
  if (typeof e !== 'number')
    throw new Error('Not a number');
}
```

上面示例中，函数`f()`的参数`x`的类型是`number|null`，即可能为空。如果为空，就不存在`x.toFixed()`方法，这样写会报错。但是，开发者可以确认，经过`validateNumber()`的前置检验，变量`x`肯定不会为空，这时就可以使用非空断言，为函数体内部的变量`x`加上后缀`!`，`x!.toFixed()`编译就不会报错了。

**class 中属性未赋值初始值时会报错，此时就可以使用 ！ 去除报错**

```typescript
class Point {
  x!:number; // 正确
  y!:number; // 正确

  constructor(x:number, y:number) {
    // ...
  }
}
```

另外，非空断言只有在打开编译选项`strictNullChecks`时才有意义。如果不打开这个选项，编译器就不会检查某个变量是否可能为`undefined`或`null`。

### 断言函数

断言函数是一种特殊函数，用于保证函数参数符合某种类型。如果函数参数达不到要求，就会抛出错误，中断程序执行；如果达到要求，就不进行任何操作，让代码按照正常流程运行。

```typescript
function isString(value:unknown):void {
  if (typeof value !== 'string')
    throw new Error('Not a string');
}
```

上面示例中，函数`isString()`就是一个断言函数，用来保证参数`value`是一个字符串，否则就会抛出错误，中断程序的执行。

下面是它的用法。

```typescript
function toUpper(x: string|number) {
  isString(x);
  return x.toUpperCase();
}
```

为了更清晰地表达断言函数，TypeScript 3.7 引入了新的类型写法。

```typescript
function isString(value:unknown):asserts value is string {
  if (typeof value !== 'string')
    throw new Error('Not a string');
}
```

上面示例中，函数`isString()`的返回值类型写成`asserts value is string`，其中**`asserts`和`is`都是关键词，`value`是函数的参数名，`string`是函数参数的预期类型**。它的意思是，该函数用来断言参数`value`的类型是`string`，如果达不到要求，程序就会在这里中断。

另外，**断言函数的`asserts`语句等同于`void`类型，所以如果返回除了`undefined`和`null`以外的值，都会报错。**

```typescript
function isString(value:unknown):asserts value is string {
  if (typeof value !== 'string')
    throw new Error('Not a string');
  return true; // 报错
}
```

如果要将断言函数用于函数表达式，可以采用下面的写法。

```typescript
// 写法一
const assertIsNumber = (
  value:unknown
):asserts value is number => {
  if (typeof value !== 'number')
    throw Error('Not a number');
};

// 写法二
type AssertIsNumber =
  (value:unknown) => asserts value is number;

const assertIsNumber:AssertIsNumber = (value) => {
  if (typeof value !== 'number')
    throw Error('Not a number');
};
```



## 模块

任何包含 import 或 export 语句的文件，就是一个模块（module）。相应地，如果文件不包含 export 语句，就是一个全局的脚本文件。

如果一个文件不包含 export 语句，但是希望把它当作一个模块（即内部变量对外不可见），可以在脚本头部添加一行语句。

```js
export {};
```

上面这行语句不产生任何实际作用，但会让当前文件被当作模块处理，所有它的代码都变成了内部代码。

### import type 语句

```typescript
// a.ts
export interface A {
  foo: string;
}

export let a = 123;

// b.ts
import { A, a } from './a';
```

这样很**不利于区分类型和正常接口**，容易造成混淆。为了解决这个问题，TypeScript 引入了两个解决方法。

第一个方法是在 import 语句输入的类型前面加上`type`关键字。

```typescript
import { type A, a } from './a';
```

上面示例中，import 语句输入的类型`A`前面有`type`关键字，表示这是一个类型。

**第二个方法是使用 import type 语句，这个语句只用来输入类型**，不用来输入正常接口。

```typescript
// 正确
import type { A } from './a';
let b:A = 'hello';

// 报错
import type { a } from './a';
let b = a;
```

同样的，export 语句也有两种方法，表示输出的是类型。

```typescript
type A = 'a';
type B = 'b';

// 方法一
export {type A, type B};

// 方法二
export type {A, B};
```

mport type 语句也可以输入默认类型。

```typescript
import type DefaultType from 'moduleA';
```

import type 在一个名称空间下，输入所有类型的写法如下。

```typescript
import type * as TypeNS from 'moduleA';
```

### importsNotUsedAsValues 编译设置

TypeScript 特有的输入类型（type）的 import 语句，编译成 JavaScript 时怎么处理呢？

TypeScript 提供了`importsNotUsedAsValues`编译设置项，有三个可能的值。

（1）`remove`：这是默认值，自动删除输入类型的 import 语句。

（2）`preserve`：保留输入类型的 import 语句。

（3）`error`：保留输入类型的 import 语句（与`preserve`相同），但是必须写成`import type`的形式，否则报错。

### CommonJs模块

CommonJS 是 Node.js 的专用模块格式，与 ES 模块格式不兼容。

#### import = 语句

TypeScript 使用`import =`语句输入 CommonJS 模块。

```typescript
import fs = require('fs');
const code = fs.readFileSync('hello.ts', 'utf8');
```

上面示例中，使用`import =`语句和`require()`命令输入了一个 CommonJS 模块。模块本身的用法跟 Node.js 是一样的。

除了使用`import =`语句，TypeScript 还允许使用**import * as [接口名] from "模块文件"**输入 CommonJS 模块。

```typescript
import * as fs from 'fs';
// 等同于
import fs = require('fs');
```

#### export = 语句

TypeScript 使用`export =`语句，输出 CommonJS 模块的对象，等同于 CommonJS 的`module.exports`对象。

```typescript
let obj = { foo: 123 };

export = obj;
```

`export =`语句输出的对象，只能使用`import =`语句加载。

```typescript
import obj = require('./a');

console.log(obj.foo); // 123
```

### Classic 方法

Classic 方法以当前脚本的路径作为“基准路径”，计算相对模块的位置。

比如，脚本`a.ts`里面有一行代码`import { b } from "./b"`，那么 TypeScript 就会在`a.ts`所在的目录，查找`b.ts`和`b.d.ts`。

至于非相对模块，也是以当前脚本的路径作为起点，一层层查找上级目录。

比如，脚本`a.ts`里面有一行代码`import { b } from "b"`，那么就会依次在每一级上层目录里面，查找`b.ts`和`b.d.ts`。

### Node 方法

Node 方法就是模拟 Node.js 的模块加载方法，也就是`require()`的实现方法。

相对模块依然是以当前脚本的路径作为“基准路径”。比如，脚本文件`a.ts`里面有一行代码`let x = require("./b");`，TypeScript 按照以下顺序查找。

1. 当前目录是否包含`b.ts`、`b.tsx`、`b.d.ts`。如果不存在就执行下一步。
2. 当前目录是否存在子目录`b`，该子目录里面的`package.json`文件是否有`types`字段指定了模块入口文件。如果不存在就执行下一步。
3. 当前目录的子目录`b`是否包含`index.ts`、`index.tsx`、`index.d.ts`。如果不存在就报错。

非相对模块则是以当前脚本的路径作为起点，逐级向上层目录查找是否存在子目录`node_modules`。比如，脚本文件`a.js`有一行`let x = require("b");`，TypeScript 按照以下顺序进行查找。

1. 当前目录的子目录`node_modules`是否包含`b.ts`、`b.tsx`、`b.d.ts`。
2. 当前目录的子目录`node_modules`，是否存在文件`package.json`，该文件的`types`字段是否指定了入口文件，如果是的就加载该文件。
3. 当前目录的子目录`node_modules`里面，是否包含子目录`@types`，在该目录中查找文件`b.d.ts`。
4. 当前目录的子目录`node_modules`里面，是否包含子目录`b`，在该目录中查找`index.ts`、`index.tsx`、`index.d.ts`。
5. 进入上一层目录，重复上面4步，直到找到为止。

### 路径映射

TypeScript 允许开发者在`tsconfig.json`文件里面，手动指定脚本模块的路径。

（1）baseUrl

`baseUrl`字段可以手动指定脚本模块的基准目录。

```json
{
  "compilerOptions": {
    "baseUrl": "."
  }
}
```

上面示例中，`baseUrl`是一个点，表示基准目录就是`tsconfig.json`所在的目录。

（2）paths

`paths`字段指定非相对路径的模块与实际脚本的映射。

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "jquery": ["node_modules/jquery/dist/jquery"]
    }
  }
}
```

上面示例中，加载模块`jquery`时，实际加载的脚本是`node_modules/jquery/dist/jquery`，它的位置要根据`baseUrl`字段计算得到。

注意，上例的`jquery`属性的值是一个数组，可以指定多个路径。如果第一个脚本路径不存在，那么就加载第二个路径，以此类推。

（3）rootDirs

`rootDirs`字段指定模块定位时必须查找的其他目录。

```json
{
  "compilerOptions": {
    "rootDirs": ["src/zh", "src/de", "src/#{locale}"]
  }
}
```

上面示例中，`rootDirs`指定了模块定位时，需要查找的不同的国际化目录。



## 装饰器

装饰器（Decorator）是一种语法结构，用来在定义时修改类（class）的行为。

在语法上，装饰器有如下几个特征。

（1）第一个字符（或者说前缀）是`@`，后面是一个表达式。

（2）`@`后面的表达式，必须是一个函数（或者执行后可以得到一个函数）。

（3）这个函数接受所修饰对象的一些相关值作为参数。

（4）这个函数要么不返回值，要么返回一个新对象取代所修饰的目标对象

装饰器有多种形式，基本上只要在`@`符号后面添加表达式都是可以的。下面都是合法的装饰器。

```typescript
@myFunc
@myFuncFactory(arg1, arg2)

@libraryModule.prop
@someObj.method(123)

@(wrap(dict['prop']))
```

注意，`@`后面的表达式，最终执行后得到的应该是一个函数

```typescript
function simpleDecorator(
  value:any,
  context:any
) {
  console.log(`hi, this is ${context.kind} ${context.name}`);
  return value;
}


@simpleDecorator
class A {} // "hi， this is class A"
```

装饰器函数的两个参数

- `value`：所装饰的对象。
- `context`：上下文对象，TypeScript 提供一个原生接口`ClassMethodDecoratorContext`，描述这个对象。

**context 对象属性**

（1）`kind`：字符串，表示所装饰对象的类型，可能取以下的值。

- 'class'
- 'method'
- 'getter'
- 'setter'
- 'field'
- 'accessor'

这表示一共有六种类型的装饰器。

（2）`name`：字符串或者 Symbol 值，所装饰对象的名字，比如类名、属性名等。

（3）`addInitializer()`：函数，用来添加类的初始化逻辑。以前，这些逻辑通常放在构造函数里面，对方法进行初始化，现在改成以函数形式传入`addInitializer()`方法。注意，`addInitializer()`没有返回值。

（4）`private`：布尔值，表示所装饰的对象是否为类的私有成员。

（5）`static`：布尔值，表示所装饰的对象是否为类的静态成员。

（6）`access`：一个对象，包含了某个值的 get 和 set 方法。

### 类装饰器

类装饰器的类型描述如下。

```typescript
type ClassDecorator = (
  value: Function,
  context: {
    kind: 'class';
    name: string | undefined;
    addInitializer(initializer: () => void): void;
  }
) => Function | void;
```

类装饰器接受两个参数：`value`（当前类本身）和`context`（上下文对象）。其中，`context`对象的`kind`属性固定为字符串`class`。addInitializer方法为类添加初始化函数

类装饰器可以返回一个函数，替代当前类的构造方法。

```typescript
function countInstances(value:any, context:any) {
  let instanceCount = 0;

  const wrapper = function (...args:any[]) {
    instanceCount++;
    const instance = new value(...args);
    instance.count = instanceCount;
    return instance;
  } as unknown as typeof MyClass;

  wrapper.prototype = value.prototype; // A
  return wrapper;
}

@countInstances
class MyClass {}

const inst1 = new MyClass();
inst1 instanceof MyClass // true
inst1.count // 1
```

新的构造方法实现了实例的计数，每新建一个实例，计数器就会加一，并且对实例添加`count`属性，表示当前实例的编号。

类装饰器也可以返回一个新的类，替代原来所装饰的类。

```typescript
function countInstances(value:any, context:any) {
  let instanceCount = 0;

  return class extends value {
    constructor(...args:any[]) {
      super(...args);
      instanceCount++;
      this.count = instanceCount;
    }
  };
}

@countInstances
class MyClass {}

const inst1 = new MyClass();
inst1 instanceof MyClass // true
inst1.count // 1
```

上面示例中，`@countInstances`返回一个`MyClass`的子类。

下面的例子是通过类装饰器，禁止使用`new`命令新建类的实例。

```typescript
function functionCallable(
  value:any, {kind}:any
):any {
  if (kind === 'class') {
    return function (...args:any) {
      if (new.target !== undefined) {
        throw new TypeError('This function can’t be new-invoked');
      }
      return new value(...args);
    }
  }
}

@functionCallable
class Person {
  name:string;
  constructor(name:string) {
    this.name = name;
  }
}

// @ts-ignore
const robin = Person('Robin');
robin.name // 'Robin'
```

### 方法装饰器

方法装饰器用来装饰类的方法（method）。它的类型描述如下。

```typescript
type ClassMethodDecorator = (
  value: Function,
  context: {
    kind: 'method';
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { get: () => unknown };
    addInitializer(initializer: () => void): void;
  }
) => Function | void;
```

根据上面的类型，方法装饰器是一个函数，接受两个参数：`value`和`context`。

参数`value`是方法本身，参数`context`是上下文对象，有以下属性。

- `kind`：值固定为字符串`method`，表示当前为方法装饰器。
- `name`：所装饰的方法名，类型为字符串或 Symbol 值。
- `static`：布尔值，表示是否为静态方法。该属性为只读属性。
- `private`：布尔值，表示是否为私有方法。该属性为只读属性。
- `access`：对象，包含了方法的存取器，但是只有`get()`方法用来取值，没有`set()`方法进行赋值。
- `addInitializer()`：为方法增加初始化函数。

方法装饰器会改写类的原始方法，实质等同于下面的操作。跟切面编程很像，可以给方法加上前置后置方法

```typescript
function trace(decoratedMethod) {
  // ...
}

class C {
  @trace
  toString() {
    return 'C';
  }
}

// `@trace` 等同于
// C.prototype.toString = trace(C.prototype.toString);
```

如果 trace 函数中返回一个新的函数，则替换 toString()

利用方法装饰器，可以将类的方法变成延迟执行。

```typescript
function delay(milliseconds: number = 0) {
  return function (value, context) {
    if (context.kind === "method") {
      return function (...args: any[]) {
        setTimeout(() => {
          value.apply(this, args);
        }, milliseconds);
      };
    }
  };
}

class Logger {
  @delay(1000)
  log(msg: string) {
    console.log(`${msg}`);
  }
}

let logger = new Logger();
logger.log("Hello World");
```

上面示例中，方法装饰器`@delay(1000)`将方法`log()`的执行推迟了1秒（1000毫秒）。

```typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
    // greet() 绑定 this
    this.greet = this.greet.bind(this);
  }
  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}

const g = new Person('张三').greet;
g() // "Hello, my name is 张三."
```

上面例子中，类`Person`的构造方法内部，将`this`与`greet()`方法进行了绑定。如果没有这一行，将`greet()`赋值给变量`g`进行调用，就会报错了。

`this`的绑定必须放在构造方法里面，因为这必须在类的初始化阶段完成。现在，它可以移到方法装饰器的`addInitializer()`里面。

```typescript
function bound(
  originalMethod:any, context:ClassMethodDecoratorContext
) {
  const methodName = context.name;
  if (context.private) {
    throw new Error(`不能绑定私有方法 ${methodName as string}`);
  }
  context.addInitializer(function () {
    this[methodName] = this[methodName].bind(this);
  });
}
```



### 属性装饰器

属性装饰器用来装饰定义在类顶部的属性（field）。它的类型描述如下。只在类初始化时生效后续不会触发

```typescript
type ClassFieldDecorator = (
  value: undefined,
  context: {
    kind: 'field';// 注意此处标识不是 attribute、property
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { get: () => unknown, set: (value: unknown) => void };
    addInitializer(initializer: () => void): void;
  }
) => (initialValue: unknown) => unknown | void;
```

属性装饰器要么**不返回值**，要么**返回一个函数**，该**函数会自动执行**，用来对所装饰属性进行初始化。该**函数的参数**是所装饰属性的**初始值**，该**函数的返回值**是该属性的**最终值**。

```typescript
function logged(value, context) {
  const { kind, name } = context;
  if (kind === 'field') {
    return function (initialValue) {
      console.log(`initializing ${name} with value ${initialValue}`);
      return initialValue;
    };
  }
}

class Color {
  @logged name = 'green';
}

const color = new Color();
// "initializing name with value green"
```

### getter 装饰器，setter 装饰器

getter 装饰器和 setter 装饰器，是分别针对类的取值器（getter）和存值器（setter）的装饰器。它们的类型描述如下。

```typescript
type ClassGetterDecorator = (
  value: Function,
  context: {
    kind: 'getter';
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { get: () => unknown };
    addInitializer(initializer: () => void): void;
  }
) => Function | void;

type ClassSetterDecorator = (
  value: Function,
  context: {
    kind: 'setter';
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { set: (value: unknown) => void };
    addInitializer(initializer: () => void): void;
  }
) => Function | void;
```

注意，getter 装饰器的上下文对象`context`的`access`属性，只包含`get()`方法；setter 装饰器的`access`属性，只包含`set()`方法。

这两个装饰器要么不返回值，要么返回一个函数，取代原来的取值器或存值器。

下面的例子是将取值器的结果，保存为一个属性，加快后面的读取。

```typescript
class C {
  @lazy
  get value() {
    console.log('正在计算……');
    return '开销大的计算结果';
  }
}

function lazy(
  value:any,
  {kind, name}:any
) {
  if (kind === 'getter') {
    return function (this:any) {
      const result = value.call(this);
      Object.defineProperty(
        this, name,
        {
          value: result,
          writable: false,
        }
      );
      return result;
    };
  }
  return;
}

const inst = new C();
inst.value
// 正在计算……
// '开销大的计算结果'
inst.value
// '开销大的计算结果'
```

### accessor 装饰器

装饰器语法引入了一个新的属性修饰符`accessor`。

```typescript
class C {
  accessor x = 1;
}
```

accessor 装饰器的类型如下。

```typescript
type ClassAutoAccessorDecorator = (
  value: {
    get: () => unknown;
    set: (value: unknown) => void;
  },
  context: {
    kind: "accessor";
    name: string | symbol;
    access: { get(): unknown, set(value: unknown): void };
    static: boolean;
    private: boolean;
    addInitializer(initializer: () => void): void;
  }
) => {
  get?: () => unknown;
  set?: (value: unknown) => void;
  init?: (initialValue: unknown) => unknown;
} | void;
```

`init()`方法，用来改变私有属性的初始值。

上面的代码等同于下面的代码。

```typescript
class C {
  #x = 1;

  get x() {
    return this.#x;
  }

  set x(val) {
    this.#x = val;
  }
}
```

`accessor`也可以与静态属性和私有属性一起使用。





### 装饰器执行顺序

装饰器的执行分为两个阶段。

（1）评估（evaluation）：计算`@`符号后面的表达式的值，得到的应该是函数。

（2）应用（application）：将评估装饰器后得到的函数，应用于所装饰对象。

```typescript
function d(str:string) {
  console.log(`评估 @d(): ${str}`);
  return (
    value:any, context:any
  ) => console.log(`应用 @d(): ${str}`);
}

function log(str:string) {
  console.log(str);
  return str;
}

@d('类装饰器')
class T {
  @d('静态属性装饰器')
  static staticField = log('静态属性值');

  @d('原型方法')
  [log('计算方法名')]() {}

  @d('实例属性')
  instanceField = log('实例属性值');

  @d('静态方法装饰器')
  static fn(){}
}
```

上面示例中，类`T`有四种装饰器：类装饰器、静态属性装饰器、方法装饰器、属性装饰器。

它的运行结果如下。

```
评估 @d(): 类装饰器
评估 @d(): 静态属性装饰器
评估 @d(): 原型方法
计算方法名
评估 @d(): 实例属性
评估 @d(): 静态方法装饰器

应用 @d(): 静态方法装饰器
应用 @d(): 原型方法
应用 @d(): 静态属性装饰器
应用 @d(): 实例属性
应用 @d(): 类装饰器
静态属性值
```



## declare

declare 关键字用来告诉编译器，某个类型是存在的，可以在当前文件中使用。

declare 关键字可以描述以下类型。

- 变量（const、let、var 命令声明）
- type 或者 interface 命令声明的类型
- class
- enum
- 函数（function）
- 模块（module）
- 命名空间（namespace）

> declare 只能用来描述已经存在的变量和数据结构，不能用来声明新的变量和数据结构。另外，所有 declare 语句都不会出现在编译后的文件里面。

### declare variable

declare 关键字可以给出外部变量的类型描述。

举例来说，当前脚本使用了其他脚本定义的全局变量`x`。

```js
x = 123; // 报错
```

上面示例中，变量`x`是其他脚本定义的，当前脚本不知道它的类型，编译器就会报错。

这时使用 declare 命令给出它的类型，就不会报错了。

```typescript
declare let x:number;
x = 1;
```

如果没有指定类型就默认为any

注意，declare 关键字只用来给出类型描述，是纯的类型代码，不允许设置变量的初始值，即不能涉及值。

```typescript
// 报错
declare let x:number = 1;
```



### declare function

declare 关键字可以给出外部函数的类型描述。

```typescript
declare function sayHello(
  name:string
):void;

sayHello('张三');
```

上面示例中，declare 命令给出了`sayHello()`的类型描述，因此可以直接使用它。

注意，这种单独的函数类型声明语句，只能用于`declare`命令后面。一方面，TypeScript 不支持单独的函数类型声明语句；另一方面，declare 关键字后面也不能带有函数的具体实现。

```typescript
// 报错
function sayHello(
  name:string
):void;
function sayHello(name) {
  return '你好，' + name;
}
```

### declare class

declare 给出 class 类型描述的写法如下。

```typescript
declare class Animal {
  constructor(name:string);
  eat():void;
  sleep():void;
}
```

同样的，declare 后面不能给出 Class 的具体实现或初始值。

### declare module，declare namespace

如果想把变量、函数、类组织在一起，可以将 declare 与 module 或 namespace 一起使用。

```typescript
declare namespace AnimalLib {
  class Animal {
    constructor(name:string);
    eat():void;
    sleep():void;
  }

  type Animals = 'Fish' | 'Dog';
}

// 或者
declare module AnimalLib {
  class Animal {
    constructor(name:string);
    eat(): void;
    sleep(): void;
  }

  type Animals = 'Fish' | 'Dog';
}
```

declare module 和 declare namespace 里面，加不加 export 关键字都可以。

**declare 关键字的另一个用途，是为外部模块添加属性和方法时，给出新增部分的类型描述。**

```typescript
import { Foo as Bar } from 'moduleA';

declare module 'moduleA' {
  interface Foo {
    custom: {
      prop1: string;
    }
  }
}
```



### declare global

如果要**为 JavaScript 引擎的原生对象添加属性和方法**，可以使用`declare global {}`语法。

```typescript
export {};

declare global {
  interface String {
    toSmallString(): string;
  }
}

String.prototype.toSmallString = ():string => {
  // 具体实现
  return '';
};
```

### declare enum

declare 关键字给出 enum 类型描述的例子如下，下面的写法都是允许的。

```typescript
declare enum E1 {
  A,
  B,
}

declare enum E2 {
  A = 0,
  B = 1,
}

declare const enum E3 {
  A,
  B,
}

declare const enum E4 {
  A = 0,
  B = 1,
}
```



## 命名空间

> 避免全局污染
>
> 方便拓展：多个同名的 namespace 会自动合并，这一点跟 interface 一样。
>
> 注意：合并时同名成员会导致报错

```js
export namespace Home {
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

## d.ts类型声明文件

​	单独使用的模块，一般会同时提供一个单独的类型声明文件（declaration file），把本模块的外部接口的所有类型都写在这个文件里面，便于模块使用者了解接口，也便于编译器检查使用者的用法是否正确。

举例来说，有一个模块的代码如下。

```typescript
const maxInterval = 12;

function getArrayLength(arr) {
  return arr.length;
}

module.exports = {
  getArrayLength,
  maxInterval,
};
```

它的类型声明文件可以写成下面这样。

```typescript
export function getArrayLength(arr: any[]): number;
export const maxInterval: 12;
```



下面是一个如何使用类型声明文件的简单例子。有一个类型声明文件`types.d.ts`。

```typescript
// types.d.ts
export interface Character {
  catchphrase?: string;
  name: string;
}
```

然后，就可以在 TypeScript 脚本里面导入该文件声明的类型。

```typescript
// index.ts
import { Character } from "./types";

export const character:Character = {
  catchphrase: "Yee-haw!",
  name: "Sandy Cheeks",
};
```

类型声明文件也可以包括在项目的 tsconfig.json 文件里面，这样的话，编译器打包项目时，会自动将类型声明文件加入编译，而不必在每个脚本里面加载类型声明文件。比如，moment 模块的类型声明文件是`moment.d.ts`，使用 moment 模块的项目可以将其加入项目的 tsconfig.json 文件。

```json
{
  "compilerOptions": {},
  "files": [
    "src/index.ts",
    "typings/moment.d.ts"
  ]
}
```

### 类型声明文件的来源

类型声明文件主要有以下三种来源。

- TypeScript 编译器自动生成。
- TypeScript 内置类型文件。
- 外部模块的类型声明文件，需要自己安装。



#### **自动生成**

只要使用编译选项`declaration`，编译器就会在编译时自动生成单独的类型声明文件。

下面是在`tsconfig.json`文件里面，打开这个选项。

```typescript
{
  "compilerOptions": {
    "declaration": true
  }
}
```

你也可以在命令行打开这个选项。

```shell
$ tsc --declaration
```

#### 内置声明文件

安装 TypeScript 语言时，会同时安装一些内置的类型声明文件，主要是内置的全局对象（JavaScript 语言接口和运行环境 API）的类型声明。

这些内置声明文件位于 TypeScript 语言安装目录的`lib`文件夹内，数量大概有几十个，下面是其中一些主要文件。

- lib.d.ts
- lib.dom.d.ts
- lib.es2015.d.ts
- lib.es2016.d.ts
- lib.es2017.d.ts
- lib.es2018.d.ts
- lib.es2019.d.ts
- lib.es2020.d.ts
- lib.es5.d.ts
- lib.es6.d.ts

这些内置声明文件的文件名统一为“lib.[description].d.ts”的形式，其中`description`部分描述了文件内容。比如，`lib.dom.d.ts`这个文件就描述了 DOM 结构的类型。

如果开发者想了解全局对象的类型接口（比如 ES6 全局对象的类型），那么就可以去查看这些内置声明文件。

TypeScript 编译器会自动根据编译目标`target`的值，加载对应的内置声明文件，所以不需要特别的配置。但是，**可以使用编译选项`lib`，指定加载哪些内置声明文件。**

```json
{
  "compilerOptions": {
    "lib": ["dom", "es2021"]
  }
}
```

上面示例中，`lib`选项指定加载`dom`和`es2021`这两个内置类型声明文件。

编译选项`noLib`会禁止加载任何内置声明文件。

#### 外部类型声明文件

如果项目中使用了外部的某个第三方代码库，那么就需要这个库的类型声明文件。

这时又分成三种情况。

（1）这个库自带了类型声明文件。

比如`moment`这个库就自带`moment.d.ts`。使用这个库可能需要单独加载它的类型声明文件。

（2）这个库没有自带，但是可以找到社区制作的类型声明文件。

第三方库如果没有提供类型声明文件，社区往往会提供。TypeScript 社区主要使用 [DefinitelyTyped 仓库](https://github.com/DefinitelyTyped/DefinitelyTyped)，各种类型声明文件都会提交到那里，已经包含了几千个第三方库。

（3）找不到类型声明文件，需要自己写。

比如，使用 jQuery 的脚本可以写成下面这样。

```typescript
declare var $:any

// 或者
declare type JQuery = any;
declare var $:JQuery;
```

上面代码表示，jQuery 的`$`对象是外部引入的，类型是`any`，也就是 TypeScript 不用对它进行类型检查。

也可以采用下面的写法，将整个外部模块的类型设为`any`。

```typescript
declare module '模块名';
```

有了上面的命令，指定模块的所有接口都将视为`any`类型。

### 三斜杠命令

如果类型声明文件的内容非常多，可以拆分成多个文件，然后入口文件使用三斜杠命令，加载其他拆分后的文件。

举例来说，入口文件是`main.d.ts`，里面的接口定义在`interfaces.d.ts`，函数定义在`functions.d.ts`。那么，`main.d.ts`里面可以用三斜杠命令，加载后面两个文件。

```
/// <reference path="./interfaces.d.ts" />
/// <reference path="./functions.d.ts" />
```

三斜杠命令主要包含三个参数，代表三种不同的命令。

- path

	`/// <reference path="" />`是最常见的三斜杠命令，告诉编译器在编译时需要包括的文件，常用来声明当前脚本依赖的类型文件。

- types

	types 参数用来告诉编译器当前脚本依赖某个 DefinitelyTyped 类型库，通常安装在`node_modules/@types`目录。

- lib

	`/// <reference lib="..." />`命令允许脚本文件显式包含内置 lib 库，等同于在`tsconfig.json`文件里面使用`lib`属性指定 lib 库。



## ts类型运算符

### keyof 运算符

keyof 是一个单目运算符，接受一个对象类型作为参数，返回该对象的所有键名组成的联合类型。

```typescript
type MyObj = {
  foo: number,
  bar: string,
};

type Keys = keyof MyObj; // 'foo'|'bar'
```

由于 JavaScript 对象的键名只有三种类型，所以对于任意对象的键名的联合类型就是`string|number|symbol`。

```typescript
// string | number | symbol
type KeyT = keyof any;
```

对于没有自定义键名的类型使用 keyof 运算符，返回`never`类型，表示不可能有这样类型的键名。

对于联合类型，keyof 返回成员共有的键名。

```typescript
type A = { a: string; z: boolean };
type B = { b: string; z: boolean };

// 返回 'z'
type KeyT = keyof (A | B);
```

keyof 运算符往往用于精确表达对象的属性类型。

**举例来说，取出对象的某个指定属性的值，JavaScript 版本可以写成下面这样。**

```ty
function prop(obj, key) {
  return obj[key];
}
```

上面这个函数添加类型，只能写成下面这样。

```typescript
function prop(
  obj: { [p:string]: any },
  key: string
):any {
  return obj[key];
}
```

上面的类型声明有两个问题，一是无法表示参数`key`与参数`obj`之间的关系，二是返回值类型只能写成`any`。

有了 keyof 以后，就可以解决这两个问题，精确表达返回值类型。

```typescript
function prop<Obj, K extends keyof Obj>(
  obj:Obj, key:K
):Obj[K] {
  return obj[key];
}
```

keyof 的另一个用途是用于属性映射，即将一个类型的所有属性逐一映射成其他值。

```typescript
type NewProps<Obj> = {
  [Prop in keyof Obj]: boolean;
};

// 用法
type MyObj = { foo: number; };

// 等于 { foo: boolean; }
type NewObj = NewProps<MyObj>;
```

上面示例中，类型`NewProps`是类型`Obj`的映射类型，前者继承了后者的所有属性，但是把所有属性值类型都改成了`boolean`。

下面的例子是去掉 readonly 修饰符。

```typescript
type Mutable<Obj> = {
  -readonly [Prop in keyof Obj]: Obj[Prop];
};
```

对应地，还有`+readonly`的写法，表示添加只读属性设置。

### in 运算符

```typescript
const obj = { a: 123 };

if ('a' in obj)
  console.log('found a');
```

上面示例中，`in`运算符用来判断对象`obj`是否包含属性`a`。

`in`运算符的左侧是一个字符串，表示属性名，右侧是一个对象。它的返回值是一个布尔值。

TypeScript 语言的类型运算中，`in`运算符有不同的用法，用来取出（遍历）联合类型的每一个成员类型。

```typescript
type U = 'a'|'b'|'c';

type Foo = {
  [Prop in U]: number;
};
// 等同于
type Foo = {
  a: number,
  b: number,
  c: number
};
```

上面示例中，`[Prop in U]`表示依次取出联合类型`U`的每一个成员。

### 方括号运算符

方括号运算符（`[]`）用于取出对象的 **键值类型**，比如`T[K]`会返回对象`T`的属性`K`的类型。

```typescript
type Person = {
  age: number;
  name: string;
  alive: boolean;
};

// Age 的类型是 number
type Age = Person['age'];
```

上面示例中，`Person['age']`返回属性`age`的类型，本例是`number`。

方括号的参数如果是联合类型，那么返回的也是联合类型。

```typescript
type Person = {
  age: number;
  name: string;
  alive: boolean;
};

// number|string
type T = Person['age'|'name'];

// number|string|boolean
type A = Person[keyof Person];
```

**如果访问不存在的属性，会报错。**

方括号运算符的参数也可以是属性名的索引类型。

```typescript
type Obj = {
  [key:string]: number,
};

// number
type T = Obj[string];
```

上面示例中，`Obj`的属性名是字符串的索引类型，所以可以写成**`Obj[string]`，代表所有字符串属性名**，返回的就是它们的类型`number`。

### extends...?: 条件运算符

条件运算符`extends...?:`可以根据当前类型是否符合某种条件，返回不同的类型。

```typescript
T extends U ? X : Y
```

上面式子中判断`T`是否为`U`的子类型，这里的`T`和`U`可以是任意类型。成立 结果类型为X，否则结果类型为 Y

如果需要判断的类型是一个联合类型，那么条件运算符会展开这个联合类型。返回新的联合类型

```typescript
(A|B) extends U ? X : Y

// 等同于

(A extends U ? X : Y) |
(B extends U ? X : Y)
```

如果不希望联合类型被条件运算符展开，可以把`extends`两侧的操作数都放在方括号里面。

```typescript
// 示例一
type ToArray<Type> =
  Type extends any ? Type[] : never;

// string[]|number[]
type T = ToArray<string|number>;

// 示例二
type ToArray<Type> =
  [Type] extends [any] ? Type[] : never;

// (string | number)[]
type T = ToArray<string|number>;
```

### infer 关键字

`infer`关键字用来定义泛型里面推断出来的类型参数，而不是外部传入的类型参数。

它通常跟条件运算符一起使用，用在`extends`关键字后面的父类型之中。

```typescript
type Flatten<Type> =
  Type extends Array<infer Item> ? Item : Type;
```

上面示例中，

`infer Item`表示`Item`这个参数是 TypeScript 自己推断出来的，不用显式传入

而`Flatten<Type>`则表示`Type`这个类型参数是外部传入的。

`Type extends Array<infer Item>`则表示，如果参数`Type`是一个数组，那么就将该数组的成员类型推断为`Item`，即`Item`是从`Type`推断出来的。

```typescript
// string
type Str = Flatten<string[]>;

// number
type Num = Flatten<number>;
```

### is 运算符

`is`运算符用来描述返回值属于`true`还是`false`。

```typescript
function isFish(
  pet: Fish|Bird
):pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

上面示例中，函数`isFish()`的返回值类型为`pet is Fish`，表示如果参数`pet`类型为`Fish`，则返回`true`，否则返回`false`。



`is`运算符可以用于类型保护。

```typescript
function isCat(a:any): a is Cat {
  return a.name === 'kitty';
}

let x:Cat|Dog;

if (isCat(x)) {
  x.meow(); // 正确，因为 x 肯定是 Cat 类型
}
```



`is`运算符还有一种特殊用法，就是用在类（class）的内部，描述类的方法的返回值。

```typescript
class Teacher {
  isStudent():this is Student {
    return false;
  }
}

class Student {
  isStudent():this is Student {
    return true;
  }
}
```

上面示例中，`isStudent()`方法的返回值类型，取决于该方法内部的`this`是否为`Student`对象。如果是的，就返回布尔值`true`，否则返回`false`。

**注意，`this is T`这种写法，只能用来描述方法的返回值类型，而不能用来描述属性的类型。**

### 模板字符串

模板字符串的最大特点，就是内部可以引用其他类型。

```typescript
type World = "world";

// "hello world"
type Greeting = `hello ${World}`;
```

上面示例中，类型`Greeting`是一个模板字符串，里面引用了另一个字符串类型`world`，因此`Greeting`实际上是字符串`hello world`。

**注意，模板字符串可以引用的类型一共6种，分别是 string、number、bigint、boolean、null、undefined。引用这6种以外的类型会报错。**

### satisfies 运算符

举例来说，有一个对象的属性名拼写错误。

```typescript
const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
  bleu: [0, 0, 255] // 属性名拼写错误
};
```

上面示例中，对象`palette`的属性名拼写错了，将`blue`拼成了`bleu`，我们希望通过指定类型，发现这个错误。

```typescript
type Colors = "red" | "green" | "blue";
type RGB = [number, number, number];

const palette: Record<Colors, string|RGB> = {
  red: [255, 0, 0],
  green: "#00ff00",
  bleu: [0, 0, 255] // 报错
};
```

上面示例中，变量`palette`的类型被指定为`Record<Colors, string|RGB>`，这是一个类型工具，用来返回一个对象

这样的写法，虽然可以发现属性名的拼写错误，但是带来了新的问题。

```typescript
const greenComponent = palette.green.substring(1, 6); // 报错
```

这时就可以使用`satisfies`运算符，对`palette`进行类型检测，但是不改变 TypeScript 对`palette`的类型推断。

```typescript
type Colors = "red" | "green" | "blue";
type RGB = [number, number, number];

const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
  bleu: [0, 0, 255] // 报错
} satisfies Record<Colors, string|RGB>;

const greenComponent = palette.green.substring(1); // 不报错
```

上面示例中，变量`palette`的值后面增加了`satisfies Record<Colors, string|RGB>`，表示该值必须满足`Record<Colors, string|RGB>`这个条件，所以能够检测出属性名`bleu`的拼写错误。同时，它不会改变`palette`的类型推断，所以，TypeScript 知道`palette.green`是一个字符串，对其调用`substring()`方法就不会报错。

`satisfies`也可以检测属性值。

```typescript
const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
  blue: [0, 0] // 报错
} satisfies Record<Colors, string|RGB>;
```

上面示例中，属性`blue`的值只有两个成员，不符合元组`RGB`必须有三个成员的条件，从而报错了。

## 类型映射

映射（mapping）指的是，将一种类型按照映射规则，转换成另一种类型，通常用于对象类型。

举例来说，现有一个类型`A`和另一个类型`B`。

```typescript
type A = {
  foo: number;
  bar: number;
};

type B = {
  foo: string;
  bar: string;
};
```

上面示例中，这两个类型的属性结构是一样的，但是属性的类型不一样。如果属性数量多的话，逐个写起来就很麻烦。

使用类型映射，就可以从类型`A`得到类型`B`。

```typescript
type A = {
  foo: number;
  bar: number;
};

type B = {
  [prop in keyof A]: string;
};
```

上面示例中，类型`B`采用了属性名索引的写法，`[prop in keyof A]`表示依次得到类型`A`的所有属性名，然后将每个属性的类型改成`string`。

在语法上，`[prop in keyof A]`是一个属性名表达式，表示这里的属性名需要计算得到。具体的计算规则如下：

- `prop`：属性名变量，名字可以随便起。
- `in`：运算符，用来取出右侧的联合类型的每一个成员。
- `keyof A`：返回类型`A`的每一个属性名，组成一个联合类型。



新版本中还能修改映射的键名，过滤属性及联合类型映射等

## 类型工具

TypeScript 提供了一些内置的类型工具，用来方便地处理各种类型，以及生成新的类型。

TypeScript 内置了17个类型工具，可以直接使用。

- [`Awaited`](https://wangdoc.com/typescript/utility#awaitedtype)
- [`ConstructorParameters`](https://wangdoc.com/typescript/utility#constructorparameterstype)
- [`Exclude`](https://wangdoc.com/typescript/utility#excludeuniontype-excludedmembers)
- [`Extract`](https://wangdoc.com/typescript/utility#extracttype-union)
- [`InstanceType`](https://wangdoc.com/typescript/utility#instancetypetype)
- [`NonNullable`](https://wangdoc.com/typescript/utility#nonnullabletype)
- [`Omit`](https://wangdoc.com/typescript/utility#omittype-keys)
- [`OmitThisParameter`](https://wangdoc.com/typescript/utility#omitthisparametertype)
- [`Parameters`](https://wangdoc.com/typescript/utility#parameterstype)
- [`Partial`](https://wangdoc.com/typescript/utility#partialtype)
- [`Pick`](https://wangdoc.com/typescript/utility#picktype-keys)
- [`Readonly`](https://wangdoc.com/typescript/utility#readonlytype)
- [`Record`](https://wangdoc.com/typescript/utility#recordkeys-type)
- [`Required`](https://wangdoc.com/typescript/utility#requiredtype)
- [`ReadonlyArray`](https://wangdoc.com/typescript/utility#readonlyarraytype)
- [`ReturnType`](https://wangdoc.com/typescript/utility#returntypetype)
- [`ThisParameterType`](https://wangdoc.com/typescript/utility#thisparametertypetype)
- [`ThisType`](https://wangdoc.com/typescript/utility#thistypetype)
- 字符串类型工具
	- [`Uppercase`](https://wangdoc.com/typescript/utility#uppercasestringtype)
	- [`Lowercase`](https://wangdoc.com/typescript/utility#lowercasestringtype)
	- [`Capitalize`](https://wangdoc.com/typescript/utility#capitalizestringtype)
	- [`Uncapitalize`](https://wangdoc.com/typescript/utility#uncapitalizestringtype)



## ts注释指令

TypeScript 接受一些注释指令。

所谓“注释指令”，指的是采用 JS 双斜杠注释的形式，向编译器发出的命令。







## ts.config.js

> `tsconfig.json`是 TypeScript 项目的配置文件，放在项目的根目录。反过来说，如果一个目录里面有`tsconfig.json`，TypeScript 就认为这是项目的根目录。
>
> 如果项目源码是 JavaScript，但是想用 TypeScript 处理，那么配置文件的名字是`jsconfig.json`，它跟`tsconfig`的写法是一样的。
>
> [中文文档](https://www.tslang.cn/docs/handbook/compiler-options.html)

> [ts中文文档](https://www.tslang.cn/docs/handbook/interfaces.html)

#### 编译

```shell
npm install -g typescript
```

- **typescript 模块**	可以编译 ts 为 js代码。

```
tsc file1.ts file2.ts 
--outFile app.js 将多个ts 编译为一个 app.js 文件
--outDir dist  编译结果输出位置
--noEmitOnError  编译错误时不生成编译产物
--noImplicitAny 自动类型推断为any时就报错
--strictNullChecks  undefined和null就不能赋值给其他类型的变量（除了any类型和unknown类型）
```

ts.confg.js

```js
{
  "files": ["file1.ts", "file2.ts"],
  "compilerOptions": {
  	"outFile": "dist/app.js",
    // "strictNullChecks": true
  }
}
```

有了这个配置文件，编译时直接调用`tsc`命令就可以了。

```
tsc
```

- **ts-node 模块**

可以，直接编译

- 类型推断问题

	ts 会根据值自动类型推断，当无法推断出来类型时则会认为是 any 类型。



### [在线测试](https://www.typescriptlang.org/zh/play?noImplicitAny=false&strictNullChecks=false#code/PTAEiJrQ5+MU3NAYlUAqBPADgUwMoGMBOBLFAF1EBR7QYb9AudUBh-gKBFEE34wejNAAOUCo5QTb9ADeUAXjQbPlAaP6BMxSqAwJUDVcoBiVQGbagAzlEqTLgLFAIW6BFf0CbXoCAGGnTCAKg0D3yoFO5E4CDNQNBygSDlAs56BO00Dw+oH95QBSugYO1AWP-79oUAFpQdkBYOUAseUAWD0B5dUBvuUBVeUAcOUB-SNU5BXRsfCJQVUAGdXtdegCgtkBleUAYrLSlTOJAW+jAVZtANz1AODlAaojALrkJXPzfIvZXQDi5QGlbQFXo4MAYFUqMlVABQEXowHxzQHylQF+A1UBMm0BG710aLAB7ADsAZ2IAQ32AUQAPE4BbFAAbNAA1E-wTgCNH0ABeUAAiAASaHu912oAA6rscPcACZ-HYHQ67R4AOlBAHMABRnK63B7PV54D6PACUenopkAnQ6Ab8VAFBy9lUoEAOASALk9ANHygFwCUCANCNmYAG00A36kcqgmQBhcoBjyMAL2aALE1AM6K-RqgAAo1SAQ-lAPYG2nh9G5gFAAwAESoATNIYgGy5QAXNoApxIagBQ5QAA+oBpzTcVEALqZscgUVSANDlADKugF+EwB6Ov0uNtHsR9rc0ABGABcx3w+3RP1AAHIwzc0EmANwIo7ItBo3ZY1ORklZkOgfYAVxuiYjKIATFmaGWcIco7t3gArNBYYi-ADeAF9G4QcEg+zQ-H4W4mANoAXSzA9AWBOhCwAAtQJi0FGzkgSaBx5Pl4jc-msWgyQPs0jURjMS2S3oAGYV-Y9vAHUDotCEF73TESSjfY0AANzQHBDwnSc9hzO8C0xJMTiTJ9j0IdccF2AB3cs0Bw84cEwnBEIIoiUMXRt6Eratfh-P8TgAp8m1-UBdl2LAowAeU7bteyg49nzYqNQAjGhr18WDbzze82KwFFBN2JjmOIWMYxHPB40TJNY0zPRY0TN8YTQZ8NLQGFG1kxNKxBRtX3fQhP32UBDirbFhOo94IIAH1jDT0QAGlAd5gKrTycB89T4wPcdoL8PBn0xUBCEUXZn1AE4fm+X4-g8iC-lAAAyAqkpStL3ky34U1CiCUP449JxwX8KxwJyMoAaiCrNjyXYFDjQUB4sxZL0FS9LQAAQiy-5cpwfKvK8kqRrKiapqqm4wtqo96qnJqWtAAA5ariJOA8OsO9aIMxd4yW60Bev6rbtsawhmqcgADAASPsTgHL73gHN7YtAa9xObCNExcm5EIjJN-KTOtyJvM97xwCMmLsj8vzo5B0CGxRhKTA50y8iMvJHCtLzqvxDiwvA13XPH0GivRurEmhhv6gBBRNHvS9yjv8oHgucyL0TZssTmjbn+yBk5hLrPxBePYWkwjNWkzZyTkYQyWmIlwiuJ4nt51nb6owjQLhbrAcFxoV4cBRFAK0OBm+ytwKONAABmAcny1+CsXtksgA)



## tsc 命令

tsc 是 TypeScript 官方的命令行编译器，用来检查代码，并将其编译成 JavaScript 代码。

tsc 默认使用当前目录下的配置文件`tsconfig.json`，但也可以接受独立的命令行参数。命令行参数会覆盖`tsconfig.json`，比如命令行指定了所要编译的文件，那么 tsc 就会忽略`tsconfig.json`的`files`属性。

[在线文档](https://wangdoc.com/typescript/tsc)