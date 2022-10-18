---
title: isarray 兼容旧版本浏览器
date: 2022-07-13 14:43:10
tags: 插件
categories: js
---

> 判断一个对象是否为数组，兼容 哪些旧版本浏览器。
>
> 如果 不需要兼容旧版浏览器，建议直接使用 数组的 isArray 方法

安装

```
$ npm install isarray
```

示例

```js
var isArray = require('isarray');
 
console.log(isArray([])); // => true
console.log(isArray({})); // => false
```

简单的展示其原理

```js
const isArray = Array.isArray || list => ({}).toString.call(list) === '[object Array]'
```

