---
title: cookie的操作
date: 2022-06-29 16:23:10
tags: cookie
categories: js
---

> 直接上操作：

以前的方式：

```js
// 读取
document.cookie

// 设置
document.cookie = "a=3; max-age=-1";

```

[“Cookie Store API”](https://developer.mozilla.org/en-US/docs/Web/API/Cookie_Store_API)

<!--more-->

- [`CookieStore.delete()`](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore/delete)

该`delete()`方法删除具有给定名称或选项对象的 cookie，它返回一个[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)在删除完成时解析的 cookie。

- [`CookieStore.get()`](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore/get)

该`get()`方法获取具有给定名称或选项对象的单个 cookie，它返回一个[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)解析单个 cookie 详细信息的 cookie。

- [`CookieStore.getAll()`](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore/getAll)

该`getAll()`方法获取所有匹配的 cookie，它返回一个[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)用 cookie 列表解析的。

- [`CookieStore.set()`](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore/set)

该`set()`方法使用给定的名称和值或选项对象设置一个 cookie，它返回一个[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)在设置 cookie 时解析的。

事件：

[`change`](https://developer.mozilla.org/en-US/docs/Web/API/CookieStore/change_event)

`change`对任何 cookie 进行更改时都会触发该事件。

```js
const day = 24 * 60 * 60 * 1000;
cookieStore.set({
  name: "cookie1",
  value: "cookie1-value",
  expires: Date.now() + day,
  domain: "example.com"
})
.then(
  function() {
    console.log("It worked!");
  },
  function(reason) {
    console.error("It failed: ", reason);
  }
);

```

> 还可以使用第三方的 js 库 js-cookie

