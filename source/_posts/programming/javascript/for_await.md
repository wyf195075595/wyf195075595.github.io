---
title: 在循环中使用await
date: 2022-06-17 08:23:10
tags: js
categories: js
---

> 在循环中使用 await ,效果是顺序等待同步执行代码。有的循环中可能会不生效

1、for：循环中使用await的写法（生效）：

```javascript
for( let i=0; i<array.length; i++ ){
    let datas = await getDatas()
    break
}
```

<!--more-->

2、forEach：循环中使用await的写法（不生效）：

```javascript
array.forEach(async (item)=>{
    let datas = await getDatas()
})
```

3、for of：循环中使用await的写法（生效）：

```javascript
for( let item of array ){
    let datas = await getDatas()
    break
}
```

4、for await of：循环的使用方法（生效）：

```javascript
for await (let item of array){
    break
}
```

> **注意：**
>
> 1. for：要使用在async异步方法里，循环会等await执行而停留，await是有效的，有break；
> 2. forEach：没有break，循环不会等await执行而停留，await是无效的；
> 3. for of：要使用在async异步方法里，执行期间，await之前的代码会执行，到了await会等待await执行完才继续往下执行，有break；
> 4. for await of：也要用在async异步方法里，有break，但是它一般是使用在item是个异步方法的情况下，并不常见，场景如下面对应的例子，要遍历的数组元素是个异步请求，异步没回来之前整个for循环代码不执行：

### **总结：**

- for、for of、for await of是生效的，forEach的await是不生效的；
- for、for of是await这一行代码在等待，for await of是整个for在等待；

### **扩展：**

- 实际开发中我们可以发现其实for、while、for in、for of、for await of使用await都是生效的；
- 而几乎有回调的遍历方法：forEach、map、filter、reduce、some、every、find等，使用await都是不生效的；

