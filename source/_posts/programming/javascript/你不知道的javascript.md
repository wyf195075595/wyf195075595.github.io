---
title: 你不知道的JavaScript
date: 2022-11-15 08:58:10
tags: js
categories: js
---





**LHS与RHS**

> LHS和RHS的含义是“赋值操作的左侧或右侧”并不一定意味着就是“=赋值操作符的左侧或右侧”。赋值操作还有其他几种形式，因此在概念上最好将其理解为“赋值操作的目标是谁（LHS）”以及“谁是赋值操作的源头（RHS）”

```js
function foo(a) {
    var b = a;
    return a + b;
}
var c = foo(2);
```

1．找到其中所有的LHS查询。（这里有3处！）

2．找到其中所有的RHS查询。（这里有4处！）

**ReferenceError**

> 如果RHS查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出ReferenceError异常.
>
> 相较之下，当引擎执行LHS查询时，如果在顶层（全局作用域）中也无法找到目标变量，全局作用域中就会创建一个具有该名称的变量，并将其返还给引擎，前提是程序运行在非“严格模式”下。

**TypeError**

> ReferenceError同作用域判别失败相关，而TypeError则代表作用域判别成功了，但是对结果的操作是非法或不合理的。



<!--more-->

## 词法作用域

> 作用域共有两种主要的工作模型。第一种是最为普遍的，被大多数编程语言所采用的词法作用域，我们会对这种作用域进行深入讨论。另外一种叫作动态作用域，仍有一些编程语言在使用（比如Bash脚本、Perl中的一些模式等）。



### 词法阶段

> 大部分标准语言编译器的第一个工作阶段叫作词法化（也叫单词化）。回忆一下，词法化的过程会对源代码中的字符进行检查，如果是有状态的解析过程，还会赋予单词语义。这个概念是理解词法作用域及其名称来历的基础。

#### **屏蔽效应**

> 作用域查找会在找到第一个匹配的标识符时停止。在多层的嵌套作用域中可以定义同名的标识符，这叫作“遮蔽效应”（内部的标识符“遮蔽”了外部的标识符）

全局变量会自动成为全局对象（比如浏览器中的window对象）的属性，因此可以不直接通过全局对象的词法名称，而是间接地通过对全局对象属性的引用来对其进行访问。

```js
window.a
```

通过这种技术可以访问那些被同名变量所遮蔽的全局变量。但非全局的变量如果被遮蔽了，无论如何都无法被访问到。

### **欺骗词法**

> 如果词法作用域完全由写代码期间函数所声明的位置来定义，怎样才能在运行时来“修改”（也可以说欺骗）词法作用域呢？

#### eval

> eval(..)函数可以接受一个字符串为参数，并将其中的内容视为好像在书写时就存在于程序中这个位置的代码

JavaScript中还有其他一些功能效果和eval(..)很相似。setTimeout(..)和setInterval(..)的第一个参数可以是字符串，字符串的内容可以被解释为一段动态生成的函数代码。这些功能已经过时且并不被提倡。不要使用它们！

new Function(..)函数的行为也很类似，最后一个参数可以接受代码字符串，并将其转化为动态生成的函数（前面的参数是这个新生成的函数的形参）。这种构建函数的语法比eval(..)略微安全一些，但也要尽量避免使用。



#### with

> JavaScript中另一个难以掌握（并且现在也不推荐使用）的用来欺骗词法作用域的功能是with关键字
>
> 

with通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象本身。

```js
var obj = {
    a: 1,
    b: 2,
    c: 3
};
// 单调乏味的重复"obj"
obj.a = 2;
obj.b = 3;
obj.c = 4;
// 简单的快捷方式
with (obj) {
    a = 3;
    b = 4;
    c = 5;
}
```



> 尽管with块可以将一个对象处理为词法作用域，但是这个块内部正常的var声明并不会被限制在这个块的作用域中，而是被添加到with所处的函数作用域中。

```js
function foo(obj) {
    with (obj) {
      a = 2;
    }
}
var o1 = {
    a: 3
};
var o2 = {
    b: 3
};
foo(o1);
console.log(o1.a); // 2
foo(o2);
console.log(o2.a); // undefined
console.log(a); // 2——不好，a被泄漏到全局作用域上了！
```



> **eval(..)函数如果接受了含有一个或多个声明的代码，就会修改其所处的词法作用域，而with声明实际上是根据你传递给它的对象凭空创建了一个全新的词法作用域。**

> 另外一个不推荐使用eval(..)和with的原因是会被严格模式所影响（限制）。with被完全禁止，而在保留核心功能的前提下，间接或非安全地使用eval(..)也被禁止了

## 函数作用域和块级作用域



> **区分函数声明和表达式**最简单的方法是看function关键字出现在声明中的位置（不仅仅是一行代码，而是整个声明中的位置）。如果function是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。

### 匿名和具名

```js
setTimeout(function() {
    console.log("I waited 1 second! ");
}, 1000 );
```



> 这叫作匿sss名函数表达式，因为function().．没有名称标识符。函数表达式可以是匿名的，而函数声明则不可以省略函数名——在JavaScript的语法中这是非法的。

1．匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。

2．如果没有函数名，当函数需要引用自身时只能使用已经过期的arguments.callee引用，比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑自身。

3．匿名函数省略了对于代码可读性/可理解性很重要的函数名。一个描述性的名称可以让代码不言自明。

#### 立即执行函数表达式

> 这种模式很常见，几年前社区给它规定了一个术语：IIFE，代表立即执行函数表达式（Immediately Invoked Function Expression）；
>
> 函数名对IIFE当然不是必须的，IIFE最常见的用法是使用一个匿名函数表达式。

```js
var a = 2;
(function foo() {
    var a = 3;
    console.log(a); // 3
})();
console.log(a); // 2
```

相较于传统的IIFE形式，很多人都更喜欢另一个改进的形式：

```js
(function(){ .. }())
```

**IIFE的另一个非常普遍的进阶用法是把它们当作函数调用并传递参数进去。**

```js
var a = 2;

(function IIFE(global) {

    var a = 3;
    console.log(a); // 3
    console.log(global.a); // 2

})(window);

console.log(a); // 2
```

**IIFE还有一种变化的用途是倒置代码的运行顺序，将需要运行的函数放在第二位，在IIFE执行之后当作参数传递进去。这种模式在UMD（Universal Module Definition）项目中被广泛使用。尽管这种模式略显冗长，但有些人认为它更易理解。**

```js
(function IIFE(def) {
    def(window);
})(function def(global) {

    var a = 3;
    console.log(a); // 3
    console.log(global.a); // 2

});
```



### 块级作用域

```js
// for 循环块级作用域
for (var i=0; i<10; i++) {
    console.log(i);
}

// if 块级作用域
var foo = true;

if (foo) {
    var bar = foo * 2;
    bar = something(bar);
    console.log(bar);
}
```

#### with

> 我们在第2章讨论过with关键字。它不仅是一个难于理解的结构，同时也是块作用域的一个例子（块作用域的一种形式），用with从对象中创建出的作用域仅在with声明中而非外部作用域中有效

#### try/catch

> 非常少有人会注意到JavaScript的ES3规范中规定try/catch的catch分句会创建一个块作用域，其中声明的变量仅在catch内部有效。

```js
try {
    undefined(); // 执行一个非法操作来强制制造一个异常
}
catch (err) {
    console.log(err); // 能够正常执行！
}

console.log(err); // ReferenceError: err not found
```

#### let

> let关键字可以将变量绑定到所在的任意作用域中（通常是{ .. }内部）。换句话说，let为其声明的变量隐式地劫持了所在的块作用域。

```js
var foo = true    
if (foo) {
        { // <-- 显式的块
          let bar = foo ＊ 2;
          bar = something(bar);
          console.log(bar);
        }
    }

    console.log(bar); // ReferenceError
```

> 只要声明是有效的，在声明中的任意位置都可以使用{ .. }括号来为let创建一个用于绑定的块。在这个例子中，我们在if声明内部显式地创建了一个块，如果需要对其进行重构，整个块都可以被方便地移动而不会对外部if声明的位置和语义产生任何影响



#### 垃圾收集

```js
        
function process(data) {
    // 在这里做点有趣的事情
}

var someReallyBigData = { ..};

process(someReallyBigData);

var btn = document.getElementById("my button");

btn.addEventListener("click", function click(evt) {
    console.log("button clicked");
}, /*capturingPhase=*/false );
```

> click函数的点击回调并不需要someReallyBigData变量。理论上这意味着当process(..)执行后，在内存中占用大量空间的数据结构就可以被垃圾回收了。但是，由于click函数形成了一个覆盖整个作用域的闭包，JavaScript引擎极有可能依然保存着这个结构（取决于具体实现）。

```js
function process(data) {
    // 在这里做点有趣的事情
}

// 在这个块中定义的内容完事可以销毁！
{
    let someReallyBigData = { .. };

    process(someReallyBigData);
}

var btn = document.getElementById("my button");

btn.addEventListener("click", function click(evt){
    console.log("button clicked");
}, /*capturingPhase=*/false );
```



#### const

> 除了let以外，ES6还引入了const，同样可以用来创建块作用域变量，但其值是固定的（常量）。之后任何试图修改值的操作都会引起错误。



### 小结

> 函数是JavaScript中最常见的作用域单元。本质上，声明在一个函数内部的变量或函数会在所处的作用域中“隐藏”起来，这是有意为之的良好软件的设计原则。但函数不是唯一的作用域单元。块作用域指的是变量和函数不仅可以属于所处的作用域，也可以属于某个代码块（通常指{ .. }内部）。从ES3开始，try/catch结构在catch分句中具有块作用域。在ES6中引入了let关键字（var关键字的表亲），用来在任意代码块中声明变量。if (..) { let a = 2; }会声明一个劫持了if的{ .. }块的变量，并且将变量添加到这个块中。有些人认为块作用域不应该完全作为函数作用域的替代方案。两种功能应该同时存在，开发者可以并且也应该根据需要选择使用何种作用域，创造可读、可维护的优良代码。

## 提升

> 变量和函数声明都会在作用域提升

```js
console.log(a)// undefined
a = 2;

// 等同于

var a;
console.log(a);
a = 2;
```

**函数表达式却不会被提升**

```js
foo(); // 不是ReferenceError, 而是TypeError!

var foo = function bar() {
    // ...
};
```

```js
var foo;

foo(); // TypeError
bar(); // ReferenceError

foo = function() {
    var bar = ...self...
    // ...
}
```

#### 函数优先

> 函数声明和变量声明都会被提升。但是一个值得注意的细节（这个细节可以出现在有多个“重复”声明的代码中）是函数会首先被提升，然后才是变量。

```js
  foo(); // 1

  var foo;

  function foo() {
      console.log(1);
  }

  foo = function() {
      console.log(2);
  };
```

**会输出1而不是2！这个代码片段会被引擎理解为如下形式：**

```js
function foo() {
    console.log(1);
}

foo(); // 1

foo = function() {
    console.log(2);
};
```

> 注意，var foo尽管出现在function foo()..．的声明之前，但它是重复的声明（因此被忽略了），因为函数声明会被提升到普通变量之前。

## 作用域闭包

> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

```js
function foo() {
    var a = 2;

    function bar() {
      console.log(a); // 2
    }

    bar();
}

foo();
```

**这是闭包吗？**

> 技术上来讲，也许是。但根据前面的定义，确切地说并不是。我认为最准确地用来解释bar()对a的引用的方法是词法作用域的查找规则，而这些规则只是闭包的一部分。（但却是非常重要的一部分！）

```js
function foo() {
    var a = 2;

    function bar() {
      console.log(a);
    }

    return bar;
}

var baz = foo();
baz(); // 2——朋友，这就是闭包的效果。
```

> 无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包。



### 循环和闭包



```js
for (var i=1; i<=5; i++) {
    setTimeout(function timer() {
      console.log(i);
    }, i＊1000 );
}
```

> 正常情况下，我们对这段代码行为的预期是分别输出数字1～5，每秒一次，每次一个。但实际上，这段代码在运行时会以每秒一次的频率输出五次6。
>
> **延迟函数的回调会在循环结束时才执行**。事实上，当定时器运行时即使每个迭代中执行的是setTimeout(.., 0)，所有的回调函数依然是在循环结束后才会被执行，因此会每次输出一个6出来。

**利用IIFE**

```js
for (var i=1; i<=5; i++) {
    (function(j) {
      setTimeout(function timer() {
          console.log(j);
      }, j*1000 );
    })(i);
}
```

> 在迭代内使用IIFE会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问。
>
> 本质上这是将一个块转换成一个可以被关闭的作用域

```js
for (let i=1; i<=5; i++) {
    setTimeout(function timer() {
      console.log(i);
    }, i*1000 );
}
```



### 模块

> 模块模式需要具备两个必要条件。
>
> 1．必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）。
>
> 2．封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

```js
function CoolModule() {
    var something = "cool";    
    var another = [1, 2, 3];

    function doSomething() {
        console.log(something);
    }

    function doAnother() {
        console.log(another.join(" ! "));
    }

    return {
        doSomething: doSomething,
        doAnother: doAnother
    };
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

### 现代的模块机制

> 大多数模块依赖加载器/管理器本质上都是将这种模块定义封装进一个友好的API。

```js
var MyModules = (function Manager() {
    var modules = {};

    function define(name, deps, impl) {
      for (var i=0; i<deps.length; i++) {
          deps[i] = modules[deps[i]];
      }
      modules[name] = impl.apply(impl, deps);
    }

    function get(name) {
      return modules[name];
    }
    return {
        define: define,
        get: get
    };
})();
```

**定义模块**

```js
MyModules.define("bar", [], function() {
    function hello(who) {
      return "Let me introduce: " + who;
    }

    return {
      hello: hello
    };
} );

MyModules.define("foo", ["bar"], function(bar) {
    var hungry = "hippo";

    function awesome() {
      console.log(bar.hello(hungry).toUpperCase());
    }

    return {
      awesome: awesome
    };
} );

var bar = MyModules.get("bar");
var foo = MyModules.get("foo");

console.log(
    bar.hello("hippo")
); // Let me introduce: hippo
foo.awesome(); // LET ME INTRODUCE: HIPPO
```

### 小结

> 当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时就产生了闭包。
>
> 模块有两个主要特征：
>
> （1）为创建内部作用域而调用了一个包装函数；
>
> （2）包装函数的返回值必须至少包括一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭包。



## 关于this



```js
function identify() {
    return this.name.toUpperCase();
}

function speak() {
    var greeting = "Hello, I'm " + identify.call(this);
    console.log(greeting);
}

var me = {
    name: "Kyle"
};

var you = {
    name: "Reader"
};

identify.call(me); // KYLE
identify.call(you); // READER

speak.call(me); // Hello, 我是KYLE
speak.call(you); // Hello, 我是READER
```

**为什么要使用this?**

> 这段代码可以在不同的上下文对象（me和you）中重复使用函数identify()和speak()，不用针对每个对象编写不同版本的函数。
>
> 如果不使用this，那就需要给identify()和speak()显式传入一个上下文对象。
>
> this提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将API设计得更加简洁并且易于复用。

### 对this的误解

#### 指向自身

> 人们很容易把this理解成指向函数自身，这个推断从英语的语法角度来说是说得通的。

记录一下函数foo被调用的次数，思考一下下面的代码：

```js
function foo(num) {
    console.log("foo: " + num);

    // 记录foo被调用的次数
    this.count++;
}

foo.count = 0;

var i;
for (i=0; i<10; i++) {
    if (i > 5) {
      foo(i);
    }
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// foo被调用了多少次？
console.log(foo.count); // 0 -- 什么？！
```

> this.count 和 foo.count 不是同一个东西，也就是说this 不是指向函数自身

> 如果要从函数对象内部引用它自身，那只使用this是不够的。一般来说你需要通过一个指向函数对象的词法标识符（变量）来引用它。
>
> 还有一种传统的但是现在已经被弃用和批判的用法，是使用arguments. callee来引用当前正在运行的函数对象。这是唯一一种可以从匿名函数对象内部引用自身的方法。

#### this指向函数的作用域

> 这个问题有点复杂，因为在某种情况下它是正确的，但是在其他情况下它却是错误的。

思考一下下面的代码，它试图（但是没有成功）跨越边界，使用this来隐式引用函数的词法作用域：

```js
function foo() {
    var a = 2;
    this.bar();
}

function bar() {
    console.log(this.a);
}

foo(); // ReferenceError: a is not defined
```

> 首先，这段代码试图通过this.bar()来引用bar()函数。这样调用能成功纯属意外
>
> 调用bar()最自然的方法是省略前面的this，直接使用词法引用标识符。
>
> 
>
> 编写这段代码的开发者还试图使用this联通foo()和bar()的词法作用域，从而让bar()可以访问foo()作用域里的变量a。这是不可能实现的，使用this不可能在词法作用域中查到什么。



### this到底是什么

> this是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。**this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。**



### 调用位置

> 在理解this的绑定过程之前，首先要理解调用位置：调用位置就是函数在代码中被调用的位置（而不是声明的位置）。只有仔细分析调用位置才能回答这个问题：这个this到底引用的是什么？

```js
function baz() {
    // 当前调用栈是：baz
    // 因此，当前调用位置是全局作用域

    console.log("baz");
    bar(); // <-- bar的调用位置
}

function bar() {
                // 当前调用栈是baz -> bar
    // 因此，当前调用位置在baz中

    console.log("bar");
    foo(); // <-- foo的调用位置
}

function foo() {
    // 当前调用栈是baz -> bar -> foo
    // 因此，当前调用位置在bar中

    console.log("foo");
}

baz(); // <-- baz的调用位置
```

### 默认绑定

```js
function foo() {
    console.log(this.a);
}

var a = 2;

foo(); // 2
```

> 我们可以看到当调用foo()时，this.a被解析成了全局变量a。为什么？因为在本例中，**函数调用时应用了this的默认绑定，因此this指向全局对象。**
>
> 【注意】**前提是在 非strict mode下**



### 隐式绑定



```js
function foo() {
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

obj.foo(); // 2
```

> 无论你如何称呼这个模式，当foo()被调用时，它的前面确实加上了对obj的引用。当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。因为调用foo()时this被绑定到obj，因此this.a和obj.a是一样的。

**对象属性引用链中只有上一层或者说最后一层在调用位置中起作用。**

```js
function foo() {
    console.log(this.a);
}

var obj2 = {
    a: 42,
    foo: foo
};

var obj1 = {
    a: 2,
    obj2: obj2
};

obj1.obj2.foo(); // 42
```



### 隐式丢失

> 一个最常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把this绑定到全局对象或者undefined上，取决于是否是严格模式。

示例1

```js
function foo() {
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; // 函数别名！

var a = "oops, global"; // a是全局对象的属性

bar(); // "oops, global"
```

示例2

```js
function foo() {
    console.log(this.a);
}

function doFoo(fn) {
    // fn其实引用的是foo

    fn(); // <-- 调用位置！
}

var obj = {
    a: 2,
    foo: foo
};

var a = "oops, global"; // a是全局对象的属性

doFoo(obj.foo); // "oops, global"
```



### 显式绑定

> 在分析隐式绑定时，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接引用函数，从而把this间接（隐式）绑定到这个对象上。
>
> 可以使用函数的**call**(..)和**apply**(..)方法

```js
function foo() {
    console.log(this.a);
}

var obj = {
    a:2
};

foo.call(obj); // 2
```

> 通过foo.call(..)，我们可以在调用foo时强制把它的this绑定到obj上。
>
> 如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作this的绑定对象，这个原始值会被转换成它的对象形式（也就是new String(..)、new Boolean(..)或者new Number(..)）。这通常被称为“装箱”。

**硬绑定**

```js
function foo(something) {
    console.log(this.a, something);
    return this.a + something;
}

// 简单的辅助绑定函数
function bind(fn, obj) {
    return function() {
      return fn.apply(obj, arguments);
    };
}

var obj = {
    a:2
};

var bar = bind(foo, obj);

var b = bar(3); // 2 3
console.log(b); // 5
```

由于硬绑定是一种非常常用的模式，所以ES5提供了内置的方法Function.prototype.bind，它的用法如下：



### new 绑定

> 使用new来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。
>
> 1．创建（或者说构造）一个全新的对象。
>
> 2．这个新对象会被执行[[Prototype]]连接。
>
> 3．这个新对象会绑定到函数调用的this。
>
> 4．如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```js
function foo(a) {
    this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

### API调用的“上下文”

> 第三方库的许多函数，以及JavaScript语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”（context），其作用和bind(..)一样，确保你的回调函数使用指定的this。举例来说：
>
> 

```js
function foo(el) {
    console.log(el, this.id);
}

var obj = {
    id: "awesome"
};

// 调用foo(..)时把this绑定到obj
[1, 2, 3].forEach(foo, obj);
// 1 awesome 2 awesome 3 awesome
```

> 这些函数实际上就是通过call(..)或者apply(..)实现了显式绑定，这样你可以少写一些代码。

### 优先级

**隐式绑定和显式绑定哪个优先级更高？**

```js
function foo() {
    console.log(this.a);
}

var obj1 = {
    a: 2,
    foo: foo
};

var obj2 = {
    a: 3,
    foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call(obj2); // 3
obj2.foo.call(obj1); // 2
```

> 可以看到，显式绑定优先级更高，也就是说在判断时应当先考虑是否可以存在显式绑定

**new绑定和隐式绑定的优先级谁高谁低?**

```js
function foo(something) {
    this.a = something;
}

var obj1 = {
    foo: foo
};

var obj2 = {};

obj1.foo(2);
console.log(obj1.a); // 2

obj1.foo.call(obj2, 3);
console.log(obj2.a); // 3

var bar = new obj1.foo(4);
console.log(obj1.a); // 2
console.log(bar.a); // 4
```

> 可以看到new绑定比隐式绑定优先级高

**new绑定和显式绑定谁的优先级更高呢？**

> new和call/apply无法一起使用，因此无法通过new foo.call(obj1)来直接进行测试。但是我们可以使用硬绑定（显示绑定的一种）来测试它俩的优先级。

```js
function foo(something) {
    this.a = something;
}

var obj1 = {};

var bar = foo.bind (obj1);
bar(2);
console.log(obj1.a); // 2

var baz = new bar(3);
console.log(obj1.a); // 2
console.log(baz.a); // 3
```



### 判断this

> 现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断：

1. 函数是否在new中调用（new绑定）？如果是的话this绑定的是新创建的对象。

	```js
	var bar = new foo()
	```

	

2. 函数是否通过call、apply（显式绑定）或者硬绑定调用？如果是的话，this绑定的是指定的对象。

	```js
	var bar = foo.call(obj2)
	```

	

3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this绑定的是那个上下文对象。

	```js
	var bar = obj1.foo()
	```

	

4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象。

	```js
	var bar = foo()
	```

	

### 绑定例外

> 规则总有例外，这里也一样。
>
> 在某些场景下this的绑定行为会出乎意料，你认为应当应用其他绑定规则时，实际上应用的可能是默认绑定规则

#### 被忽略的this

> 如果你把null或者undefined作为this的绑定对象传入call、apply或者bind，这些值在调用时会被忽略，实际应用的是默认绑定规则：

```js
function foo() {
    console.log(this.a);
}

var a = 2;

foo.call(null); // 2
```

> 当我们的函数不需要关心this指向只关心参数时，但第一个参数需要占位符，null是一个不错的选择
>
> 然而，总是使用null来忽略this绑定可能产生一些副作用。如果某个函数确实使用了this（比如第三方库中的一个函数），那默认绑定规则会把this绑定到全局对象（在浏览器中这个对象是window），这将导致不可预计的后果（比如修改全局对象）。

#### 更安全的this

> 一种“更安全”的做法是传入一个特殊的对象，把this绑定到这个对象不会对你的程序产生任何副作用.
>
> 我们可以创建一个“DMZ”（demilitarized zone，非军事区）对象——它就是一个空的非委托的对象

```js
function foo(a, b) {
    console.log("a:" + a + ", b:" + b);
}

// 我们的DMZ空对象
var ø = Object.create(null);

// 把数组展开成参数
foo.apply(ø, [2, 3]); // a:2, b:3

// 使用bind(..)进行柯里化
var bar = foo.bind(ø, 2);
bar(3); // a:2, b:3
```

#### 间接引用

> 另一个需要注意的是，你有可能（有意或者无意地）创建一个函数的“间接引用”，在这种情况下，调用这个函数会应用默认绑定规则。

```js
function foo() {
    console.log(this.a);
}
var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

> 赋值表达式p.foo = o.foo的返回值是目标函数的引用，因此调用位置是foo()而不是p.foo()或者o.foo()。根据我们之前说过的，这里会应用默认绑定。
>
> 注意：对于默认绑定来说，决定this绑定对象的并不是调用位置是否处于严格模式，而是函数体是否处于严格模式。如果函数体处于严格模式，this会被绑定到undefined，否则this会被绑定到全局对象。

####  软绑定

> 之前我们已经看到过，硬绑定这种方式可以把this强制绑定到指定的对象（除了使用new时），防止函数调用应用默认绑定规则。问题在于，硬绑定会大大降低函数的灵活性，使用硬绑定之后就无法使用隐式绑定或者显式绑定来修改this。
>
> 如果可以给默认绑定指定一个全局对象和undefined以外的值，那就可以实现和硬绑定相同的效果，同时保留隐式绑定或者显式绑定修改this的能力

```js
if (! Function.prototype.softBind) {
    Function.prototype.softBind = function(obj) {
      var fn = this;
      // 捕获所有 curried 参数
      var curried = [].slice.call (arguments, 1);
      var bound = function() {
          return fn.apply(
              (! this || this === (window || global)) ?
                  obj : this,
              curried.concat.apply(curried, arguments)
          );
      };
      bound.prototype = Object.create(fn.prototype);
      return bound;
    };
}
```

> 它会对指定的函数进行封装，首先检查调用时的this，如果this绑定到全局对象或者undefined，那就把指定的默认对象obj绑定到this，否则不会修改this



### this 词法

> 之前介绍的四条规则已经可以包含所有正常的函数。但是ES6中介绍了一种无法使用这些规则的特殊函数类型：箭头函数。
>
> 箭头函数并不是使用function关键字定义的，而是使用被称为“胖箭头”的操作符=>定义的。箭头函数不使用this的四种标准规则，而是根据外层（函数或者全局）作用域来决定this。

```js
function foo() {
    // 返回一个箭头函数
    return (a) => {
      //this继承自foo()
      console.log(this.a);
    };
}

var obj1 = {
    a:2
};

var obj2 = {
    a:3
};

var bar = foo.call(obj1);
bar.call(obj2); // 2, 不是3！
```

### 小结

> 如果要判断一个运行中函数的this绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面这四条规则来判断this的绑定对象。
>
> 1．由new调用？绑定到新创建的对象。
>
> 2．由call或者apply（或者bind）调用？绑定到指定的对象。
>
> 3．由上下文对象调用？绑定到那个上下文对象。
>
> 4．默认：在严格模式下绑定到undefined，否则绑定到全局对象。
>
> 一定要注意，有些调用可能在无意中使用默认绑定规则。如果想“更安全”地忽略this绑定，你可以使用一个DMZ对象，比如ø =Object.create(null)，以保护全局对象。
>
> ES6中的**箭头函数**并不会使用四条标准的绑定规则，而是**根据当前的词法作用域来决定this**，具体来说，箭头函数会继承外层函数调用的this绑定（无论this绑定到什么）。这其实和ES6之前代码中的self = this机制一样。



## 对象

> 简单基本类型（string、boolean、number、null和undefined）本身并不是对象。null有时会被当作一种对象类型，但是这其实只是语言本身的一个bug，即对null执行typeof null时会返回字符串"object"。[插图]实际上，null本身是基本类型。



### 内容

> 对象的内容是由一些存储在特定命名位置的（任意类型的）值组成的，我们称之为属性。
>
> 在引擎内部，这些值的存储方式是多种多样的，一般并不会存在对象容器内部。存储在对象容器内部的是这些属性的名称，它们就像指针（从技术角度来说就是引用）一样，指向这些值真正的存储位置。



**可计算属性名**

> ES6增加了可计算属性名，可以在文字形式中使用[]包裹一个表达式来当作属性名：
>
> 可计算属性名最常用的场景可能是ES6的符号（Symbol）

```js
var prefix = "foo";

var myObject = {
    [prefix + "bar"]:"hello",
    [prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

**数组**

> 你完全可以把数组当作一个普通的键/值对象来使用，并且不添加任何数值索引，但是这并不是一个好主意。数组和普通的对象都根据其对应的行为和用途进行了优化，所以最好只用对象来存储键/值对，只用数组来存储数值下标/值对。

注意：如果你试图向数组添加一个属性，但是属性名“看起来”像一个数字，那它会变成一个数值下标（因此会修改数组的内容而不是添加一个属性）：

```js
var myArray = [ "foo", 42, "bar" ];

myArray["3"] = "baz";

myArray.length; // 4

myArray[3]; // "baz"
```



#### 复制对象

> 浅复制只复制对象引用，不是创建一个新的一模一样的对象。深复制比较复杂，如果对象存在相互引用则会出现死循环

深复制：

```js
var newObj = JSON.parse(JSON.stringify(someObj));
```

浅复制：

```js
Object.assign(..)
```



#### 属性描述符

> 在ES5之前，JavaScript语言本身并没有提供可以直接检测属性特性的方法，比如判断属性是否是只读。但是从ES5开始，所有的属性都具备了属性描述符。

```js
var myObject = {
    a:2
};

Object.getOwnPropertyDescriptor(myObject, "a");
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```

除了value属性还有三个特性：writable（可写）、enumerable（可枚举）和configurable（可配置



```js
var myObject = {};

Object.defineProperty(myObject, "a", {
    value: 2,
    writable: true,
    configurable: true,
    enumerable: true
} );

myObject.a; // 2
```

> 我们使用defineProperty(..)给myObject添加了一个普通的属性并显式指定了一些特性。然而，一般来说你不会使用这种方式，除非你想修改属性描述符。

1. **Writable**

	> writable决定是否可以修改属性的值。

	

2. **Configurable**

	> 只要属性是可配置的，就可以使用defineProperty(..)方法来修改属性描述符
	>
	> 注意：把configurable修改成false是单向操作，无法撤销！
	>
	> 要注意有一个小小的例外：即便属性是configurable:false，我们还是可以把writable的状态由true改为false，但是无法由false改为true。除了无法修改，configurable:false还会禁止删除这个属性：

	

	```js
	var myObject = {
	    a:2
	};
	
	myObject.a; // 2
	
	delete myObject.a;
	myObject.a; // undefined
	
	Object.defineProperty(myObject, "a", {
	    value: 2,
	    writable: true,
	    configurable: false,
	    enumerable: true
	} );
	
	myObject.a; // 2
	delete myObject.a;
	myObject.a; // 2
	```

	

3. **Enumerable**

	> 这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说for..in循环。如果把enumerable设置成false，这个属性就不会出现在枚举中，虽然仍然可以正常访问它。相对地，设置成true就会让它出现在枚举中



#### 不变性

> 描述符的可变性是浅层的，当有嵌套属性是对象时就控制不了

1. 对象常量

	> 结合writable:false和configurable:false就可以创建一个真正的常量属性（不可修改、重定义或者删除）：

	

	```js
	var myObject = {};
	
	Object.defineProperty(myObject, "FAVORITE NUMBER", {
	    value: 42,
	    writable: false,
	    configurable: false
	} );
	```

	

2. 禁止扩展

	> 如果你想禁止一个对象添加新属性并且保留已有属性，可以使用Object.prevent Extensions(..)：

	

	```js
	var myObject = {
	    a:2
	};
	
	Object.preventExtensions(myObject);
	
	myObject.b = 3;
	myObject.b; // undefined
	```

	> 在非严格模式下，创建属性b会静默失败。在严格模式下，将会抛出TypeError错误。

	

3. 密封

	> Object.seal(..)会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用Object.preventExtensions(..)并把所有现有属性标记为**configurable:false**。
	>
	> 

	

4. 冻结

	> Object.freeze(..)会创建一个冻结对象，这个方法实际上会在一个现有对象上调用Object.seal(..)并把所有“数据访问”属性标记为**writable:false**，这样就无法修改它们的值。

	

5. [get]

	> 对象默认的内置[[Get]]操作首先在对象中查找是否有名称相同的属性，
	>
	> 如果找到就会返回这个属性的值。然而，如果没有找到名称相同的属性，按照[[Get]]算法的定义会执行另外一种非常重要的行为。
	>
	> (其实就是遍历可能存在的[[Prototype]]链，也就是原型链）。
	>
	> 如果无论如何都没有找到名称相同的属性，那[[Get]]操作会返回值undefined：

	

6. [put]

	> 1．属性是否是访问描述符？如果是并且存在setter就调用setter。
	>
	> 2．属性的数据描述符中writable是否是false？如果是，在非严格模式下静默失败，在严格模式下抛出TypeError异常。
	>
	> 3．如果都不是，将该值设置为属性的值。

	

7. Getter和Setter

	> 对象默认的 [get],[put] 行为可以重写

	```js
	var myObject = {
	    // 给a定义一个getter
	    get a() {
	      return 2;
	    }
	};
	
	myObject.a = 3;
	
	myObject.a; // 2
	```

	

#### **存在性**

> 前面我们介绍过，如myObject.a的属性访问返回值可能是undefined，但是这个值有可能是属性中存储的undefined，也可能是因为属性不存在所以返回undefined。那么如何区分这两种情况呢？

我们可以在不访问属性值的情况下判断对象中是否存在这个属性：

```js
var myObject = {
    a:2
};

("a" in myObject); // true
("b" in myObject); // false

myObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("b"); // false
```

> in操作符会检查属性是否在对象及其[[Prototype]]原型链中.。
>
> 相比之下，hasOwnProperty(..)只会检查属性是否在myObject对象中，不会检查[[Prototype]]链
>
> Object.prototype.hasOwnProperty. call(myObject, "a")，它借用基础的hasOwnProperty(..)方法并把它显式绑定到myObject上。

> 看起来**in操作符可以检查容器内是否有某个值**，但是**它实际上检查的是某个属性名是否存在**。
>
> 对于数组来说这个区别非常重要，4 in [2, 4, 6]的结果并不是你期待的True，因为[2, 4,6]这个数组中包含的属性名是0、1、2，没有4。

#### 枚举

```js
var myObject = { };

Object.defineProperty(
    myObject,
    "a",
    // 让a像普通属性一样可以枚举
    { enumerable: true, value: 2 }
);

Object.defineProperty(
    myObject,
    "b",
    // 让b不可枚举
    { enumerable: false, value: 3 }
);

myObject.b; // 3
("b" in myObject); // true
myObject.hasOwnProperty("b"); // true

// .......

for (var k in myObject) {
    console.log(k, myObject[k]);
}
// "a" 2
```

> myObject.b确实存在并且有访问值，但是却不会出现在for..in循环中（尽管可以通过in操作符来判断是否存在）。原因是“可枚举”就相当于“可以出现在对象属性的遍历中”。
>
> 在数组上应用for..in循环有时会产生出人意料的结果，因为这种枚举不仅会包含所有数值索引，还会包含所有可枚举属性。最好只在对象上应用for..in循环，如果要遍历数组就使用传统的for循环来遍历数值索引。

**判断是否可枚举**

```js
var myObject = { };

Object.defineProperty(
    myObject,
    "a",
    // 让a像普通属性一样可以枚举
    { enumerable: true, value: 2 }
);

Object.defineProperty(
                  myObject,
      "b",
      // 让b不可枚举
      { enumerable: false, value: 3 }
  );

  myObject.propertyIsEnumerable("a"); // true
  myObject.propertyIsEnumerable("b"); // false

  Object.keys(myObject); // ["a"]
  Object.getOwnPropertyNames(myObject); // ["a", "b"]
```

> propertyIsEnumerable(..)会检查给定的属性名是否直接存在于对象中（而不是在原型链上）并且满足enumerable:true。
>
> Object.keys(..)会返回一个数组，包含所有可枚举属性，Object.getOwnPropertyNames(..)会返回一个数组，包含所有属性，无论它们是否可枚举。
>
> in和hasOwnProperty(..)的区别在于前者会查找[[Prototype]]链，然而，Object.keys(..)和Object.getOwnPropertyNames(..)都只会查找对象直接包含的属性。



### 遍历

> for..in循环可以用来遍历对象的可枚举属性列表（包括[[Prototype]]链）。但是如何遍历属性的值呢？

> ES5中增加了一些数组的辅助迭代器，包括forEach(..)、every(..)和some(..)。
>
> **遍历数组下标时采用的是数字顺序**（for循环或者其他迭代器），**但是遍历对象属性时的顺序是不确定的**，在不同的JavaScript引擎中可能不一样。因此，在不同的环境中需要保证一致性时，一定不要相信任何观察到的顺序，它们是不可靠的。





## 类

> 在研究类的具体机制之前，我们首先会介绍面向类的设计模式：实例化（instantiation）、继承（inheritance）和（相对）多态（polymorphism）。



### 混入

> 在继承或者实例化时，JavaScript的对象机制并不会自动执行复制行为。简单来说，JavaScript中只有对象，并不存在可以被实例化的“类”。一个对象并不会被复制到其他对象，它们会被关联起来
>
> 因此JavaScript开发者也想出了一个方法来模拟类的复制行为，这个方法就是混入

#### 显式混入

```js
// 非常简单的mixin(..)例子：
function mixin(sourceObj, targetObj) {
    for (var key in sourceObj) {
      // 只会在不存在的情况下复制
      if (! (key in targetObj)) {
          targetObj[key] = sourceObj[key];
      }
    }

    return targetObj;
}

var Vehicle = {
    engines: 1,
    ignition: function() {
      console.log("Turning on my engine.");
    },

    drive: function() {
      this.ignition();
      console.log("Steering and moving forward! ");
    }
};

var Car = mixin(Vehicle, {
    wheels: 4,
    drive: function() {
      Vehicle.drive.call(this);
      console.log(
          "Rolling on all " + this.wheels + " wheels! "
      );
    }
} );
```

> Vehicle.drive()，函数调用中的this会被绑定到Vehicle对象而不是Car对象，这并不是我们想要的。因此，我们会使用．call(this)来确保drive()在Car对象的上下文中执行。





### 原型

> JavaScript中的对象有一个特殊的[[Prototype]]内置属性，其实就是对于其他对象的引用。几乎所有的对象在创建时[[Prototype]]属性都会被赋予一个非空的值。



对于默认的[[Get]]操作来说，如果无法在对象本身找到需要的属性，就会继续访问对象的[[Prototype]]链：

```js
var anotherObject = {
    a:2
};

// 创建一个关联到anotherObject的对象
var myObject = Object.create(anotherObject);

myObject.a; // 2
```

> 使用for..in遍历对象时原理和查找[[Prototype]]链类似，任何可以通过原型链访问到（并且是enumerable）的属性都会被枚举。
>
> 使用in操作符来检查属性在对象中是否存在时，同样会查找对象的整条原型链（无论属性是否可枚举）

#### **但是到哪里是[[Prototype]]的“尽头”呢？**

> **所有普通的[[Prototype]]链最终都会指向内置的Object.prototype**



#### 属性设置和屏蔽

> 给一个对象设置属性并不仅仅是添加一个新属性或者修改已有的属性值。现在我们完整地讲解一下这个过程：

```js
  myObject.foo = "bar";
```

**如果myObject对象中包含名为foo的普通数据访问属性**，这条赋值语句只会修改已有的属性值。

**如果foo不是直接存在于myObject中**，[[Prototype]]链就会被遍历，类似[[Get]]操作。如果原型链上找不到foo, foo就会被直接添加到myObject上。

> 如果属性名foo既出现在myObject中也出现在myObject的[[Prototype]]链上层，那么就会发生屏蔽。**myObject中包含的foo属性会屏蔽原型链上层的所有foo属性，因为myObject.foo总是会选择原型链中最底层的foo属性。**

**如果foo不直接存在于myObject中 而是存在于原型链上层时myObject.foo = "bar"会出现的三种情况。**

1．如果在[[Prototype]]链上层存在名为foo的普通数据访问属性并且没有被标记为只读（writable:false），那就会直接在myObject中添加一个名为foo的新属性，它是屏蔽属性。

2．如果在[[Prototype]]链上层存在foo，但是它被标记为只读（writable:false），那么无法修改已有属性或者在myObject上创建屏蔽属性。如果运行在严格模式下，代码会抛出一个错误。否则，这条赋值语句会被忽略。总之，不会发生屏蔽。

3．如果在[[Prototype]]链上层存在foo并且它是一个setter，那就一定会调用这个setter。foo不会被添加到（或者说屏蔽于）myObject，也不会重新定义foo这个setter。

> 第二种情况可能是最令人意外的，只读属性会阻止[[Prototype]]链下层隐式创建（屏蔽）同名属性。这样做主要是为了模拟类属性的继承。你可以把原型链上层的foo看作是父类中的属性，它会被myObject继承（复制），这样一来myObject中的foo属性也是只读，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制（参见第4章和第5章）。这看起来有点奇怪，myObject对象竟然会因为其他对象中有一个只读foo就不能包含foo属性。更奇怪的是，这个限制只存在于=赋值中，使用Object.defineProperty(..)并不会受到影响。



```js
var anotherObject = {
    a:2
};

var myObject = Object.create(anotherObject);

anotherObject.a; // 2
myObject.a; // 2
anotherObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("a"); // false

myObject.a++; // 隐式屏蔽！

anotherObject.a; // 2
myObject.a; // 3

myObject.hasOwnProperty("a"); // true
```

> ++操作相当于myObject.a = myObject.a + 1。因此++操作首先会通过[[Prototype]]查找属性a并从anotherObject.a获取当前属性值2，然后给这个值加1，接着用[[Put]]将值3赋给myObject中新建的屏蔽属性a



### 类



```js
function Foo() {
    // ...
}

var a = new Foo();

Object.getPrototypeOf(a) === Foo.prototype; // true
```

> 调用new Foo()时会创建a,其中一步就是将a内部的[[Prototype]]链接到Foo.prototype所指向的对象。
>
> 实际上，绝大多数JavaScript开发者不知道的秘密是，new Foo()这个函数调用实际上并没有直接创建关联，这个关联只是一个意外的副作用。new Foo()只是间接完成了我们的目标：一个关联到其他对象的新对象
>
> 继承意味着复制操作，JavaScript（默认）并不会复制对象属性。相反，JavaScript会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。

**到底是什么让我们认为Foo是一个“类”呢？**

> 其中一个原因是我们看到了关键字new，在面向类的语言中构造类实例时也会用到它。另一个原因是，看起来我们执行了类的构造函数方法，Foo()的调用方式很像初始化类时类构造函数的调用方式。

```js
function Foo() {
    // ...
}

Foo.prototype.constructor === Foo; // true

var a = new Foo();
a.constructor === Foo; // true
```

> Foo.prototype默认有一个公有并且不可枚举的属性．constructor，这个属性引用的是对象关联的函数。此外，我们可以看到通过“构造函数”调用new Foo()创建的对象也有一个．constructor属性，指向“创建这个对象的函数”。

#### 构造函数还是调用

> 上一段代码很容易让人认为Foo是一个构造函数，因为我们使用new来调用它并且看到它“构造”了一个对象。
>
> 实际上，Foo和你程序中的其他函数没有任何区别。函数本身并不是构造函数，然而，当你在普通的函数调用前面加上new关键字之后，就会把这个函数调用变成一个“构造函数调用”。实际上，new会劫持所有普通函数并用构造对象的形式来调用它。

举例来说：

```js
function NothingSpecial() {
    console.log("Don't mind me! ");
}

var a = new NothingSpecial();
// "Don't mind me! "

a; // {}
```

> **函数不是构造函数，但是当且仅当使用new时，函数调用会变成“构造函数调用”**



```js
function Foo(name) {
    this.name = name;
}

Foo.prototype.myName = function() {
    return this.name;
};

var a = new Foo("a");
var b = new Foo("b");

a.myName(); // "a"
b.myName(); // "b"
```

**这段代码展示了另外两种**“面向类”的技巧：

1．this.name = name给每个对象（也就是a和b）都添加了．name属性，有点像类实例封装的数据值。

2．Foo.prototype.myName = ..．可能个更有趣的技巧，它会给Foo.prototype对象添加一个属性（函数）。

现在，a.myName()可以正常工作，但是你可能会觉得很惊讶，这是什么原理呢？

> 在这段代码中，看起来似乎创建a和b时会把Foo.prototype对象复制到这两个对象中，然而事实并不是这样
>
> 在创建的过程中，a和b的内部[[Prototype]]都会关联到Foo.prototype上。当a和b中无法找到myName时，它会（通过委托）在Foo.prototype上找到。



**之前讨论．constructor属性时我们说过，看起来a.constructor === Foo为真意味着a确实有一个指向Foo的．constructor属性，**

> 但是事实不是这样。实际上，.constructor引用同样被委托给了Foo.prototype，而Foo.prototype.constructor默认指向Foo。
>
> Foo.prototype的．constructor属性只是Foo函数在声明时的默认属性。**如果你创建了一个新对象并替换了函数默认的．prototype对象引用，那么新对象并不会自动获得．constructor属性。**

```js
function Foo() { /＊ .. ＊/ }

Foo.prototype = { /＊ .. ＊/ }; // 创建一个新原型对象

var a1 = new Foo();
a1.constructor === Foo; // false!
a1.constructor === Object; // true!
```

> .constructor并不是一个不可变属性。它是不可枚举（参见上面的代码）的，但是它的值是可写的（可以被修改）。此外，你可以给任意[[Prototype]]链中的任意对象添加一个名为constructor的属性或者对其进行修改，你可以任意对其赋值。



### （原型）继承



```js
function Foo(name) {
    this.name = name;
}

Foo.prototype.myName = function() {
    return this.name;
};

function Bar(name, label) {
    Foo.call(this, name);
    this.label = label;
}

// 我们创建了一个新的Bar.prototype对象并关联到Foo.prototype
Bar.prototype = Object.create(Foo.prototype);

// 注意！现在没有Bar.prototype.constructor了
// 如果你需要这个属性的话可能需要手动修复一下它

Bar.prototype.myLabel = function() {
    return this.label;
};

var a = new Bar("a", "obj a");

a.myName(); // "a"
a.myLabel(); // "obj a"
```

> 这段代码的核心部分就是语句Bar.prototype =Object.create(Foo.prototype)。调用Object.create(..)会凭空创建一个“新”对象并把新对象内部的[[Prototype]]关联到你指定的对象

**注意，下面这两种方式是常见的错误做法，实际上它们都存在一些问题：**

```js
// 和你想要的机制不一样！
Bar.prototype = Foo.prototype;

// 基本上满足你的需求，但是可能会产生一些副作用:(
Bar.prototype = new Foo();
```

> Bar.prototype = Foo.prototype并不会创建一个关联到Bar.prototype的新对象，它只是让Bar.prototype直接引用Foo.prototype对象。因此当你执行类似Bar.prototype.myLabel = ..．的赋值语句时会直接修改Foo.prototype对象本身。显然这不是你想要的结果，否则你根本不需要Bar对象，直接使用Foo就可以了，这样代码也会更简单一些。
>
> Bar.prototype = new Foo()的确会创建一个关联到Bar.prototype的新对象。但是它使用了Foo(..)的“构造函数调用”，如果函数Foo有一些副作用（比如写日志、修改状态、注册到其他对象、给this添加数据属性，等等）的话，就会影响到Bar()的“后代”，后果不堪设想。

**ES6添加了辅助函数可以用标准并且可靠的方法来修改关联。**

```js
// ES6之前需要抛弃默认的Bar.prototype
Bar.ptototype = Object.create(Foo.prototype);

// ES6开始可以直接修改现有的Bar.prototype
Object.setPrototypeOf(Bar.prototype, Foo.prototype);
```



#### 检查类的关系

> 假设有对象a，如何寻找对象a委托的对象（如果存在的话）呢？

```js
function Foo() {
    // ...
}

Foo.prototype.blah = ...;

var a = new Foo();

a instanceof Foo; // true
```

> instanceof回答的问题是：在a的整条[[Prototype]]链中是否有指向Foo.prototype的对象？
>
> 这个方法只能处理对象（a）和函数（带．prototype引用的Foo）之间的关系。如果你想判断两个对象（比如a和b）之间是否通过[[Prototype]]链关联，只用instanceof无法实现。
>
> 如果使用内置的．bind(..)函数来生成一个硬绑定函数的话，该函数是没有．prototype属性的。在这样的函数上使用instanceof的话，目标函数的．prototype会代替硬绑定函数的．prototype。

**判断[[Prototype]]反射的方法**

```js
Foo.prototype.isPrototypeOf(a); // true
```

> isPrototypeOf(..)回答的问题是：在a的整条[[Prototype]]链中是否出现过Foo.prototype？

**我们也可以直接获取一个对象的[[Prototype]]链。在ES5中，标准的方法是：**

```js
Object.getPrototypeOf(a);
// 验证
          
Object.getPrototypeOf(a) === Foo.prototype; // true
```

**绝大多数（不是所有！）浏览器也支持一种非标准的方法来访问内部[[Prototype]]属性：**

```js
a.__proto__ === Foo.prototype; // true
```

.__proto__的实现大致上是这样的

```js
Object.defineProperty(Object.prototype, " __proto__", {
    get: function() {
      return Object.getPrototypeOf(this);
    },
    set: function(o) {
      // ES6中的setPrototypeOf(..)
      Object.setPrototypeOf(this, o);
      return o;
    }
} );
```



### 对象关联

> 现在我们知道了，[[Prototype]]机制就是存在于对象中的一个内部链接，它会引用其他对象。
>
> 通常来说，这个链接的作用是：如果在对象上没有找到需要的属性或者方法引用，引擎就会继续在[[Prototype]]关联的对象上进行查找。同理，如果在后者中也没有找到需要的引用就会继续查找它的[[Prototype]]，以此类推。这一系列对象的链接被称为“原型链”。

#### 创建对象

>  Object.create(null)会创建一个拥有空（或者说null）[[Prototype]]链接的对象，这个对象无法进行委托。由于这个对象没有原型链，所以instanceof操作符（之前解释过）无法进行判断，因此总是会返回false。这些特殊的空[[Prototype]]对象通常被称作“字典”，它们完全不会受到原型链的干扰，因此非常适合用来存储数据。

Object.create()的polyfill 代码

```js
if (! Object.create) {
    Object.create = function(o) {
      function F(){}
      F.prototype = o;
      return new F();
    };
}
```

> Object.create(..)的第二个参数指定了需要添加到新对象中的属性名以及这些属性的属性描述符。因为ES5之前的版本无法模拟属性操作符，所以polyfill代码无法实现这个附加功能。

```js
var anotherObject = {
    a:2
};

var myObject = Object.create(anotherObject, {
    b: {
      enumerable: false,
      writable: true,
      configurable: false,
      value: 3
    },
    c: {
      enumerable: true,
      writable: false,
      configurable: false,
      value: 4
    }
});

myObject.hasOwnProperty("a"); // false
myObject.hasOwnProperty("b"); // true
myObject.hasOwnProperty("c"); // true

myObject.a; // 2
myObject.b; // 3
myObject.c; // 4
```

#### 关联关系是备用

> 看起来对象之间的关联关系是处理“缺失”属性或者方法时的一种备用选项。这个说法有点道理，但是我认为这并不是[[Prototype]]的本质。

```js
var anotherObject = {
    cool: function() {
      console.log("cool! ");
    }
};

var myObject = Object.create(anotherObject);

myObject.cool(); // "cool! "
```

> **小结：**
>
> 如果要访问对象中并不存在的一个属性，[[Get]]操作（参见第3章）就会查找对象内部[[Prototype]]关联的对象。这个关联关系实际上定义了一条“原型链”（有点像嵌套的作用域链），在查找属性时会对它进行遍历。
>
> 出于各种原因，以“继承”结尾的术语（包括“原型继承”）和其他面向对象的术语都无法帮助你理解JavaScript的真实机制（不仅仅是限制我们的思维模式）。
>
> 相比之下，“委托”是一个更合适的术语，因为对象之间的关系不是复制而是委托。



## 行为委托

>  [[Prototype]]机制就是指对象中的一个内部链接引用另一个对象。不断向上层查找这一系列对象的链接被称为“原型链”。
>
> 换句话说，JavaScript中这个机制的本质就是对象之间的关联关系。
>
> 这个观点对于理解行为委托很重要

###  面向委托的设计

> 面向类的设计中有些原则依然有效，因此不要把所有知识都抛掉。（只需要抛掉大部分就够了！）举例来说，封装是非常有用的，它同样可以应用在委托中（虽然不太常见）。

#### 类理论

> 如果使用类，那设计方法可能是这样的：定义一个通用父（基）类，可以将其命名为Task，在Task类中定义所有任务都有的行为。接着定义子类XYZ和ABC，它们都继承自Task并且会添加一些特殊的行为来处理对应的任务。
>
> 非常重要的是，类设计模式鼓励你在继承时使用方法重写（和多态），比如说在XYZ任务中重写Task中定义的一些通用方法，甚至在添加新行为时通过super调用这个方法的原始版本。你会发现许多行为可以先“抽象”到父类然后再用子类进行特殊化（重写）。

```js
class Task {
    id;

    // 构造函数Task()
    Task(ID) { id = ID; }
    outputTask() { output(id); }
}

class XYZ inherits Task {
    label;

    // 构造函数XYZ()
    XYZ(ID, Label) { super(ID); label = Label; }
    outputTask() { super(); output(label); }
}

class ABC inherits Task {
    // ...
}
```



基本上你可以想象成，执行任务“XYZ”需要两个兄弟对象（XYZ和Task）协作完成。但是我们并不需要把这些行为放在一起，通过类的复制，我们可以把它们分别放在各自独立的对象中，需要时可以允许XYZ对象委托给Task。

```js
Task = {
    setID: function(ID) { this.id = ID; },
    outputID: function() { console.log(this.id); }
};

// 让XYZ委托Task
XYZ = Object.create(Task);

XYZ.prepareTask = function(ID, Label) {
    this.setID(ID);
    this.label = Label;
};

XYZ.outputTaskDetails = function() {
    this.outputID();
    console.log(this.label);
};

// ABC = Object.create(Task);
// ABC ... = ...
```

> 相比于面向类（或者说面向对象），我会把这种编码风格称为“对象关联”
>
> 我们真正关心的只是XYZ对象（和ABC对象）委托了Task对象。

1．在上面的代码中，id和label数据成员都是直接存储在XYZ上（而不是Task）。通常来说，在[[Prototype]]委托中最好把状态保存在委托者（XYZ、ABC）而不是委托目标（Task）上。

2．在类设计模式中，我们故意让父类（Task）和子类（XYZ）中都有outputTask方法，这样就可以利用重写（多态）的优势。在委托行为中则恰好相反：我们会尽量避免在[[Prototype]]链的不同级别中使用相同的命名，否则就需要使用笨拙并且脆弱的语法来消除引用歧义

3．this.setID(ID); XYZ中的方法首先会寻找XYZ自身是否有setID(..)，但是XYZ中并没有这个方法名，因此会通过[[Prototype]]委托关联到Task继续寻找，这时就可以找到setID(..)方法。此外，由于调用位置触发了this的隐式绑定规则，因此虽然setID(..)方法在Task中，运行时this仍然会绑定到XYZ，这正是我们想要的



#### 互相委托（禁止）

> 你无法在两个或两个以上互相（双向）委托的对象之间创建循环委托。如果你把B关联到A然后试着把A关联到B，就会出错。(但其实所有引用严格限制，理论上相互委托是可以正常运行工作的)
>
> 之所以要禁止互相委托，是因为引擎的开发者们发现在设置时检查（并禁止！）一次无限循环引用要更加高效，否则每次从对象中查找属性时都需要进行检查。



#### 调试

> JavaScript规范并不会控制浏览器中开发者工具对于特定值或者结构的表示方式，浏览器和引擎可以自己选择合适的方式来进行解析,

```js
function Foo() {}

var a1 = new Foo();

a1; // {}   Chrome中， 火狐中 Object {}

```

> 本例中Chrome的控制台确实使用了．constructor.name



#### 比较思维模型

> “类”和“委托”这两种设计模式的理论，接下来我们看看它们在思维模型方面的区别。

典型的（“原型”）面向对象风格：

```js
function Foo(who) {
    this.me = who;
}
Foo.prototype.identify = function() {
    return "I am " + this.me;
};

function Bar(who) {
    Foo.call(this, who);
}
Bar.prototype = Object.create(Foo.prototype);

Bar.prototype.speak = function() {
    alert("Hello, " + this.identify() + ".");
};

var b1 = new Bar("b1");
var b2 = new Bar("b2");

b1.speak();
b2.speak();
```

用对象关联风格来编写功能完全相同的代码：

```js
Foo = {
    init: function(who) {
      this.me = who;
    },
    identify: function() {
      return "I am " + this.me;
    }
};
Bar = Object.create(Foo);

Bar.speak = function() {
    alert("Hello, " + this.identify() + ".");
};

var b1 = Object.create(Bar);
b1.init("b1");
var b2 = Object.create(Bar);
```



#### 类与对象

组件实例

**类风格：**

es5 写法

```js
  // 父类
  function Widget(width, height) {
      this.width = width || 50;
      this.height = height || 50;
      this.$elem = null;
  }

  Widget.prototype.render = function($where){
      if (this.$elem) {
        this.$elem.css({
            width: this.width + "px",
            height: this.height + "px"
        }).appendTo($where);
      }
  };

  // 子类
  function Button(width, height, label) {
      // 调用“super”构造函数
      Widget.call(this, width, height);
      this.label = label || "Default";
      this.$elem = $("<button>").text(this.label);
  }

  // 让Button“继承”Widget
  Button.prototype = Object.create(Widget.prototype);

  // 重写render(..)
  Button.prototype.render = function($where) {
      // “super”调用
      Widget.prototype.render.call(this, $where);
      this.$elem.click(this.onClick.bind(this));
  };

  Button.prototype.onClick = function(evt) {
      console.log("Button '" + this.label + "' clicked! ");
  };
$(document).ready(function(){
    var $body = $(document.body);
    var btn1 = new Button(125, 30, "Hello");
    var btn2 = new Button(150, 40, "World");

    btn1.render($body);
    btn2.render($body);
} );
```

es6 class语法糖写法

```js
class Widget {
    constructor(width, height) {
      this.width = width || 50;
      this.height = height || 50;
      this.$elem = null;
    }
    render($where){
      if (this.$elem) {
          this.$elem.css({
              width: this.width + "px",
              height: this.height + "px"
          }).appendTo($where);
      }
    }
}

class Button extends Widget {
    constructor(width, height, label) {
      super(width, height);
      this.label = label || "Default";
      this.$elem = $("<button>").text(this.label);
    }
    render($where) {
      super.render($where);
      this.$elem.click(this.onClick.bind(this));
    }
    onClick(evt) {
      console.log("Button '" + this.label + "' clicked! ");
    }
}
```

**委托控件对象**

```js
var Widget = {
    init: function(width, height){
      this.width = width || 50;
      this.height = height || 50;
      this.$elem = null;
    },
    insert: function($where){
      if (this.$elem) {
          this.$elem.css({
              width: this.width + "px",
              height: this.height + "px"
          }).appendTo($where);
      }
    }
};

var Button = Object.create(Widget);

Button.setup = function(width, height, label){
    // 委托调用
    this.init(width, height);
    this.label = label || "Default";

    this.$elem = $("<button>").text(this.label);
};
Button.build = function($where) {
  // 委托调用
  this.insert($where);
  this.$elem.click(this.onClick.bind(this));
};
Button.onClick = function(evt) {
  console.log("Button '" + this.label + "' clicked! ");
};

$(document).ready(function(){
  var $body = $(document.body);

  var btn1 = Object.create(Button);
  btn1.setup(125, 30, "Hello");

  var btn2 = Object.create(Button);
  btn2.setup(150, 40, "World");

  btn1.build($body);
  btn2.build($body);
} );
```

> 对象关联可以更好地支持关注分离（separation of concerns）原则，创建和初始化并不需要合并为一个步骤。



#### 更简洁的设计

> 对象关联除了能让代码看起来更简洁（并且更具扩展性）外还可以通过行为委托模式简化代码结构。
>
> 我们来看最后一个例子，它展示了对象关联如何简化整体设计。在这个场景中我们有两个控制器对象，一个用来操作网页中的登录表单，另一个用来与服务器进行验证（通信）。

**传统类设计**

```js
// 父类
function Controller() {
    this.errors = [];
}
Controller.prototype.showDialog = function(title, msg) {
    // 给用户显示标题和消息
};
Controller.prototype.success = function(msg) {
    this.showDialog("Success", msg);
};
Controller.prototype.failure = function(err) {
    this.errors.push(err);
    this.showDialog("Error", err);
};

// 子类
function LoginController() {
    Controller.call(this);
}
// 把子类关联到父类
LoginController.prototype = Object.create(Controller.prototype);
LoginController.prototype.getUser = function() {
    return document.getElementById("login username").value;
};
LoginController.prototype.getPassword = function() {
    return document.getElementById("login password").value;
};
LoginController.prototype.validateEntry = function(user, pw) {
    user = user || this.getUser();
    pw = pw || this.getPassword();
    if (! (user && pw)) {
      return this.failure(
          "Please enter a username & password! "
      );
    }
    else if (pw.length < 5) {
      return this.failure(
          "Password must be 5+ characters! "
      );
    }

    // 如果执行到这里说明通过验证
    return true;
};
// 重写基础的failure()
LoginController.prototype.failure = function(err) {
    // “super”调用
    Controller.prototype.failure.call(
      this,
      "Login invalid: " + err
    );
};

// 子类
function AuthController(login) {
    Controller.call(this);
    // 合成
    this.login = login;
}
// 把子类关联到父类
AuthController.prototype = Object.create(Controller.prototype);
AuthController.prototype.server = function(url, data) {
    return $.ajax({
      url: url,
      data: data
    } );
};
AuthController.prototype.checkAuth = function() {
    var user = this.login.getUser();
    var pw = this.login.getPassword();

    if (this.login.validateEntry(user, pw)) {
        this.server("/check-auth", {
            user: user,
            pw: pw
        } )
        .then(this.success.bind(this))
        .fail(this.failure.bind(this));
        }
    };
    // 重写基础的success()
    AuthController.prototype.success = function() {
    // “super”调用
    Controller.prototype.success.call(this, "Authenticated! ");
    };
    // 重写基础的failure()
    AuthController.prototype.failure = function(err) {
    // “super”调用
    Controller.prototype.failure.call(
        this,
        "Auth Failed: " + err
    );
};

var auth = new AuthController(
    // 除了继承，我们还需要合成
    new LoginController()
);
auth.checkAuth();
```

**使用对象关联风格的行为委托来实现更简单的设计**

```js
var LoginController = {
    errors: [],
    getUser: function() {
      return document.getElementById(
          "login username"
      ).value;
    },
    getPassword: function() {
      return document.getElementById(
          "login password"
      ).value;
    },
    validateEntry: function(user, pw) {
      user = user || this.getUser();
      pw = pw || this.getPassword();

      if (! (user && pw)) {
          return this.failure(
            "Please enter a username & password! "
          );
      }
      else if (pw.length < 5) {
          return this.failure(
            "Password must be 5+ characters! "
          );
      }

      // 如果执行到这里说明通过验证
      return true;
    },
    showDialog: function(title, msg) {
      // 给用户显示标题和消息
    },
    failure: function(err) {
      this.errors.push(err);
      this.showDialog("Error", "Login invalid: " + err);
    }
};
// 让AuthController委托LoginController
var AuthController = Object.create(LoginController);

AuthController.errors = [];
AuthController.checkAuth = function() {
    var user = this.getUser();
    var pw = this.getPassword();
    if (this.validateEntry(user, pw)) {
      this.server("/check-auth", {
          user: user,
            pw: pw
      } )
      .then(this.accepted.bind(this))
      .fail(this.rejected.bind(this));
    }
};
AuthController.server = function(url, data) {
    return $.ajax({
      url: url,
      data: data
    } );
};
AuthController.accepted = function() {
    this.showDialog("Success", "Authenticated! ")
};
AuthController.rejected = function(err) {
    this.failure("Auth Failed: " + err);
};
```

> 我们用一种（极其）简单的设计实现了同样的功能，这就是对象关联风格代码和行为委托设计模式的力量。



#### 内省

> 内省就是检查实例的类型。类实例的内省主要目的是通过创建方式来判断对象的结构和功能。



> **小结：**
>
> 行为委托认为对象之间是兄弟关系，互相委托，而不是父类和子类的关系。JavaScript的[[Prototype]]机制本质上就是行为委托机制。也就是说，我们可以选择在JavaScript中努力实现类机制，也可以拥抱更自然的[[Prototype]]委托机制。
>
> 当你只用对象来设计代码时，不仅可以让语法更加简洁，而且可以让代码结构更加清晰。对象关联（对象之前互相关联）是一种编码风格，它倡导的是直接创建和关联对象，不把它们抽象成类。对象关联可以用基于[[Prototype]]的行为委托非常自然地实现。





## 类型和语法



### typeof 处理undeclared (未声明)变量的方式

```js
var a;

typeof a; // "undefined"

typeof b; // "undefined"
```

> 对于undeclared（或者not defined）变量，typeof照样返回"undefined"。请注意虽然b是一个undeclared变量，但typeof b并没有报错。这是因为typeof有一个特殊的安全防范机制。

**利用这个安全机制检测变量是否存在**

```js
// 这样会抛出错误
if (DEBUG) {
    console.log( "Debugging is starting" );
}

// 这样是安全的
if (typeof DEBUG ! == "undefined") {
    console.log( "Debugging is starting" );
}
```



### 字符串借用数组的不变更成员函数

> 可惜我们无法“借用”数组的可变更成员函数，因为字符串是不可变的

```js
a.join;         // undefined
a.map;          // undefined

var c = Array.prototype.join.call( a, "-" );
var d = Array.prototype.map.call( a, function(v){
    return v.toUpperCase() + ".";
} ).join( "" );

c;              // "f-o-o"
d;              // "F.O.O."
```



### 数字

> 数字字面量还可以用其他格式来表示，如二进制、八进制和十六进制。
>
> 从ES6开始，严格模式（strict mode）不再支持0363八进制格式

```js
// 无效语法：
42.toFixed( 3 );    // SyntaxError

// 下面的语法都有效：
(42).toFixed( 3 );  // "42.000"
0.42.toFixed( 3 );  // "0.420"
42..toFixed( 3 );   // "42.000"
```

> 42.toFixed(3)是无效语法，因为．被视为常量42．的一部分（如前所述），所以没有．属性访问运算符来调用toFixed方法。

ES6支持以下新格式：


```js
  0o363;      // 243的八进制
  0O363;      // 同上

  0b11110011; // 243的二进制
  0B11110011; // 同上
```



#### **二进制浮点数最大的问题**

```js
        0.1 + 0.2 === 0.3; // false
```

> 简单来说，二进制浮点数中的0.1和0.2并不是十分精确，它们相加的结果并非刚好等于0.3，而是一个比较接近的数字0.30000000000000004，所以条件判断结果为false。

#### **整数的安全范围**

> es6 新增了 Number.MAX_SAFE_INTEGER，Number.MIN_SAFE_INTEGER 储存了最大最小安全整数

#### **整数检测**

> 可以使用ES6中的Number.isInteger(..)方法：

```js
Number.isInteger( 42 );     // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 );   // false
```

es6之前的方法

```js
if (! Number.isInteger) {
    Number.isInteger = function(num) {
      return typeof num == "number" && num % 1 == 0;
    };
}
```



#### 要检测一个值是否是安全的整数

> 可以使用ES6中的Number.isSafeInteger(..)方法：

```js
Number.isSafeInteger( Number.MAX SAFE INTEGER );    // true
Number.isSafeInteger( Math.pow( 2, 53 ) );          // false
Number.isSafeInteger( Math.pow( 2, 53 ) -1 );      // true
```

es6之前方法

```js
if (! Number.isSafeInteger) {
    Number.isSafeInteger = function(num) {
      return Number.isInteger( num ) &&
          Math.abs( num ) <= Number.MAX SAFE INTEGER;
    };
}
```



#### void

> undefined是一个内置标识符,它的值为undefined，通过void运算符即可得到该值。
>
> 表达式void______没有返回值，因此返回结果是undefined。void并不改变表达式的结果，只是让表达式不返回值：

```js
function doSomething() {
    // 注：APP.ready由程序自己定义
    if (! APP.ready) {
      // 稍后再试
      return void setTimeout( doSomething,100 );
    }

    var result;

    // 其他
    return result;
}

// 现在可以了吗？
if (doSomething()) {
    // 立即执行下一个任务
}
```

#### 特殊的数字

> 如果数学运算的操作数不是数字类型（或者无法解析为常规的十进制或十六进制数字），就无法返回一个有效的数字，这种情况下返回值为**NaN**。

```js
var a = 2 / "foo";

a == NaN;   // false
a === NaN;  // false
```

> aN是一个特殊值，它和自身不相等，是唯一一个非自反（自反，reflexive，即x === x不成立）的值。而NaN ! =NaN为true，很奇怪吧？

**那如何来判断它们呢？**

> 可以使用内建的全局工具函数isNaN(..)来判断一个值是否是NaN。

```js
var a = 2 / "foo";
var b = "foo";

a; // NaN
b; "foo"

window.isNaN( a ); // true
window.isNaN( b ); // true——晕！
```

> 它的检查方式过于死板，就是“**检查参数是否不是NaN，也不是数字**”
>
> 很明显"foo"不是一个数字，但是它也不是NaN。这个bug自JavaScript问世以来就一直存在，至今已超过19年。

> 从ES6开始我们可以使用工具函数Number.isNaN(..)。ES6之前的浏览器的polyfill如下：

```js
if (!Number.isNaN) {
    Number.isNaN = function(n) {
      return (
          typeof n === "number" &&
          window.isNaN( n )
      );
    };
}

var a = 2 / "foo";
var b = "foo";

Number.isNaN( a ); // true
Number.isNaN( b ); // false——好！
```

#### 无穷数

> JavaScript的运算结果有可能溢出，此时结果为Infinity或者-Infinity。

```js
var a = 1 / 0;  // Infinity
var b = -1 / 0; // -Infinity
```

> Infinity/Infinity是一个未定义操作，结果为NaN。
>
> 有穷正数除以Infinity，结果为0。
>
> 有穷负数除以Infinity，结果为-0。

#### 零值

> JavaScript有一个常规的0（也叫作+0）和一个-0。

根据规范，对负零进行字符串化会返回"0"：

```js
var a = 0 / -3;

// 至少在某些浏览器的控制台中显示是正确的
a;                           // -0

// 但是规范定义的返回结果是这样！
a.toString();               // "0"
a + "";                     // "0"
String( a );                // "0"

// JSON也如此，很奇怪
JSON.stringify( a );   // "0"
```

如果反过来将其从字符串转换为数字，得到的结果是准确的：

```js
+"-0";              // -0
Number( "-0" );     // -0
JSON.parse( "-0" ); // -0
```

负零转换为字符串的结果令人费解，它的比较操作也是如此：

```js
var a = 0;
var b = 0 / -3;

a == b;     // true

-0 == 0;    // true

a === b;    // true
-0 === 0;   // true

0 > -0;     // false
a > b;      // false
```

**判断是否是 -0**

```js
  function isNegZero(n) {
      n = Number( n );
      return (n === 0) && (1 / n === -Infinity);
  }

  isNegZero( -0 );        // true
  isNegZero( 0 / -3 );    // true
  isNegZero( 0 );         // false
```

**为啥需要存在 -0?**

> 有些应用程序中的数据需要以级数形式来表示（比如动画帧的移动速度），数字的符号位（sign）用来代表其他信息（比如移动的方向）。此时如果一个值为0的变量失去了它的符号位，它的方向信息就会丢失。所以保留0值的符号位可以防止这类情况发生。

#### 特殊等式

> NaN和-0在相等比较时的表现有些特别。由于NaN和自身不相等，所以必须使用ES6中的Number.isNaN(..)。而-0等于0（对于===也是如此），
>
> ES6中新加入了一个工具方法Object.is(..)来判断两个值是否绝对相等，可以用来处理上述所有的特殊情况：

```js
  var a = 2 / "foo";
  var b = -3 * 0;

  Object.is( a, NaN );    // true
  Object.is( b, -0 );     // true

  Object.is( b, 0 );      // false
```



### 值和引用

> 简单值（即标量基本类型值，scalar primitive）总是通过值复制的方式来赋值/传递，包括null、undefined、字符串、数字、布尔和ES6中的symbol。
>
> 复合值（compound value）——对象（包括数组和封装对象）和函数，则总是通过引用复制的方式来赋值/传递。

```js
  var a = 2;
  var b = a; // b是a的值的一个复本
  b++;
  a; // 2
  b; // 3

  var c = [1,2,3];
  var d = c; // d是[1,2,3]的一个引用
  d.push( 4 );
  c; // [1,2,3,4]
  d; // [1,2,3,4]
```

产生的困惑

```js
function foo(x) {
    x.push( 4 );
    x; // [1,2,3,4]

    // 然后
    x = [4,5,6];
    x.push( 7 );
    x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // 是[1,2,3,4]，不是[4,5,6,7]
```

> 我们不能通过引用x来更改引用a的指向，只能更改a和x共同指向的值。



我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定。

### 原生函数

- String()
-  Number()
-  Boolean()
-  Array()
-  Object()
-  Function()
-  RegExp()
-  Date()
-  Error()
-  Symbol()——ES6中新加入的！

可以这样来用：

```js
var s = new String( "Hello World! " );

console.log( s.toString() ); // "Hello World! "
```

但其构造出来的对象可能会和我们设想的有所出入：

```js
var a = new String( "abc" );

typeof a;// 是"object"，不是"String"

a instanceof String; // true

Object.prototype.toString.call( a ); // "[object String]"
```

> new String("abc")创建的是字符串"abc"的封装对象，而非基本类型值"abc"。



####  原生函数作为构造函数

> 关于数组（array）、对象（object）、函数（function）和正则表达式，我们通常喜欢以常量的形式来创建它们。实际上，使用常量和使用构造函数的效果是一样的
>
> **应该尽量避免使用构造函数，除非十分必要，因为它们经常会产生意想不到的结果**

**Array(..)**

> 我们将包含至少一个“空单元”的数组称为“稀疏数组”。

```js
    var a = new Array( 3 );

    a.length; // 3
    a;
```

> 我们可以通过下述方式来创建包含undefined单元（而非“空单元”）的数组：

```js
var a = Array.apply( null, { length: 3 } );
a; // [ undefined, undefined, undefined ]
```

> 总之，永远不要创建和使用空单元数组。



#### Object(..)、Function(..)和RegExp(..)

> 同样，除非万不得已，否则尽量不要使用Object(..)/Function(..)/RegExp(..)：

比如动态定义正则表达式时可以使用 RegExp()



####  Date(..)和Error(..)

> 这两个构造函数用的比较多

####  Symbol(..)

> ES6中新加入了一个基本数据类型 ——符号（Symbol）。符号是具有唯一性的特殊值（并非绝对），用它来命名对象属性不容易导致重名。该类型的引入主要源于ES6的一些特殊构造，此外符号也可以自行定义。

> ES6中有一些预定义符号，以Symbol的静态属性形式出现，如Symbol.create、Symbol. iterator等，可以这样来使用：

```js
obj[Symbol.iterator] = function(){ /*..*/ };
```



> 符号并非对象，而是一种简单标量基本类型





#### 小结

> JavaScript为基本数据类型值提供了封装对象，称为原生函数（如String、Number、Boolean等）。它们为基本数据类型值提供了该子类型所特有的方法和属性（如：String#trim()和Array#concat(..)）。对于简单标量基本类型值，比如"abc"，如果要访问它的length属性或String.prototype方法，JavaScript引擎会自动对该值进行封装（即用相应类型的封装对象来包装它）来实现对这些属性和方法的访问。



### 强制类型转换



#### 值类型转换

> 将值从一种类型转换为另一种类型通常称为类型转换（type casting），这是显式的情况；隐式的情况称为强制类型转换（coercion）。
>
> JavaScript中的强制类型转换总是返回标量基本类型值。如字符串、数字和布尔值，不会返回对象和函数

然而在JavaScript中通常将它们统称为强制类型转换，我个人则倾向于用“**隐式强制类型转换**”（implicit coercion）和“**显式强制类型转换**”（explicit coercion）来区分。

二者的区别显而易见：**我们能够从代码中看出哪些地方是显式强制类型转换，而隐式强制类型转换则不那么明显，通常是某些操作产生的副作用。**

```js
var a = 42;

var b = a + "";         // 隐式强制类型转换

var c = String( a );    // 显式强制类型转换
```

> 要是你明白a + ""是怎么回事，它对你来说就是“显式”的。相反，如果你不知道String(..)可以用来做字符串强制类型转换，它对你来说可能就是“隐式”的。

##### ToString

> 它负责处理非字符串到字符串的强制类型转换。
>
> 基本类型值的字符串化规则为：null转换为"null", undefined转换为"undefined", true转换为"true"。数字的字符串化则遵循通用规则，不过前面讲过的那些极小和极大的数字使用指数形式

```js
// 1.07 连续乘以七个 1000
var a = 1.07 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000;

// 七个1000一共21位数字
a.toString(); // "1.07e21"
```

> 对普通对象来说，除非自行定义，否则toString()（Object.prototype.toString()）返回内部属性[[Class]]的值，如"[object Object]"。



##### JSON字符串化

> 工具函数JSON.stringify(..)在将JSON对象序列化为字符串时也用到了ToString。

对大多数简单值来说，JSON字符串化和toString()的效果基本相同，只不过序列化的结果总是字符串：

```js
  JSON.stringify( 42 );   // "42"
  JSON.stringify( "42" ); // ""42""（含有双引号的字符串）
  JSON.stringify( null ); // "null"
  JSON.stringify( true ); // "true"
```

> 所有安全的JSON值（JSON-safe）都可以使用JSON.stringify(..)字符串化。安全的JSON值是指能够呈现为有效JSON格式的值。
>
> 不安全的JSON值：
>
> undefined、function、symbol（ES6+）和包含循环引用（对象之间相互引用，形成一个无限循环）的对象都不符合JSON结构标准，其他支持JSON的语言无法处理它们

如果对象中定义了toJSON()方法，JSON字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

```js
var o = { };

var a = {
  b: 42,
  c: o,
  d: function(){}
};
// 在a中创建一个循环引用
o.e = a;

// 循环引用在这里会产生错误
// JSON.stringify( a );

// 自定义的JSON序列化
a.toJSON = function() {
    // 序列化仅包含b
    return { b: this.b };
};

JSON.stringify( a ); // "{"b":42}"
```

> **也就是说，toJSON()应该“返回一个能够被字符串化的安全的JSON值”，而不是“返回一个JSON字符串”。**

例如：

```js
var a = {
    val: [1,2,3],

    // 可能是我们想要的结果！
    toJSON: function(){
      return this.val.slice( 1 );
    }
};

var b = {
    val: [1,2,3],

    // 可能不是我们想要的结果！
    toJSON: function(){
      return "[" +
          this.val.slice( 1 ).join() +
      "]";
    }
};

JSON.stringify( a ); // "[2,3]"

JSON.stringify( b ); // ""[2,3]""
```

> 这里第二个函数是对toJSON返回的字符串做字符串化，而非数组本身。

**JSON.stringify(..)**也可以传递一个可选参数replacer，它可以是**数组或者函数**，**用来指定对象序列化过程中哪些属性应该被处理，哪些应该被排除**，和toJSON()很像。

如果replacer是一个函数，它会对对象本身调用一次，然后对对象中的每个属性各调用一次，每次传递两个参数，键和值。**如果要忽略某个键就返回undefined，否则返回指定的值。**

```js
var a = {
    b: 42,
    c: "42",
    d: [1,2,3]
};

JSON.stringify( a, ["b", "c"] ); // "{"b":42, "c":"42"}"

JSON.stringify( a, function(k, v){
    if (k ! == "c") return v;
} );
// "{"b":42, "d":[1,2,3]}"
```

JSON.stringify还有一个可选参数space，用来指定输出的缩进格式。space为正整数时是指定每一级缩进的字符数，它还可以是字符串，此时最前面的十个字符被用于每一级的缩进：

```js
var a = {
    b: 42,
    c: "42",
    d: [1,2,3]
};

JSON.stringify( a, null, 3 );
// "{
//    "b": 42,
//    "c": "42",
//    "d": [
//       1,
//       2,
//       3
//    ]
// }"

JSON.stringify( a, null, "-----" );
// "{
// -----"b": 42,
// -----"c": "42",
// -----"d": [
// ----------1,
// ----------2,
// ----------3
// -----]
// }"
```

> JSON.stringify(..)并不是强制类型转换。在这里介绍是因为它涉及ToString强制类型转换，具体表现在以下两点。
>
> (1) 字符串、数字、布尔值和null的JSON.stringify(..)规则与ToString基本相同。
>
> (2) 如果传递给JSON.stringify(..)的对象中定义了toJSON()方法，那么该方法会在字符串化前调用，以便将对象转换为安全的JSON值。



##### ToNumber

> 有时我们需要将非数字值当作数字来使用，比如数学运算。
>
> 为此ES5规范在9.3节定义了抽象操作ToNumber。其中**true转换为1, false转换为0。undefined转换为NaN, null转换为0**。
>
> ToNumber对字符串的处理基本遵循数字常量的相关规则/语法.处理失败时返回NaN。不同之处是ToNumber对以0开头的十六进制数并不按十六进制处理（而是按十进制
>
> 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

为了**将值转换为相应的基本类型值**，抽象操作ToPrimitive（参见ES5规范9.1节）会首先（通过内部操作DefaultValue，参见ES5规范8.12.8节）**检查该值是否有valueOf()方法**。如果有并且返回基本类型值，就使用该值进行强制类型转换。**如果没有就使用toString()的返回值**（如果存在）来进行强制类型转换。如果valueOf()和toString()均不返回基本类型值，会产生TypeError错误。

```js
var a = {
    valueOf: function(){
      return "42";
    }
};

var b = {
    toString: function(){
      return "42";
    }
};

var c = [4,2];
c.toString = function(){
    return this.join( "" ); // "42"
};

Number( a );                // 42
Number( b );                // 42
Number( c );                // 42
Number( "" );               // 0
Number( [] );               // 0
Number( [ "abc" ] );        // NaN
```

##### ToBoolean

> 在JavaScript中布尔值和数字是不一样的。虽然我们可以将1强制类型转换为true，将0强制类型转换为false，反之亦然，但它们并不是一回事。

**假值**

> • undefined
>
> • null
>
> • false
>
> • +0、-0和NaN
>
> • ""
>
> 假值的布尔强制类型转换结果为false。从逻辑上说，假值列表以外的都应该是真值（truthy）。

**假值对象（falsy object）**

> 前面讲过规范规定所有的对象都是真值，怎么还会有假值对象呢？

有人可能会以为假值对象就是包装了假值的封装对象（如""、0和false，参见第3章），实际不然。

```js
var a = new Boolean( false );
var b = new Number( 0 );
var c = new String( "" );

var d = Boolean( a && b && c );

d; // true
```

如果假值对象并非封装了假值的对象，那它究竟是什么？

> 最常见的例子是document.all，它是一个类数组对象，包含了页面上的所有元素，由DOM（而不是JavaScript引擎）提供给JavaScript程序使用。它以前曾是一个真正意义上的对象，布尔强制类型转换结果为true，不过现在它是一个假值对象。

##### 真值

> 真值就是假值列表之外的值。



##### **显示强制转换**

> 在JavaScript开源社区中，一元运算+被普遍认为是显式强制类型转换。
>
> 一元运算符+紧挨着++和--也很容易引起混淆。例如a +++b、a + ++b和a+ + +b

加法运算符（`+`）为两种不同的运算重载：数字加法和字符串连接。在求值时，它首先[将两个操作数强制转换为基本类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#强制原始值转换)。然后，检查两个操作数的类型：

- 如果有一方是字符串，另外一方则会被[转换为字符串](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String#转换数字为字符串)，并且与它们连接起来。
- 如果双方都是[BigInt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)，则执行 BigInt 加法。如果一方是 BigInt 而另一方不是，则会抛出[`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError)。
- 否则，双方都会被[转换为数字](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number#转换数字字符串为数字)，执行数字加法。

**日期显式转换为数字**

> 一元运算符+的另一个常见用途是将日期（Date）对象强制类型转换为数字，返回结果为Unix时间戳

```js
var d = new Date( "Mon, 18 Aug 2014 08:53:06 CDT" );

+d; // 1408369986000
```

##### 奇特的~运算符

> ~和indexOf()一起可以将结果强制类型转换（实际上仅仅是转换）为真/假值：

```js
var a = "Hello World";

~a.indexOf( "lo" );         // -4   <-- 真值！

if (~a.indexOf( "lo" )) {   // true
    // 找到匹配！
}

~a.indexOf( "ol" );         // 0    <-- 假值！
!~a.indexOf( "ol" );        // true

if (! ~a.indexOf( "ol" )) {  // true
    // 没有找到匹配！
}
```

##### 字位截除

> ~~来截除数字值的小数部分，以为这和Math.floor(..)的效果一样，实际上并非如此。
>
> 首先它只适用于32位数字，更重要的是它对负数的处理与Math. floor(..)不同。

```js
Math.floor( -49.6 );    // -50
~~-49.6;                // -49
```



#### 字符串和数字之间的隐式强制类型转换

> +运算符即能用于数字加法，也能用于字符串拼接。

```js
var a = "42";
var b = "0";

var c = 42;
var d = 0;

a + b; // "420"
c + d; // 42
```

> 某个操作数是字符串或者能够通过以下步骤转换为字符串的话，+将进行拼接操作。如果其中一个操作数是对象（包括数组），则首先对其调用ToPrimitive抽象操作（规范9.1节），该抽象操作再调用[[DefaultValue]]（规范8.12.8节），以数字作为上下文。

####  布尔值到数字的隐式强制类型转换

> 在将某些复杂的布尔逻辑转换为数字加法的时候，隐式强制类型转换能派上大用场。

```js
// 只有一个为真的条件返回true
function onlyOne() {
    var sum = 0;
    for (var i=0; i < arguments.length; i++) {
      // 跳过假值，和处理0一样，但是避免了NaN
      if (arguments[i]) {
          sum += arguments[i];
      }
    }
    return sum == 1;
}

var a = true;
var b = false;

onlyOne( b, a );                // true
onlyOne( b, a, b, b, b );       // true
```

#### 隐式强制类型转换为布尔值

> 相对布尔值，数字和字符串操作中的隐式强制类型转换还算比较明显。下面的情况会发生布尔值隐式强制类型转换。

(1) if (..)语句中的条件判断表达式。

(2) for ( .. ; .. ; .. )语句中的条件判断表达式（第二个）。

(3) while (..)和do..while(..)循环中的条件判断表达式。

(4) ? ：中的条件判断表达式。

(5) 逻辑运算符 ||（逻辑或）和 &&（逻辑与）左边的操作数（作为条件判断表达式）。

```js
var a = 42;
var b = "abc";
var c;
var d = null;

if (a) {
    console.log( "yep" );       // yep
}

while (c) {
    console.log( "nope, never runs" );
}

c = d ? a : b;
c;                               // "abc"

if ((a && d) || c) {
    console.log( "yep" );       // yep
}
```



#### || 和 &&

> 逻辑运算符||（或）和&&（与）应该并不陌生. 它们的返回值是两个操作数中的一个（且仅一个）。即选择两个操作数中的一个，然后返回它的值。 因此 可能 称之为 **操作数选择器运算符** 更恰当

```js
var a = 42;
var b = "abc";
var c = null;

a || b;     // 42
a && b;     // "abc"

c || b;     // "abc"
c && b;     // null
```

||和&&首先会对第一个操作数（a和c）执行条件判断，如果其不是布尔值（如上例）就先进行ToBoolean强制类型转换，然后再执行条件判断。

> 对于||来说，如果条件判断结果为true就返回第一个操作数（a和c）的值，如果为false就返回第二个操作数（b）的值。
>
> &&则相反，如果条件判断结果为true就返回第二个操作数（b）的值，如果为false就返回第一个操作数（a和c）的值。

换个角度理解：

```js
a || b;
// 大致相当于(roughly equivalent to):
a ? a : b;

a && b;
// 大致相当于(roughly equivalent to):
a ? b : a;
```

> a = b || "something"和a && b()用到了“短路”机制

#### 符号的强制类型转换

> ES6中引入了符号类型，它的强制类型转换有一个坑，在这里有必要提一下。ES6允许从符号到字符串的显式强制类型转换，然而隐式强制类型转换会产生错误

```js
  var s1 = Symbol( "cool" );
  String( s1 );     // "Symbol(cool)"

  var s2 = Symbol( "not cool" );
  s2 + "";      // TypeError
```

> **符号不能够被强制类型转换为数字**（显式和隐式都会产生错误），但可以**被强制类型转换为布尔值**（显式和隐式结果都是true）。

#### 宽松相等和严格相等

> 宽松相等（loose equals）==和严格相等（strict equals）===都用来判断两个值是否“相等”，但是它们之间有一个很重要的区别，特别是在判断条件上。
>
> **常见的误区是“==检查值是否相等，===检查值和类型是否相等”。**
>
> **正确的解释是：“==允许在相等比较中进行强制类型转换，而===不允许。”**

#### 相等比较操作的性能

> 有人觉得==会比===慢，实际上虽然强制类型转换确实要多花点时间，但仅仅是微秒级（百万分之一秒）的差别而已。
>
> 不用在乎性能
>
> ==和===都会检查操作数的类型。区别在于操作数类型不同时它们的处理方式不同。

#### 抽象相等

> “抽象相等”（abstract equality）的这些规则正是隐式强制类型转换被诟病的原因。

> es5规定 如果两个值的类型相同，就仅比较它们是否相等**。例如，42等于42, "abc"等于"abc"。
>
> 有几个非常规的情况需要注意
>
> • NaN不等于NaN
>
> • +0等于-0

> **==在比较两个不同类型的值时会发生隐式强制类型转换**，会将其中之一或两者都转换为相同的类型后再进行比较。

> 宽松不相等（loose not-equality）! =就是==的相反值，! ==同理。

##### 字符串和数字之间的相等比较

> 

```js
var a = 42;
var b = "42";

a === b;    // false
a == b;     // true
```

**类型不相等时会发生强制类型转换，具体怎么转换？**

ES5规范11.9.3.4-5这样定义：

(1) 如果Type(x)是数字，Type(y)是字符串，则返回x ==ToNumber(y)的结果。

(2) 如果Type(x)是字符串，Type(y)是数字，则返回ToNumber(x)== y的结果。

> 字符串，数字之间比较时，将字符串强制转为数字

##### 其他类型和布尔类型之间的相等比较

> ==最容易出错的一个地方是true和false与其他类型之间的相等比较。

```js
var a = "42";
var b = true;

a == b; // false
```

规范11.9.3.6-7是这样说的：

(1) 如果Type(x)是布尔类型，则返回ToNumber(x) == y的结果；

(2) 如果Type(y)是布尔类型，则返回x == ToNumber(y)的结果。

> 先将布尔值转换成数字，在应用数字与其他类型的比较规则

#####  null和undefined之间的相等比较

> null和undefined之间的==也涉及隐式强制类型转换。

ES5规范11.9.3.2-3规定：

(1) 如果x为null, y为undefined，则结果为true。

(2) 如果x为undefined, y为null，则结果为true。

除此之外，其他比较结果都是 false

```js
var a = doSomething();

if (a == null) {
    // ..
}
```

> 条件判断a == null仅在doSomething()返回null和undefined时才成立，除此之外其他值都不成立，包括0、false和""这样的假值。

> 这也就是说，在==中null和undefined是一回事，可以相互进行隐式强制类型转换

```js
var a = null;
var b;

a == b;     // true
a == null;  // true
b == null;  // true

a == false; // false
b == false; // false
a == "";    // false
b == "";    // false
a == 0;     // false
b == 0;     // false
```



##### 对象和非对象之间的相等比较

> 关于对象（对象/函数/数组）和标量基本类型（字符串/数字/布尔值）之间的相等比较

ES5规范11.9.3.8-9做如下规定：

(1) 如果Type(x)是字符串或数字，Type(y)是对象，则返回x ==ToPrimitive(y)的结果；

(2) 如果Type(x)是对象，Type(y)是字符串或数字，则返回ToPrimitive(x) == y的结果。

> **toPrimitive**(input,preferedType?):
>
> 1. 如果input是原始值，直接返回这个值；
>
>    2. 否则，如果input是对象，调用input.valueOf()，如果结果是原始值，返回结果；
>
>    3. 否则，调用input.toString()。如果结果是原始值，返回结果；
>
>    4. 否则，抛出错误。





> 这里没提到布尔值，因为布尔值会先被强制类型转换为数字

```js
  var a = "abc";
  var b = Object( a );    // 和new String( a )一样

  a === b;                // false
  a == b;                 // true
```



但有一些值不这样，原因是==算法中其他优先级更高的规则

```js
  var a = null;
  var b = Object( a );    // 和Object()一样
  a == b;                 // false

  var c = undefined;
  var d = Object( c );    // 和Object()一样
  c == d;                 // false

  var e = NaN;
  var f = Object( e );    // 和new Number( e )一样
  e == f;                 // false
```

> 因为没有对应的封装对象，所以null和undefined不能够被封装（boxed）, Object(null)和Object()均返回一个常规对象。
>
> NaN能够被封装为数字封装对象，但拆封之后NaN == NaN返回false，因为NaN不等于NaN

##### 比较少见的情况

> 更改内置原生原型会导致哪些奇怪的结果。

**返回其他数字**

```js
Number.prototype.valueOf = function() {
    return 3;
};

new Number( 2 ) == 3;   // true
```

> 2 == 3不会有这种问题，因为2和3都是数字基本类型值，不会调用Number.prototype.valueOf()方法。而Number(2)涉及ToPrimitive强制类型转换，因此会调用valueOf()

还有更奇怪的情况：

```js
if (a == 2 && a == 3) {
    // ..
}
```

如果让a.valueOf()每次调用都产生副作用，比如第一次返回2，第二次返回3，就会出现这样的情况。这实现起来很简单：

```js
var i = 2;

Number.prototype.valueOf = function() {
    return i++;
};

var a = new Number( 42 );

if (a == 2 && a == 3) {
    console.log( "Yep, this happened." );
}
```

但是千万不要滥用，我们应该正确合理地运用强制类型转换，避免这些极端的情况。

**假值的相等比较**

> ==中的隐式强制类型转换最为人诟病的地方是假值的相等比较

```js
"0" == null;           // false
"0" == undefined;      // false
"0" == false;          // true -- 晕！
"0" == NaN;            // false
"0" == 0;              // true
"0" == "";             // false

false == null;         // false
false == undefined;    // false
false == NaN;          // false
false == 0;            // true -- 晕！
false == "";           // true -- 晕！
false == [];           // true -- 晕！
false == {};           // false

"" == null;            // false
"" == undefined;       // false
"" == NaN;             // false
"" == 0;               // true -- 晕！
"" == [];              // true -- 晕！
"" == {};              // false

0 == null;             // false
0 == undefined;        // false
0 == NaN;              // false
0 == [];               // true -- 晕！
0 == {};               // false
```

**极端情况**

```js
[] ==  ![]   // true
```

> 根据ToBoolean规则，它会进行布尔值的显式强制类型转换（同时反转奇偶校验位）。所以[] == ! []变成了[] == false。布尔值强制转换数字 0，[] == 0, 数字跟对象比较 

其他情况

```js
2 == [2];       // true
"" == [null];   // true
```

还有一个坑常常被提到：

```js
0 == "\n";  // true
```

> ""、"\n"（或者" "等其他空格组合）等空字符串被ToNumber强制类型转换为0

#### 安全运用隐式强制类型转换

> 我们要对==两边的值认真推敲，以下两个原则可以让我们有效地避免出错。

• 如果两边的值中有true或者false，千万不要使用==。

• 如果两边的值中有[]、""或者0，尽量不要使用==。



#### 抽象关系比较

> a < b中涉及的隐式强制类型转换不太引人注意，不过还是很有必要深入了解一下。
>
> ES5规范11.8.5节定义了“抽象关系比较”（abstract relational comparison），分为两个部分：比较双方都是字符串（后半部分）和其他情况（前半部分）。



比较双方首先调用ToPrimitive，如果结果出现非字符串，就根据ToNumber规则将双方强制类型转换为数字来进行比较。

```js
var a = [ 42 ];
var b = [ "43" ];
a < b;  // true
b < a;  // false
```

如果比较双方都是字符串，则按字母顺序来进行比较：

```js
var a = [ "42" ];
var b = [ "043" ];

a < b;  // false
```

再比如：

```js
var a = { b: 42 };
var b = { b: 43 };

a < b;  // ? ?
```

结果居然是 false !!!, 🙄这是因为 a,b都被转换成了 "[object Object]"

下面的例子就有些奇怪了：

```js
var a = { b: 42 };
var b = { b: 43 };

a < b;  // false
a == b; // false
a > b;  // false

a <= b; // true
a >= b; // true
```

对象相等的情况就是引用相同，所以 a!=b,但是如果a < b和a == b结果为false，**为什么a <= b和a >= b的结果会是true呢？**

> 因为根据规范a <= b被处理为b < a，然后将结果反转。因为b <a的结果是false，所以a <= b的结果是true。
>
> 这可能与我们设想的大相径庭，即<=应该是“小于或者等于”。实际上JavaScript中<=是“不大于”的意思（即！(a > b)，处理为！(b < a)）。同理，a >= b处理为b <= a。

**为避免出啥幺蛾子，最好保证比较双方类型相同**



### 语句和表达式

> 语句相当于句子，表达式相当于短语，运算符则相当于标点符号和连接词。

```js
  var a = 3 * 6;
  var b = a;
  b;
```

这里，3 ＊ 6是一个表达式（结果为18）。第二行的a也是一个表达式，第三行的b也是。表达式a和b的结果值都是18。

**var a = 3 ＊ 6和var b = a称为“声明语句”**（declaration statement），因为它们声明了变量（还可以为其赋值）。a = 3 ＊ 6和b = a**（不带var）叫作“赋值表达式”**。



#### 语句的结果值

> 如果你用开发控制台（或者JavaScript REPL——read/evaluate/print/loop工具）调试过代码，应该会看到很多语句的返回值显示为undefined，只是你可能从未探究过其中的原因。其实控制台中显示的就是语句的结果值。但是在代码中没有办法获取这个结果值

例如：

```js
var b;

if (true) {
    b = 4 + 38;
}
```

在控制台/REPL中输入以上代码应该会显示42，即最后一个语句/表达式b = 4 + 38的结果值。

但下面这样的代码无法运行：

```js
var a, b;

a = if (true) {
    b = 4 + 38;
};
```

> 因为语法不允许我们获得语句的结果值并将其赋值给另一个变量（至少目前不行）。

**那应该怎样获得语句的结果值呢？**

可以使用万恶的eval(..)（又读作“evil”）来获得结果值：

```js
var a, b;

a = eval( "if (true) { b = 4 + 38; }" );

a;  // 42
```

#### 表达式的副作用

> 大部分表达式没有副作用。
>
> 最常见的有副作用（也可能没有）的表达式是函数调用：

```js
function foo() {
    a = a + 1;
}

var a = 1;
foo();      // 结果值：undefined。副作用：a的值被改变
```

其他一些表达式也有副作用，比如：

```js
var a = 42;
var b = a++;

a;  // 43
b;  // 42
```

语句系列逗号运算符（statement-series comma operator）将多个独立的表达式语句串联成一个语句：

```js
var a = 42, b;
b = ( a++, a );

a;  // 43
b;  // 43
```

a++, a中第二个表达式a在a++之后执行，结果为43，并被赋值给b。

再如delete运算符。delete用来删除对象中的属性和数组中的单元。它通常以单独一个语句的形式出现：

```js
var obj = {
    a: 42
};

obj.a;          // 42
delete obj.a;   // true
obj.a;          // undefined
```

如果操作成功，delete返回true，否则返回false。其副作用是属性被从对象中删除（或者单元从array中删除）。

另一个有趣的例子是=赋值运算符。

```js
var a;

a = 42;     // 42
a;          // 42
```

> 组合赋值运算符，如+=和-=等也是如此。例如，a = b += 2首先执行b +=2（即b = b + 2），然后结果再被赋值给a。



#### 上下文规则

> 在JavaScript语法规则中，有时候同样的语法在不同的情况下会有不同的解释。这些语法规则孤立起来会很难理解。

##### 大括号

> 下面两种情况会用到大括号{ .. }（随着JavaScript的演进会出现更多类似的情况）。

1.  对象常量

	```js
	// 假定函数bar()已经定义
	
	var a = {
	    foo: bar()
	};
	```

	

2.  标签

	如果将上例中的var a =去掉会发生什么情况呢？

	```js
	// 假设 bar已定义
	{
	  foo: bar()
	}
	```

	很多开发人员以为这里的{ .. }只是一个孤立的对象常量，没有赋值。事实上不是这样。

	这里涉及JavaScript中一个不太为人知（也不建议使用）的特性，叫作“**标签语句**”（labeled statement）。**foo是语句bar()的标签**（后面没有；）。标签语句具体是做什么用的呢？

	```js
	  // 标签为foo的循环
	  foo: for (var i=0; i<4; i++) {
	      for (var j=0; j<4; j++) {
	        // 如果j和i相等，继续外层循环
	        if (j == i) {
	            // 跳转到foo的下一个循环
	            continue foo;
	        }
	
	        // 跳过奇数结果
	        if ((j ＊ i) % 2 == 1) {
	            // 继续内层循环（没有标签的）
	            continue;
	        }
	
	        console.log( i, j );
	      }
	  }
	  // 1 0
	  // 2 0
	  // 2 1
	  // 3 0
	  // 3 2
	```

	break foo不是指“跳转到标签foo所在位置继续执行”，而是“跳出标签foo所在的循环/代码块，继续执行后面的代码”

	> 标签也能用于非循环代码块，但只有break才可以。我们可以对带标签的代码块使用break______，但是不能对带标签的非循环代码块使用continue，也不能对不带标签的代码块使用break：

	```js
	// 标签为bar的代码块
	function foo() {
	      bar: {
	              console.log( "Hello" );
	              break bar;
	            console.log( "never runs" );
	      }
	      console.log( "World" );
	}
	
	foo();
	// Hello
	// World
	```

	> 带标签的循环/代码块十分少见，也不建议使用。

##### 代码块

```js
[] + {}; // "[object Object]"
{} + []; // 0
```

第一行代码中，{}出现在+运算符表达式中，因此它被当作一个值（空对象）来处理。[]会被强制类型转换为""，而{}会被强制类型转换为"[object Object]"。

但在第二行代码中，{}被当作一个独立的空代码块（不执行任何操作）。代码块结尾不需要分号，所以这里不存在语法上的问题。最后+ []将[]显式强制类型转换为0。



##### 对象解构

> 从ES6开始，{ .. }也可用于“解构赋值”

```js
  function getData() {
      // ..
      return {
        a: 42,
        b: "foo"
      };
  }

  var { a, b } = getData();

  console.log( a, b ); // 42 "foo"

{ a , b } = .．就是ES6中的解构赋值，相当于下面的代码：

  var res = getData();
  var a = res.a;
  var b = res.b;
```

{ a, b }实际上是{ a: a, b: b }的简化版本，两者均可，只不过{ a,b }更简洁。

##### else if和可选代码块

> 很多人误以为JavaScript中有else if，因为我们可以这样来写代码：

```js
if (a) {
    // ..
}
else if (b) {
    // ..
}
else {
    // ..
}
```

事实上JavaScript没有else if，但if和else只包含单条语句的时候可以省略代码块的{ }。下面的代码你一定不会陌生：

```js
if (a) doSomething( a );
```

很多JavaScript代码检查工具建议对单条语句也应该加上{ }，如：

```js
if (a) { doSomething( a ); }
```

else也是如此，所以我们经常用到的else if实际上是这样的：

```js
if (a) {
    // ..
}
else {
    if (b) {
      // ..
    }
    else {
      // ..
    }
}
```

> else if极为常见，能省掉一层代码缩进，所以很受青睐。但这只是我们自己发明的用法，切勿想当然地认为这些都属于JavaScript语法的范畴。



### 运算符优先级

> 超过一个运算符时表达式的执行顺序。 对此js没有具体介绍[参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

&&运算符的优先级高于=

&&运算符先于||执行

||的优先级又高于？:

##### 短路

> 对&&和||来说，如果从左边的操作数能够得出结果，就可以忽略右边的操作数。我们将这种现象称为“短路”（即执行最短路径）。

以a && b为例，如果a是一个假值，足以决定&&的结果，就没有必要再判断b的值。同样对于a || b，如果a是一个真值，也足以决定||的结果，也就没有必要再判断b的值。



##### 更强的绑定

多个运算符串联在一起的例子

```js
  a && b || c ? c || b ? a : c && b : a
```

其中？：运算符的优先级比&&和||高还是低呢？执行顺序是怎样？

```js
  (a && b || c) ? (c || b) ? a : (c && b) : a
```

> 因为&&运算符的优先级高于||，而||的优先级又高于？ :。



##### 关联

> &&和||运算符先于？：执行，那么如果多个相同优先级的运算符同时出现，又该如何处理呢？它们的执行顺序是从左到右还是从右到左？

一般说来，运算符的关联（associativity）不是从左到右就是从右到左，这取决于组合（grouping）是从左开始还是从右开始。

```js
  var a = foo() && bar();
```

这里遵循从左到右的顺序（JavaScript的默认执行顺序）。这里foo()首先执行，它的返回结果决定了bar()是否执行。所以如果bar()在foo()之前执行，整个结果会完全不同。

而a && b && c这样的表达式就涉及组合（隐式），这意味着a && b或b && c会先执行。

> 如果&&是右关联的话会被处理为a && (b && c)。但这并不意味着c会在b之前执行。右关联不是指从右往左执行，而是指从右往左组合。任何时候，不论是组合还是关联，严格的执行顺序都应该是从左到右，a, b，然后c。

比如？：（三目运算符）

```js
a ? b : c ? d : e;
```

?：是右关联，它的组合顺序是以下哪一种呢？

• a ? b : (c ? d : e)

• (a ? b : c) ? d : e

答案是a ? b : (c ? d : e)。

**更为复杂的赋值表达式的例子：**

```js
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

d;      // 42
```

掌握了优先级和关联等相关知识之后，就能够根据组合规则将上面的代码分解如下：

```js
((a && b) || c) ? ((c || b) ? a : (c && b)) : a
```



### 语法



#### 自动分号

> 有时JavaScript会自动为代码行补上缺失的分号，即自动分号插入（Automatic Semicolon Insertion, ASI）。
>
> 请注意，ASI只在换行符处起作用，而不会在代码行的中间插入分号。

```js
    var a = 42, b
    c;
```

JavaScript判断b之后应该有；，所以c；被处理为一个独立的表达式语句。

语法规定do..while循环后面必须带；,但是大多数开发人员会不记得，所以ASI会自动补上，其他涉及ASI的情况是break、continue、return和yield（ES6）等关键字

**纠错机制**

对于ASI自动添加 ; , js社区争议

- 正方认为ASI机制大有裨益，能省略掉那些不必要的；，让代码更简洁。

- 反方则认为ASI机制问题太多，对于缺乏经验的初学者尤其如此，自动插入会改变代码逻辑。应该通过linter这样的工具来找出这些错误，而不是依赖JavaScript引擎来改正错误。

> 建议在所有需要的地方加上分号，将对ASI的依赖降到最低。



#### 错误

> JavaScript不仅有各种类型的运行时错误（TypeError、ReferenceError、SyntaxError等），它的语法中也定义了一些编译时错误。
>
> 如非法正则，重名函数，重名属性...
>
> 从语义角度来说，这些错误并非词法错误，而是语法错误，因为它们在词法上是正确的。只不过由于没有GrammarError类型，一些浏览器选择用SyntaxError来代替。

##### 提前使用变量

> ES6规范定义了一个新概念，叫作TDZ（Temporal Dead Zone，暂时性死区）。

```js
  {
      a = 2;      // ReferenceError!
      let a;
  }
```

a = 2试图在let a初始化a之前使用该变量（其作用域在{ .. }内），这里就是a的TDZ，会产生错误。

有意思的是，对未声明变量使用typeof不会产生错误，但在TDZ中却会报错：

```js
  {
      typeof a;   // undefined
      typeof b;   // ReferenceError! (TDZ)
      let b;
  }
```



#### 函数参数

> 另一个TDZ违规的例子是ES6中的参数默认值

```js
  var b = 3;

  function foo( a = 42, b = a + b + 5 ) {
      // ..
  }
```

> b = a + b + 5在参数b（=右边的b，而不是函数外的那个）的TDZ中访问b，所以会出错。而访问a却没有问题，因为此时刚好跨出了参数a的TDZ。



#### try..finally

> try..catch对我们来说可能已经非常熟悉了。但你是否知道try可以和catch或者finally配对使用，并且必要时两者可同时出现？

在一种情况下需要finally:

```js
function foo() {
      try {
              return 42;
              // throw 42;
      }
      finally {
              console.log( "Hello" );
      }

      console.log( "never runs" );
}

console.log( foo() );
// Hello
// 42
```

当try 语句用有return 时，finally 会在return 前执行。 当try中 使用 throw 时也是如此

如果finally中抛出异常（无论是有意还是无意），函数就会在此处终止。如果此前try中已经有return设置了返回值，则该值会被丢弃：

```js
function foo() {
      try {
              return 42;
      }
      finally {
              throw "Oops! ";
      }

      console.log( "never runs" );
}

console.log( foo() );
// Uncaught Exception: Oops!
```

continue和break等控制语句也是如此：finally 中语句会在 关键字之前执行

```js
for (var i=0; i<10; i++) {
      try {
              continue;
      }
      finally {
              console.log( i );
      }
}
// 0 1 2 3 4 5 6 7 8 9
```

finally中的return会覆盖try和catch中return的返回值：

```js
function foo() {
      try {
              return 42;
      }
      finally {
              // 没有返回语句，所以没有覆盖
      }
}

function bar() {
      try {
              return 42;
      }
      finally {
              // 覆盖前面的return 42
              return;
      }
}

function baz() {
      try {
              return 42;
      }
      finally {
              // 覆盖前面的return 42
              return "Hello";
      }
}

foo();  // 42
bar();  // undefined
baz();  // Hello
```

> 通常来说，在函数中省略return的结果和return；及return undefined；是一样的，但是在finally中省略return则会返回前面的return设定的返回值。



#### switch

> 可以把它看作if..else if..else.．的简化版本：a与case的值执行严格相等（===）

```js
switch (a) {
      case 2:
              // 执行一些代码
              break;
      case 42:
              // 执行另外一些代码
              break;
      default:
              // 执行缺省代码
}
```

> 这里a与case表达式逐一进行比较。如果匹配就执行该case中的代码，直到break或者switch代码块结束。



## 异步现在和将来

### 异步和性能



#### 异步控制台

> 并没有什么规范或一组需求指定console.＊方法族如何工作——它们并不是JavaScript正式的一部分，而是由宿主环境添加到JavaScript中的。

```js
  var a = {
      index: 1
  };

  // 然后
  console.log( a ); // ? ?

  // 再然后
  a.index++;
```

正常情况下会输出 { index: 1}， 但如果遇到控制台I/O延迟情况，可能会输出 { index: 2}

#### 事件循环

> 现在我们来澄清一件事情（可能令人震惊）：尽管你显然能够编写异步JavaScript代码（就像前面我们看到的定时代码），但直到最近（ES6）, JavaScript才真正内建有直接的异步概念。
>
> 宿主环境（浏览器-js，服务器-nodejs）提供了一种机制来处理程序中多个块的执行，且执行每块时调用JavaScript引擎，这种机制被称为事件循环。
>
> 换句话说，JavaScript引擎本身并没有时间的概念，只是一个按需执行JavaScript任意代码片段的环境。“事件”（JavaScript代码执行）调度总是由包含它的环境进行。

通过伪代码了解这个概念：

```js
  // eventLoop是一个用作队列的数组
  //（先进，先出）
  var eventLoop = [ ];
  var event;

  // “永远”执行
  while (true) {
      // 一次tick
      if (eventLoop.length > 0) {
            // 拿到队列中的下一个事件
            event = eventLoop.shift();

            // 现在，执行下一个事件
            try {
                event();
            }
            catch (err) {
                reportError(err);
            }
        }
  }
```

> 有一个用while循环实现的持续运行的循环，循环的每一轮称为一个tick。对每个tick而言，如果在队列中有等待事件，那么就会从队列中摘下一个事件并执行。这些事件就是你的回调函数。
>
> 要搞清楚的是，setTimeout(..)并没有把你的回调函数挂在事件循环队列中。它所做的是设定一个定时器。当定时器到时后，环境会把你的回调函数放在事件循环中，这样，在未来某个时刻的tick会摘下并执行这个回调。
>
> 如果这时候事件循环中已经有20个项目了会怎样呢？你的回调就会等待。它得排在其他项目后面——通常没有抢占式的方式支持直接将其排到队首。这也就是为啥说setTimeout()定时器精度不高的原因了
>
> 现在ES6精确指定了事件循环的工作细节，这意味着在技术上将其纳入了JavaScript引擎的势力范围，而不是只由宿主环境来管理。这个改变的一个主要原因是ES6中Promise的引入，因为这项技术要求对事件循环队列的调度运行能够直接进行精细控制

#### 并行线程

> 术语“异步”和“并行”常常被混为一谈，但实际上它们的意义完全不同。记住，**异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情。**

```js
var a = 20;

function foo() {
    a = a + 1;
}

function bar() {
    a = a * 2;
}

// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar );
```

根据 foo,bar 的执行顺序不同可能得到不一样的结果a(2/40), 但如果 foo,bar 同时运行？两个操作共享a的内存地址，这是很复杂的操作，好在js是单线程不需要考虑这个问题

#### 完整运行

> 由于JavaScript的单线程特性，foo()（以及bar()）中的代码具有原子性。也就是说，一旦foo()开始运行，它的所有代码都会在bar()中的任意代码运行之前完成，或者相反。这称为完整运行（run-to-completion）特性。

foo,bar中的内容越长，其运行的不确定性就越多，但是其确定性肯定比多线程要高

#### 并发

> 现在让我们来设想一个展示状态更新列表（比如社交网络新闻种子）的网站，其随着用户向下滚动列表而逐渐加载更多内容。要正确地实现这一特性，需要（至少）两个独立的“**进程**”同时运行（也就是说，是在同一段时间内，并不需要在同一时刻）。
>
> 【“进程”】这是虚拟进程，并非真正意义上的操作系统进程。之所以使用“进程”而不是“任务”，是因为从概念上来讲，“进程”的定义更符合这里我们使用的意义。

- 第一个“进程”在用户向下滚动页面触发onscroll事件时响应这些事件（发起Ajax请求要求新的内容）。

- 第二个“进程”接收Ajax响应（把内容展示到页面）。

如果用户滚动够快的话，可能会有两个或者更多事件被触发。两个或多个“进程”同时执行就出现了并发

下面列出了事件循环队列中所有这些交替事件：

```text
  onscroll, 请求1       <--- 进程1启动
  onscroll, 请求2
  响应1                 <--- 进程2启动
  onscroll, 请求3
  响应2
  响应3
  onscroll, 请求4
  onscroll, 请求5
  onscroll, 请求6
  响应4
  onscroll, 请求7       <--- 进程1结束
  响应6
  响应5
  响应7                 <--- 进程2结束
```

单线程事件循环是并发的一种形式

##### 非交互

> 两个或多个“进程”在同一个程序内并发地交替运行它们的步骤/事件时，如果这些任务彼此不相关，就不一定需要交互。如果进程间没有相互影响的话，不确定性是完全可以接受的。

```js
var res = {};

function foo(results) {
    res.foo = results;
}

function bar(results) {
    res.bar = results;
}

// ajax(..)是某个库提供的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar );
```



##### 交互

> 更常见的情况是，并发的“进程”需要相互交流，通过作用域或DOM间接交互。正如前面介绍的，如果出现这样的交互，就需要对它们的交互进行协调以避免竞态的出现。

```js
  var res = [];

  function response(data) {
      if (data.url == "http://some.url.1") {
        res[0] = data;
      }
      else if (data.url == "http://some.url.2") {
        res[1] = data;
      }
  }

  // ajax(..)是某个库中提供的某个Ajax函数
  ajax( "http://some.url.1", response );
  ajax( "http://some.url.2", response );
```

##### 协作

> 还有一种并发合作方式，称为并发协作（cooperative concurrency）。这里的重点不再是通过共享作用域中的值进行交互（尽管显然这也是允许的！）。这里的目标是取到一个长期运行的“进程”，并将其分割成多个步骤或多批任务，使得其他并发“进程”有机会将自己的运算插入到事件循环队列中交替运行。

举例来说，考虑一个需要遍历很长的结果列表进行值转换的Ajax响应处理函数。我们会使用Array#map(..)让代码更简洁：

```js
var res = [];

// response(..)从Ajax调用中取得结果数组
function response(data) {
    // 添加到已有的res数组
    res = res.concat(
      // 创建一个新的变换数组把所有data值加倍
      data.map( function(val){
          return val * 2;
      } )
    );
}

// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response );
```

然而数据量多的话，比如1000万条

所以，要创建一个协作性更强更友好且不会霸占事件循环队列的并发系统，你可以异步地批处理这些结果 。每次处理之后返回事件循环，让其他等待事件有机会运行。

```js
var res = [];

// response(..)从Ajax调用中取得结果数组
function response(data) {
    // 一次处理1000个
    var chunk = data.splice( 0, 1000 );

    // 添加到已有的res组
    res = res.concat(
      // 创建一个新的数组把chunk中所有值加倍
      chunk.map( function(val){
          return val * 2;
      } )
    );

    // 还有剩下的需要处理吗？
    if (data.length > 0) {
      // 异步调度下一次批处理
      setTimeout( function(){
          response( data );
      }, 0 );
    }
}

// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response );
```

这里使用setTimeout(..0)（hack）进行异步调度，基本上它的意思就是“把这个函数插入到当前事件循环队列的结尾处”。

#### 任务

> 在ES6中，有一个新的概念建立在事件循环队列之上，叫作任务队列（job queue）。这个概念给大家带来的最大影响可能是Promise的异步特性\
>
> 遗憾的是，目前为止，这是一个没有公开API的机制，因此要展示清楚有些困难。所以我们目前只从概念上进行描述

设想一个调度任务（直接地，不要hack）的API，称其为schedule(..)。考虑：

```js
console.log( "A" );

setTimeout( function(){
    console.log( "B" );
}, 0 );

// 理论上的"任务API"
schedule( function(){
    console.log( "C" );

    schedule( function(){
      console.log( "D" );
    } );
} );
```

可能你认为这里会打印出A B C D，但实际打印的结果是A C D B。因为任务处理是在当前事件循环tick结尾处，而定时器触发是为了调度下一个事件循环tick（如果可用的话！）。

####  语句顺序

> 代码中语句的顺序和JavaScript引擎执行语句的顺序并不一定要一致。

编写的代码：

```js
var a, b;

a = 10;
b = 30;

a = a + 1;
b = b + 1;

console.log( a + b ); // 42
```

js引擎可能会发现，其实这样执行会更快：重排代码

```js
var a, b;

a = 11;
b = 31;

console.log( a + b ); // 42
```

或者甚至这样：

```js
// 因为a和b不会被再次使用
// 我们可以inline，从而完全不需要它们！
console.log( 42 ); // 42
```

但是这里有一种场景，其中特定的优化是不安全的，因此也是不允许的:

```js
var a, b;

a = 10;
b = 30;

// 我们需要a和b处于递增之前的状态！
console.log( a * b ); // 300

a = a + 1;
b = b + 1;

console.log( a + b ); // 42
```

还有其他一些例子，其中编译器重新排序会产生可见的副作用，比如会产生副作用的函数调用（特别是getter函数），或ES6代理对象

```js
function foo() {
    console.log( b );
    return 1;
}

var a, b, c;

// ES5.1 getter字面量语法
c = {
    get bar() {
      console.log( a );
      return 1;
    }
};

a = 10;
b = 30;
a += foo();              // 30
b += c.bar;              // 11

console.log( a + b );    // 42
```

如果不是代码片段中的语句 console.log(),js引擎本来可以把代码重新排序如下：

```js
// ...

a = 10 + foo();
b = 30 + c.bar;

// ...
```

尽管JavaScript语义让我们不会见到编译器语句重排序可能导致的噩梦，这是一种幸运，但是代码编写的方式（从上到下的模式）和编译后执行的方式之间的联系非常脆弱，理解这一点也非常重要。



### 回调

> 到目前为止，回调是编写和处理JavaScript程序异步逻辑的最常用方式。确实，回调是这门语言中最基础的异步模式。

#### continuation

```js
// A
ajax( "..", function(..){
    // C
} );
// B
```

// A和// B表示程序的前半部分（也就是现在的部分），而// C标识了程序的后半部分（也就是将来的部分）。前半部分立刻执行，然后是一段时间不确定的停顿。在未来的某个时刻，如果Ajax调用完成，程序就会从停下的位置继续执行后半部分。

换句话说，回调函数包裹或者说封装了程序的延续（continuation）。

简化代码：

```js
  // A
  setTimeout( function(){
      // C
  }, 1000 );
  // B
```



#### 嵌套回调与链式回调

```js
listen( "click", function handler(evt){
    setTimeout( function request(){
      ajax( "http://some.url.1", function response(text){
          if (text == "hello") {
              handler();
          }
          else if (text == "world") {
              request();
          }
      } );
    }, 500) ;
} );
```

你很可能非常熟悉这样的代码。这里我们得到了三个函数嵌套在一起构成的链，其中每个函数代表异步序列（任务，“进程”）中的一个步骤。

这种代码常常被称为**回调地狱**（callback hell）



```js
doA( function(){
    doB();
    doC( function(){
        doD();
    } )

    doE();
} );

doF();
```

你第一眼看到前面这段代码就分析出正确的顺序了吗？

#### 信任问题

假设你是一名开发人员，为某个销售昂贵电视的网站建立商务结账系统。你已经做好了结账系统的各个界面。在最后一页，当用户点击“确定”就可以购买电视时，你需要调用（假设由某个分析追踪公司提供的）第三方函数以便跟踪这个交易。

你注意到，可能是为了提高性能，他们提供了一个看似用于异步追踪的工具，这意味着你需要传入一个回调函数。在传入的这个continuation中，你需要提供向客户收费和展示感谢页面的最终代码。代码可能是这样：

```js
analytics.trackPurchase( purchaseData, function(){
  chargeCreditCard();
  displayThankyouPage();
} );
```

六个月过去了，没有任何问题。你几乎已经忘了自己写过这么一段代码。某个上班之前的早晨，你像往常一样在咖啡馆里享用一杯拿铁。突然，你的老板惊慌失措地打电话过来，让你放下咖啡赶紧到办公室。

原因是一位高级客户购买了一台电视，信用卡被刷了5次。老板很生气并询问你为何出现这种事情，这种情况你没有测试过么？

你甚至都不记得自己写过这段代码。但是，你得深入研究这些代码，并开始寻找问题产生的原因。

通过分析日志，你得出一个结论：唯一的解释就是那个分析工具出于某种原因把你的回调调用了五次而不是一次。他们的文档中完全没有提到这种情况。

然后你联系它们的客服，客服表示吃惊并马上联系开发者，明天给你回复。第二天你收到一封很长的信，是他们解释的原因，于是你转发给老板

然后呢？你和老板讨论此事，他对这种状况却不怎么满意。他坚持认为，你不能再信任他们了（你们受到了伤害）。对此你也只能无奈接受，并且你需要找到某种方法来保护结账代码，保证不再出问题。

于是乎面临这样的代码：

```js
var tracked = false;

analytics.trackPurchase( purchaseData, function(){
    if (! tracked) {
      tracked = true;
      chargeCreditCard();
      displayThankyouPage();
    }
} );
```

再后来，又有人质疑说

• 调用回调过早（在追踪之前）；

• 调用回调过晚（或没有调用）；

• 调用回调的次数太少或太多（就像你遇到过的问题！）；

• 没有把所需的环境/参数成功传给你的回调函数；

• 吞掉可能出现的错误或异常；

• ……

你可能已经开始慢慢意识到，对于被传给你无法信任的工具的每个回调，你都将不得不创建大量的混乱逻辑。

现在你应该更加明白回调地狱是多像地狱了吧。



### promise

> 回忆一下，我们用回调函数来封装程序中的continuation，然后把回调交给第三方（甚至可能是外部代码），接着期待其能够调用回调，实现正确的功能。通过这种形式，我们要表达的意思是：“这是将来要做的事情，要在当前的步骤完成之后发生。”
>
> 但是，如果我们能够把控制反转再反转回来，会怎样呢？如果我们不把自己程序的continuation传给第三方，而是希望第三方给我们提供了解其任务何时结束的能力，然后由我们自己的代码来决定下一步做什么，那将会怎样呢？
>
> 这种范式就称为Promise

##### 什么是Promise

> 

```js
function add(xPromise, yPromise) {
    // Promise.all([ .. ])接受一个promise数组并返回一个新的promise，
    // 这个新promise等待数组中的所有promise完成
    return Promise.all( [xPromise, yPromise] )

    // 这个promise决议之后，我们取得收到的X和Y值并加在一起
    .then( function(values){
      // values是来自于之前决议的promise的消息数组
      return values[0] + values[1];
    } );
}

// fetchX()和fetchY()返回相应值的promise，可能已经就绪，
// 也可能以后就绪
add( fetchX(), fetchY() )

// 我们得到一个这两个数组的和的promise
// 现在链式调用then(..)来等待返回promise的决议
.then( function(sum){
    console.log( sum ); // 这更简单！
} );
```

> 在add(..)内部，Promise.all([ .. ])调用创建了一个promise（这个promise等待promiseX和promiseY的决议）。链式调用．then(..)创建了另外一个promise。这个promise由return values[0] + values[1]这一行立即决议（得到加运算的结果）。
>
> 因此，链add(..)调用终止处的调用then(..)——在代码结尾处——实际上操作的是返回的第二个promise，而不是由Promise.all([ .. ])创建的第一个promise。还有，尽管第二个then(..)后面没有链接任何东西，但它实际上也创建了一个新的promise，如果想要观察或者使用它的话就可以看到。
>
> 

new Promise( function(..){ .. } )模式通常称为[revealing constructor](http://domenic.me/2014/02/13/the-revealing-constructor-pattern/) **传入的函数会立即执行**（不会像then(..)中的回调一样异步延迟），它有两个参数，在本例中我们将其分别称为resolve和reject。这些是promise的决议函数。resolve(..)通常标识完成，而reject(..)则标识拒绝。

检查一个对象是否是Promise对象？

```js
p instanceof Promise
```

这种方法有缺陷，浏览器不同窗口的 Promise 实例不同，比如当前窗口Promise与，当前窗口iframe的不同

```js
if (
    p ! == null &&
    (
      typeof p === "object" ||
      typeof p === "function"
    ) &&
    typeof p.then === "function"
) {
    // 假定这是一个thenable!
}
else {
    // 不是thenable
}
```

> 识别Promise（或者行为类似于Promise的东西）就是定义某种称为thenable的东西，将其定义为任何具有then(..)方法的对象和函数。我们认为，任何这样的值就是Promise一致的thenable。

先回顾一下只用回调编码的信任问题。把一个回调传入工具foo(..)时可能出现如下问题：

Promise的特性就是专门用来为这些问题提供一个有效的可复用的答案。

• **调用回调过早**；

> 根据定义，Promise就不必担心这种问题，因为即使是立即完成的Promise（类似于new Promise(function(resolve){ resolve(42);})）也无法被同步观察到。
>
> 也就是说，对一个Promise调用then(..)的时候，即使这个Promise已经决议，提供给then(..)的回调也总会被异步调用

• **调用回调过晚（或不被调用）**；

> 和前面一点类似，Promise创建对象调用resolve(..)或reject(..)时，这个Promise的then(..)注册的观察回调就会被自动调度。可以确信，这些被调度的回调在下一个异步事件点上一定会被触发.
>
> 这些回调中的任意一个都无法影响或延误对其他回调的调用。

```js
p.then( function(){
    p.then( function(){
      console.log( "C" );
    } );
    console.log( "A" );
} );
p.then( function(){
    console.log( "B" );
} );
// A B C

```

这里，"C"无法打断或抢占"B"，这是因为Promise的运作方式。

如果两个promise p1和p2都已经决议，那么p1.then(..);p2.then(..)应该最终会先调用p1的回调，然后是p2的那些。但还有一些微妙的场景可能不是这样的，比如以下代码：

```js
var p3 = new Promise( function(resolve, reject){
    resolve( "B" );
} );

var p1 = new Promise( function(resolve, reject){
    resolve( p3 );
} );

var p2 = new Promise( function(resolve, reject){
    resolve( "A" );
} );

p1.then( function(v){
    console.log( v );
} );
p2.then( function(v){
    console.log( v );
} );

// A B    <-- 而不是像你可能认为的B A
```

防止不被调用的超时模式：

```js
// 用于超时一个Promise的工具
function timeoutPromise(delay) {
    return new Promise( function(resolve, reject){
      setTimeout( function(){
          reject( "Timeout! " );
      }, delay );
    } );
}

// 设置foo()超时
Promise.race( [
    foo(),                      // 试着开始foo()
    timeoutPromise( 3000 )      // 给它3秒钟
] )
.then(
    function(){
      // foo(..)及时完成！
    },
                  function(err){
          // 或者foo()被拒绝，或者只是没能按时完成
          // 查看err来了解是哪种情况
      }
  );
```

Promise.race,返回多个Promise中最快的那一个



• **调用回调次数过少或过多**；

> 根据定义，回调被调用的正确次数应该是1。“过少”的情况就是调用0次，和前面解释过的“未被”调用是同一种情况。
>
> 如果出于某种原因，Promise创建代码试图调用resolve(..)或reject(..)多次，或者试图两者都调用，那么这个Promise将只会接受第一次决议，并默默地忽略任何后续调用。



• **未能传递所需的环境和参数**；

> Promise至多只能有一个决议值（完成或拒绝）。
>
> 如果你没有用任何值显式决议，那么这个值就是undefined，这是JavaScript常见的处理方式。但不管这个值是什么，无论当前或未来，它都会被传给所有注册的（且适当的完成或拒绝）回调。
>
> 如果使用多个参数调用resovle(..)或者reject(..)，第一个参数之后的所有参数都会被默默忽略。
>
> 如果要传递多个值，你就必须要把它们封装在单个值中传递，比如通过一个数组或对象。

• **吞掉可能出现的错误和异常**。

> 基本上，这部分是上个要点的再次说明。如果拒绝一个Promise并给出一个理由（也就是一个出错消息），这个值就会被传给拒绝回调。
>
> 如果在Promise的创建过程中或在查看其决议结果过程中的任何时间点上出现了一个JavaScript异常错误，比如一个TypeError或ReferenceError，那这个异常就会被捕捉，并且会使这个Promise被拒绝。

```js
var p = new Promise( function(resolve, reject){
    foo.bar();  // foo未定义，所以会出错！
    resolve( 42 );  // 永远不会到达这里 :(
} );

p.then(
    function fulfilled(){
      // 永远不会到达这里 :(
    },
    function rejected(err){
      // err将会是一个来自foo.bar()这一行的TypeError异常对象
    }
);
```

foo.bar()中发生的JavaScript异常导致了Promise拒绝，你可以捕捉并对其作出响应。

##### Promise是可信任的么

> Promise并没有完全摆脱回调。它们只是改变了传递回调的位置。
>
> 为什么这就比单纯使用回调更值得信任呢？如何能够确定返回的这个东西实际上就是一个可信任的Promise呢？

Promise对这个问题已经有一个解决方案。包含在原生ES6 Promise实现中的解决方案就是Promise.resolve(..)。

如果向Promise.resolve(..)传递一个非Promise、非thenable的立即值，就会得到一个用这个值填充的promise。下面这种情况下，promise p1和promise p2的行为是完全一样的：

```js
var p1 = new Promise( function(resolve, reject){
    resolve( 42 );
} );

var p2 = Promise.resolve( 42 );
```

更重要的是，如果向Promise.resolve(..)传递了一个非Promise的thenable值，前者就会试图展开这个值，而且展开过程会持续到提取出一个具体的非类Promise的最终值

```js
var p = {
    then: function(cb) {
      cb( 42 );
    }
};

// 这可以工作，但只是因为幸运而已
p
.then(
    function fulfilled(val){
      console.log( val ); // 42
    },
    function rejected(err){
      // 永远不会到达这里
    }
);
```

Promise.resolve(..)提供了可信任的Promise封装工具，可以链接使用：

```js
// 不要只是这么做：
foo( 42 )
.then( function(v){
    console.log( v );
} );

// 而要这么做：
Promise.resolve( foo( 42 ) )
.then( function(v){
    console.log( v );
} );
```

##### 链式流

> 我们可以把多个Promise连接到一起以表示一系列异步步骤
>
> 这种方式可以实现的关键在于以下两个Promise固有行为特性：
>
> • 每次你对Promise调用then(..)，它都会创建并返回一个新的Promise，我们可以将其链接起来；
>
> • 不管从then(..)调用的完成回调（第一个参数）返回的值是什么，它都会被自动设置为被链接Promise（第一点中的）的完成。

```js
var p = Promise.resolve( 21 );

p
.then( function(v){
    console.log( v );    // 21

    // 用值42完成连接的promise
    return v * 2;
} )
// 这里是链接的promise
.then( function(v){
    console.log( v );    // 42
} );
```

在这些例子中，一步步传递的值是可选的。如果不显式返回一个值，就会隐式返回undefined，并且这些promise仍然会以同样的方式链接在一起。

**如果这个Promise链中的某个步骤出错了怎么办？**

这意味着可能在链的任意位置捕捉到这样的错误，而这个捕捉动作在某种程度上就相当于在这一位置将整条链“重置”回了正常运作：

```js
// 步骤1：
request( "http://some.url.1/" )

// 步骤2：
.then( function(response1){
    foo.bar(); // undefined，出错！

    // 永远不会到达这里
    return request( "http://some.url.2/? v=" + response1 );
} )

// 步骤3：
.then(
    function fulfilled(response2){
      // 永远不会到达这里
    },

    // 捕捉错误的拒绝处理函数
    function rejected(err){
      console.log( err );
      // 来自foo.bar()的错误TypeError
      return 42;
    }
)

// 步骤4：
.then( function(msg){
    console.log( msg );           // 42
} );
```

> 调用then(..)时的完成处理函数或拒绝处理函数如果抛出异常，都会导致（链中的）下一个promise因这个异常而立即被拒绝。
>
> 调用promise的then(..)，并且只传入一个完成处理函数，一个默认拒绝处理函数就会顶替上来。默认拒绝处理函数只是把错误重新抛出，这最终会使得链条下一步用同样的错误理由拒绝
>
> 实际上啥都不传也会有一个默认的处理成功的函数
>
> ```js
> function(val) {
>     return val
> }
> ```
>
> 

第2步出错后，第3步的拒绝处理函数会捕捉到这个错误。拒绝处理函数的返回值（这段代码中是42），如果有的话，会用来完成交给下一个步骤（第4步）的promise，这样，这个链现在就回到了完成状态。

##### 术语：决议、完成以及拒绝

> 对于术语决议（resolve）、完成（fulfill）和拒绝（reject），在更深入学习Promise之前，我们还有一些模糊之处需要澄清。

```js
var p = new Promise( function(X, Y){
    // X()用于完成
    // Y()用于拒绝
} );
```

第一个通常用于标识Promise已经完成，第二个总是用于标识Promise被拒绝.ES6规范将这两个回调命名为onFulfilled(..)和onRejected(..)，所以这两个术语很准确。

##### 错误处理

> 错误处理最自然的形式就是同步的try..catch结构。遗憾的是，它只能是同步的，无法用于异步代码模式

如果通过无效的方式使用Promise API，并且出现一个错误阻碍了正常的Promise构造，那么结果会得到一个立即抛出的异常，而不是一个被拒绝的Promise。

例子：new Promise(null)、Promise.all()、Promise.race(42)，等等

如果一开始你就没能有效使用Promise API真正构造出一个Promise，那就无法得到一个被拒绝的Promise！

为了避免丢失被忽略和抛弃的Promise错误，一些开发者表示，Promise链的一个最佳实践就是最后总以一个catch(..)结束:

```js
  var p = Promise.resolve( 42 );

  p.then(
      function fulfilled(msg){
        // 数字没有string函数，所以会抛出错误
        console.log( msg.toLowerCase() );
      }
  )
  .catch( handleErrors );
```

如果handleErrors(..)本身内部也有错误怎么办呢？

##### 成功的坑

> 接下来的内容只是理论上的
>
> • 默认情况下，Promsie在下一个任务或时间循环tick上（向开发者终端）报告所有拒绝，如果在这个时间点上该Promise上还没有注册错误处理函数。
>
> • 如果想要一个被拒绝的Promise在查看之前的某个时间段内保持被拒绝状态，可以调用defer()，这个函数优先级高于该Promise的自动错误报告。

如果一个Promise被拒绝的话，默认情况下会向开发者终端报告这个事实（而不是默认为沉默）。可以选择隐式（在拒绝之前注册一个错误处理函数）或者显式（通过defer()）禁止这种报告。在这两种情况下，都是由你来控制误报的情况。

```js
var p = Promise.reject( "Oops" ).defer();

// foo(..)是支持Promise的
foo( 42 )
.then(
    function fulfilled(){
      return p;
    },
    function rejected(err){
      // 处理foo(..)错误
    }
);
...
```

创建p的时候，我们知道需要等待一段时间才能使用或查看它的拒绝结果，所以我们就调用defer()，这样就不会有全局报告出现。为了便于链接，defer()只是返回这同一个promise。

从foo(..)返回的promise立刻就被关联了一个错误处理函数，所以它也隐式消除了出错全局报告。

##### Promise模式

> **Promise.all**([ .. ])需要一个参数，是一个数组，通常由Promise实例组成。从Promise. all([ .. ])调用返回的promise会收到一个完成消息（代码片段中的msg）。这是一个由所有传入promise的完成消息组成的数组，与指定的顺序一致（与完成顺序无关）。
>
> 数组中的值可以是Promise、thenable，甚至是立即值



> **Promise.race**([ .. ])就会完成；一旦有任何一个Promise决议为拒绝，它就会拒绝。

变体模式

• none([ .. ])这个模式类似于all([ .. ])，不过完成和拒绝的情况互换了。所有的Promise都要被拒绝，即拒绝转化为完成值，反之亦然。

• any([ .. ])这个模式与all([ .. ])类似，但是会忽略拒绝，所以只需要完成一个而不是全部。

• first([ .. ])这个模式类似于与any([ .. ])的竞争，即只要第一个Promise完成，它就会忽略后续的任何拒绝和完成。

• last([ .. ])这个模式类似于first([ .. ])，但却是只有最后一个完成胜出。

自己实现：

```js
// polyfill安全的guard检查
if (! Promise.first) {
    Promise.first = function(prs) {
      return new Promise( function(resolve, reject){
          // 在所有promise上循环
          prs.forEach( function(pr){
              // 把值规整化
              Promise.resolve( pr )
              // 不管哪个最先完成，就决议主promise
              .then( resolve );
          } );
      } );
    };
}
```

如果所有的promise都被拒绝，就在主promise上调用reject()。这个实现留给你当练习。

#####  then(..)和catch(..)

> catch捕获错误后，设置返回值。那么Promise链后面还能获取到catch的返回值，如果 onReject函数存在语法错误，会走catch语句

```js
Promise.reject('reject')
    .then(res=>{
         console.log('没错误：',res)   
    },err=>{
        console.log('出错！', err)
        return 'reject'
    })
    .catch(err=>{
        console.log('有错误：',err)
        return 'catch'
    })
    .then(res=>{
        console.log('下一轮：', res)
    })
// 出错！ reject
// 下一轮： reject
```

基于回调的工具

```js
// polyfill安全的guard检查
if (!Promise.wrap) {
    Promise.wrap = function(fn) {
        return function() {
            var args = [].slice.call( arguments );
            return new Promise( function(resolve, reject){
                  fn.apply(
                    null,
                    args.concat( function(err, v){
                        if (err) {
                            reject( err );
                        }
                        else {
                            resolve( v );
                        }
                    } )
                  );
              } );
          };
    };
}
```

使用

```js
var request = Promise.wrap( ajax );

request( "http://some.url.1/" )
.then( .. )
..
```

Promise.wrap(..)并不产出Promise。它产出的是一个将产生Promise的函数。在某种意义上，产生Promise的函数可以看作是一个Promise工厂。



### 生成器

> 一个函数一旦开始执行，就会运行到结束，期间不会有其他代码能够打断它并插入其间。
>
> 生成器就是一类特殊的函数，可以一次或多次启动和停止，并不一定非得要完成。

##### 迭代消息传递

> 除了能够接受参数并提供返回值之外，生成器甚至提供了更强大更引人注目的内建消息输入输出能力，通过yield和next(..)实现。

```js
  function *foo(x) {
      var y = x * (yield);
      return y;
  }

  var it = foo( 6 );

  // 启动foo(..)
  it.next();

  var res = it.next( 7 );

  res.value;     // 42
```

> 根据你的视角不同，yield和next(..)调用有一个不匹配。一般来说，需要的next(..)调用要比yield语句多一个，前面的代码片段有一个yield和两个next(..)调用。
>
> 因为第一个next(..)总是启动一个生成器，并运行到第一个yield处。不过，是第二个next(..)调用完成第一个被暂停的yield表达式，第三个next(..)调用完成第二个yield，以此类推。

**注意：在生成器的起始处我们调用第一个next()时，还没有暂停的yield来接受这样一个值。规范和所有兼容浏览器都会默默丢弃传递给第一个next()的任何东西。因此，启动生成器时一定要用不带参数的next()。**

如果你的生成器中没有return的话——在生成器中和在普通函数中一样，return当然不是必需的——总有一个假定的/隐式的return;（也就是return undefined;），**它会在默认情况下回答最后的it.next(7)调用提出的问题**。

##### 迭代器

> 它的接口中有一个next()方法。而与其紧密相关的一个术语是iterable（可迭代），即指一个包含可以在其值上迭代的迭代器的对象。
>
> 其名称是专门的ES6符号值Symbol.iterator。调用这个函数时，它会返回一个迭代器。通常每次调用会返回一个全新的迭代器，虽然这一点并不是必须的。

生成器实现标准的迭代器接口：

```js
var something = (function(){
    var nextVal;

    return {
      // for..of循环需要
      [Symbol.iterator]: function(){ return this; },

      // 标准迭代器接口方法
      next: function(){
          if (nextVal === undefined) {
              nextVal = 1;
          }
          else {
              nextVal = (3 * nextVal) + 6;
          }

          return { done:false, value:nextVal };
      }
    };
})();

something.next().value;     // 1
something.next().value;     // 9
something.next().value;     // 33
something.next().value;     // 105
```

内置迭代器：

```js
var a = [1,3,5,7,9];

var it = a[Symbol.iterator]();

it.next().value;    // 1
it.next().value;    // 3
it.next().value;    // 5
..
```

前面的代码中列出了定义的something，你可能已经注意到了这一行

```js
// for..of循环需要
[Symbol.iterator]: function(){ return this; }
```

这段有点令人疑惑的代码是在将something的值（迭代器something的接口）也构建成为一个iterable。现在它既是iterable，也是迭代器。然后我们把something传给for..of循环：

```js
for (var v of something) {
    ..
}
```



##### 生成器迭代器

> 可以把生成器看作一个值的生产者，我们通过迭代器接口的next()调用一次提取出一个值。
>
> 严格说来，生成器本身并不是iterable，尽管非常类似——当你执行一个生成器，就得到了一个迭代器：

```js
function *foo(){ .. }

var it = foo();
```

可以通过生成器实现前面的这个something无限数字序列生产者，类似这样：

```js
function *something() {
    var nextVal;

    while (true) {
      if (nextVal === undefined) {
          nextVal = 1;
      }
      else {
          nextVal = (3 * nextVal) + 6;
      }

      yield nextVal;
    }
}
```

• 为什么不能用for (var v of something) .. ？因为这里的something是生成器，并不是iterable。我们需要调用something()来构造一个生产者供for..of循环迭代。

• something()调用产生一个迭代器，但for..of循环需要的是一个iterable，对吧？是的。生成器的迭代器也有一个Symbol.iterator函数，基本上这个函数做的就是return this，和我们前面定义的iterable something一样。换句话说，生成器的迭代器也是一个iterable！

##### 停止生成器

> something()生成器的迭代器实例在循环中的break调用之后就永远留在了挂起状态。
>
> 其实有一个隐藏的特性会帮助你管理此事。for..of循环的“异常结束”（也就是“提前终止”），通常由break、return或者未捕获异常引起，会向生成器的迭代器发送一个信号使其终止。



如果在生成器内有try..finally语句，它将总是运行，即使生成器已经外部结束。如果需要清理资源的话（数据库连接等），这一点非常有用：

```js
function *something() {
    try {
      var nextVal;

      while (true) {
          if (nextVal === undefined) {
              nextVal = 1;
          }
          else {
              nextVal = (3 ＊ nextVal) + 6;
          }

          yield nextVal;
      }
    }
    // 清理子句
    finally {
      console.log( "cleaning up! " );
    }
}
```

之前的例子中，for..of循环内的break会触发finally语句。但是，也可以在外部通过return(..)手工终止生成器的迭代器实例

```js
var it = something();
for (var v of it) {
    console.log( v );

    // 不要死循环！
    if (v > 500) {
      console.log(
          // 完成生成器的迭代器
          it.return( "Hello World" ).value
      );
      // 这里不需要break
    }
}
// 1 9 33105 321969
// cleaning up!
// Hello World
```

#####  异步迭代生成器

如果想要通过生成器来表达同样的任务流程控制，可以这样实现：

```js
function foo(x, y) {
    ajax(
      "http://some.url.1/? x=" + x + "&y=" + y,
      function(err, data){
          if (err) {
              // 向*main()抛出一个错误
              it.throw( err );
          }
          else {
              // 用收到的data恢复＊main()
              it.next( data );
          }
      }
    );
}

function *main() {
    try {
      var text = yield foo( 11, 31 );
      console.log( text );
    }
    catch (err) {
        console.error( err );
    }
  }

  var it = main();

  // 这里启动！
  it.next();
```

这是如何工作的呢？调用foo(..)是异步完成的，难道try..catch不是无法捕获异步错误，就像我们在第3章中看到的一样吗？

我们已经看到yield是如何让赋值语句暂停来等待foo(..)完成，使得响应完成后可以被赋给text。精彩的部分在于yield暂停也使得生成器能够捕获错误。通过这段前面列出的代码把错误抛出到生成器中：

```js
if (err) {
      // 向*main()抛出一个错误
      it.throw( err );
}
```

生成器yield暂停的特性意味着我们不仅能够从异步函数调用得到看似同步的返回值，还可以同步捕获来自这些异步函数调用的错误！

#####  生成器+Promise

> ES6中最完美的世界就是生成器（看似同步的异步代码）和Promise（可信任可组合）的结合

```js
function foo(x, y) {
    return request("http://some.url.1/? x=" + x + "&y=" + y);
}

function *main() {
    try {
      var text = yield foo( 11, 31 );
      console.log( text );
    }
    catch (err) {
        console.error( err );
    }
}

  var it = main();


var p = it.next().value;

// 等待promise p决议
p.then(
    function(text){
      it.next( text );
    },

    function(err){
      it.throw( err );
    }
);
```

使用专门设计用来以我们前面展示的方式运行Promise-yielding生成器的工具。

```js
// 在此感谢Benjamin Gruenbaum（@benjamingr on GitHub）的巨大改进！
function run(gen) {
    // 去除第一个参数
    var args = [].slice.call( arguments, 1), it;

    // 在当前上下文中初始化生成器
    it = gen.apply( this, args );

    // 返回一个promise用于生成器完成
    return Promise.resolve()
        .then( function handleNext(value){
            // 对下一个yield出的值运行
            var next = it.next( value );

            return (function handleResult(next){
                // 生成器运行完毕了吗？
                if (next.done) {
                    return next.value;
                }
                // 否则继续运行
                else {
                    return Promise.resolve( next.value )
                    .then(
                        // 成功就恢复异步循环，把决议的值发回生成器
                        handleNext,

                        // 如果value是被拒绝的promise，
                        // 就把错误传回生成器进行出错处理
                        function handleErr(err) {
                            return Promise.resolve(
                            it.throw( err )
                            .then( handleResult );
                        }
                    );
                }
            })(next);
        } );
}
```

在上边例子使用：

```js
function *main() {
    // ..
}

run( main );
```



##### ES7:async与await?



```js
function foo(x, y) {
    return request(
      "http://some.url.1/? x=" + x + "&y=" + y
    );
}

async function main() {
    try {
      var text = await foo( 11, 31 );
      console.log( text );
    }
                catch (err) {
        console.error( err );
    }
}

main();
```



###  程序性能



#### Web Worker

> 如果你有一些处理密集型的任务要执行，但不希望它们都在主线程运行（这可能会减慢浏览器/UI），可能你就会希望JavaScript能够以多线程的方式运行。
>
> 浏览器这样的环境，很容易提供多个JavaScript引擎实例，各自运行在自己的线程上，这样你可以在每个线程上运行不同的程序。程序中每一个这样的独立的多线程部分被称为一个（Web）Worker。

```js
  var w1 = new Worker( "http://some.url.1/mycoolworker.js" );
```

这个**URL应该指向一个JavaScript文件的位置**，还可以通过提供一个**Blob URL**（另外一个HTML5特性）创建一个在线Worker（Inline Worker)，本质上就是一个存储在单个（二进制）值中的在线文件

Worker之间以及**它们和主程序之间，不会共享任何作用域或资源**，那会把所有多线程编程的噩梦带到前端领域，而是通过一个基本的**事件消息机制相互联系**。

Worker w1对象是一个事件侦听者和触发者，可以通过订阅它来获得这个Worker发出的事件以及发送事件给这个Worker。

```js
// 监听 worker 消息发送
w1.addEventListener( "message", function(evt){
    // evt.data
} );
// 主动向worker 发送消息
w1.postMessage( "something cool to say" );
```

Worker内部，收发消息是完全对称的：

```js
// "mycoolworker.js"

addEventListener( "message", function(evt){
// evt.data
} );

postMessage( "a really cool reply" );
```

Worker 也可以实例化子Worker，称为 subworker.有时候吧这个工作交给主worker创建其他worker处理部分很有用，不幸的是，到写作本书时为止，Chrome还不支持subworker，不过Firefox支持。

##### **终止Worker**

> 突然终止Worker线程不会给它任何机会完成它的工作或者清理任何资源。这就类似于通过关闭浏览器标签页来关闭页面。

```js
w1.terminate()
```

如果浏览器中有两个或多个页面（或同一页上的多个tab!）试图从同一个文件URL创建Worker，那么最终得到的实际上是完全独立的Worker。后面我们会简单介绍如何共享Worker。

##### **Worker环境**

> 在Worker内部是无法访问主程序的任何资源的。这意味着你不能访问它的任何全局变量，也不能访问页面的DOM或者其他资源。这是一个完全独立的线程
>
> 但是，你可以执行**网络操作（Ajax、WebSockets）以及设定定时器**。还有，Worker可以访问几个重要的全局变量和功能的本地复本，包括**navigator、location、JSON和applicationCache**。

可以通过importScripts(..)向Worker加载额外的JavaScript脚本：

```js
// 在Worker内部
importScripts( "foo.js", "bar.js" );
```

这些脚本加载是同步的。也就是说，importScripts(..)调用会阻塞余下Worker的执行，直到文件加载和执行完成。

Web Worker通常应用于哪些方面呢？

• 处理密集型数学计算

• 大数据集排序

• 数据处理（压缩、音频分析、图像处理等）

• 高流量网络通信

##### 数据传递

> 你可能已经注意到这些应用中的大多数有一个共性，就是需要在线程之间通过事件机制传递大量的信息，可能是双向的。

早期 Worker中，唯一的选择就是把所有数据序列化到一个字符串值中。除了双向序列化导致的速度损失之外，另一个主要的负面因素是数据需要被复制，这意味着两倍的内存使用（及其引起的垃圾收集方面的波动）。

**结构化克隆算法**

> 如果要传递一个对象，可以**使用结构化克隆算法把这个对象复制到另一边**。这个算法非常高级，甚至可以处理要复制的对象有循环引用的情况。



**Transferable对象**

> 对于大数据集而言，就是使用Transferable对象。这时发生的是**对象所有权的转移**，数据本身并没有移动。一旦你把对象传递到一个Worker中，在原来的位置上，它就变为空的或者是不可访问的，这样就消除了多线程编程作用域共享带来的混乱。当然，所有权传递是可以双向进行的。

如果选择Transferable对象的话，其实不需要做什么。任何实现了Transferable接口（http://developer.mozilla.org/en-US/docs/Web/API/Transferable）的数据结构就自动按照这种方式传输（Firefox和Chrome都支持）。

下面是如何使用postMessage(..)发送一个Transferable对象：

```js
// 比如foo是一个Uint8Array

postMessage( foo.buffer, [ foo.buffer ] );
```

不支持Transferable对象的浏览器就降级到结构化克隆，这会带来性能下降而不是彻底的功能失效。

##### 共享Worker

> 如果你的站点或app允许加载同一个页面的多个tab（一个常见的功能），那你可能非常希望通过防止重复专用Worker来降低系统的资源使用。在这一方面最常见的有限资源就是socket网络连接，因为浏览器限制了到同一个主机的同时连接数目。当然，限制来自于同一客户端的连接数也减轻了你的资源压力。

这称为SharedWorker，可通过下面的方式创建（只有Firefox和Chrome支持这一功能）：

```js
var w1 = new SharedWorker( "http://some.url.1/mycoolworker.js" );
```

Worker需要通过某种方式来得知消息来自于哪个程序。这个唯一标识符称为端口（port），可以类比网络socket的端口。因此，调用程序必须使用Worker的port对象用于通信：

```js
  w1.port.addEventListener( "message", handleMessages );

  // ..

  w1.port.postMessage( "something cool" );
```

端口连接必须初始化

```js
w1.port.start();
```

在共享Worker内部，必须要处理额外的一个事件："connect"。这个事件为这个特定的连接提供了端口对象。保持多个连接独立的最简单办法就是使用port上的闭包就像下面的代码一样，把这个链接上的事件侦听和传递定义在"connect"事件的处理函数内部：

```js
// 在共享Worker内部
addEventListener( "connect", function(evt){
  // 这个连接分配的端口
  var port = evt.ports[0];

  port.addEventListener( "message", function(evt){
    // ..

    port.postMessage( .. );

    // ..
  } );

  // 初始化端口连接
  port.start();
} );
```

#####  模拟Web Worker

> 为了在缺乏对此支持的老浏览器中运行代码
>
> 因为Worker是一种API而不是语法，所以我们可以作为扩展来模拟它。如果浏览器不支持Worker，那么从性能的角度来说是没法模拟多线程的。通常认为Iframe提供了并行环境，但是在所有的现代浏览器中，它们实际上都是和主页面运行在同一个线程中的，所以并不足以模拟并发。

[模拟worker](https://gist.github.com/getify/1b26accb1a09aa53ad25)



##### SIMD

> 单指令多数据（SIMD）是一种数据并行（data parallelism）方式，与Web Worker的任务并行（task parallelism）相对，因为这里的重点实际上不再是把程序逻辑分成并行的块，而是并行处理数据的多个位。
>
> 通过SIMD，线程不再提供并行。取而代之的是，现代CPU通过数字“向量”（特定类型的数组），以及可以在所有这些数字上并行操作的指令，来提供SIMD功能。这是利用低级指令级并行的底层运算。

##### asm.js

> asm.js（http://asmjs.org）这个标签是指JavaScript语言中可以高度优化的一个子集。通过小心避免某些难以优化的机制和模式（垃圾收集、类型强制转换，等等）, asm.js风格的代码可以被JavaScript引擎识别并进行特别激进的底层优化。



#### 性能测试与调优



##### 性能测试

> 如果被问到如何测试某个运算的速度（执行时间），绝大多数JavaScript开发者都会从类似下面的代码开始：

```js
var start = (new Date()).getTime(); // 或者Date.now()

// 进行一些操作

var end = (new Date()).getTime();

console.log( "Duration:", (end - start) );
```

这个方案有很多错误。不管报告的时长是多少，你能知道的唯一一点就是，这个运算的这次特定的运行消耗了大概这么长时间。而它是不是总是以这样的速度运行，你基本上一无所知。你不知道引擎或系统在这个时候有没有受到什么影响，以及其他时候这个运算会不会运行得更快。

##### 重复

> “好吧，”你现在会说，“那就用一个循环把它包起来，这样整个测试的运行时间就会更长一些了。”如果重复一个运算100次，然后整个循环报告共消耗了137ms，那你就可以把它除以100，得到每次运算的平均用时为1.37ms，是这样吗？

并不完全是这样。

简单的数学平均值绝对不足以对你要外推到整个应用范围的性能作出判断。迭代100次，即使只有几个（过高或过低的）的异常值也可以影响整个平均值，然后在重复应用这个结论的时候，你还会扩散这个误差，产生更大的欺骗性。

你也可以不以固定次数执行运算，转而循环运行测试，直到达到某个固定的时间。这可能会更可靠一些，但如何确定要执行多长时间呢？你可能会猜测，执行时间应该是你的运算执行的单次时长的若干倍。错。

实际上，重复执行的时间长度应该根据使用的定时器的精度而定，专门用来最小化不精确性。定时器的精度越低，你需要运行的时间就越长，这样才能确保错误率最小化。15ms的定时器对于精确的性能测试来说是非常差劲的。要最小化它的不确定性（也就是出错率）到小于1%，需要把你的每轮测试迭代运行750ms。而1ms定时器时只需要每轮运行50ms就可以达到同样的置信度。



##### Benchmark.js

> 幸运的是，像John-David Dalton和Mathias Bynens这样的聪明人了解这些概念，并编写了一个统计学上有效的性能测试工具，名为Benchmark.js（http://benchmarkjs.com/）。因此，对于这个悬而未决的问题，我的答案就是：“使用这个工具就好了。”

使用Benchmark.js来运行一个快速的性能测试：

```js
function foo() {
    // 要测试的运算
}

var bench = new Benchmark(
  "foo test",             // 测试名称
    foo,                    // 要测试的函数（也即内容）
                {
        // ..             // 可选的额外选项（参见文档）
    }
);

bench.hz;                // 每秒运算数
bench.stats.moe;         // 出错边界
bench.stats.variance;    // 样本方差
// ..
```

##### 环境为王

> 对特定的性能测试来说，不要忘了检查测试环境，特别是比较任务Ⅹ和Y这样的比对测试。仅仅因为你的测试显示Ⅹ比Y快，并不能说明结论Ⅹ比Y快就有实际的意义。

##### 引擎优化

> 你无法可靠地推断，如果在你的独立测试中Ⅹ比Y要快上10μs，就意味着Ⅹ总是比Y要快，就应该总是使用Ⅹ。性能并不是这样发挥效力的。它要比这复杂得多。

##### jsPerf.com

> 尽管在所有的JavaScript运行环境下，Benchmark.js都可用于测试代码的性能，但有一点一定要强调，如果你想要得到可靠的测试结论的话，就需要在很多不同的环境（桌面浏览器、移动设备，等等）中测试汇集测试结果。

有一个很棒的网站正是因这样的需求而诞生的，名为jsPerf（http://jsperf.com）。它使用我们前面介绍的Benchmark.js库来运行统计上精确可靠的测试，并把测试结果放在一个公开可得的URL上，你可以把这个URL转发给别人。

每次测试运行的时候，测试结果就会被收集并持久化，累积的测试结果会被图形化，并展示到一个页面上以供查看。



##### 尾调用优化

> 简单地说，尾调用就是一个出现在另一个函数“结尾”处的函数调用。这个调用结束后就没有其余事情要做了（除了可能要返回结果值）。

```js
function foo(x) {
    return x;
}

function bar(y) {
    return foo( y + 1 );     // 尾调用
}

function baz() {
    return 1 + bar( 40 );    // 非尾调用
}

baz();                       // 42
```

不详细谈那么多本质细节的话，调用一个新的函数需要额外的一块预留内存来管理调用栈，称为栈帧。所以前面的代码一般会同时需要为每个baz()、bar(..)和foo(..)保留一个栈帧。

然而，如果支持TCO的引擎能够意识到foo(y+1)调用位于尾部，这意味着bar(..)基本上已经完成了，那么在调用foo(..)时，它就不需要创建一个新的栈帧，而是可以重用已有的bar(..)的栈帧。这样不仅速度更快，也更节省内存。

在简单的代码片段中，这类优化算不了什么，但是在处理递归时，这就解决了大问题，特别是如果递归可能会导致成百上千个栈帧的时候。有了TCO，引擎可以用同一个栈帧执行所有这类调用！