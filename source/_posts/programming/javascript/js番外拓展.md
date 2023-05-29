---
title: js拓展
date: 2022-06-17 08:23:10
tags: 拓展
categories: js
---

## 1、发展史



###  5大主流浏览器

| 名称    | 内核         |
| ------- | ------------ |
| IE      | trident      |
| chrome  | webkit blink |
| safari  | webkit       |
| firefox | gecko        |
| opera   | presto       |

### 历史事件



## 2、js相关概念

<!--more-->

### ECMA

> European Computer Manufacturers Association（欧洲计算机制造联合会）
>
> 评估、开发、认可 电信、计算机标准
>
> ECMA - 262 （标准清单的第262行）  脚本语言的规范及标准
>
> 像 es5、6、7...都是对脚本语言制定的规范

### 编译型与解释型语言

> 编译型：源码 -> 编译器 -> 机器语言 -> 可执行文件 
>
> 解释型：源码 ->解释器 -> 解析一行，执行一行
>
> 解释型语言不需要根据不同平台进行移植，只要有解释器就能执行
>
> 编译型语言需要特定的编译环境，但运行速度比解释型语言快一些，适用于大型程序，大量数据处理等场景
>
> JAVA类似于混合型
>
> .java -> javac  -> .class  -> JVM解释执行 

### 脚本语言

> -> 脚本引擎 -> 解释器
>
> javaScript、Jscript、vbscript 客户端脚本
>
> php 服务端脚本

### 单线程多线程

> js引擎是单线程
>
> 模拟多线程：通过轮转时间片
>
> 将多个任务，切分成多个任务碎片，然后随机排列组成队列，按照这个队列将任务碎片送进JS进程，再按顺序执行任务碎片（多个任务之间反复横跳）

### 获取全局对象方式

```js
//浏览器 web
window 、 this  、frames 、self
//node
global
//worker
self
//通用 以上环境均可拿到全局对象
globalThis



// The one and only way of getting global scope in all environments
//在所有环境中获得全局范围的唯一方法
// https://stackoverflow.com/q/3277182/1008999
var _global = typeof window === 'object' && window.window === window
  ? window : typeof self === 'object' && self.self === self
  ? self : typeof global === 'object' && global.global === global
  ? global
  : this
```



### this指向

> 函数中this的指向，谁调用方法，函数默认 this 就指向谁
>
> 严格模式下 函数默认 this 为 undefined
>
> 1、隐式绑定this，也就是全局this->window
> 2、预编译函数this->window
> 3、显示绑定：谁调用this指向谁
> 4、apply/call 强绑定修改this
> 5、箭头函数this指向父级this
> 6、构造函数this指向实例化对象

```js
function test () {
   'use strick'
 
    return this;
}

test();// this -> undefined

winidow.test();// this -> window

```

(1）第一段代码，在foo()函数体内采用严格模式并使用了this.a，this会指向undefined；第二段代码foo()在严格模式下调用，this不会指向undefined
 （2）决定 this 绑定对象的并不是调用位置是否处于严格模式，而是函数体是否处于严格模式。如果函数体处于严格模式，this 会被绑定到 undefined，否则this 会被绑定到全局对象

```js
// 片段1
function foo() {
    "use strict";  
    console.log( this.a );      //调用this.a的函数体是严格模式
}
var a = 2;
foo();      // 8z5toyO:13 Uncaught TypeError: Cannot read property 'a' of undefined




// 片段2
function foo() {
    console.log( this.a );       //调用this.a的函数体是非严格模式
}

var a = 2;
(function(){
    "use strict";
    foo();      // 2  调用foo()的位置是严格模式
})();
```



### call 、apply、bind

```js
//call 多个参数依次传递
let obj = {
    a:'obj-a',
    getA(a,b,c){
        console.log(this.a,a,b,c);
    }
}

obj.getA(1,2,3);//obj-a 1 2 3

obj.getA.call({a:'call-a'},1,2,3);//call-a 1 2 3

//apply 多个参数传数组对象
obj.getA.apply({a:'apply-a'},[1,2,3]);//apply-a 1 2 3

//bind  多个参数依次传递、返回新的函数，不会立即执行（多次.bind()只会生效一次）
let test2 = obj.getA.bind({a:'bind-a'},1,2,3);
test2();//bind-a 1 2 3
```

### 获取窗口大小

```js
let pageWidth = window.innerWidth, 
    pageHeight = window.innerHeight; 
//不管是在最外层 window 上使用，还是在窗格<frame>中使用。innerWidth 和 innerHeight 返回浏览器窗口中页面视口的大小（不包含浏览器边框和工具栏）。
if (typeof pageWidth != "number") { 
    //检查页面是否处于标准模式
    if (document.compatMode == "CSS1Compat"){ 
            pageWidth = document.documentElement.clientWidth; 
            pageHeight = document.documentElement.clientHeight; 
    } else { 
            pageWidth = document.body.clientWidth; 
            pageHeight = document.body.clientHeight;
    } 
}
```

### 地址栏参数处理

```js
// URLSearchParams

方法： 
 append
 delete
 get
 getAll
 has
 set
 sort
 keys
 values
 forEach
 toString
 entries

 //https://www.baidu.com/s?ie=UTF-8&wd=保护视力
let searchParams = new URLSearchParams(location.search);

function print(){
    for(const param of searchParams.entries()) {
        console.log('参数：',param);
    }
}
print();
 //["ie", "UTF-8"]
 //["wd", "保护视力"]

1、append

searchParams.append('name','www');
print();
//["ie", "UTF-8"]
//["wd", "保护视力"]
//["name", "www"]

2、delete

searchParams.delete('ie');
print();
//["wd", "保护视力"]
//["name", "www"]

3、get

searchParams.get('name');// www 不存在则返回 null

4、getAll

searchParams.getAll('name'); // ["www"]  不存在则返回 []

5、has

searchParams.has('names');// false 存在则返回true

6、
```

### web (World wide web)

```js
web（World Wide Web）即全球广域网，也称为万维网，它是一种基于超文本和HTTP的、全球性的、动态交互的、跨平台的分布式图形信息系统。
```

### web开发概念

```js
b/s（浏览器和服务器） 就是 web开发
c/s（客户端和服务器） 就是 客户端开发
```

### H5

```js
	时代迫切要求一个统一的互联网通用标准。HTML之前，由于个浏览器之间的不统一，光是修改Web浏览器之间兼容性而引起的bug就浪费了大量时间。而H5的目标就是将Web带入一个成熟的应用平台，在html5平台上，视频，音频，图像，动画，一级同电脑交互都被标准化。
```

### 旧时代的网页三剑客

```js
-fireworks
	主要负责作图，与ps功能基本相同，也算是竞争对手，在使用上比ps更简单，2005年被Adobe收购
    
-Dreamweaver
	编辑器，可以拖动控件生成页面

 -Flash
	不仅代表工具还代表了动画，但由于技术过时，不安全和更新繁琐，Adobe公司放弃了它。后被H5取代了
```



### 面向对象 OOP

> 面向对象的三大特性：继承、多态、

``` js
	继承 ：子类继承父类的属性和方法，通过extends关键字
		private 私有关键字 加上这个关键字的属性和方法。只能被自己访问，不能被子类及其他类访问
	
		public 公共关键字  加上这个关键字的属性和方法。可以被自己、子类及其他类访问
		
		protected 加上这个关键字的属性和方法。只能被自己和子类访问
	
	多态：
		父类中有的方法，可以被子类继承并重写。不同子类可以重写相同父类方法，执行不同的结果。（子类）
		
	
	封装：
		成员私有化
		
		
意义：数据结构化
	
	 程序结构化（顺序、判断、循环），是为了更好的管理数据
	 
	 程序复用
	 
	 
```

### 面向对象/面向过程编程

``` js
 面向过程
 	实现把大象装进冰箱的方法
 	function 大象装入冰箱(){
 		打开冰箱；
 		大象装入；
 		关闭冰箱
 	}
 	大象装入冰箱();
 	
 面向对象
 	实现把大象装进冰箱的方法
 	class 冰箱 {
 		constructor(){
 			this.门 = 冰箱门
 		}
 		开门(){
 			...
 		}
 		关门(){
 			...
 		}
 		装东西(){
 			...
 		}
 	}
 	
 	const 冰箱实例 = new 冰箱();
 	冰箱实例.开门();
 	冰箱实例.装东西('大象');
 	冰箱实例.关门();
 	
```

### 高阶函数

> 定义：接收一个函数作为参数，或返回一个函数 的函数 称之为高阶函数
>
> 高阶函数就是操作函数的函数，它接收一个或多个函数作为参数并返回一个新函数(犀牛书)
>
> 例如：数组的map,reduce方法，就是高阶函数

### 柯里化

> 将一个接收多个参数的函数，转化为接收一个参数的函数

```js
优点：、、
1、参数复用

 正常正则验证字符串 reg.test(txt)

 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后  
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
//【第一个参数可以被复用】

2、提前确认
var on = function(element, event, handler) {
    if (document.addEventListener) {
        if (element && event && handler) {
            element.addEventListener(event, handler, false);
        }
    } else {
        if (element && event && handler) {
            element.attachEvent('on' + event, handler);
        }
    }
}
// Currying后  
var on = function(isSupport, element, event, handler) {
    isSupport = isSupport || document.addEventListener;
    if (isSupport) {
        return element.addEventListener(event, handler, false);
    } else {
        return element.attachEvent('on' + event, handler);
    }
}

3、延迟调用
Function.prototype.bind = function (context) {
    var _this = this
    var args = Array.prototype.slice.call(arguments, 1)
 
    return function() {
        return _this.apply(context, args)
    }
}
//像我们js中经常使用的bind，实现的机制就是Currying.


经典案例
 add(1)(2)(3) = 6;
 add(1,2,3)(4) = 10;
 add(1)(2)(3)(4)(5) = 15;

function add(){
    let args = Array.from(arguments);
    let _add = function(){
        args.push(...arguments);
        
        return _add
    }
    _add.toString = function(){
        return args.reduce((a,b)=> a+b,0)
    }
    return _add
}

相关文章 https://www.jianshu.com/p/2975c25e4d71


Function.method('curry', function(){
	var args = arguments, that = this;
    return function(){
		return that.apply(null, [...args,...argugemts]);
    }
})
```



### 闭包

> 匿名函数与IIFE（自执行函数）不是闭包。
>
> 定义：能够读取到函数内部变量的函数
>
> 优点：保持对函数内部变量的引用，避免被回收机制回收
>
> 缺点：IE中可能导致内存泄漏。所以在退出函数前要将不使用的局部变量删除

### 立即执行函数

> IIFE .除了使用括号，还可以使用 + - = ！等运算符
>
> (（）=>{}() )
>
> (()=>{})()
>
> !()=>{}()
>
> 优点：构建一个新的函数作用域，避免全局污染

### 纯函数

```js
简单来说，一个函数的返回值只依赖于它的参数，并且执行过程中没有任何副作用。

1、只依赖它的参数

const a = 1;
const foo = (a)=>a + b;

foo(a);

foo不是纯函数，因为它依赖外部的a

2、执行过程没有副作用

const a = 1;
const obj = {x:2};

const foo = (obj,a){
	obj.x += 4;
    return obj.x + a;
}
foo(obj,a);

foo不是纯函数，应为它执行过程中改变了 obj的属性x的值
```

### 惰性函数

```js
惰性函数执行时的分支，只会在第一次调用时执行。之后会被进入的分支中的代码所代替

例如：dom监听事件的方法
function bindEvent(type, ele, fn){
	if(ele.addEventListener){
		ele.addEventListener(type, fn, false);
	}else if(ele.attachEvent){
        ele.attachEvent('on'+type, fn);
    }else{
        ele['on'+type] = fn;
    }
}
改写成惰性函数 (特性：只执行一次)
1、首先对document进行能力检测，利用闭包在页面加载时执行
function bindEvent(type, ele, fn){
	if(document.addEventListener){
		return function(type, ele, fn){
	        ele.addEventListener(type, fn, false);
        } 
	}else if(document.attachEvent){
        return function(type, ele, fn){
	        ele.attachEvent('on'+type, fn);
        }
    }else{
        return function(type, ele, fn){
	        ele['on'+type] = fn;
        }
    }
}()
2、对元素执行能力检测并显示重写
function bindEvent(type, ele, fn){
	if(ele.addEventListener){
		bindEvent = function(type, ele, fn){
	        ele.addEventListener(type, fn, false);
        } 
	}else if(ele.attachEvent){
        bindEvent = function(type, ele, fn){
	        ele.attachEvent('on'+type, fn);
        }
    }else{
        bindEvent = function(type, ele, fn){
	        ele['on'+type] = fn;
        }
    }
    //执行重新定义的方法
    bindEvent(type, ele, fn);
}

```

### 记忆函数

```js
通过缓存计算结果的方式来减少计算的次数,以达到提升性能的结果

function memoizedFibinacci(){
	let cache = {};
	
	return function fibnacci(n){
	    if(cache[n]){
			return cache[n]
	    }
        console.log('计算了：',n);
        if(n<2){
            return cache[n] = 1;
        }else{
            return cache[n] = fibnacci(n-2) + fibnacci(n -1);
        }
    }
}

let fb = memoizedFibinacci();
fb(10);
console.log('-----------------10----------------');
fb(5);

VM512:8 计算了： 10
VM512:8 计算了： 8
VM512:8 计算了： 6
VM512:8 计算了： 4
VM512:8 计算了： 2
VM512:8 计算了： 0
VM512:8 计算了： 1
VM512:8 计算了： 3
VM512:8 计算了： 5
VM512:8 计算了： 7
VM512:8 计算了： 9
VM512:18 -----------------10----------------
8


 function memoize(func, hasher) {
    var memoize = function(key) {
      var cache = memoize.cache;
      var address = hasher ? hasher.apply(this, arguments) : key;
      if (address in cache) {
          cache[address] = func.apply(this, arguments);
      }
      return cache[key];
    };
    memoize.cache = {};
    return memoize;
  };
```

### 深浅拷贝

```
对于js的原始数据类型的拷贝都是深拷贝
例如： let a = '123'; 
	  let b = a;
	  b = 'b'
	  console.log(a);//'123'	  
	  console.log(b);//'b'

引用数据类型的浅拷贝 - 只复制了值的引用
1、展开操作符
2、Object.assign()
3、数组的 map、filter、reduce
4、Array.slice

深拷贝
1、手动拷贝嵌套对象

2、JSON.stringify(),JSON.parse()
它会忽略 undefined、Symbol、不能序列化函数、不能解决循环引用对象

3、$.extend()

4、loadsh._cloneDeep()

```

### 节流防抖

> - 防抖：防止抖动，单位时间内事件触发会被重置，避免事件被误伤触发多次。**代码实现重在清零 `clearTimeout`**。防抖可以比作等电梯，只要有一个人进来，就需要再等一会儿。业务场景有避免登录按钮多次点击的重复提交。
> - 节流：控制流量，单位时间内事件只能触发一次，与服务器端的限流 (Rate Limit) 类似。**代码实现重在开锁关锁 `timer=timeout; timer=null`**。节流可以比作过红绿灯，每等一个红灯时间就可以过一批。

```js
防抖
特点：多次触发只执行一次。
应用：搜索、登录等按钮
function debounce(fn, delay){
    let timer = null;
    return (...args)=>{
        if(timer){
            clearTimeout(timer);
        }

        timer = setTimeout(()=>{
            fn.call(this,...args);
        }, delay)
    }
}

 let fun = debounce((...data)=>{
     console.log('3秒',...data)
 },3000)
 fun('参数1','参数2','参数3')

 setTimeout(()=>{
     fun('参数1','参数2','参数3')
 },2000)

节流
特点：指定时间内只执行一次。
应用：监听滚动条等触发次数频繁的事件
function throttle(fn, delay){
    let timer = null;
    return (...args)=>{
        if(timer)return;

        timer = setTimeout(()=>{
            fn.call(this,...args);
            timer = null;
        }, delay)
    }
}

let testjl = throttle((...args)=>{
    console.log('haha:', ...args);
},3000)

testjl('1', 2, 3, 1);
testjl('1', 2, 3, 2);
testjl('1', 2, 3, 3);

setTimeout(()=>{
    testjl('1', 2, 3, 5);
},5000)
```

### AOP在JS中的实现

```js
在程序中时常用到的某些程序步骤、阶段、片段抽离出来，与陈旭本身的程序逻辑抽离

before（前置通知）
/**
 * 给方法加入前置切片函数
 * 可以在执行方法之前执行一些操作,
 * 前置切片的返回值为false时，不影响原方法的执行
 * @param func {Function} 被前置执行的函数
 * @return {Function} 加入前置通知的函数
 */
Function.prototype._before = function(func){
    var __self = this;
    return function(){
        func.apply(__self, arguments);
        return __self.apply(__self, arguments);
    }
}

// 代码
function a(){
    console.log('I\'m a');
}

a = a._before(function(){
    console.log('before');
});

a();
// 结果：
// before
// I'm a


after（后置通知）
/**
 *
 给方法加入后置切片函数
 * 可以在执行方法之之后执行一些操作
 * 后置切片的返回值为false时，不影响原方法的执行
 * @param func {Function} 被后置执行的函数
 * @return {Function} 加入后置通知的函数
 * @constructor
 */
Function.prototype._after = function(func){
    var __self = this;
    return function(){
        var ret = __self.apply(__self, arguments);
        func.apply(__self, arguments);
        return ret;
    }
}

// 代码
function b(){
    console.log('I\'m b');
}

b = b._after(function(){
    console.log('after');
});

b();
// 结果：
// I'm b
// after


around（环绕通知）
。。。




//添加前置后置
function constructor(originFn, before, after){
	return function (){
        before.call(this,...arguments);
        originFn.call(this,...arguments);
        after.call(this,...arguments);
    }
}

function calcAdd(a,b){
    console.log('origin',a,b);
    return a + b;
}

let _add = constructor(calcAdd, function(){
  console.log('before',arguments);  
},function(){
    console.log('after',arguments);
})

_add(1, 2);
//before Arguments(2) [1, 2, callee: ƒ, Symbol(Symbol.iterator): ƒ]
//aop:10 origin 1 2
//after Arguments(2) [1, 2, callee: ƒ, Symbol(Symbol.iterator): ƒ]


参考：https://blog.csdn.net/qq_21460229/article/details/79696159
	https://www.cnblogs.com/zengyuanjun/p/7429968.html

```

### 二进制算法

```
	２＾７		２＾６		２＾５		２＾４		２＾３		２＾２		２＾１		２＾０
	１２８		６４		　３２		　１６		　８		　　４		　　２			１

只有０／１

１８：０　　　　　０　　　　　　０　　　　　１　　　　　０　　　　　０　　　　　１　　　　　０


１）找它大于的最近的数　１８＞１６　　　１６对应位为１

２）１８　－　１６　＝　２　　　２对应位为１

二进制结果　１００１０
```

### 桶排序

```js
var arr = [3,1,9,13,3,2,4,5];

function bunketsort(arr){
    let bunk = [];//创建桶
    for(let i = 0; i<arr.length; i++){
        let item = arr[i];
        //把数据设置为桶的下标，并设置值为1，表示出现了一次
        if(bunk[item]){
            bunk[item] += 1;
        }else{
            bunk[item] = 1;
        }
    }
    //清空传入数组，重新赋值
    arr.length = 0;
    //遍历有值的下标，取值判断出现了几次，一次添入新数组
    for(let k  in bunk ){
        for(let i = 0; i< bunk[k] ; i++){
            arr.push(+k);
        }
    }
    return arr;
}
bunketsort(arr);// [1, 2, 3, 3, 4, 5, 9, 13]
```

### 睡眠排序

```js
var arr=[400,200,700,500,600,0,800]; 
var sleepSort=function(arr,callback){ 
    let res=[]; 
    arr.forEach(item=>{ 
        setTimeout(()=>{ 
            res.push(item) 
            // 如果执行完毕，回调 
            if(res.length===arr.length) {
                callback(res); 
            };
        },item)
    })
  }
 sleepSort(arr,res=>{console.log(res)})
```

### TreeShaking

```
他是webpack自带的一个功能，在打包代码时，会将没有使用的模块，或者变量之类的代码。自动过滤掉。不打包，以减少打包代码大小。就像一棵树将身上枯萎的树叶摇晃掉
```

### AST

```
抽象语法树（类似与html的DOM树，ast就是描述js的vdom树）
```

### Virtural Dom

```
虚拟DOM：它并不是真正意义上的DOM，而是一个轻量级的JavaScript 对象，在状态发生变化时，Virtual Dom会进行Diff运算，来更新只需要被替换的Don,而不是全部，而不是全部重绘。运行过程如下图
```

![](D:\Typora\workSpace\resource\vituralDom.png)

### js运行机制

![](D:\Typora\workSpace\resource\jsrunning.png)

### 

[过程演示]: http://latentflip.com/loupe/?code=CgokLm9uKCdidXR0b24nLCAnY2xpY2snLCBmdW5jdGlvbiBvbkNsaWNrKCkgewogICAgc2V0VGltZW91dChmdW5jdGlvbiB0aW1lcigpIHsKICAgICAgICBjb25zb2xlLmxvZygnWW91IGNsaWNrZWQgdGhlIGJ1dHRvbiEnKTsgICAgCiAgICB9LCAyMDAwKTsKfSk7Cgpjb25zb2xlLmxvZygiSGkhIik7CgpzZXRUaW1lb3V0KGZ1bmN0aW9uIHRpbWVvdXQoKSB7CiAgICBjb25zb2xlLmxvZygiQ2xpY2sgdGhlIGJ1dHRvbiEiKTsKfSwgNTAwMCk7Cgpjb25zb2xlLmxvZygiV2VsY29tZSB0byBsb3VwZS4iKTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D



```js
1、堆
	基于散列算法的数据结构，存储引用类型的数据，如对象、数组等

2、栈
	先进后出的数据结构
    基本数据类型都是一些简单的数据段，它们是存储在栈内存中

3、调用栈
	JavaScript是单线程的，意味着他有一个单独的调用栈，意味着他一次能做一件事。
    JavaScript的运行时一次只能做一件事
	调用栈就是一个记录程序执行位置的数据结构。程序进入一个函数，就往栈里面塞东西。如果程序从函数中return了，就从栈顶弹出一些东西

4、WebApi
	webapi = DOM + BOM
	浏览器提供的api 如 DOM操作，Ajax、fetch、setTimeout等，webApi会把它的回调函数推到 回调队列中

5、事件循环
	它再监视 调用栈 里面是否有东西，如果没有就将回调队列第一个函数取出来，放到调用栈让它执行

6、回调队列
	一种先进先出（FIFO）的数据结构，用于放置webApi推过来的函数

7、阻塞
    实际上就是程序执行慢。执行console.log不慢，但是一个从1到1,000,000,000的while循环，图像处理或者网络请求这些操作的执行就比较费时了。这些执行慢的东西堆在一起就发生了阻塞。
    
    

```

### 字符串编码

> 最早出现的编码表是美国的ASCII码表【采用一个字节编码】，但支持数字英文和符号。
>
> 随着计算机发展，后来出现了一种编码放啊是，是国际化标准组织ISO制定的，这种编码方式支持西欧语言，向上兼容 ACSCII码，但不支持中文。这种编码方式为：ISO-8859-1【latain-1】
>
> 随着计算机向亚洲发展，计算机开始支持中文，日文，韩文等国家文字，其中支持中文的编码方式：
>
> **BG2312** < **GBK** < **GB18030**
>
> 支持繁体中文：大五码 **big5**
>
> 后来出现了一种编码方式统一了全球所有文字，容量较大，这种编码方式叫做：Unicode编码，它有很多具体的实现方式：
>
> - UTF-8
> - UTF-16
> - UTF-32
> - ...

```js
1、ASCII 码
		我们知道，计算机内部，所有信息最终都是一个二进制值。每一个二进制位（bit）有0和1两种状态，因此八个二进制位就可以组合出 256 种状态，这被称为一个字节（byte）。也就是说，一个字节一共可以用来表示 256 种不同的状态，每一个状态对应一个符号，就是 256 个符号，从 00000000到 11111111。

	上个世纪60年代，美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。这被称为 ASCII 码，一直沿用至今。

	ASCII 码一共规定了128个字符的编码，比如空格 SPACE 是32（二进制 00100000），大写的字母A是65（二进制 01000001）。这128个符号（包括32个不能打印出来的控制符号），只占用了一个字节的后面7位，最前面的一位统一规定为0。

2、非ASCII 码
	英语用128个符号编码就够了，但是用来表示其他语言，128个符号是不够的。比如，在法语中，字母上方有注音符号，它就无法用 ASCII 码表示。于是，一些欧洲国家就决定，利用字节中闲置的最高位编入新的符号。比如，法语中的é的编码为130（二进制 10000010）。这样一来，这些欧洲国家使用的编码体系，可以表示最多256个符号。

但是，这里又出现了新的问题。不同的国家有不同的字母，因此，哪怕它们都使用256个符号的编码方式，代表的字母却不一样。比如，130在法语编码中代表了é，在希伯来语编码中却代表了字母Gimel (ג)，在俄语编码中又会代表另一个符号。但是不管怎样，所有这些编码方式中，0--127表示的符号是一样的，不一样的只是128--255的这一段。

至于亚洲国家的文字，使用的符号就更多了，汉字就多达10万左右。一个字节只能表示256种符号，肯定是不够的，就必须使用多个字节表达一个符号。比如，简体中文常见的编码方式是 GB2312，使用两个字节表示一个汉字，所以理论上最多可以表示 256 x 256 = 65536 个符号。

中文编码的问题需要专文讨论，这篇笔记不涉及。这里只指出，虽然都是用多个字节表示一个符号，但是GB类的汉字编码与后文的 Unicode 和 UTF-8 是毫无关系的。

3、Unicode
	正如上一节所说，世界上存在着多种编码方式，同一个二进制数字可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。为什么电子邮件常常出现乱码？就是因为发信人和收信人使用的编码方式不一样。

	可以想象，如果有一种编码，将世界上所有的符号都纳入其中。每一个符号都给予一个独一无二的编码，那么乱码问题就会消失。这就是 Unicode，就像它的名字都表示的，这是一种所有符号的编码。

	Unicode 当然是一个很大的集合，现在的规模可以容纳100多万个符号。每个符号的编码都不一样，比如，U+0639表示阿拉伯字母Ain，U+0041表示英语的大写字母A，U+4E25表示汉字严。具体的符号对应表，可以查询unicode.org，或者专门的汉字对应表。

【Unicode的问题】
	需要注意的是，Unicode 只是一个符号集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。

比如，汉字严的 Unicode 是十六进制数4E25，转换成二进制数足足有15位（100111000100101），也就是说，这个符号的表示至少需要2个字节。表示其他更大的符号，可能需要3个字节或者4个字节，甚至更多。

	这里就有两个严重的问题，第一个问题是，如何才能区别 Unicode 和 ASCII ？计算机怎么知道三个字节表示一个符号，而不是分别表示三个符号呢？第二个问题是，我们已经知道，英文字母只用一个字节表示就够了，如果 Unicode 统一规定，每个符号用三个或四个字节表示，那么每个英文字母前都必然有二到三个字节是0，这对于存储来说是极大的浪费，文本文件的大小会因此大出二三倍，这是无法接受的。

它们造成的结果是：1）出现了 Unicode 的多种存储方式，也就是说有许多种不同的二进制格式，可以用来表示 Unicode。2）Unicode 在很长一段时间内无法推广，直到互联网的出现。

4、UTF-8
	互联网的普及，强烈要求出现一种统一的编码方式。UTF-8 就是在互联网上使用最广的一种 Unicode 的实现方式。其他实现方式还包括 UTF-16（字符用两个字节或四个字节表示）和 UTF-32（字符用四个字节表示），不过在互联网上基本不用。重复一遍，这里的关系是，UTF-8 是 Unicode 的实现方式之一。

UTF-8 最大的一个特点，就是它是一种变长的编码方式。它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度。

UTF-8 的编码规则很简单，只有二条：

1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的 Unicode 码。因此对于英语字母，UTF-8 编码和 ASCII 码是相同的。

2）对于n字节的符号（n > 1），第一个字节的前n位都设为1，第n + 1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的 Unicode 码。

下表总结了编码规则，字母x表示可用编码的位。

Unicode符号范围     |        UTF-8编码方式
(十六进制)          |        （二进制）
----------------------+---------------------------------------------
0000 0000-0000 007F | 0xxxxxxx
0000 0080-0000 07FF | 110xxxxx 10xxxxxx
0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
跟据上表，解读 UTF-8 编码非常简单。如果一个字节的第一位是0，则这个字节单独就是一个字符；如果第一位是1，则连续有多少个1，就表示当前字符占用多少个字节。

下面，还是以汉字严为例，演示如何实现 UTF-8 编码。

	严的 Unicode 是4E25（100111000100101），根据上表，可以发现4E25处在第三行的范围内（0000 0800 - 0000 FFFF），因此严的 UTF-8 编码需要三个字节，即格式是1110xxxx 10xxxxxx 10xxxxxx。然后，从严的最后一个二进制位开始，依次从后向前填入格式中的x，多出的位补0。这样就得到了，严的 UTF-8 编码是11100100 10111000 10100101，转换成十六进制就是E4B8A5。
    
5、BMP 与 代理对
	16 位只能唯一表示 65 536 个字符。这对于大多数语言字符集是足够了，在 Unicode 中称为基本多语言平面（BMP）。为了 表示更多的字符，Unicode 采用了一个策略，即每个字符使用另外 16 位去选择一个增补平面。这种每个 字符使用两个 16 位码元的策略称为代理对。
    
【资料拓展】
	http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html
```

### Object与Function关系

```js
Object、Function和其它对象的关系可以归纳为下面四点：

1、一切对象都最终继承自Object对象，Object对象直接继承自根源对象null

	.... → Object.prototype → null。
    例如定义一个num变量var num = 1，
    则num的原型链为x → Number.prototype → Object.prototype → null; 

	定义一个函数对象fnfunction fn() {}，
    则fn的原型链为fn → Function.prototype → Object.prototype → null;等等...
    
    一切对象都包含有Object的原型方法，Object的原型方法包括了toString、valueOf、hasOwnProperty等等，在js中不管是普通对象，还是函数对象都拥有这些方法

2、一切函数对象（包括Object对象)都直接继承自Function对象
	函数对象包括了Function、Object、Array、String、Number，还有正则对象RegExp、Date对象等等，它们在js中的构造源码都是function xxx() {[native code]);，Function其实不仅让我们用于构造函数，它也充当了函数对象的构造器，甚至它也是自己的构造器。

从原型链可以佐证：

 js中 对象.__proto__ === 构造器.prototype，由此可以见得它们之间的关系。
                                                                                               
 
3、Object对象直接继承自Function对象
4、Function对象直接继承自己，最终继承自Object对象

【疑问】
 1）Object对象都继承自Function对象了，而一切对象又都继承自Object对象，这边是不是有矛盾，Object对象和Function对象的关系是不是有点♂复♀杂？

   答：其实疑问的内容就是上面结论的三四点，它们没有矛盾，关系也不复杂。

 （1）.一切对象都继承自Object对象是因为一切对象的原型链最终都是.... → Object.prototype → null，包括Function对象，只是Function的原型链稍微绕了一点，
    Function的原型链为Function → Function.prototype → Object.prototype → null，它与其它对象的特别之处就在于它的构造器为自己，即直接继承了自己，最终继承于Object，上面的原型链可以在浏览器验证：
    
 （2）. Object继承自Function，Object的原型链为 Object → Function.prototype → Object.prototype → null，原型链又绕回来了，并且跟第一点没有冲突。可以说Object和Function是互相继承的关系
 
2）一切对象继承自Object，Object又继承自Function，那一切对象是不是都有Function的原型方法？

答：不对，普通对象都没有Function的原型方法。从我们所写原型链中可以看出，Object是继承自Function，而Object也有Function的原型方法（比如bind），但Object继承得到的方法储存于__proto__属性中，普通对象从Object继承到的原型方法却在于prototype属性中，因而不对。

3）Function对象怎么那么怪，自己继承自己？

答：就是 就是。



//扩充类型
Function.prototype,method = function (name, fn){
    if(!this.prototype[name]){
		this.prototype[name] = fn;
    }
    return this;
}
```

### 文件格式转化 Base64,FIle,Blob

```js
  // 文件类型转base64    ----主要应用场景：图片预览
    const fileToBase64 = (file, callback) => {
        const reader = new FileReader();
        reader.onload = function(evt) {
            if(typeof callback === 'function') {
                callback(evt.target.result)
            } else {
                console.log("我是base64:", evt.target.result);
            }
        };
        reader.readAsDataURL(file);
    };

	// 文件类型转Blob   ---主要应用场景：文件上传
    const fileToBlob = (file, callback) => {
        const type = file.type;
        const reader = new FileReader();
        reader.onload = function(evt) {
            const blob = new Blob([evt.target.result], {type});
            if(typeof callback === 'function') {
                callback(blob)
            } else {
                console.log("我是 blob:", blob);
            }
        };
        reader.readAsDataURL(file);
    };

 // Base64 转 File   ----主要应用场景：文件上传
    const base64ToFile = (base64, fileName) => {
        let arr = base64.split(','),
            type = arr[0].match(/:(.*?);/)[1], 
            bstr = atob(arr[1]), n = bstr.length, 
            u8arr = new Uint8Array(n);
        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], fileName, {type});
    };

// base64 to Blob

function base64ToBlob(code) {
    const parts = code.split(";base64,");
    const contentType = parts[0].split(":")[1];
    const raw = window.atob(parts[1]);
    const rawLength = raw.length;
    const uInt8Array = new Uint8Array(rawLength);
    for (let i = 0; i < rawLength; ++i) {
        uInt8Array[i] = raw.charCodeAt(i);
    }
    return new Blob([uInt8Array], { type: contentType });
}
```

### HTML 的实体编码

> HTML 实体是一段以连字号（&）开头、以分号（;）结尾的字符串。用以显示不可见字符及保留字符 (如 HTML 标签)
>
> 在前端，一般为了避免 XSS 攻击，会将 `<>` 编码为 `<` 与 `>`，这些就是 HTML 实体编码。

- 不可分的空格:＆nbsp;
- <(小于符号):＆lt;
- (大于符号):＆gt;
- ＆(与符号):＆amp;
- ″(双引号):＆quot;
- '(单引号):'＆apos;
- ……

在 HTML 转义时，仅仅只需要对六个字符进行编码: `&`, `<`, `>`, `"`, `'`, ```。可使用 [he (opens new window)](https://npm.devtool.tech/he)这个库进行编码及转义

```js
// 实体编码
> he.encode('<img src=""></img>')
< "&#x3C;img src=&#x22;&#x22;&#x3E;&#x3C;/img&#x3E;"

// 转义
> he.escape('<img src=""></img>')
< "&lt;img src=&quot;&quot;&gt;&lt;/img&gt;"
```



### 什么是 Data URL

> Data URL 是前缀为`data:`协议的 URL； 它允许内容创建者向文档中嵌入小文件，比如图片等。 Data URL 由四部分组成：
>
> - 前缀`data:`
> - 指示数据类型的 MIME 类型。例如`image/jpeg`表示 JPEG 图像文件；如果此部分被省略，则默认值为`text/plain;charset=US-SACII`
> - 如果为非文本数据，则可选 base64 做标记
> - 数据

```
data:[mediatype][;base64], data
```

缺陷：

- base64 编码后的图片会比原来的体积大三分之一左右。
- Data URL 形式的图片不会缓存下来，每次访问页面都要被下载一次。可以将 Data URL 写入到 CSS 文件中随着 CSS 被缓存下来。



### 计算首屏、白屏时间

```js
白屏时间: 
window.performance.timing.domLoading - window.performance.timing.navigationStart

首屏时间：
window.performance.timing.domInteractive - window.performance.timing.navigationStart
```

### 重绘、重排

> 重排和重绘是**[关键渲染路径](https://q.shanyue.tech/fe/%E5%89%8D%E7%AB%AF%E5%B7%A5%E7%A8%8B%E5%8C%96/391.html)**中的两步

1. 重排(Reflow)：元素的位置发生变动时发生重排，也叫回流。此时在关键渲染路径中的 Layout 阶段，计算每一个元素在设备视口内的确切位置和大小。当一个元素位置发生变化时，其父元素及其后边的元素位置都可能发生变化，代价极高
2. 重绘(Repaint): 元素的样式发生变动，但是位置没有改变。此时在关键渲染路径中的 Paint 阶段，将渲染树中的每个节点转换成屏幕上的实际像素，这一步通常称为绘制或栅格化

> 另外，重排必定会造成重绘。以下是避免过多重拍重绘的方法
>
> 1. 使用 `DocumentFragment` 进行 DOM 操作，不过现在原生操作很少也基本上用不到
> 2. CSS 样式尽量批量修改
> 3. 避免使用 table 布局
> 4. 为元素提前设置好高宽，不因多次渲染改变位置

### cookie 

> 根据同源策略，cookie 是区分端口的，但是浏览器实现来说，“cookie 区分域，而不区分端口，也就是说，同一个 ip 下的多个端口下的 cookie 是共享的！
>
> 所以：localhost:3000 与 localhost:5000 的 cookie 信息共享

属性：

- Domain
- Path
- Expire/MaxAge
- HttpOnly
- Secure
- SameSite

### CSRF

> 跨站请求伪造（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法。跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

1. 使用 JSON API。当进行 CSRF 攻击时，请求体通过 `<form>` 构建，请求头为 `application/www-form-urlencoded`。它难以发送 JSON 数据被服务器所理解。
2. CSRF Token。生成一个随机的 token，切勿放在 cookie 中，每次请求手动携带该 token 进行校验。
3. SameSite Cookie。设置为 Lax 或者 Strict，禁止发送第三方 Cookie。

### CSP

> CSP 的实质就是白名单制度，开发者明确告诉客户端，哪些外部资源可以加载和执行，等同于提供白名单。它的实现和执行全部由浏览器完成，开发者只需提供配置。

两种方法可以启用 CSP：

1. 通过 HTTP 头信息的`Content-Security-Policy`的字段

	```text
	Content-Security-Policy: script-src 'self'; object-src 'none';
	style-src cdn.example.org third-party.org; child-src https:
	```

	

2. 网页的`<meta>`标签

	```html
	<meta 
	      http-equiv="Content-Security-Policy" 
	      content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:">
	
	
	脚本：只信任当前域名
	<object>标签：不信任任何URL，即不加载任何资源
	样式表：只信任cdn.example.org和third-party.org
	框架（frame）：必须使用HTTPS协议加载
	其他资源：没有限制
	```

	> [拓展链接](http://www.ruanyifeng.com/blog/2016/09/csp.html)

### 跨域

```js
	出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）


当前页面url				被请求页面url					是否跨域			原因
http://www.test.com/	http://www.test.com/index.html	  否		 同源（协议、域名、端口号相同）
http://www.test.com/	https://www.test.com/index.html	  跨域	 协议不同（http/https）
http://www.test.com/	http://www.baidu.com/			 跨域		主域名不同（test/baidu）
http://www.test.com/	http://blog.test.com/			 跨域		子域名不同（www/blog）
http://www.test.com:8080/	http://www.test.com:7001/	  跨域	 端口号不同（8080/7001）

非同源的限制
【1】无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB

【2】无法接触非同源网页的 DOM

【3】无法向非同源地址发送 AJAX 请求

跨域解决方案
1、设置document.domain
	因为浏览器是通过document.domain属性来检查两个页面是否同源，因此只要通过设置相同的document.domain，两个页面就可以共享Cookie（此方案仅限主域相同，子域不同的跨域应用场景。）
2、跨文档通信 API：window.postMessage()
	页面和其打开的新窗口的数据传递
    多窗口之间消息传递
    页面与嵌套的iframe消息传递
    上面三个场景的跨域数据传递
3、JSONP
	JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，兼容性好（兼容低版本IE），缺点是只支持get请求，不支持post请求。
    
    $.ajax({
        url: 'http://www.test.com:8080/login',
        type: 'get',
        dataType: 'jsonp',  // 请求方式为jsonp
        jsonpCallback: "handleCallback",    // 自定义回调函数名
        data: {}
    });
4、CORS
CORS 是跨域资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，属于跨源 AJAX 请求的根本解决方法。

1、普通跨域请求：只需服务器端设置Access-Control-Allow-Origin

2、带cookie跨域请求：前后端都需要进行设置

eg【站长统计】
//统计代理
var Count = (function(){
    //缓存图片
    var _img = new Image();
    return function(param){
        var str = 'http://www.count.com/a.gif';
        for(var i in param){
            str += i + '=' + param[i];
        }
        //发送统计请求
        _img.src = str;
    }
})()
Count({num: 10});

5、Reverse Proxy，在 nginx/traefik/haproxy 等反向代理服务器中设置为同一域名
```



### MVC

```js
    经典MVC模式中，M是指业务模型，V是指用户界面，C则是控制器，使用MVC的目的是将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。其中，View的定义比较清晰，就是用户界面。
    
    Model（模型）表示应用程序核心（如数据库）。

    View（视图）显示效果（HTML页面）。

    Controller（控制器）处理输入（业务逻辑）。
```

![](D:\Typora\workSpace\resource\mvc.png)

### MVP

```js
全称：Model-View-Presenter ；MVP 是从经典的模式MVC演变而来，它们的基本思想有相通的地方Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。



MVC 与 MVP 区别

	作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过 Controller。
	在MVC里，View是可以直接访问Model的！从而，View里会包含Model信息，不可避免的还要包括一些业务逻辑。 在MVC模型里，更关注的Model的改变，而同时有多个对Model的不同显示，即View。所以，在MVC模型里，Model不依赖于View，但是View是依赖于Model的。不仅如此，因为有一些业务逻辑在View里实现了，导致要更改View也是比较困难的，至少那些业务逻辑是无法重用的。
虽然 MVC 中的 View 的确“可以”访问 Model，但是我们不建议在 View 中依赖 Model，而是要求尽可能把所有业务逻辑都放在 Controller 中处理，而 View 只和 Controller 交互。

```

![](D:\Typora\workSpace\resource\mvp.png)

### MVVM

```js
    简单的讲，MVVM是MVC的改进版。我们都知道MVC软件架构模式是苹果推荐的开发模式。

MVC中的M就是单纯的从网络获取回来的数据模型，V指的我们的视图界面，而C就是我们的ViewController。

在其中，ViewController负责View和Model之间调度，View发生交互事件会通过target-action或者delegate方式回调给ViewController，与此同时ViewController还要承担把Model通过KVO、Notification方式传来的数据传输给View用于展示的责任。随着业务越来越复杂，视图交互越复杂，导致Controller越来越臃肿，负重前行。脏活累活都它干了，到头来还一点不讨好。福报修多了的结果就是，不行了就重构你，重构不了就换掉你。

来一张斯坦福老头经典的MVC架构图。


```

![](D:\Typora\workSpace\resource\mvvm.png)

```
    所以为了解决这个问题，MVVM就闪亮登场了。他把View和Contrller都放在了View层（相当于把Controller一部分逻辑抽离了出来），Model层依然是服务端返回的数据模型。而ViewModel充当了一个UI适配器的角色，也就是说View中每个UI元素都应该在ViewModel找到与之对应的属性。除此之外，从Controller抽离出来的与UI有关的逻辑都放在了ViewModel中，这样就减轻了Controller的负担
    
    
MVVM 与 MVP 区别
	mvvm模式将Presener改名为View Model，基本上与MVP模式完全一致，唯一的区别是，它采用双向绑定(data-binding): View的 变动，自动反映在View Model，反之亦然。这样开发者就不用处理接收事件和View更新的工作，框架已经帮你做好了。

```

### 数组的坍塌

```js
数组坍塌是什么呢？

当数组执行删除单元操作时，被删除单元之后的单元会往前移，进而顶替被删除单元，出现在被删除的单元位置上，造成数组的长度减少，这种现象就被称为数组的坍塌

举个例子来说，就像是一个5层的楼层，突然二楼没了，三四五楼直接集体往下移一个单位。

//先建立一个数组，然后遍历
for(var i =0;i<=arr,lenth-1;i++){
//正常来说会这样执行到循环结束
}
//那么当数组坍塌时是什么样子呢？

for(var i = 0;i <= arr.length-1; i++){	
	if( i== 2){arr.splice(i , 1)}
	//当i=2时，执行删除单元操作，删除当前索引是i的值，也就是2的单元
	//逆战班
}	

//那该如何解决这个问题呢

for(var i = 0; i < arr.length-1 ; i++){
	if( i == 2){ arr.splice( i , 1 ); i--;} 
	//触发条件，删除单元，数组坍塌
	//在执行删除语句后，执行i--操作；
}
```

### HLS与RHS 操作

```js
LHS 和 RHS 的含义是“赋值操作的左侧或右侧”并不一定意味着就是“= 赋值操作符的左侧或右侧”。
赋值操作还有其他几种形式，因此在概念上最 好将其理解为“赋值操作的目标是谁（LHS）”以及“谁是赋值操作的源头 （RHS）”。

function foo(a) { 
    var b = a; 
    return a + b; 
} 
var c = foo( 2 ); 

1. 找出所有的 LHS 查询（这里有 3 处！） c = ..;、a = 2（隐式变量分配）、b = .. 

2. 找出所有的 RHS 查询（这里有 4 处！） foo(2..、= a;、a ..、.. b

```

### 解构赋值重命名操作

```js
const obj = {a: 1, b: 2, c: 3};

// 结构对象时重命名 【注意】被解构的对象不能为undefined/null,所以下面使用了默认值

let {a: num1, b: num2, c: num3} = obj || {};

console.log(num1, num2, num3)
//  1 2 3
```

### 空值合并运算符

```js
如果操作符左边是null，undefined，会返回操作符右边的值否则返回左边的值

var nick = null;
var result = nick ?? '没有 nick'
```

### 可选链式操作符

```js
const obj = { a: [1, 2], b() {} };
// 访问数组
obj?.a?.[0];
//使用方法
obj?.b?.();

let persion = {
    namek: 'jack',
    getName: function(){
        console.log('jack')
    }
}

person?.age  //jack
person?.age  // undefined
person?.age?.num  // undefined
person.age.num // 报错
person.getName?.() // 输出 jack
person?.getName() // 报错
```

### IIFE（立即调用函数表达式）

```js
IIFE（ 立即调用函数表达式）是一个在定义时就会立即执行的  JavaScript 函数。

主要包含两部分。
第一部分是包围在 圆括号运算符 () 里的一个匿名函数，这个匿名函数拥有独立的词法作用域。这不仅避免了外界访问此 IIFE 中的变量，而且又不会污染全局作用域。

第二部分再一次使用 () 创建了一个立即执行函数表达式，JavaScript 引擎到此将直接执行函数。
```

### JSON序列化与解析

```js
// json -> object

JOSN.parse(json字符串, reviver 函数)

reviver 函数，
	在调用过程中，当前属性所属的对象会作为 this 值，当前属性名和属性值会分别作为第一个和第二个参数传入 reviver 中。如果 reviver 返回 undefined，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。
    
    eg:

    JSON.parse('{"1": 1, "2": 2,"3": {"4": 4, "5": {"6": 6}}}', function (k, v) {
        console.log(k, v, typeof v, this); 
        if(typeof v !== 'object') {
            return '入侵成功'
        } else {
            return v
        }
    });

// object -> json

JSON.stringify(value, replacer, space )

replacer 参数可以是一个函数或者一个数组。
作为函数，它有两个参数，键（key）和值（value），它们都会被序列化。

在开始时, replacer 函数会被传入一个空字符串作为 key 值，代表着要被 stringify 的这个对象。随后每个对象或数组上的属性会被依次传入。 

函数应当返回JSON字符串中的value, 如下所示:

如果返回一个 Number, 转换成相应的字符串作为属性值被添加入 JSON 字符串。
如果返回一个 String, 该字符串作为属性值被添加入 JSON 字符串。
如果返回一个 Boolean, "true" 或者 "false" 作为属性值被添加入 JSON 字符串。
如果返回任何其他对象，该对象递归地序列化成 JSON 字符串，对每个属性调用 replacer 方法。除非该对象是一个函数，这种情况将不会被序列化成 JSON 字符串。
如果返回 undefined，该属性值不会在 JSON 字符串中输出。
注意: 不能用 replacer 方法，从数组中移除值（values），如若返回 undefined 或者一个函数，将会被 null 取代。

let obj = {
    name: '123',
    age: 45,
    sex: '楠'
}

JSON.stringify(obj, function(k, v) {
    console.log(k ,v)
    return v
}, '####')



// name 123
// age 45
// sex 楠
// {
// ####"name": "123",
// ####"age": 45,
// ####"sex": "楠"
// }
```

### LRUCache

> LruCache算法，又称为近期最少使用算法。
>
> 缓存最近的少量的记录，超出则移出近期最少使用的数据

```js
class LRUCache {
  constructor(limit) {
    this.limit = limit;
    this.cache = new Map();
  }

  get(key) {
    if (!this.cache.has(key)) return undefined;
    const value = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, value);
    return value;
  }

  put(key, value) {
    if (this.cache.has(key)) this.cache.delete(key);
    else if (this.cache.size >= this.limit) {
      this.cache.delete(this.cache.keys().next().value);
    }
    this.cache.set(key, value);
  }
}

// 创建一个只能缓存最近两条操作记录
const lruCache = new LRUCache(2);
// 放入两条
lruCache.put(1, 1);
lruCache.put(2, 2);
// 获取最近第一条记录
const res1 = lruCache.get(1);
// 添加第三条记录，此时key为2的记录被移除
lruCache.put(3, 3);
// 此时获取的值 于 res1 相同
const res2 = lruCache.get(2);
// 添加第四条记录
lruCache.put(4, 4);
// 此时只缓存了最近的两条数据 key 3,4
const res3 = lruCache.get(1);
const res4 = lruCache.get(3);
const res5 = lruCache.get(4);
```



### js 代码压缩 minify 的原理是什么

> 我们知道 `javascript` 代码经压缩 (uglify) 后，可以使体积变得更小，那它代码压缩的原理是什么。
>
> 如果你来做这么一个功能的话，你会怎么去压缩一段 `js` 代码的体积

- uglify 包里有 ast.js 所以它一定是生成了抽象语法树 接着遍历语法树并作出优化，像是替换语法树中的变量，变成a，b，c那样的看不出意义的变量名。还有把 if/else 合并成三元运算符等。 最后输出代码的时候，全都输出成一行。

- 通过 AST 分析，根据选项配置一些策略，来生成一颗更小体积的 AST 并生成代码。

	目前前端工程化中使用 [terser (opens new window)](https://terser.org/docs/api-reference#compress-options)和 [swc (opens new window)](https://swc.rs/docs/configuration/minification)进行 JS 代码压缩，他们拥有相同的 API。

	常见用以压缩 AST 的几种方案如下:

	1. 去除多余字符: 空格，换行及注释

	```js
	// 压缩前
	// 对两个数求和
	function sum (a, b) {
	  return a + b;
	}
	
	// 压缩后
	function sum(a,b){return a+b}
	```

	1. 压缩变量名：变量名，函数名及属性名

		```js
		function sum (first, second) {
		  return first + second;  
		}
		
		
		// 压缩: 缩短变量名
		function sum (x, y) {
		  return x + y;  
		}
		
		// 再压缩: 去除空余字符
		function s(x,y){return x+y}
		```

		

	2. 解析程序逻辑：合并声明以及布尔值简化

		```js
		// 压缩前
		const a = 3;
		const b = 4;
		
		// 压缩后
		const a = 3, b = 4;
		
		// 压缩前
		!b && !c && !d && !e
		
		// 压缩后
		!(b||c||d||e)
		```

		

	3. 解析程序逻辑: 编译预计算

		```js
		// 压缩前
		const ONE_YEAR = 365 * 24 * 60 * 60
		
		// 压缩后
		const ONE_YAAR = 31536000
		
		
		
		// 压缩前
		function hello () {
		  console.log('hello, world')
		}
		
		hello()
		
		// 压缩后
		console.log('hello, world')
		```

		

	4. ...

### toJSON方法

```js
	Object.prototype实际上并未定义toJSON()方法，但JSON.stringify()方法会从要序列化的对象上寻找toJSON()方法。如果要序列化的对象上存在这个方法，就会调用它，然后序列化该方法的返回值，而不是原始对象。
日期Date类就定义了自己的toJSON方法

与 toString 用法类似，都是重写

let obj = {
    firstName: 'a',
    lastName: 'b',
    toJSON: function() {
        // return `我的名字：${this.firstName}${this.lastName}`
        return {
            name: this.firstName + this.lastName
        }
    }
};
JSON.stringify(obj)
// '"我的名字：ab"'
// '{"name":"ab"}'
```

### js静态导入、动态导入js模块

```js
1、静态导入
import * as state from './state.js'

2、动态导入并使用
import('./state.js').then(status=> {
    let average = stats.mean(data)
})
3、async 函数处理
async analyzeData(data) {
    let stats = await impport('./state.js')
}
```

### import.meta.url

```js
	import.meta.url的主要使用场景是引用与模块位于同一（或相对）目录下的图片、数据文件或其他资源。使用URL()构造函数可以非常方便地相对于import.meta.url这样的绝对URL来解析相对URL。例如，假设你要写一个模块，其中包含需要本地化的字符串，而相关的本地化文件保存在l10n/目录下，这个目录也保存着模块本身。
	你的模块可以使用通过下面的函数创建的URL来加载其字符串：
	
	function localStringURL (locale) {
        return new URL(`l10n/${locale}.json`, import.meta.ual);
    }
[1] 例如，对于更新比较频繁的Web应用，经常回访的用户会发现使用小模块而不是大文件的平均加载时间更短，因为可以更好地利用浏览器缓存。
```

### 自定义错误子类型

```js
	
	Error对象有两个属性：message和name，还有一个toString()方法。
message属性的值是我们传给Error()构造函数的值，必须时会被转换为字符串。对使用Error()创建的错误对象，name属性的值始终是“Error”。
toString()方法返回一个字符串，由name属性的值后跟一个冒号和一个空格，再后跟message属性的值构成。


	除了Error类，JavaScript还定义了一些它的子类，以便触发ECMAScript定义的一些特殊类型的错误。这些子类包括：EvalError、RangeError、ReferenceError、SyntaxError、TypeError和URIError。你可以按照自己认为合适的方式在代码中使用这些错误类。与基类Error一样，这些子类也都有一个构造函数，接收一个消息参数。每个子类的实例都有一个name属性，其值就是构造函数的名字。
    
    
    class HTTPError extends Error {
        constructor(status, statusText, url) {	
            super(`${status}  ${statusText}: ${url}`);
            this.status = status;
            this.statusText = statusText;
            this.url = url;
        }
        get name() { return 'HTTPError'; }
    }
	let error = new HTTPError(404, 'Not Found', 'http://example.com/')
    
```

### Intl 对象        [详情MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat)

```js
	Intl 对象是 ECMAScript 国际化 API 的一个命名空间，它提供了精确的字符串对比、数字格式化，和日期时间格式化。Collator，NumberFormat 和 DateTimeFormat 对象的构造函数是 Intl 对象的属性。
    
1、    lntl.NumberFormat() 格式化数值
	这个构造函数接收两个参数，第一个参数指定作为数值格式化依据的地区，第二个参数是用于指定格式化细节的对象
    
    lntl.NumberFormat('zh-CN', {
        style: 'decimal', // 如果指定"percent"则按百分比格式化数值，指定"curreny"则表示数值为货币数量。
        currency: 'RMB',// 用于指定3个字母的ISO货币代码（如"USD"表示美元，"GBP"表示英镑）。
        currencyDisplay: 'symbol',// 默认symbolsymbol货币有符号则使用货币符号。值"code"表示使用3个字母的ISO代码
        useGrouping: false, // 如果不想让数值有千分位分隔符（或其他地区相关的样式），将这个属性设置为false。
        minimumIntegerDigits: 21,// 数值中最少显示几位整数。如果数值的位数小于这个值，则在左侧填补0。默认值是1，但最高可以设置为21。
        minimumFractionDigits: 0,// 小数位最低保留多少位
        maximumFractionDigits: 20,// 小数位最高保留多少位 这两属性范围 0-20
        minimumSignificantDigits:,// 控制数值中的有效位 最低是 1
        maximumSignificantDigits:,// 控制数值中的有效位 最高是 21
        ...
    }).format
    
    console.log(new Intl.NumberFormat('zh-CN', { notation: "compact" }).format(987654321));
	// → 9.9亿
	// 只显示三个有效数字
    var number = 123456.789;
    console.log(new Intl.NumberFormat('en-IN', { maximumSignificantDigits: 3 }).format(number));
    // → 1,23,000

2、Intl.DateTimeFormat 格式化日期时间
	默认使用儒略历，但也可以使用其他日历。虽然有些地区默认可能使用非儒略历，但可以在地区中添加-u-ca-后跟日期名来明确指定要使用什么日历。可以使用的日历名包括“buddhist”“chinese”“coptic”“ethiopic”“gregory”“hebrew”“indian”“islamic”“iso8601”“japanese”和“persian”
Intl.DateTimeFormat('chinese', {
    year: 'numeric', // numeric 完整4年 、 2-digit 表示2位简写
    month: '2-digit' // numeric 较短数字， 2-digit 2位数字
    day: '2-digit',
    weekday: 'long', // 使用"long"表示全名，如“Monday”，或使用"short"表示简称，如“Mon”，或使用"narrow"表示高度简写的名字，如“M”，但不保证唯一
    era: 'long',// 这个属性指定日期在格式化时是否考虑纪元，例如CE或BCE。这个属性在格式化很久以前的日期或者使用日文日历时有用。合法值为"long""short"和"narrow"。
    hour: 'numeric', // numeric"表示1位或2位数字，使用"2-digit"表示强制1位数值在左侧填补0。
    minute:'numeric',
    second: 'numeric',
    timeZone: '', // 指定时区， 省略默认本地时区
    timeZoneName: 'long', // long全称，short 简写
    hour12: true,// 是否使用12小时制
    hourCycle: 'h24'// 个属性允许指定半夜12点是写为0时、12时还是24时。默认值取决于地区设备，但可以使用这个属性来覆盖。注意hour12相比这个属性具有更高的优先级。使用"h11"指定半夜12点是0时，而此前1小时是晚上11点。使用"h12"指定半夜是12点。使用"h23"指定半夜是0时，而此前1小时是23时。最后，使用"h24"将半夜指定为24时。
})


Intl.DateTimeFormat('chinese', {
    houe12: true,
    year: 'numeric',
    month: 'numeric',
    dayr: 'numeric',
    hour: 'numeric',
    minute: 'numeric',
    second: 'numeric',
    weekday:'long'
}).format(Date.now())
// '2021年10月 星期三 下午5:17:16'


3、Intl.Collator() 字符串比较

第一个参数指定地区或地区数组，
第二个参数是一个可选的对象，其属性指定具体执行哪种比较。以下是选项对象参数支持的属性。

new Intl.Collator([], {
    usage: 'sort',// 定如何使用整理器（collator）对象，默认值为"sort"，但也可以指定为"search"。
    sensitivity: 'base',// 指定整理器在比较字符串时是否区分字母大小写和重音,详情见MDN文档
    ignorePunctuation: true,// 设置为true以便在比较字符串时忽略空格和标点符号, 此时字符串“any one”和“anyone”会被认为相等。
    
    numeric: true,// 如果比较的内容是整数或包含整数，而你希望按照数值顺序而非字母顺序对它们进行排序，要将这个属性设置为true。设置这个选项后，字符串“Version 9”会排在“Version 10”前面。
    
    caseFirst: 'upper',// 指定是大写字母还是小写字母应该排在前面。如果指定"upper"，则"A"会排在"a"前面。如果指定"lower"，则"a"会排在"A"前面。无论哪种形式优先，同一字母的大写变体和小写变体在排序中都会紧挨在一起，而不同于所有ASCII大写字母会位于所有ASCII小写字母之前的Unicode字典顺序（即Array的sort()方法的默认行为）。这个属性的默认值因地区而异，实现可能会忽略这个属性，不允许我们覆盖大小写排列的顺序。
    
}).compare


【注】
sensitivity: 
"base": 只有字母不同的字母比较不相等。例子: a ≠ b, a = á, a = A。
"accent": 只有不同的基本字母或重音符号和其他变音符号的字符串比较为不相等。 例如: a ≠ b, a ≠ á, a = A。
"case": 只有不同的基本字母或大小写的字符串比较不相等。 Examples: a ≠ b, a = á, a ≠ A。
"variant": 字符串的字母，口音和其他变音符号、或不同大小写比较不相等。 也可以考虑其他差异。例如： a ≠ b, a ≠ á, a ≠ A.
"variant" 的默认值使用 "sort"; 它的 locale 依赖于使用 "search".

let stp = new Intl.Collator('co', {
    usage: 'sort',
    sensitivity: 'base',
    ignorePunctuation: true,
    numeric: true,
    caseFirst: false
}).compare

// ['a1','1','A1','100','c',1.12].sort(stp)
// (6) ['1', 1.12, '100', 'a1', 'A1', 'c']
```

### 　URL API

```js
1、URL类可以解析URL，同时允许修改已有的URL（如添加搜索参数或修改路径），还可以正确处理对不同URL组件的转义和反转义。

网页地址： https://developer.mozilla.org/zh-CN/docs/Web/API/URL/URL

let uuu = new URL(location.href)

    hash: ""
    host: "developer.mozilla.org"
    hostname: "developer.mozilla.org"
    href: "https://developer.mozilla.org/zh-CN/docs/Web/API/URL/URL"
    origin: "https://developer.mozilla.org"
    password: ""
    pathname: "/zh-CN/docs/Web/API/URL/URL"
    port: ""
    protocol: "https:"
    search: ""
    searchParams: URLSearchParams {}
    username: ""

uuu.search = 'findIndex'
uuu.port = 8000

    hash: ""
    host: "developer.mozilla.org:8000"
    hostname: "developer.mozilla.org"
    href: "https://developer.mozilla.org:8000/zh-CN/docs/Web/API/URL/URL?findIndex"
    origin: "https://developer.mozilla.org:8000"
    password: ""
    pathname: "/zh-CN/docs/Web/API/URL/URL"
    port: "8000"
    protocol: "https:"
    search: "?findIndex"
    searchParams: URLSearchParams {}
    username: ""

2、searchParams
	如果要把这种名/值对编码为URL的查询部分，那么searchParams属性比search属性更有用。search属性是一个可读写的字符串，通过它可以获取或设置URL的查询部分。searchParams属性则是一个对URLSearchParams对象的只读引用，而URLSearchParams对象具有获取、设置、添加、删除和排序参数（该参数编码为URL的查询部分）的API：

uuu.searchParams.append('q','trim');// 添加搜索参数
uuu.searchParams.set('q','trims');// 修改搜索参数
uuu.searchParams.get('q');// 修改搜索参数
uuu.searchParams.has('q');// 查询是否有此搜索参数
uuu.searchParams.getAll('q');// 获取所有搜索参数
uuu.searchParams.delete('findIndex');// 删除指定搜索参数

    hash: ""
    host: "developer.mozilla.org:8000"
    hostname: "developer.mozilla.org"
    href: "https://developer.mozilla.org:8000/zh-CN/docs/Web/API/URL/URL?q=trims"
    origin: "https://developer.mozilla.org:8000"
    password: ""
    pathname: "/zh-CN/docs/Web/API/URL/URL"
    port: "8000"
    protocol: "https:"
    search: "?q=trims"
    searchParams: URLSearchParams {}
    username: ""

```

### 公认符号 Symbol

```js
1、Symbol.hasInstance 与 instanceof 组合使用

	// 定义一个作为’类型‘的对象，以便于 instanceof 一起使用
	let unit8 = {
        [Symbol.hasInstance](x) {
            return Number.isInteger(x) &&　x >= 0 && x <= 255;
        }
    };
	
	128 instanceof unit8 // true
	256 instanceof unit8 // false 过大
	Math.PI instanceof unit8 // false 不是整数

2、Symbol.toStringTag
	调用一个简单JavaScript对象的toString()方法会得到字符串"[object Object]"：
    如果调用与内置类型实例的方法相同的Object.prototype.toString()函数，则会得到一些有趣的结果：
    	Object.prototype.toString.call([]);// "[object Array]"
    	Object.prototype.toString.call(/./);// "[object RegExp]"
    	Object.prototype.toString.call(()=>{});// "[object Function]"
    	Object.prototype.toString.call('');// "[object Strinig]"
    	Object.prototype.toString.call(8);// "[object Number]"
    	Object.prototype.toString.call(false);// "[object Booleang]"
    说明，使用这种Object.prototype.toString().call()技术检查任何JavaScript值，
    
    // 用于检测各种数据类型，比typeof好使
    function classof(o) {
        return Object.prototype.toString.call(o).slice(8, -1)
    }

	当
    class Range {
        get [Symbol.toStringTag]() {return 'Range'}
        ...
    } 
    let r = new Range(1, 10);
	Object.prototype.toString.call(r); // "[object Range]"
    classof(r); // "Range"

3、Symbol.species
	
	在ES6之前，JavaScript没有提供任何实际的方式去创建内置类（如Array）的子类。但在ES6中，我们使用class和extends关键字就可以方便地扩展任何内置类。
    
    class EZArray extends Array {
        get first(){ return this[0] }
        get last() {return this[this.length - 1]}
    }
	
	let e = new EZArray(1,2,3);
	let f = e.map(x=> x*x);
	e.last; // 3
	f.last;// 9

	Array定义了concat()、filter()、map()、slice()和splice()方法，这些方法仍然返回数组。
    在创建类似EZArray的数组子类时也会继承这些方法，这些方法应该返回Array的实例，还是返回EZArray的实例？
    ES6规范认为这5个数组方法（默认）将返回子类的实例
    
    但是如果想要返回 Array 实例呢？我们就可以通过 [Symbol.species]  修改默认行为
    // 此属性为只读属性，不能这样设置
    EZArray[Symbol.species] = Array; 
	// 使用 defineProperty
	Object.defineProperty(EZArray,[Symbol.species], {
        value: Array
    })

4、Symbol.isConcatSpreadable 与 concat
	
	如果你创建了一个类数组对象，并且希望把它传给concat()时该对象能像真正的数组一样，那可以给这个对象添加这么一个符号属性：
    
    let arraylike = {
        length: 1,
        0: 1,
        [Symbol.isConcatSpreadable]: true
    };
	[].cancat(arraylisk) // [1] (  如果 [Symbol.isConcatSpreadable]: false. 这里就是[[1]]  )

5、Symbol.toPrimitive
	在ES6中，公认符号Symbol.toPrimitive允许我们覆盖这个默认的对象到原始值的转换行为，让我们完全控制自己的类实例如何转换为原始值。
	1）·如果这个参数是"string"，则表示JavaScript是在一个预期或偏好（但不是必需）为字符串的上下文中做这个转换。比如，把对象作为字符串插值到一个模板字面量中。
	2）·如果这个参数是"number"，则表示JavaScript是在一个预期或偏好（但不是必需）为数值的上下文中做这个转换。在通过<或>操作符比较对象，或者使用算术操作符-或*来计算对象时属于这种情况。
	3）·如果这个参数是"default"，则表示JavaScript做这个转换的上下文可以接受数值也可以接受字符串。在使用+、==或!=操作符时就是这样。
```

### 客户端 JavaScript 的线程模型

```js
	JavaScript是单线程的语言，而单线程执行让编程更容易：你可以保证自己写的两个事件处理程序永远不会同时运行。在操作文档内容时，你敢肯定不会有别的线程会同时去修改它。而且，在写JavaScript代码时，你永远不需要关心锁、死锁或者资源争用。
    单线程执行意味着浏览器会在脚本和事件处理程序执行期间停止响应用户输入。JavaScript程序员为此有责任确保JavaScript脚本和事件处理程序不会长时间运行。如果脚本执行计算量大的任务，就会导致文档加载延迟，用户在脚本执行结束前将看不到文档内容。如果事件处理程序执行计算密集型任务，浏览器可能会变得没有响应，有可能导致用户以为程序已经崩溃了。
    Web平台定义了一种受控的编程模型，即Web工作线程（Web worker）。工作线程是一个后台线程，可以执行计算密集型任务而不冻结用户界面。
```

### 客户端JavaScript时间线

```js
1、浏览器创建Document对象并开始解析网页，随着对HTML元素及其文本内容的解析，不断向文档中添加Element对象和Text节点。此时，document.readyState属性的值是“loading”。

2、HTML解析器在碰到一个没有async、defer或type="module"属性的<script>标签时，会把该标签添加到文档中，然后执行其中的脚本。脚本下载运行期间， HTML解析器暂停。  不带async或defer属性的非模块脚本可以看到它自己的<script>标签及该标签之前的文档内容。

3、解析器在碰到一个有async属性集的<script>元素时，会开始下载该脚本的代码（如果该脚本是模块，也会递归地下载模块的所有依赖）并继续解析文档。脚本在下载完成后会尽快执行，但解析器不会停下来等待它下载。

4、当文档解析完成后，document.readState属性变成“interactive”。

5、任何有defer属性集的脚本（以及任何没有async属性的模块脚本）都会在按照它们在文档中出现的顺序依次执行。异步脚本也有可能在此时执行。延迟脚本可以访问完整的文档

6、浏览器在Document对象上派发“DOMContentLoaded”事件。这标志着程序执行从同步脚本执行阶段过渡到异步的事件驱动阶段。但要注意，此时仍然可能存在尚未执行的async脚本。

7、此时文档已经解析完全，但浏览器可能仍在等待其他内容（如图片）加载。当所有外部资源都加载完成，且所有async脚本都加载并执行完成时，document.readyState属性变成“complete”，浏览器在Window对象上派发“load”事件。

8.　从这一刻起，作为对用户输入事件、网络事件、定时器超时等的响应，浏览器开始异步调用事件处理程序。

```

### web安全模型

```js
	由于网页可以在你的私人设备上执行任意JavaScript代码，因此存在明显的安全隐患。浏览器厂商一直在努力平衡两个相互制约的目标：
·定义强大的客户端API，让Web应用用途更广。
·防止恶意代码读取或修改用户数据、侵犯用户隐私、欺诈用户或浪费用户的时间。

1、不持支某些能力。 
	例如，客户端JavaScript不能向客户端计算机中写入或删除任何文件，也不能展示任意目录的内容。这意味着JavaScript程序不能删除数据，也不能植入病毒。
    类似地，客户端JavaScript没有通用网络能力。
    客户端JavaScript程序可以发送HTTP请求。而另一个标准，即WebSocket，定义了一套类似套接口的API，用于跟特定的服务器通信。但这些API都无法随意访问任意服务器。使用客户端JavaScript写不出通用互联网客户端和服务器。

    2、同源策略
	同源策略指的是对JavaScript代码能够访问和操作什么Web内容的一整套限制。
    通常在页面中包含<iframe>元素时就会涉及同源策略。同源策略控制着一个窗格中的JavaScript与另一个窗格中的JavaScript的交互。
	-比如，脚本只能读取与包含它的文档同源的Window和Document对象的属性。
		文档的源就是文档URL的 协议(http/https)、主机(ip地址/域名)和端口。
     
     -浏览器通常把每个file: URL看成一个独立的源，这意味着如果你写的程序会显示同一台服务器上的多个文档，则可能无法使用file: URL在本地测试它，而必须在开发期间运行一个静态Web服务器。
     
     -脚本自身的源与同源策略不相关，script 的 src 地址不受同源限制，但如果顶级文档包含另一个<iframe> 同源策略会起作用
	 -同源策略对使用多子域的大型网站造成了麻烦。例如百度（www.baidu.com）。
	如 https://image.baidu.com/ 需要访问 https://www.baidu.com/ 上的资源则会跨域
	为了支持这种多子域名网站，脚本可以通过把document.domain设置为一个域名后缀来修改自己的源。因此，源为			https://orders.example.com 的脚本通过把document.domain设置为“example.com”，可以把自己的源修改为
	https://example.com。但是，该脚本不能把document.domain设置为“orders.example”“ample.com”或“com”。

3、跨域资源共享
	它允许服务器决定对哪些源提供服务。CORS扩展了HTTP，增加了一个新的Origin:请求头和一个新的Access-Control-Allow-Origin响应头。服务器可以使用这个头部明确列出对哪些源提供服务，或者使用通配符表示可以接收任何网站的请求。浏览器会根据这些CORS头部的有无决定是否放松同源限制。

4、跨站点攻击(xss)
	是一种攻击方式，指攻击者向目标网站注入HTML标签或脚本。客户端JavaScript程序员必须了解并防范跨站点脚本。
    eg: 
	<script>
      let name = new URL(document.URL).searchParams.get('name');
	  document.querySelector('h1').innerHTML = 'Hello' + name;
     </script>

	正常- http://127.0.0.1:5501/index.html?name=123
	异常- http://127.0.0.1:5501/index.html?name=%3Cimg%20src%3D%22%2Fimages%2Fhome%2Fblue.png%22%20onload%3D%22alert(111)%22%2F%3E
    
    -策略
    	1）一般来说，防止XSS攻击的办法是从不可信数据中删除HTML标签，然后再用它去动态创建文档内容。
    使用标签模板替换特殊字符为html实体
    	2）应对XSS攻击的另一个思路是让自己的Web应用始终在一个<ifarme>中显示不可信内容，并将这个<iframe>的sandbox属性设置为禁用脚本和其他能力。
```

### sandbox

```js
<iframe sandbox="value">
    
value 					说明
    
""						启用所有限制条件
allow-same-origin		允许将内容作为普通来源对待。如果未使用该关键字，嵌入的内容将被视为一个独立的源。

allow-top-navigation	嵌入的页面的上下文可以导航（加载）内容到顶级的浏览上下文环境（browsing context）。
						如果未使用该关键字，这个操作将不可用。

allow-forms				允许表单提交。

allow-scripts			允许脚本执行。
```

### **BigInt** 

```js

BigInt 是一种内置对象，它提供了一种方法来表示大于 253 - 1 的整数。这原本是 Javascript中可以用 Number 表示的最大数字。BigInt 可以表示任意大的整数。


以下操作符可以和 BigInt 一起使用：
    +、`*`、`-`、`**`、`%` 。除 >>> （无符号右移）之外的 位操作 也可以支持。因为 BigInt 都是有符号的， >>> （无符号右移）不能用于 BigInt。

【注意】
- 不能用于 Math 对象中的方法；
- 不能和任何 Number 实例混合运算，两者必须转换成同一种类型。在两种类型来回转换时要小心，因为 BigInt 变量在转换成 Number 变量时可能会丢失精度。
- 当使用 BigInt 时，带小数的运算会被取整。
- / 操作符 不会返回小数部分
- BigInt 和 Number 不是严格相等的，但是宽松相等的。
- Object 包装的 BigInts 使用 object 的比较规则进行比较，只用同一个对象在比较时才会相等。



eg：
	
	const theBiggestInt = 9007199254740991n;

    const alsoHuge = BigInt(9007199254740991);
    // ↪ 9007199254740991n
	
	typeof 1n === 'bigint'; // true

	0n === 0
    // ↪ false
    0n == 0
    // ↪ true
    
    0n === Object(0n); // false
    Object(0n) === Object(0n); // false
    
    const o = Object(0n);
    o === o // true

静态方法：

    BigInt.asIntN()
        将 BigInt 值转换为一个 -2width-1 与 2width-1-1 之间的有符号整数。
    BigInt.asUintN()
        将一个 BigInt 值转换为 0 与 2width-1 之间的无符号整数。
```

### sort排序

```js
可以使用localeCompare() 方法来实现中文按照拼音排序，方法相当简单

var array = ['成长型','稳健型','保守型','稳健型','成长型','进取型','进取型','稳健型','平衡型','成长型','进取型'];

array = array.sort(function compareFunction(item1, item2) {

  return item1.localeCompare(item2);

});

 

按照特定的中文字符进行排序的方法

var array = ['成长型','稳健型','平衡型','保守型','稳健型','成长型','进取型','进取型','稳健型','平衡型','成长型','进取型','保守型'];

var order=['保守型','稳健型','平衡型','成长型','进取型'];

array = array.sort(function compareFunction(item1, item2) {

  return order.indexOf(item1)- order.indexOf(item2);

});
```

### 数组扁平化

```js
ES6

arr.flat(Infinity);

数组中全为数字

arr.toString().split(',').map(Number)

递归

const myFlat = (arr) => {
  const helper = (arr) => {
    let res = [];
    for (const item of arr) {
      if (typeof item === 'object') {
        res.push(...item);
      } else {
        res.push(item);
      }
    }
    return res;
  };

  while (arr.some(item => typeof item === 'object')) {
    arr = helper(arr);
  }
  return arr;
};

栈代替递归

const myFlat1 = (input) => {
  const stack = [...input];
  const res = [];
  while (stack.length) {
    const next = stack.pop();
    if (Array.isArray(next)) {
      stack.push(...next);
    } else {
      res.push(next);
    }
  }
  return res.reverse();
};

正则过滤

function myFlat3(arr) {
  let str = JSON.stringify(arr);
  // 过滤所有的中中括号
  str = str.replace(/(\[|\])/g, '');
  str = '[' + str + ']';
  return JSON.parse(str);
}

生成器

function* myFlat2(array, depth = 1) {
  for (const item of array) {
    if (Array.isArray(item) && depth > 0) {
      yield* myFlat2(item, depth - 1);
    } else {
      yield item;
    }
  }
}
```

### 页面显示隐藏

```js
1、pageshow/pagehide 会在页面跳转前后触发

    // http://127.0.0.1:5503/html/grid.html,  f12 查看 true
	window.addEventListener('pageshow', function () {
        localStorage.setItem('flag', true)
    });
    window.addEventListener('pagehide', function () {
        localStorage.setItem('flag', false)
    });

	// http://127.0.0.1:5503/html/girde.html,  f12 查看 false

2、visibilitychange 会在页面被隐藏或显示时(如浏览器tab切换、窗口被最小化等)触发，配合 Document.hidden 可以实现如 "页面被切到后台时停止声音播放，切到前台时恢复播放"等功能。

 // 设置隐藏属性和改变可见属性的事件的名称
// 不同浏览器 时间名， 可见属性名字不一样
    var hidden, visibilityChange; 
    if (typeof document.hidden !== "undefined") { // Opera 12.10 and Firefox 18 and later support 
      hidden = "hidden";
      visibilityChange = "visibilitychange";
    } else if (typeof document.msHidden !== "undefined") {
      hidden = "msHidden";
      visibilityChange = "msvisibilitychange";
    } else if (typeof document.webkitHidden !== "undefined") {
      hidden = "webkitHidden";
      visibilityChange = "webkitvisibilitychange";
    }

    var videoElement = document.getElementById("videoElement");

    // 如果页面是隐藏状态，则暂停视频
    // 如果页面是展示状态，则播放视频
    function handleVisibilityChange() {
      if (document[hidden]) {
        videoElement.pause();
      } else {
        videoElement.play();
      }
    }

    // 如果浏览器不支持addEventListener 或 Page Visibility API 给出警告
    if (typeof document.addEventListener === "undefined" || typeof document[hidden] === "undefined") {
      console.log("This demo requires a browser, such as Google Chrome or Firefox, that supports the Page Visibility API.");
    } else {
      
    // 处理页面可见属性的改变
    document.addEventListener(visibilityChange, handleVisibilityChange, false);
      
    // 当视频暂停，设置title
    // This shows the paused
    videoElement.addEventListener("pause", function(){
      document.title = 'Paused';
    }, false);
      
    // 当视频播放，设置title
    videoElement.addEventListener("play", function(){
      document.title = 'Playing'; 
    }, false);
  }
```

### 路由模式

```js
扩展　
	hash模式是利用window.location.hash来完成页内跳转的，跳转后会改变URL路径，改变后的URL路径内包含 '#'，就像一个锚点链接路径一样。

	history模式是利用window.history.pushState来完成页内跳转的，跳转后也会改变URL路径，但改变后的路径不包含'#'，和正常的URL路径并无区别。为了防止服务端误认为它是一个新请求，需要在服务端做相应的配置才能避免出错（让服务端接收到类似请求后返回单页应用的主页地址即可）。
```

### 浏览器的缓存机制

```js
// 

1、HTTP文件缓存
	通过 mate 标签
	<meta http-equiv="Cache-Control" content="max-age=7200">
2、LocalStorage、 sessionStorage、cookie、indexDB、webSQL
	
3、Application Cathe
	就是 minifest 文件，在 html 标签应用
    
4、CatheStorage
	window.caches.open() // 打开一个caches对象并且返回promise 对象；
    window.caches.has( ) //检查如果包含一个caches对象，则返回promise对象；
    window.caches.delete()
    window.caches.key()
    window.caches.metch()

5、serviceWorker

if('serviceWorker' in navigator) {
    console.log('支持')
    window.navigator.serviceWorker
      .register('/sw.js', {
        scope: '/' // 作用域
      })
      .then(registration => {
        console.log('注册成功', registration.update)
      })
      .catch(error => {
        console.log('注册失败', error.message)
      })
  } else {
    console.log('不支持')
  }
 
 
self.addEventListener('install', event => {
    // event.waitUtil 用于在安装成功之前执行一些预装逻辑
    // 建议只做一些非常重要资源的缓存，减少安装失败的概率
    // 安装成功后 ServiceWorker 状态会从 installing 变installed
    event.waitUntil(
        // 使用 cache API 打开指定的 cache 文件
        caches.open('版本标识').then(cache => {
            console.log(cache);
            // 添加要缓存的资源列表
            return cache.addAll('要缓存的文件列表');
        })
    );
})


serviceWorker 与 WebWork
后者
```

### 浏览器指纹

> 什么是浏览器指纹?
> 我们常说的指纹，都是指人们手指上的指纹，因具有唯一性，所以可以被用来标识一个人的唯一身份。而浏览器指纹是指仅通过浏览器的各种信息，如CPU核心数、显卡信息、系统字体、屏幕分辨率、浏览器插件等组合成的一个字符串，就能近乎绝对定位一个用户，就算使用浏览器的隐私窗口模式，也无法避免。

#### 实现浏览器指纹的技术有哪些？

1、基本指纹

浏览器基本指纹是任何浏览器都具有的特征标识，比如屏幕分辨率、硬件类型、操作系统、用户代理（User agent）、系统字体、语言、浏览器插件 、浏览器扩展、浏览器设置 、时区差等众多信息，这些指纹信息“类似”人类的身高、年龄等，有很大的冲突概率，只能作为辅助识别。可以在该网址进行查看本地浏览器的基本特征，https://www.whatismybrowser.com/

2、高级指纹

浏览器高级指纹与基本指纹的区别是，基本指纹就像是人的外貌特征，外貌可以用男女、身高、体重区分，然而这些特征不能对某个人进行唯一性标识，仅使用基本指纹也无法对客户端进行唯一性判定，基于HTML5的诸多高级功能就能生成高级指纹了。

- Canvas指纹

> Canvas（画布）是HTML5中一种动态绘图的标签，可以使用其生成甚至处理高级图片

原理大致如下：

相同的HTMLCanvasElement元素绘制操作，在不同操作系统、不同浏览器上，产生的图片内容不完全相同。在图片格式上，不同浏览器使用了不同的图形处理引擎、不同的图片导出选项、不同的默认压缩级别等。在像素级别来看，操作系统各自使用了不同的设置和算法来进行抗锯齿和子像素渲染操作。即使相同的绘图操作，产生的图片数据的CRC检验也不相同。Canvas几乎已被所有主流浏览器支持，可以通过大部分的PC、平板、智能手机访问。

> [在线测试地址](https://www.browserleaks.com/canvas)
>
> [生成canvas 指纹第三方库](https://github.com/fingerprintjs/fingerprintjs)
>
> [FingerprintJS Pro 文档](https://dev.fingerprint.com/docs)

- WebGL指纹

> 通过HTMLCanvasElement元素可以获取到Webgl对象（canvas.getContext(“webgl”)）,通过此对象可以获取到用户的硬件信息，比如显卡名称、显卡型号、显卡制造商等，比如：ANGLE (NVIDIA GeForce GTX 1050 Ti Direct3D11 vs_5_0 ps_5_0)，Google Inc.。
>
> 由于硬件一般是不会随意更换的，有些是电脑买来到电脑报废就没更换过硬件，电脑硬件种类也比较多，虽然非常大的碰撞率，但是依然可以被用来当做用户指纹的一部分，收集用户的信息也多，就越能代表用户的唯一指纹，这点不可忽视

- AudioContext指纹

> HTML5提供给JavaScript编程用的Audio API则让开发者有能力在代码中直接操作原始的音频流数据，对其进行任意生成、加工、再造，诸如提高音色，改变音调，音频分割等多种操作，甚至可称为网页版的Adobe Audition。
>

AudioContext指纹原理大致如下：

方法一：生成音频信息流(三角波)，对其进行FFT变换，计算SHA值作为指纹。

方法二：生成音频信息流（正弦波），进行动态压缩处理，计算MD5值。

两种方法都是在音频输出到音频设备之前进行清除，用户根本就毫无察觉就被获取了指纹。

AudioContext指纹基本原理：

> 主机或浏览器硬件或软件的细微差别，导致音频信号的处理上的差异，相同器上的同款浏览器产生相同的音频输出，不同机器或不同浏览器产生的音频输出会存在差异。
>

从上可以看出AudioContext和Canvas指纹原理很类似，都是利用硬件或软件的差异，前者生成音频，后者生成图片，然后计算得到不同哈希值来作为标识。音频指纹测试地址：https://audiofingerprint.openwpm.com/

- WebRTC指纹

> WebRTC（网页实时通信，Web Real Time Communication），是可以让浏览器有音视频实时通信的能力，它提供了三个主要的API来让JS可以实时获取和交换音视频数据，MediaStream、RTCPeerConnection和RTCDataChannel。当然如果要使用WebRTC获得通信能力，用户的真实ip就得暴露出来（NAT穿透），所以RTCPeerConnection就提供了这样的API，直接使用JS就可以拿到用户的IP地址。用户的内网IP地址也是大多数情况下不会改变，所以也是可以用来当做用户指纹的其中一个因子。

- 如何防止被生成 用户指纹

	...

	> [拓展链接](https://blog.csdn.net/weixin_39521520/article/details/111039051)

	


###  js 中在 new 的时候发生了什么

> 1. 创建了一个新对象
> 2. 链接到原型
> 3. 绑定 this 指向 
> 4. 返回这个对象

```js
function _new() {
  let obj = {};
  let Con = [].shift.call(arguments);
  obj.__proto__ = Con.prototype;
  let result = Con.apply(obj, arguments);
  return typeof obj === "object" ? obj : {};
}
```

### JSBridge

> JSBridge 就是 JavaScript 和 Native(IOS/Android)之间的桥梁，提供两者相互调用的能力

### 生成随机字符串

```js
1、方式1
// 可生成 10-11 为随机字符串，n 最大应该是 10
const random = (n) => Math.random().toString(36).slice(2, 2 + n);

2、方式2
function randomString(e) {  
  var e = e || 32,
  t = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678",
  a = t.length,
  n = "";
  for (i = 0; i < e; i++) n += t.charAt(Math.floor(Math.random() * a));
  return n
}

3、方式3
// 范围随机数， 最小值+（0 - max-mix）区间值
function GetRandomNum(Min,Max){
    var Range = Max - Min;
    var Rand = Math.random();
    return (Min + Math.round(Rand * Range));
}

```

Number.isNaN 与 isNaN

> 确定传递的值是否为 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)
>
> 全局的 isNaN 方法 会将不是Number 类型的数 转化为 Number 类型再判断
>
> es5的 Number.isNaN 不会对传入值 进行 类型转换，只对 NaN 返回 true

```js
isNaN(NaN)
Number.isNaN(NaN)
// true

isNaN('1a') // true
Number.isNaN('1a') // false
```

### 多版本chrome浏览器切换

> [教程地址](https://liubing.me/chrome-multiple-versions.html)

### 图片防盗链原理

> **防盗链**，就是防有人盗用你的链接。别人在他的网站上引用了你的资源 (图片，音频)，这样就会浪费你的流量，资源被引用的多了起来，你这边的服务器可能就扛不住挂了，你说这是多么悲哀的事情！

实现图片防盗链：

通过获取请求头的 **Host**(请求的主机) 和 **Referer**(来源) 两个参数。如果 Host和Referer 值不相同说明是其他地方引用了你的视图资源，可以 返回一张默认图片

```js
let fs = require('fs');
let path = require('path');
let http = require('http');
let url = require('url');
let static = path.resolve(__dirname, "public"); // 静态资源目录
let server = http.createServer((req, res) => {
  let referer = req.headers['referer'] || req.headers['referered']; // 兼容性处理

  let pos = path.join(static, url.parse(req.url).pathname); // 处理成绝对路径
  fs.stat(pos, err => { // 先判断文件存不存在
    if (!err) {
      if (referer) { // 当存在referer
        let refererHost = url.parse(referer).hostname; // referer地址
        let host = req.headers['host'].split(':')[0]; // host地址

        if (refererHost !== host) {
          // 若不一致，则替换图片
          fs.createReadStream(path.join(static, '/fail.png')).pipe(res);
        } else {
          fs.createReadStream(pos).pipe(res);
        }
      } else {
        // 第一次index.html的显示
        fs.createReadStream(pos).pipe(res);
      }
    } else {
      res.end("Not Found");
    }
  });

})
server.listen('8888', () => {
  console.log('服务器开启成功', '端口:8888');
})
```

###  什么是浏览器的关键渲染路径

1.  DOM

	生成 DOM 会从远程下载 Byte，并根据相应的编码 (如 `utf8`) 转化为字符串，通过 AST 解析为 Token，生成 Node 及最后的 DOM。

2. CSSOM

	当解析 CSS 文件时，最终会生成 CSSOM

3. Render Tree

	DOM 与 CSSOM 会一起生成 Render Tree，只包含渲染网页所需的节点。

4. Layout

	计算每一个元素在设备视口内的确切位置和大小

5. Paint

	将渲染树中的每个节点转换成屏幕上的实际像素，这一步通常称为绘制或栅格化

### 什么是安全正则

> 有些正则可能导致死机
>
> ```js
> const safe = require("safe-regex");
> const re = /(x+x+)+y/;
> 
> // 能跑死 CPU 的一个正则
> re.test("xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");
> ```

[safe-regex](https://github.com/substack/safe-regex) 用于检测一个正则是否安全

```js
const safe = require("safe-regex");

/ 使用 safe-regex 判断正则是否安全
safe(re); // false
```

### 在网站开发中，常见的跨域问题。

 

1. 不同域名属于跨域，如：www.a.com 和www.b.com，另外www.a.com 和www.a.com.cn也属于不同域名

2. 主域名和子域名（二级域名、三级域名等）跨域，如：www.a.com 和 bbs.a.com 跨域

3. 不同协议属于跨域，如：http://www.a.com 和 https://www.a.com

4. Ip和域名属于跨域，如：123.23.23.12 和 www.a.com

## 方法

### npm i 与 npm ci

> 两个命名都是下载依赖，不同的是 npm ci 不改变 package.lock.json

npm ci (6.0 版本以上)

- 会删除项目中的 node_modules 文件夹； 

- 会依照项目中的package.json 来安装确切版本的依赖项； 

- 不像 npm install, npm ci 不会修改你的 package-lock.json 但是它确实期望你的项目中有一个 - package-lock.json 文件 - 如果你没有这个文件， npm ci 将不起作用，此时必须使用 npm install



### 禁止浏览器debugger 行为

打开控制台时，一直debugger

```js
setInterval(() => {
    (
        function () {
            return false;
        }
        ["constructor"]("debugger")
        ["call"]()
    );
}, 50);

等价于
setInterval(() => {
    Function("debugger")();
}, 50);
```

判断是否打开控制台

> 第三方库：devtools-detector
>
> 

```js
<script src="/js/devtools-detector.js"></script>

var view = document.createElement('div');
    document.body.appendChild(view);
    let timer = null;
    devtoolsDetector.addListener(function(isOpen) {
        console.log(isOpen);
        if(isOpen) {
            alert('请尊重他人劳动成果!')
            timer = setInterval(() => {
                (
                    function () {
                        return false;
                    }
                    ["constructor"]("debugger")
                    ["call"]()
                );
            }, 50);
        } else {
            timer && clearInterval(timer)
        }
        // view.innerText = isOpen
        // ? 'devtools status: open'
        // : 'devtools status: close';
    });
    devtoolsDetector.launch();
```

### 十进制二进制相互转换

```js
// 二进制转十进制

function binaryToDecimal(num) {
  let negative = false;
  if (num < 0) {
    num = -num;
    negative = true;
  }
  num += "";
  let point = num.indexOf(".");
  let int;
  let intDecimal = 0;
  let float;
  let floatDecimal = 0;

  if (point > -1) {
    int = num.slice(0, point);
    float = `${num.slice(point + 1)}`;
  } else {
    int = num;
  }
  if (int) {
    for (let i = 0; i < int.length; i++) {
      intDecimal += int[int.length - i - 1] * Math.pow(2, i);
    }
  }

  if (float) {
    for (let i = 0; i < float.length; i++) {
      floatDecimal += float[i] * Math.pow(2, -(i + 1));
    }
  }
  let result = "";
  if (negative) {
    result += "-";
  }
  result += intDecimal + floatDecimal;
  return +result;
}
// 十进制转二进制

function decimalToBinary(num) {
  let negative = false;
  if (num < 0) {
    num = -num;
    negative = true;
  }
  num += "";
  let point = num.indexOf(".");
  let int;
  let intBinary = "";
  let float;
  let floatBinary = "";

  if (point > -1) {
    int = num.slice(0, point);
    float = Number(`0${num.slice(point)}`);
  } else {
    int = num;
  }
  if (int) {
    int = +int;
    while (int >= 1) {
      intBinary = (int % 2) + intBinary;
      int = (int / 2) | 0;
    }
  }

  if (float) {
    while (float) {
      float *= 2;
      if (float >= 1) {
        floatBinary += 1;
      } else {
        floatBinary += 0;
      }
      float = float % 1;
    }
  }
  let result = "";
  if (negative) {
    result += "-";
  }
  result += `${intBinary || 0}.${floatBinary}`;
  return result;
}
```



### 浏览器复制

```js
// 等同于触发ctrl+c
function copy(copyContent, isAlert, maxLength) {
    if (window.clipboardData) {
        window.clipboardData.setData("Text", copyContent);
    }
    if (document.execCommand) {
        var copyTextarea = document.createElement("textarea");
        document.body.appendChild(copyTextarea);
        copyTextarea.innerText = copyContent;
        copyTextarea.select();
        document.execCommand("Copy");
        copyTextarea.parentNode.removeChild(copyTextarea);
    }
    if (isAlert) {
        if (typeof isAlert === "function") {
            isAlert(copyContent);
        } else {
            maxLength && (copyContent.length > maxLength) && (copyContent = copyContent.substr(0, maxLength) + "…");
            alert(`“${copyContent}”复制成功！`);
        }
    }
}
```

### addeventListener  第三个参数

```
options 可选
一个指定有关 listener 属性的可选参数对象。可用的选项如下：

- capture:  
Boolean，表示 listener 会在该类型的事件捕获阶段传播到该 EventTarget 时触发。

- once:  
Boolean，表示 listener 在添加之后最多只调用一次。如果是 true listener 会在其被调用之后自动移除。

- passive: 
Boolean，设置为 true 时，表示 listener 永远不会调用preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。查看 使用 passive 改善的滚屏性能 了解更多。
signal：AbortSignal，该 AbortSignal 的 abort() 方法被调用时，监听器会被移除。
```



### 浏览器中 Frame 与 Event Loop 的关系是什么

> 浏览器组成中有两大引擎，JS 引擎和渲染引擎。
>
> Frame(帧)是渲染引擎每隔 16ms(默认 60fps)将渲染树渲染、合成成位图的结果
>
> 每次 Event Loop 是 JS 引擎执行的一个周期，执行过程中可能依赖渲染引擎的执行结果，比如访问 DOM 和 CSSOM，也可能影响渲染引擎绘制帧，比如调用 requestAnimationFrame，在每个帧开始绘制时执行一段回调函数(通常包含影响渲染结果的代码)
>
> 因此 Frame 和 Event Loop 是相对独立运行的，但是 Event Loop 中执行的代码可能依赖或影响 Frame



### 读取二进制信息

> ArrayBuffer，二进制数组
>
> [推荐链接](https://zh.javascript.info/arraybuffer-binary-arrays)
>
> [总](https://zh.javascript.info/binary)

#### [TypedArray](https://zh.javascript.info/arraybuffer-binary-arrays#typedarray)

所有这些视图（`Uint8Array`，`Uint32Array` 等）的通用术语是 [TypedArray](https://tc39.github.io/ecma262/#sec-typedarray-objects)。它们都享有同一组方法和属性。

请注意，没有名为 `TypedArray` 的构造器，它只是表示 `ArrayBuffer` 上的视图之一的通用总称术语：`Int8Array`，`Uint8Array` 及其他，很快就会有完整列表。

当你看到 `new TypedArray` 之类的内容时，它表示 `new Int8Array`、`new Uint8Array` 及其他中之一。

类型化数组的行为类似于常规数组：具有索引，并且是可迭代的。

一个类型化数组的构造器（无论是 `Int8Array` 或 `Float64Array`，都无关紧要），其行为各不相同，并且取决于参数类型。

参数有 5 种变体：

```javascript
new TypedArray(buffer, [byteOffset], [length]);
new TypedArray(object);
new TypedArray(typedArray);
new TypedArray(length);
new TypedArray();
```

如果二进制数据实际上是一个字符串怎么办？

> 内建的 [TextDecoder](https://encoding.spec.whatwg.org/#interface-textdecoder) 对象在给定缓冲区（buffer）和编码格式（encoding）的情况下，允许将值读取为实际的 JavaScript 字符串。

#### TextDecoder

```js
let decoder = new TextDecoder([label], [options]);
```

- **`label`** —— 编码格式，默认为 `utf-8`，但同时也支持 `big5`，`windows-1251` 等许多其他编码格式。

- `options`

	 

	—— 可选对象：

	- **`fatal`** —— 布尔值，如果为 `true` 则为无效（不可解码）字符抛出异常，否则（默认）用字符 `\uFFFD` 替换无效字符。
	- **`ignoreBOM`** —— 布尔值，如果为 `true` 则 BOM（可选的字节顺序 Unicode 标记），很少需要使用。

解码：

```js
let str = decoder.decode([input], [options]);
```

- **`input`** —— 要被解码的 `BufferSource`。

- `options`

	 

	—— 可选对象：

	- **`stream`** —— 对于解码流，为 true，则将传入的数据块（chunk）作为参数重复调用 `decoder`。在这种情况下，多字节的字符可能偶尔会在块与块之间被分割。这个选项告诉 `TextDecoder` 记住“未完成”的字符，并在下一个数据块来的时候进行解码。

通过创建子数组视图来解码

```js
let uint8Array = new Uint8Array([0, 72, 101, 108, 108, 111, 0]);

// 该字符串位于中间
// 在不复制任何内容的前提下，创建一个新的视图
let binaryString = uint8Array.subarray(1, -1);

alert( new TextDecoder().decode(binaryString) ); // Hello
```

#### [TextEncoder](https://zh.javascript.info/text-decoder#textencoder)

> [TextEncoder](https://encoding.spec.whatwg.org/#interface-textencoder) 做相反的事情 —— 将字符串转换为字节。

```js
let encoder = new TextEncoder();
```

只支持 `utf-8` 编码。

它有两种方法：

- **`encode(str)`** —— 从字符串返回 `Uint8Array`。
- **`encodeInto(str, destination)`** —— 将 `str` 编码到 `destination` 中，该目标必须为 `Uint8Array`。

```javascript
let encoder = new TextEncoder();

let uint8Array = encoder.encode("Hello");
alert(uint8Array); // 72,101,108,108,111
```

#### Blob

> `arrayBuffer` 和视图（view）都是 ECMA 标准的一部分，是 JavaScript 的一部分。
>
> 在浏览器中，还有其他更高级的对象，特别是 `Blob`，在 [File API](https://www.w3.org/TR/FileAPI/) 中有相关描述。
>
> `Blob` 由一个可选的字符串 `type`（通常是 MIME 类型）和 `blobParts` 组成 —— 一系列其他 `Blob` 对象，字符串和 `BufferSource`。

![image-20220705093349805](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220705093349805.png)

构造函数的语法为：

```javascript
new Blob(blobParts, options);
```

- **`blobParts`** 是 `Blob`/`BufferSource`/`String` 类型的值的数组。

- `options`

	 

	可选对象：

	- **`type`** —— `Blob` 类型，通常是 MIME 类型，例如 `image/png`，
	- **`endings`** —— 是否转换换行符，使 `Blob` 对应于当前操作系统的换行符（`\r\n` 或 `\n`）。默认为 `"transparent"`（啥也不做），不过也可以是 `"native"`（转换）。

eg:

```js
// 从字符串创建 Blob
let blob = new Blob(["<html>…</html>"], {type: 'text/html'});
// 请注意：第一个参数必须是一个数组 [...]

// 从类型化数组（typed array）和字符串创建 Blob
let hello = new Uint8Array([72, 101, 108, 108, 111]); // 二进制格式的 "hello"

let blob = new Blob([hello, ' ', 'world'], {type: 'text/plain'});
```

我们可以用 `slice` 方法来提取 `Blob` 片段：

```js
blob.slice([byteStart], [byteEnd], [contentType]);
```

- **`yteStart`** —— 起始字节，默认为 0。
- **`byteEnd`** —— 最后一个字节（专有，默认为最后）。
- **`contentType`** —— 新 blob 的 `type`，默认与源 blob 相同。

> **`Blob` 对象是不可改变的**
>
> 我们无法直接在 `Blob` 中更改数据，但我们可以通过 `slice` 获得 `Blob` 的多个部分，从这些部分创建新的 `Blob` 对象，将它们组成新的 `Blob`，等。
>
> 这种行为类似于 JavaScript 字符串：我们无法更改字符串中的字符，但可以生成一个新的改动过的字符串。

Blob 作用于 URL 及转换

- **URL.createObjectURL(blob)**

	- 如果介意内存，我们需要撤销（revoke）它们
	- 直接访问 `Blob`，无需“编码/解码”

	```html
	<!-- download 特性（attribute）强制浏览器下载而不是导航 -->
	<a download="hello.txt" href='#' id="link">Download</a>
	
	<script>
	let blob = new Blob(["Hello, world!"], {type: 'text/plain'});
	
	link.href = URL.createObjectURL(blob);
	</script>
	```

	

- **Blob 转换为 data url**

	- 无需撤销（revoke）任何操作。
	- 对大的 `Blob` 进行编码时，性能和内存会有损耗。

	```js
	// `Blob` 转换为 base64
	let link = document.createElement('a');
	link.download = 'hello.txt';
	
	let blob = new Blob(['Hello, world!'], {type: 'text/plain'});
	
	let reader = new FileReader();
	reader.readAsDataURL(blob); // 将 Blob 转换为 base64 并调用 onload
	
	reader.onload = function() {
	  link.href = reader.result; // data url
	  link.click();
	};
	```

- #### image to blob

	> 对于页面截屏，我们可以使用诸如 https://github.com/niklasvh/html2canvas 之类的库。它所做的只是扫一遍浏览器页面，并将其绘制在 `<canvas>` 上。然后，我们就可以像上面一样获取一个它的 `Blob`。

	

	```js
	// 获取任何图像
	let img = document.querySelector('img');
	
	// 生成同尺寸的 <canvas>
	let canvas = document.createElement('canvas');
	canvas.width = img.clientWidth;
	canvas.height = img.clientHeight;
	
	let context = canvas.getContext('2d');
	
	// 向其中复制图像（此方法允许剪裁图像）
	context.drawImage(img, 0, 0);
	// 我们 context.rotate()，并在 canvas 上做很多其他事情
	
	// toBlob 是异步操作，结束后会调用 callback
	canvas.toBlob(function(blob) {
	  // blob 创建完成，下载它
	  let link = document.createElement('a');
	  link.download = 'example.png';
	
	  link.href = URL.createObjectURL(blob);
	  link.click();
	
	  // 删除内部 blob 引用，这样浏览器可以从内存中将其清除
	  URL.revokeObjectURL(link.href);
	}, 'image/png');
	```

- #### [Blob 转换为 Stream](https://zh.javascript.info/blob#blob-zhuan-huan-wei-stream)

	> 当我们读取和写入超过 `2 GB` 的 blob 时，将其转换为 `arrayBuffer` 的使用对我们来说会更加占用内存。这种情况下，我们可以直接将 blob 转换为 stream 进行处理。

	`Blob` 接口里的 `stream()` 方法返回一个 `ReadableStream`，在被读取时可以返回 `Blob` 中包含的数据。

	如下所示：

	```javascript
	// 从 blob 获取可读流（readableStream）
	const readableStream = blob.stream();
	const stream = readableStream.getReader();
	
	while (true) {
	  // 对于每次迭代：data 是下一个 blob 数据片段
	  let { done, data } = await stream.read();
	  if (done) {
	    // 读取完毕，stream 里已经没有数据了
	    console.log('all blob processed.');
	    break;
	  }
	
	  // 对刚从 blob 中读取的数据片段做一些处理
	  console.log(data);
	}
	```





### 浏览器访问媒体

```js
MediaDevices
	MediaDevices 接口提供访问连接媒体输入的设备，如照相机和麦克风，以及屏幕共享等。它可以使你取得任何硬件资源的媒体数据。

1、访问相机
    let constraints = {
	 audio: false,// 不获取音频
      video: {
        width: { ideal: 1280 }, // 分辨率
        height: { ideal: 720 },
        width: { min: 1024, ideal: 1280, max: 1920 },
        height: { min: 776, ideal: 720, max: 1080 }
        facingMode: "user",// 优先使用 user：前置，environment：后置📷
        facingMode: { exact: "environment" }// 强制使用后置📷
      }
    }
    // 此方法触发用户授权
	navigator.mediaDevices.getUserMedia(constraints)
    .then(function(stream) {
        var video = document.querySelector('video');
      /* 使用这个 stream stream */
        // 防止在新的浏览器里使用它，应为它已经不再支持了
        if ("srcObject" in video) {
           video.srcObject = stream;
         } else {
           // 防止在新的浏览器里使用它，应为它已经不再支持了
           video.src = window.URL.createObjectURL(stream);
         }
    })
    .catch(function(err) {
      /* 处理 error */
    });

返回的 promise 对象可能既不会 resolve 也不会 reject，因为用户不是必须选择允许或拒绝。
eg:
// 音视频测试
    async getMedia() {
        let option = {
            audio: true,
            video: {width: 360, height: 360}
        };
        let MediaStream = await navigator.mediaDevices.getUserMedia(option)
        let mst = typeof MediaStream.stop === 'function'? MediaStream:MediaStream.getTracks()[1];
        this.setState({
            MediaStreamTrack: mst,
            isPhotograph: false
        })
        let video = document.getElementById("my-video")
        video.srcObject = MediaStream;

        video.onloadedmetadata = (e)=> {
            video.play();
        };
    }
//拍照
    const takePhoto = ()=> {
        //获得Canvas对象
        let video = document.getElementById("my-video");
        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext('2d');
        ctx.drawImage(video, 0, 0,360, 360);
        let imgData = document.getElementById("canvas").toDataURL("image/jpeg");
    }

2、getDisplayMedia() 
	方法提示用户去选择和授权捕获展示的内容或部分内容（如一个窗口）在一个  MediaStream 里. 然后，这个媒体流可以通过使用 MediaStream Recording API 被记录或者作为WebRTC 会话的一部分被传输。
    
async function startCapture(displayMediaOptions) {
  let captureStream = null;

  try {
    captureStream = await navigator.mediaDevices.getDisplayMedia(displayMediaOptions);
  } catch(err) {
    console.error("Error: " + err);
  }
  return captureStream;
}

3、屏幕录制
	MediaRecorder，创建一个新的MediaRecorder对象，对指定的MediaStream 对象进行录制，支持的配置项包括设置容器的 MIME 类型 (例如"video/webm" 或者 "video/mp4") 和音频及视频的码率或者二者同用一个码率。
    ▪配置项：
    	mimeType：  返回 MediaRecorder 对象创建时选择器选择的录制容器的 MIME type
    	state：返回录制对象MediaRecorder  的当前状态 (闲置中，录制中或者暂停 ) (inactive, recording, or paused.)
	    stream： 返回录制器对象 MediaRecorder 创建时构造函数传入的 stream 对象
        ignoreMutedMedia：   用以指定 MediaRecorder是否录制无声的输入源。默认值是 false
	    videoBitsPerSecond：返回视频采用的编码比率。它可能和构造函数的设置比率不同
        audioBitsPerSecond：返回音频采用的编码比率，它可能和构造函数中设置的比率不同.
        
    ▪MediaRecoder主要方法
    -MediaRecorder.isTypeSupported()
    静态方法，判断给定的 MIME 类型是否支持。返回Boolean (en-US)类型的值。
    -MediaRecorder.ondataavailable
    该事件可用于获取录制的媒体资源 (在事件的 data 属性中会提供一个可用的 Blob 对象.)
    -MediaRecorder.onerror (en-US)
    错误监听
    -MediaRecorder.onpause (en-US)
    媒体暂停录制时触发（MediaRecorder.pause()）.
    -MediaRecorder.onresume (en-US)
    在暂停后回复录制视频时触发（MediaRecorder.resume() (en-US)）.
    -MediaRecorder.onstart (en-US)
    在媒体开始录制时触发（MediaRecorder.start() (en-US)）.
    -MediaRecorder.onstop (en-US)
    在媒体录制结束时、媒体流（MediaStream）结束时、或者调用MediaRecorder.stop() (en-US) 方法后触发。
    
屏幕共享，录屏代码如下：
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>屏幕共享</title>
</head>
<style>
    #video {
        border: 1px solid #999;
        width: 98%;
        max-width: 860px;
    }

    .error {
        color: red;
    }

    .warn {
        color: orange;
    }

    .info {
        color: darkgreen;
    }
</style>
<body>
    <p>This example shows you the contents of the selected part of your display.
        Click the Start Capture button to begin.
    </p>
        
    <p><button id="start">Start Capture</button>&nbsp;<button id="stop">Stop Capture</button><a id="download" href="" download="test.mp4">download</a></p>
    
    <video id="video" autoplay></video>
    <br>
    
    <strong>Log:</strong>
    <br>
    <pre id="log"></pre>

    <script>
        const videoElem = document.getElementById("video");
        const logElem = document.getElementById("log");
        const startElem = document.getElementById("start");
        const stopElem = document.getElementById("stop");
        const download = document.getElementById('download')
        let chunk,mediaStream,recorder;
        // Options for getDisplayMedia()
        var displayMediaOptions = {
            video: {
                cursor: "always"
            },
            audio: false
        };

        // Set event listeners for the start and stop buttons
        startElem.addEventListener("click", function(evt) {
            startCapture();
        }, false);

        stopElem.addEventListener("click", function(evt) {
            stopCapture();
        }, false);

        console.log = msg => logElem.innerHTML += `${msg}<br>`;
        console.error = msg => logElem.innerHTML += `<span class="error">${msg}</span><br>`;
        console.warn = msg => logElem.innerHTML += `<span class="warn">${msg}<span><br>`;
        console.info = msg => logElem.innerHTML += `<span class="info">${msg}</span><br>`;
        function dumpOptionsInfo() {
            const videoTrack = videoElem.srcObject.getVideoTracks()[0];

            console.info("Track settings:");
            console.info(JSON.stringify(videoTrack.getSettings(), null, 2));
            console.info("Track constraints:");
            console.info(JSON.stringify(videoTrack.getConstraints(), null, 2));
        }
        function stopCapture(evt) {
            let tracks = videoElem.srcObject.getTracks();

            tracks.forEach(track => track.stop());
            videoElem.srcObject = null;
            recorder.stop();
        }
        async function startCapture() {
            logElem.innerHTML = "";
            try {
                let stream = await navigator.mediaDevices.getDisplayMedia(displayMediaOptions);
                videoElem.srcObject = stream;
                mediaStream = stream;
                recorder = new MediaRecorder(stream);
                // 调用它用来处理 dataavailable 事件，该事件可用于获取录制的媒体资源 (在事件的 data 属性中会提供一个可用的 Blob 对象.)
                recorder.ondataavailable = e => {
                    chunk = e.data;
                    download.href = URL.createObjectURL(chunk);
                };
                recorder.start();
                dumpOptionsInfo();
            } catch(err) {
                console.error("Error: " + err);
            }
        }
    </script>
</body>
</html>
```

```js
前端实现录音功能

// 预先设置一个变量来存MediaRecorder实例
let mediaRecorder  = null;
// 首先打开麦克风
navigator.mediaDevices.getUserMedia({audio: true}).then(stream=>{
	let chunks = [];
	mediaRecorder = new MediaRecorder(stream);
	mediaRecorder.start();
	
	// 录音开始事件监听（即调用 mediaRecorder.start()时会触发该事件）
	mediaRecorder.onstart = () =>{
        console.log("record start")
    }

	// 录音可用事件监听，发生于mediaRecorder.stop()调用后，mediaRecorder.onstop 前
    mediaRecorder.ondataavailable = (e) =>{
    	console.log("dataavailable")
    	console.log(e)
        chunks.push(e.data)
    }
    
    // 录音结束事件监听，发生在mediaRecorder.stop()和 mediaRecorder.ondataavailable 调用后
    mediaRecorder.onstop = () =>{
    	console.log("record end")
    	// 获取到录音的blob
		let blob = new Blob(chunks,{type:"audio/webm;codecs=opus"}); 
		
		//  将blob转换为file对象，名字可以自己改，一般用于需要将文件上传到后台的情况
		let file = new window.File([blob],"record.webm");
		
		// 将blob转换为地址，一般用于页面上面的回显，这个url可以直接被 audio 标签使用
		let url = (window.URL || webkitURL).createObjectURL(blob);
	}
})

// 录音结束事件，在需要结束录音时调用，录音结束后的操作请在 mediaRecorder.onstop 里面写
mediaRecorder && mediaRecorder.stop()
```

### 统计页面出现的元素出现次数

```javascript
const allElements = document.querySelectorAll("*");
const elementFrequency = Array.from(allElements).reduce((a, b) => {
  a[b.tagName] = a[b.tagName] ? a[b.tagName] + 1 : 1;
  return a;
}, {});
```

### 页面禁止选中复制

> 或使用 JS 如下，监听 `selectstart` 事件，禁止选中。
>
> 当用户选中一片区域时，将触发 `selectstart` 事件，Selection API 将会选中一片区域。禁止选中区域即可实现页面文本不可复制。

```js
document.body.onselectstart = (e) => {
  e.preventDefault();
};

document.body.oncopy = (e) => {
  e.preventDefault();
};
```

### 设置一个支持过期时间的 localStrage



```js
function initLocalStorage() {
  localStorage.setItem = function (key, value, time = 1000) {
    const expiresTime = Date.now() + time * 1000;
    const payload = {
      __data: value,
      __expiresTime: expiresTime,
    };
    Storage.prototype.setItem.call(localStorage, key, JSON.stringify(payload));
  };
  localStorage.getItem = function (key) {
    const value = Storage.prototype.getItem.call(localStorage, key);
    if (typeof value === "string") {
      const jsonVal = JSON.parse(value);
      if (jsonVal.__expiresTime) {
        if (jsonVal.__expiresTime >= Date.now()) {
          return JSON.stringify(jsonVal.__data);
        } else {
          return null;
        }
      }
    }
    return value;
  };
}
initLocalStorage();
```

想了下 只能去改 api 去封装一层 不过存在一个问题就是别人在使用的时候 也需要遵循这个规则 没想到别的办法

### history 、hash 路由实现原理

> 端路由实现的本质是**监听 url 变化**，实现方式有两种：Hash 模式和 History 模式，无需刷新页面就能重新加载相应的页面。 Hash url 的格式为`www.a.com/#/`，当#后的哈希值发生变化时，通过 hashchange 事件监听，然后页面跳转。 History url 通过`history.pushState`和`history.replaceState`改变 url

两者区别

- hash 只能改变#后的值，而 history 模式可以随意设置同源 url；
- hash 只能添加字符串类的数据，而 history 可以通过 API 添加多种类型的数据；
- hash 的历史记录只显示之前的`www.a.com`而不会显示 hash 值，而 history 的每条记录都会进入到历史记录；
- hash 无需后端配置且兼容性好，而 history 需要配置`index.html`用于匹配不到资源的情况。

history API

- 通过 `history.pushState()` 跳转路由
- 通过 `popstate event` 监听路由变化，但无法监听到 `history.pushState()` 时的路由变化

hash

- 通过 `location.hash` 跳转路由
- 通过 `hashchange event` 监听路由变化



### DOM 中判断事件是否可阻止默认行为

```
通过 e.cancelable 判断事件是否可取消

e.preventDefault(): 取消事件

```



### DOM 转 图片

> 第三方库
>
> 1. [html2canvas (opens new window)](https://html2canvas.hertzen.com/): Screenshots with JavaScript
> 2. [dom-to-image (opens new window)](https://github.com/tsayen/dom-to-image): Generates an image from a DOM node using HTML5 canvas

思路： DOM -> SVG -> Canvas -> JPEG/PNG

重点：foreignObject 标签，将 html -> canvas

### 在canvas 中 图片跨域

```js
img.setAttribute("crossOrigin", "anonymous");
```

### 取消请求

根据发送网络请求的 API 不同，取消方法不同

- xhr
- fetch
- axios

1. 如果使用`fetch`发送请求可以使用`AbortController`

	```js
	const controller = new AbortController();
	// signal 选项控制 fetch 请求
	const signal = controller.signal;
	fetch('https://somewhere', { signal })
	// 取消请求
	controller.abort()
	```

	

2. 如果使用`axios`，取消原理同 fetch

	```js
	var CancelToken = axios.CancelToken;
	var source = CancelToken.source();
	
	axios.get('/https://somewhere', {
	  cancelToken: source.token
	}
	// 取消请求
	source.cancel()
	```

	

3. XHR 使用 `xhr.abort()`

	```js
	const xhr = new XMLHttpRequest(),
	  method = "GET",
	  url = "https://developer.mozilla.org/";
	xhr.open(method, url, true);
	
	xhr.send();
	
	// 取消发送请求
	xhr.abort();
	```

### 检测对象中是否循环引用

> 示例，如下数据为循环结构/循环引用
>
> ```js
> const user = { id: 10086, name: "山月" };
> user._user = user;
> ```

```js
function isCircularReference(value) {
  const isObject = (value) =>
    Object.prototype.toString.call(value) === "[object Object]";
  const memory = new WeakMap();
  let isCycled = false;
  const traverse = function (value) {
    if (isObject(value)) {
      if (memory.has(value)) {
        isCycled = true;
        return;
      }
      memory.set(value, true);
      const keys = Object.keys(value);
      for (const key of keys) {
        traverse(value[key]);
      }
    }
  };
  traverse(value);
  return isCycled;
}
```


### 判断当前浏览器 是否PC端，哪个手机端

```js
// 
function isPc() {
    var userAgentInfo = navigator.userAgent
    var Agents = new Array(
      'Android',
      'iPhone',
      'SymbianOS',
      'Windows Phone',
      'iPad',
      'iPod'
    )
    var flag = true
    flag = !Agents.some((ele) => {
        return userAgentInfo.indexOf(ele) > 0
    })
    return flag
  }
```

> 使用第三方库：https://github.com/kaimallea/isMobile
>
> ```js
> import isMobile from "ismobilejs";
> 
> const mobile = isMobile();
> ```
>

### bind/softBind/apply/call 都是 this 显式绑定的方法

- bind 会返回一个硬绑定的新函数，新函数会使用指定的第一个 thisCtx 去调用原始函数，并将其它参数传给原始函数。 硬绑定会降低函数的灵活性，在绑定之后不能通过显式或硬绑定的方式改变 this，只能通过 new 改变
- softBind 会对指定的函数进行封装，首先检查调用时的 this，如果 this 绑定到全局对象或者 undefined，那就用指定的 thisCtx 去调用函数，否则不会修改 this
- apply 和 call 功能相同，都是以指定的 thisCtx 和参数去执行方法，并返回原方法的返回值，只是 apply 中参数以数组传递

```javascript
Function.prototype.myBind = function (ctx = globalThis) {
  const fn = this;
  const args = Array.from(arguments).slice(1);
  function bound() {
    if (this instanceof bound) {
      fn.apply(this, args);
    } else {
      fn.apply(ctx, args);
    }
  }
  bound.prototype = fn.prototype;
  return bound;
};
Function.prototype.mySoftBind = function (ctx = globalThis) {
  const fn = this;
  const args = Array.from(arguments).slice(1);
  function bound() {
    if (!this || this === globalThis) {
      fn.apply(ctx, args);
    } else {
      fn.apply(this, args);
    }
  }
  bound.prototype = fn.prototype;
  return bound;
};
Function.prototype.myCall = function (ctx = globalThis) {
  const args = Array.from(arguments).slice(1);
  const key = Symbol("key");
  ctx[key] = this;
  const res = ctx[key](...args);
  delete ctx[key];
  return res;
};
Function.prototype.myApply = function (ctx = globalThis) {
  const args = arguments[1];
  const key = Symbol("key");
  ctx[key] = this;
  const res = ctx[key](...args);
  delete ctx[key];
  return res;
};
```

### js 实现 bind

简单版

```js
Function.prototype.fakeBind = function (obj, ...args) {
  return (...rest) => this.call(obj, ...args, ...rest);
};
```

bind 优化版本：考虑 bind 后返回的函数作为构造函数被 new

```js
Function.prototype.bind = function (context, ...args) {
  const self = this;
  const fn = function (...newArgs) {
    self.apply(this instanceof fn ? this : context, args.concat(newArgs));
  };

  fn.prototype = Object.create(this.prototype);

  return fn;
};
```

### js 实现 softbind 

> bind 函数多次调用会已第一次绑定的 this 为准，softbind 已最后一次绑定传入的 this 为准；

```js
Function.prototype.softBind = function(obj, ...rest) {
    const fn = this
    const bound = function(...args) {
      const o = !this || this === (window || global) ? obj : this
      return fn.apply(o, [...rest, ...args])
    }

    bound.prototype = Object.create(fn.prototype)
    return bound
  }
  
  

测试
let obj = {name: "obj"};
obj2 = {name: "obj2"};
obj3 = {name: "obj3"};

let fooBJ = foo.softBind(obj);
fooBJ();  // name: obj   这个时候软绑定已经生效了，this绑定到obj上
obj2.foo = foo.softBind(obj);
obj2.foo(); //name: obj2   这里已经的this隐式绑定到obj2上了
fooBJ.call(obj3); // name: obj3  这里this被硬绑定到obj3上了
setTimeout(obj2.foo, 100); // name: obj  软绑定了最初的obj
```

### 如何实现 promise.map，限制 promise 并发数

> 实现一个 promise.map，进行并发数控制，有以下测试用例
>
> ```js
> pMap([1, 2, 3, 4, 5], (x) => Promise.resolve(x + 1));
> 
> pMap([Promise.resolve(1), Promise.resolve(2)], (x) => x + 1);
> 
> // 注意输出时间控制
> pMap([1, 1, 1, 1, 1, 1, 1, 1], (x) => sleep(1000), { concurrency: 2 });
> ```

```js
function pMap(list, mapper, concurrency = Infinity) {
  // list 为 Iterator，先转化为 Array
  list = Array.from(list);
  return new Promise((resolve, reject) => {
    let currentIndex = 0;
    let result = [];
    let resolveCount = 0;
    let len = list.length;
    function next() {
      const index = currentIndex;
      currentIndex++;
      Promise.resolve(list[index])
        .then((o) => mapper(o, index))
        .then((o) => {
          result[index] = o;
          resolveCount++;
          if (resolveCount === len) {
            resolve(result);
          }
          if (currentIndex < len) {
            next();
          }
        });
    }
    for (let i = 0; i < concurrency && i < len; i++) {
      next();
    }
  });
}
```

### 关于 JSON.stringify

```js
const obj = {
  a: 3,
  b: 4,
  c: null,
  d: undefined,
  get e() {}, // 这个不是函数，是重写 e 属性get方法，如果有返回值则多出 e 属性
};

console.log(JSON.stringify(obj));

// 输出
{"a":3,"b":4,"c":null}

JSON.stringify 会对 undefined，函数，忽略
```

### 把类数组转化成数组

```
Array.from(arrayLike);
Array.apply(null, arrayLike);
Array.prototype.concat.apply([], arrayLike);
[...arrLike]
```

### 如何在 url 中传递数组

```
a=3&a=4&a=5

a=3,4,5

a[]=3&a[]=4&a[]=5

a[0]=3&a[1]=4&a[2]=5
```

> [qs: 据说是对 querystring 复杂对象解析最好的库](https://github.com/ljharb/qs#parsing-arrays)

### 实现 compose 函数，进行函数合成

```js
// 同步版本
function compose(...funcs) {
  if (funcs.length === 0) {
    return args => args
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduceRight((a, b) => (...args) => b(a(...args)))
}

compose(
    (a)=> {
      console.log('我是a, 返回一个 a, 我收集的参数是:', a)
      return 'a'
       
        
    },
   (b)=> {
        console.log('我是b, 返回一个 b,, 我收集的参数是:', b)
       return 'b'
    }
)()



// 异步版本
function composePromise(...funcs) {
  if (funcs.length === 0) {
    return args => args
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduceRight((a, b) => async (...args) => b(await a(...args)))
}

composePromise(
    (a)=> {
      console.log('我是a, 返回一个 a, 我收集的参数是:', a)
      return new Promise((resolve, reject)=> {
        setTimeout(()=> resolve('a'), 1000)
      })
       
        
    },
   (b)=> {
        console.log('我是b, 返回一个 b,, 我收集的参数是:', b)
       return new Promise((resolve, reject)=> {
        setTimeout(()=> resolve('b'), 1000)
      })
    }
)()
```

### Loadsh.get 方法的实现

```js
function get(obj, keys, defaultValue) {
    let tempObj = obj;
    let arr = [];
    if (typeof keys === "string") {
        let key = "";
        let index = 0;

        while (index < keys.length) {
            const k = keys[index];
            if (["[", "'", '"', ".", "]"].includes(k)) {
                if (key.length) {
                    arr.push(key);
                }
                key = "";
            } else {
                key = key + k;
            }
            index = index + 1;
        }
        key && arr.push(key);
    } else {
        arr = keys;
    }
    console.log(arr)
    while (arr.length) {
        tempObj = tempObj[arr.shift()];
        if (tempObj === undefined || tempObj === null) {
            return defaultValue;
        }
    }
    return tempObj;
}

// eg
let obj1 = {
    'you': {
        name: 'aa',
        son: [{
            name: 'qq',
            age: 12,

        }]
    }
}

get(obj1, '["you"].son[0].name')
```

### js 深拷贝

```js
/**
 * 深拷贝关注点:
 * 1. JavaScript内置对象的复制: Set、Map、Date、Regex等
 * 2. 循环引用问题
 * @param {*} object
 * @returns
 */
function deepClone(source, memory) {
  const isPrimitive = (value) => {
    return /Number|Boolean|String|Null|Undefined|Symbol|Function/.test(
      Object.prototype.toString.call(value)
    );
  };
  let result = null;

  memory || (memory = new WeakMap());
  // 原始数据类型及函数
  if (isPrimitive(source)) {
    console.log("current copy is primitive", source);
    result = source;
  }
  // 数组
  else if (Array.isArray(source)) {
    result = source.map((value) => deepClone(value, memory));
  }
  // 内置对象Date、Regex
  else if (Object.prototype.toString.call(source) === "[object Date]") {
    result = new Date(source);
  } else if (Object.prototype.toString.call(source) === "[object RegExp]") {
    result = new RegExp(source);
  }
  // 内置对象Set、Map
  else if (Object.prototype.toString.call(source) === "[object Set]") {
    result = new Set();
    for (const value of source) {
      result.add(deepClone(value, memory));
    }
  } else if (Object.prototype.toString.call(source) === "[object Map]") {
    result = new Map();
    for (const [key, value] of source.entries()) {
      result.set(key, deepClone(value, memory));
    }
  }
  // 引用类型
  else {
    if (memory.has(source)) {
      result = memory.get(source);
    } else {
      result = Object.create(null);
      memory.set(source, result);
      Object.keys(source).forEach((key) => {
        const value = source[key];
        result[key] = deepClone(value, memory);
      });
    }
  }
  return result;
}

let obj2 = {
      re: /hello/,
      f() {},
      date: new Date(),
      map: new Map(),
      list: [1, 2, 3],
      a: 3,
      b: 4,
}
let rs = deepClone(obj2)

console.log(rs)
```

### 实现一个once函数，记忆返回结果只执行一次

> [once](https://npm.devtool.tech/once)是社区使用最广泛的一个库，代码实现与上大同小异，然而每月下载量可达上亿，比 vue/react/angular 三者一个月的下载量加起来还要高一倍

```js
function onceCache(fn) {
  let toggle = false,
    ret = null;
  return function () {
    if (toggle) return ret;
    toggle = true;
    return (ret = fn.apply(this, arguments));
  };
}
```

### 实现一个无限累加的　sum 函数

> 示例如下：
>
> ```js
> sum(1, 2, 3).valueOf(); //6
> sum(2, 3)(2).valueOf(); //7
> sum(1)(2)(3)(4).valueOf(); //10
> sum(2)(4, 1)(2).valueOf(); //9
> sum(1)(2)(3)(4)(5)(6).valueOf(); // 21
> ```

```js
function sum(...args) {
  const f = (...rest) => sum(...args, ...rest);
  f.valueOf = () => args.reduce((x, y) => x + y, 0);
  return f;
}
```

### 实现一个同步的 sleep 函数

```js
// 版本1
function sleep (t = 1000) {
    console.log('>>> sleep start');
    let startTime = +(new Date());
    let curTime = startTime;
    while (true) {
        curTime = +(new Date());
        if (curTime - startTime >= t) break;
    }
    console.log('>>> sleep finish');
}

// test
sleep(3000);
console.log('>>> hi');


// 版本2
function sleepSync(ttl) {
  const now = Date.now();
  ttl *= 1000;
  while (Date.now() - now < ttl) {}
}

// 版本3
const sleep = (t = 0) => new Promise((resolve) => setTimeout(resolve, t));
```

### sample 函数，从数组随机取一个元素

>  数组原型上面拓展，先判断调用者(this) 是否为数组，是数组就随机一个下标

```js

Array.prototype.sample = function () {
  if (!Array.isArray(this)) {
    throw new Error("not a Array");
  }

  return this[Math.floor(Math.random() * this.length)];
};
```

### 数组洗牌函数 shuffle

> ```js
> // 打乱数组，有可能是 [1, 3, 2, 4]，但对原数组没有影响
> shuffle([1, 2, 3, 4]);
> ```

```js

const shuffle = (list) => list.sort((x, y) => Math.random() - 0.5);
```

### 翻转一个字符串

```js
1、方法1
const reverse = (s) => s.split("").reverse().join("");

2、方法2
const reverse = (s) => [...s].reduceRight((acc, cur) => acc + cur);
```

### loadsh_merge  合并对象

> 将两个对象深度合并

```js
const getRawType = (val) => {
  return Object.prototype.toString.call(val).slice(8, -1);
};
const isPlainObject = (val) => {
  return getRawType(val) === "Object";
};

const isPlainObjectOrArray = (val) => {
  return isPlainObject(val) || Array.isArray(val);
};

const merge = (object, ...sources) => {
  for (const source of sources) {
    for (const key in source) {
      if (source[key] === undefined && key in object) {
        continue;
      }
      if (isPlainObjectOrArray(source[key])) {
        if (
          isPlainObjectOrArray(object[key]) &&
          getRawType(object[key]) === getRawType(source[key])
        ) {
            merge(object[key], source[key]);
        } else {
          object[key] = source[key];
        }
      } else {
        object[key] = source[key];
      }
    }
  }
};

// 示例：
var objs = {
    x: {
        name: '1',
    },
    y: [
        {
            name: '2'
        },
        {
            name: '3'
        }
    ],
    name: '0',
}
let rs = merge(objs, {
    x: [
        {
            name: '0'
        }
    ],
    y: [
        {
            age: 22
        }
    ]
})

// 结果
{
    "x": [
        {
            "name": "0"
        }
    ],
    "y": [
        {
            "name": "2",
            "age": 22
        },
        {
            "name": "3"
        }
    ],
    "name": "0"
}
```

### 如何过滤数组的 假值(falsy value)

> falsy value 包含：`false`, `null`, `0`, `""`, `undefined`, `NaN`

```js
function compact(array) {
  return array.filter(Boolean);
}
```

### 创建一个数组大小为 100，每个值都为0的数组

```js
// 方法一:
Array(100).fill(0);

// 方法二:
// 注: 如果直接使用 map，会出现稀疏数组
Array.from(Array(100), (x) => 0);

// 方法二变体:
Array.from({ length: 100 }, (x) => 0);
```

### Number.prototype.toLocaleString()

> 返回这个数字在特定语言环境下的表示字符串
>
> 语法：
>
> ```js
> numObj.toLocaleString([locales [, options]])
> ```

- locales

	> 缩写语言代码
	>
	> - "zh": 中文
	> - "zh-cmn": 中文普通话
	> - "zh-cmn-Hans": 中文普通话简体
	> - zh-Hans": 简体中文
	> - "zh-Hans-CN": 中华人民共和国大陆简体中文
	> - "'zh-Hans-CN-u-nu-hanidec'" 数字中文
	>
	> 

- options

	- style	要使用的格式样式，默认为 “decimal”
	- numberingSystem    编号系统。可能的值包

- 示例

	```js
	var number = 123456.789;
	
	// 德国使用逗号作为小数分隔符，分位周期为千位
	console.log(number.toLocaleString('de-DE'));
	// → 123.456,789
	
	// 在大多数阿拉伯语国家使用阿拉伯语数字
	console.log(number.toLocaleString('ar-EG'));
	// → ١٢٣٤٥٦٫٧٨٩
	
	// 印度使用千位/拉克（十万）/克若尔（千万）分隔
	console.log(number.toLocaleString('en-IN'));
	// → 1,23,456.789
	
	// nu 扩展字段要求编号系统，e.g. 中文十进制
	console.log(number.toLocaleString('zh-Hans-CN-u-nu-hanidec'));
	// → 一二三,四五六.七八九
	
	// 当请求不支持的语言时，例如巴厘语，加入一个备用语言，比如印尼语
	console.log(number.toLocaleString(['ban', 'id']));
	// → 123.456,789
	
	// 要求货币格式
	console.log(number.toLocaleString('de-DE', { style: 'currency', currency: 'EUR' }));
	// → 123.456,79 €
	
	// 日元不使用小数位
	console.log(number.toLocaleString('ja-JP', { style: 'currency', currency: 'JPY' }))
	// → ￥123,457
	
	// 限制三位有效数字
	console.log(number.toLocaleString('en-IN', { maximumSignificantDigits: 3 }));
	// → 1,23,000
	```

	> [MDN 文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)

### 对象的深度比较

> 默认对象比较相同地址引用的对象为true, 而内容相同，地址引用不同则不相等

```js
function isEqual(x, y) {
  if (x === y) {
    return true;
  } else if (
    typeof x === "object" &&
    x !== null &&
    typeof y === "object" &&
    y !== null
  ) {
    const keysX = Object.keys(x);
    const keysY = Object.keys(y);
    if (keysX.length !== keysY.length) {
      return false;
    }
    for (const key of keysX) {
      if (!isEqual(x[key], y[key])) {
        return false;
      }
    }
    return true;
  } else {
    return false;
  }
}
```

Object.is 与 “===” 的区别

> `Object.is()` 方法判断两个值是否为[同一个值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)，如果满足以下任意条件则两个值相等
>
> - 都是 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)
> - 都是 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/null)
> - 都是 `true` 或都是 `false`
> - 都是相同长度、相同字符、按相同顺序排列的字符串
> - 都是相同对象（意味着都是同一个对象的值引用）
> - 都是数字且
> 	- 都是 `+0`
> 	- 都是 `-0`
> 	- 都是 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)
> 	- 都是同一个值，非零且都不是 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)

`Object.is()` 与 [`==`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#相等运算符) 不同。`==` 运算符在判断相等前对两边的变量（如果它们不是同一类型）进行强制转换（这种行为将 `"" == false` 判断为 `true`），而 `Object.is` 不会强制转换两边的值。

`Object.is()` 与 [`===`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#全等运算符) 也不相同。差别是它们对待有符号的零和 NaN 不同，例如，`===` 运算符（也包括 `==` 运算符）将数字 `-0` 和 `+0` 视为相等，而将 [`Number.NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/NaN) 与 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN) 视为不相等。

### 同一页面三个组件请求同一个 API 发送了三次请求，如何优化

> 缓存结果，避免请求相同数据

```js
const fetchUser = (id) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log("Fetch: ", id);
      resolve(id);
    }, 5000);
  });
};


const cache = {};
const cacheFetchUser = (id) => {
  if (cache[id]) {
    return cache[id];
  }
  cache[id] = fetchUser(id);
  return cache[id];
};
```

### 获取数字整数与小数部分

> 如：
>
> - `1.3e-19`
> - `1.6`

```js
function splitNum(data: unknown): [string, string] {
    // 类型判断 数字&& 不是NaN
  if (typeof data === "number" && !isNaN(data)) {
    // 转字符串
    const dataStr = data.toString();
    // 判断是否科学计数法
    const eIndex = dataStr.indexOf("e-");
    if (eIndex > -1) {// 是
      const result = dataStr.split("e-");
      return [
        "0",
        `0.${"0".repeat(Number(result[1]) - 1)}${result[0].replace(".", "")}`,
      ];
    } else {// 否
      const result = dataStr.split(".");
      if (result.length === 1) {
        result.push("0");
      }
      return [result[0], `0.${result[1]}`];
    }
  }
  return ["0", "0"];
}
```

### 拦截页面click 事件

> 事件捕获阶段阻止事件向子元素传递

```js
window.addEventListener('click', function(event){
    event.preventDefault&&event.preventDefault()
    event.stopPropagation&&event.stopPropagation()
    return false
}, true)
```

### 监测电脑主题是否暗色模式

```js
const isDarkMode = () => window.matchMedia && window.matchMedia("(prefers-color-scheme: dark)").matches;

console.log(isDarkMode())
```

#### **Intl.Segmenter**

> 浏览器方法：	**字符串拆分为句子、单词或字素**。 话说不是有 split 方法么，为啥还要这个捏？

```js
'Hello! How are you?'.split(/[.!?]/);
// ['Hello', ' How are you', '']

// I am a cat. My name is Tanuki.
'吾輩は猫である。名前はたぬき。'

问题：
将丢失定义的分隔符并在所有位置包含所有这些空格。而且因为它依赖于硬编码的定界符，所以它对语言不敏感。
如果不会日语，怎么拆分句子，单词呢？
```

由此它诞生了。

```js
const segmenter = new Intl.Segmenter(
  // 语言，granularity：sentence-句子,grapheme-字素,word-单词.返回值为类数组对象
  'en', { granularity: 'grapheme' }
);

console.log(
  Array.from(
    segmenter.segment(`I'm like 🫣. But you are, too! 🫵`),
    s => s.segment
  )
);
```



> 服务器端，它从 Node.js 16 开始支持。

## 属性

### document.referrer

> **`document.referrer`** 返回的是一个 [URI](https://www.w3.org/Addressing/#background)，当前页面就是从这个 URI 所代表的页面跳转或打开的。

例如：领奖页面A.html, 从此页面点击跳转 到领奖详情 B.html. 此时B.html 中 输出  document.referrer 值为 A.html. 但是如果 不从 A.html 点击跳转进入，直接访问 B.html 则  此时B.html 中 输出  document.referrer 值为 空

**下面的场景无法获得 referrer 信息**

- 直接在浏览器中输入地址
- 使用location.reload()刷新（location.href或者location.replace()刷新有信息）
- 在微信对话框中，点击进入微信自身浏览器
- 扫码进入微信或QQ的浏览器
- 直接新窗口打开一个页面
- 从https的网站直接进入一个http协议的网站（Chrome下亲测）
- a标签设置rel="noreferrer"（兼容IE7+）
- meta标签来控制不让浏览器发送referer
- 点击 flash 内部链接
- Chrome4.0以下，IE 5.5+以下返回空的字符串
- 使用 修改 Location 进行页面导航的方法，会导致在IE下丢失 referrer，这可能是IE的一个BUG
- 跨域

> 在 [`iframe`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe) 中，`Document.referrer` 会初始化为父窗口 [`Window.location`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/location) 的 [`href`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLAnchorElement/href)。

## js拓展

### Pomelo

```
网易游戏服务器开发框架(https://www.oschina.net/p/pomelo)
```

### 主流对象储存平台

```js
1、amazon s3
2、Google cloud storage
3、阿里云
4、cos
5、七牛云
	-优势
	▪中文平台，文档更新及时并准确
    ▪可以免费体验
    ▪提供丰富的SDK，包括 Node.js
```

### 七牛云对象储存

```
https://qiniu.com
1、注册用户并登录
2、实名认证，系统会送东西
3、新建储存空间，要先实名
4、从文档进入开发者中心
5、从SDK下载 找到node sdk 下载 查阅文档
```

### js游戏引擎

- 1、PlayCanvas，或者Babylon.js

	> 这两者都是定位为 Web端 的游戏引擎的，有作为游戏引擎较为完备的工具链，并且都有自己的编辑器。

- 2、treejs

	> threejs是一个定位为渲染的库，这就意味着它不会被业务绑定，可以做什么完全取决于你自己的业务场景，这就好比一把刀，有人拿来切菜 有人拿来砍人 有人拿来雕刻 刀本身只是一把刀而已。
	>
	> 这里列出一些主要的业务场景
	>
	> 游戏互动 当然游戏互动会融合各种其他技术，比如物理引擎 界面gui 服务器通讯等等 还有其他更专业的框架可以使用 比如Babylon.js playcanvas laya等更偏游戏的引擎
	>
	> 建筑家装 bim和家装也是webgl的一个比较大的应用场景，基于threejs的bim 家装引擎二次开发也是市面较常见的技术方案
	>
	> 大屏可视化 数据可视化和大屏，配合gis echart啥的搞个炫酷的大屏系统，绝对能唬住领导

- 白鹭引擎

	> Egret Engine 是一款使用 TypeScript 编写的 HTML5 游戏引擎，包含渲染、声音、用户交互、资源管理等诸多功能，解决了 HTML5 性能、碎片化问题，应用于 2D 游戏、3D 游戏开发，及移动端交互式应用构建，拥有完善的跨平台运行能力。 现在 Egret Engine 及其它工具已全部整合到 Egret Launcher，请大家直接下载安装开启您的游戏项目创作。
	>
	> 2D/3D双核心渲染，开发工具链闭环整合，全流程开发，由此开始

	

### 项目部署

```
gitlab有gitlab-ci，使用docker部署即可；
github有github actions，也可以使用docker部署，
总结：2022了，使用docker即可
```

### 2D,3D

```
1、cesium
	在于3d 地图
2、three.js
	做3d模型
3、opanlayers
	在于2d地图
4、d3js,echarts,AntV, React-Vis
	图表展示
5、VTK
	可视化工具包 是用于操作和显示科学数据的开源软件。它配备了最先进的 3D 渲染工具、一套用于 3D 交互的小部件以及广泛的 2D 绘图功能。
VTK 是 Kitware支持的软件开发平台集合的一部分。该平台在全球范围内用于商业应用以及研发。
```

### 微前端的概念

```js
    微前端就是将不同的功能按照不同的维度拆分成多个子应用。通过主应用来加载这些子应用。 微前端的核心在于  `如何拆` 和 `拆完后如何合`
    
### 微前端的优势

- 技术栈无关

- 主框架不限制介入应用的技术栈，微应用具备完全自主权

- 独立开发、独立部署

- 增量升级

- 微前端是一种非常好的实现渐进式重构的手段和策略

- 微应用仓库独立，前后端可独立开发，主框架自动完成同步更新

- 独立运行

- 每个微应用之间状态隔离，运行时状态不共享

### 劣势

- 接入难度较高

- 应用场景移动端少，管理端多

微前端的演变：

- 1.iframe方案

- 2.single-spa方案

# iframe方案

对于iframe方案，其实就是通过iframe标签在一个页面里嵌套了另一个页面，不过它有一定的弊端：

- 路由限制：在iframe内的页面里切换路由后，无法跟随浏览器进行前进后退

- 资源加载：每次iframe的页面都需要重新加载

- 资源共享：与外层的父组件隔离，无法实现状态共享

- dom结构不共享：iframe里的全局modal框也是显示在iframe里

# single-spa

single-spa方案解决了iframe方案的弊端。



微前端方案
-飞冰 icejs
-乾坤 qiankun
```

目前较成熟的微前方案有 qiankun、micro-app、EMP 方案

##### qiankun 方案

qiankun 方案是基于 single-spa 的微前端方案。

**特点**

- html entry 的方式引入子应用，相比 js entry 极大的降低了应用改造的成本；
- 完备的沙箱方案，js 沙箱做了 SnapshotSandbox、LegacySandbox、ProxySandbox 三套渐进增强方案，css 沙箱做了 strictStyleIsolation、experimentalStyleIsolation 两套适用不同场景的方案；
- 做了静态资源预加载能力；

**不足**

- 适配成本比较高，工程化、生命周期、静态资源路径、路由等都要做一系列的适配工作；
- css 沙箱采用严格隔离会有各种问题，js 沙箱在某些场景下执行性能下降严重；
- 无法同时激活多个子应用，也不支持子应用保活；
- 无法支持 vite 等 esmodule 脚本运行；

##### micro-app 方案

micro-app 是基于 webcomponent + qiankun sandbox 的微前端方案。

**特点**

- 使用 webcomponet 加载子应用相比 single-spa 这种注册监听方案更加优雅；
- 复用经过大量项目验证过 qiankun 的沙箱机制也使得框架更加可靠；
- 组件式的 api 更加符合使用习惯，支持子应用保活；
- 降低子应用改造的成本，提供静态资源预加载能力；

**不足**

- 接入成本较 qiankun 有所降低，但是路由依然存在依赖；
- 多应用激活后无法保持各子应用的路由状态，刷新后全部丢失；
- css 沙箱依然无法绝对的隔离，js 沙箱做全局变量查找缓存，性能有所优化；
- 支持 vite 运行，但必须使用 plugin 改造子应用，且 js 代码没办法做沙箱隔离；
- 对于不支持 webcompnent 的浏览器没有做降级处理；

##### EMP 方案

EMP 方案是基于 webpack 5 module federation 的微前端方案。

**特点**

- webpack 联邦编译可以保证所有子应用依赖解耦；
- 应用间去中心化的调用、共享模块；
- 模块远程 ts 支持；

**不足**

- 对 webpack 强依赖，老旧项目不友好；
- 没有有效的 css 沙箱和 js 沙箱，需要靠用户自觉；
- 子应用保活、多应用激活无法实现；
- 主、子应用的路由可能发生冲突；

##### 结论

qiankun 方案对 single-spa 微前端方案做了较大的提升同时也遗留下来了不少问题长时间没有解决；
micro-app 方案对 qiankun 方案做了较多提升但基于 qiankun 的沙箱也相应会继承其存在的问题；
EMP 方案基于 webpack 5 联邦编译则约束了其使用范围；
目前的微前端方案在用户的核心诉求上都没有很好的满足，有很大的优化提升空间。

#### 无界方案

无界微前端方案基于 webcomponent 容器 + iframe 沙箱，能够完善的解决适配成本、样式隔离、运行性能、页面白屏、子应用通信、子应用保活、多应用激活、vite 框架支持、应用共享等用户的核心诉求。

[文档地址](https://mp.weixin.qq.com/s?__biz=MzI0NDQ0ODU3MA==&mid=2247511377&idx=1&sn=3ae8cab49fd2078b6d5c841f13136e26&chksm=e95f4104de28c812bbc223ae54e322f03c9fa234548c33fffbc2d28defa0978317c643cc94d2&scene=126&sessionid=1660615385&key=73ffa7209515a8693dae26db834ca16a6ae92e0cbccdd8fdba4f29b18ec2703c3d38b0502d21605ce8a731a4e265fe2d95f473a13f3b78f361f08b546ecd8c7bdb619606d945d1b0936331d2b916c97abe3fd0cb4972e78af553deaf29e79ef2efcb9c63fc8c14839cecd8e4df543bebe64516c9906fc62b45edf5b6fd47bd18&ascene=1&uin=MjI1NzgwMjk3Mg%3D%3D&devicetype=Windows+10+x64&version=6302019c&lang=zh_CN&exportkey=A8c99k%2B04aAyY1HnI0HPQIg%3D&acctmode=0&pass_ticket=dxCUOw%2F86KYDPF7dyM7ijUZ5GWcYzfiCiBDDbFLuE4or%2FUYTFBPNFIRYezNNibuy&wx_header=0&fontgear=2)，demo 地址:https://wujie-micro.github.io/demo-main-vue/home，git 地址:https://github.com/Tencent/wujie

### 微服务

```js
一、单体软件
	早期的软件，所有功能都写在一起，这称为单体架构（monolithic software）。
    
（1）所有功能耦合在一起，互相影响，最终难以管理。

（2）哪怕只修改一行代码，整个软件就要重新构建和部署，成本非常高。

（3）因为软件做成了一个整体，不可能每个功能单独开发和测试，只能整体开发和测试，导致必须采用瀑布式开发模型。


二、面向服务架构

	为了解决上面这些问题，很早就有人提出来，必须打破代码的耦合，拆分单体架构，将软件拆分成一个个独立的功能单元。
    "面向服务架构"就是把一个大型的单体程序，拆分成一个个独立服务，也就是较小的程序。每个服务都是一个独立的功能单元，承担不同的功能，服务之间通过通信协议连在一起。
    
（1）每种服务功能单一，相当于一个小型软件，便于开发和测试。

（2）各个服务独立运行，简化了架构，提高了可靠性。

（3）鼓励和支持代码重用，同一个服务可以用于多种目的。

（4）不同服务可以单独开发和部署，便于升级。

（5）扩展性好，可以容易地加机器、加功能，承受高负载。

（6）不容易出现单点故障。即使一个服务失败了，不会影响到其他服务。

三、微服务

    Docker 出现，彻底改变了软件开发的面貌。它让程序运行在容器中，每个容器可以分别设定运行环境，并且只占用很少的系统资源。
    
    简单说，微服务就是采用容器技术的面向服务架构。它依然使用"服务"作为功能单元，但是变成了轻量级实现，不需要新增服务器，只需要新建容器（一个进程），所以才叫做"微服务"。
    一个微服务就是一个独立的进程。 这个进程可以运行在本机，也可以运行在别的服务器，或者在云端（比如云服务和云函数 FaaS）。
```

### CI 和 CD 

```
CI 和 CD 之间（以及不同 CD 之间）有什么区别？

	缩略词 CI / CD 具有几个不同的含义。CI/CD 中的“CI”始终指持续集成，它属于开发人员的自动化流程。成功的 CI 意味着应用代码的新更改会定期构建、测试并合并到共享存储库中。该解决方案可以解决在一次开发中有太多应用分支，从而导致相互冲突的问题。

	CI/CD 中的“CD”指的是持续交付和/或持续部署，这些相关概念有时会交叉使用。两者都事关管道后续阶段的自动化，但它们有时也会单独使用，用于说明自动化程度。
	
	
	持续交付通常是指开发人员对应用的更改会自动进行错误测试并上传到存储库（如 GitHub 或容器注册表），然后由运维团队将其部署到实时生产环境中。这旨在解决开发和运维团队之间可见性及沟通较差的问题。因此，持续交付的目的就是确保尽可能减少部署新代码时所需的工作量。

	持续部署（另一种“CD”）指的是自动将开发人员的更改从存储库发布到生产环境，以供客户使用。它主要为了解决因手动流程降低应用交付速度，从而使运维团队超负荷的问题。持续部署以持续交付的优势为根基，实现了管道后续阶段的自动化。

CI/CD 既可能仅指持续集成和持续交付构成的关联环节，也可以指持续集成、持续交付和持续部署这三项构成的关联环节。更为复杂的是，有时“持续交付”也包含了持续部署流程。	
	
总结：	
    CI/CD 实际上就是一个流程（通常形象地表述为管道），用于在更大程度上实现应用开发的持续自动化和持续监控。因案例而异，该术语的具体含义取决于 CI/CD 管道的自动化程度。许多企业最开始先添加 CI，然后逐步实现交付和部署的自动化（例如作为云原生应用的一部分）。
```

### 模块化方案

1、cjs (commonjs)
    commonjs 是 Node 中的模块规范，通过 require 及 exports 进行导入导出。同时，webpack 也对 cjs 模块得以解析，因此 cjs 模块可以运行在 node 环境及 webpack 环境下的，但不能在浏览器中直接使用。
    

    静态加载：
    // sum.js
    exports.sum = (x, y) => x + y;
    // index.js
    const { sum } = require("./sum.js");
    
    动态加载：
    require(`./${a}`);

2、esm (es module)
	esm 是 tc39 对于 ESMAScript 的模块话规范，正因是语言层规范，因此在 Node 及 浏览器中均会支持。
它使用 import/export 进行模块导入导出.

    静态加载
    // sum.js
    export const sum = (x, y) => x + y;
    // index.js
    import { sum } from "./sum";
    
    动态加载
    const ms = await import("https://cdn.skypack.dev/ms@latest");
    ms.default(1000);
    
    esm 是未来的趋势，目前一些 CDN 厂商，前端构建工具均致力于 cjs 模块向 esm 的转化，比如 skypack、 snowpack、vite 等。

 3、AMD
 	

> AMD说明：
>         AMD：Asynchronous Module Definition(异步模块定义)
>         专门用于浏览器端，模块的加载是异步的
>     基本语法：
>         定义暴露模块：
>             1. 没有依赖的模块：
>                 define(function(){
>                     return 模块
>                 })
>                         2. 定义有依赖的模块：
>                 define(['module1','module2'],function(m1,m2){
>                         return 模块
>                 })
>                         引入使用模块：
>                                     require(['module1','module2'],function(m1,m2){
>                 使用m1/m2
>                                     })
>             实现(浏览器端)：
>                         使用库： Require.js
>
> [参考链接](http://javascript.ruanyifeng.com/tool/requirejs.html)

未使用 AMD

```js
// dataService.js：(用于提供数据)
// 定义一个没有依赖的模块
(function(window){
    let name = 'dataService.js';
    function getName(){
        return name;
    }
    window.dataService = {getName};
})(window)

```



```js
// alerter.js: (用于使用数据)
// 定义一个有依赖的模块
(function (window, dataService) {
    let msg = 'alerter.js';
    function showMsg() {
        console.log(msg,dataService.getName());
    }
    window.alerter = {showMsg};
})(window, dataService)

// app.js: (作为根模板)
(function(alerter){
    alerter.showMsg();
})(alerter)
```



```html
// index.html: （在浏览器端展示,引用顺序不能乱）
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script src="./js/dataService.js"></script>
    <script src="./js/alerter.js"></script>
    <script src="./app.js"></script>
</body>
</html>
```

使用AMD

```js
// dataService.js:
// 定义没有依赖的模板
define(function() {
    let name = 'dataService.js';
    function getName() {
        return name;
    }
    // 暴露模块
    return {getName};
});
```



```js
// alerter.js:
// 定义有依赖的模块
define(['dataService','jquery'], function(dataService, $) {
    let msg = 'alerter.js';
    function showMsg() {
        console.log(msg, dataService.getName());
    }
    $('body').css('background', 'pink');
    // 暴露模块
    return {showMsg};
});
```



```js
// app.js:
(function(){
    requirejs.config({ // 参数配置
        // baseURL: 'js/', 基本路径，若开启，则路径为baseURL+paths
        paths: { // 配置路径,用以寻找模块
            dataService: './modules/dataService', // 此处不加.js后缀，因为运行时默认会加上
            alerter: './modules/alerter',
            jquery: './libs/jquery-2.2.4.min'
        }
    })

    requirejs(['alerter'], function(alerter){
        alerter.showMsg();
    })
})()
```



```html
// index.html:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- data-main引入入口js文件，src引入require.js库 -->
    <script data-main="./js/app.js" src="./js/libs/require.min.js"></script>
</body>
</html>
```

![目录展示](https://img-blog.csdnimg.cn/20210529172033614.png)

```js
4、umd
	一种兼容 cjs 与 amd 的模块，既可以在 node/webpack 环境中被 require 引用，也可以在浏览器中直接用 CDN 被 script.src 引入。
    (function (root, factory) {
      if (typeof define === "function" && define.amd) {
        // AMD
        define(["jquery"], factory);
      } else if (typeof exports === "object") {
        // CommonJS
        module.exports = factory(require("jquery"));
      } else {
        // 全局变量
        root.returnExports = factory(root.jQuery);
      }
    })(this, function ($) {
      // ...
    });
```

### ORC 文字图片识别文字

```js
    光学字符识别或光学字符阅读器 (OCR) 是将文本图像转换为机器编码文本的过程。例如，您可以为书页拍照，然后通过 OCR 软件运行以提取文本。
    
1、Tesseract OCR 库。
    	Tesseract 是用 C/C++ 编写的，最初是在 1985 年至 1994 年间由惠普开发的。惠普于 2005 年开源了该软件。从那时起，谷歌一直在开发和维护它。
        

```

### 跨平台开发

> 兼容多个平台的前端技术栈
>
> 

1. [cordova](https://cordova.apache.org/)

	可以看作是前端领域使用的第一代跨平台技术

	Cordova 将您的 HTML/JavaScript 应用程序包装到一个本机容器中，该容器可以访问多个平台的设备功能。这些功能通过统一的 JavaScript API 公开，让您可以轻松编写一组代码，以针对当今市场上的**几乎所有手机或平板电脑**并发布到他们的应用商店。

	

2. React Native

	由Facebook于2015年4月开源的跨平台移动应用框架，它可能是目前使用最广的跨平台开发技术.

	2018年6月，Airbnb技术团队在尝试推广RN技术2年后宣布放弃使用React Native而回归自研的原生开发框架，并接连发表了5篇博文[插图]来阐述他们两年间对于RN技术优缺点的总结和体会

	React Native对独立开发者而言不够友好，它更适合于对用户体验有一定诉求且人员结构多样化的团队来使用。建议在选用一项技术之前做好评估工作，它的优势是大家都希望借助的，但它的风险和代价却不是每个团队都能承担的。

3. 小程序

	动端的桌面上，这样一来，对于普通用户而言，它与App的使用体验似乎也没有什么区别了，更方便的是，它只需要重启一次就可以完成版本更新，所以可以将它也算作跨平台方案之一。如果你不介意自己的应用程序依附于第三方平台而存在，甚至本来就需要依赖平台方的流量扶持，那么小程序可能是目前最好的选择

	与传统的Hybrid技术一样，小程序本质上也是基于原生应用中的WebView控件来实现跨平台运行的

	小程序平台众多，会出现A平台小程序不能在B程序中运行。著名的**uni-app**框架和**Taro**框架都是基于这种多平台的开发需求而产生的解决方案

4. Google推出的Flutter、阿里推出的Weex、移动端快应用等