---
title: fetch API 的使用
date: 2022-07-11 16:55:25
tags: js
categories: js
---

> Fetch API 能够执行 XMLHttpRequest 对象的所有任务，
>
> Fetch API 则必须是异步。 

### 简单示例：

fetch()只有一个必需的参数 input。多数情况下，这个参数是要获取资源的 URL。这个方法返回 一个期约：

```js
let p = fetch('../resource/001.txt');
// 获取文本
p.then(res=>
    res.text()
).then(text=>{
    console.log('文本:',text);
})
```

### 响应状态

> 200 成功  | res.redirected 重定向为 ture 时 状态码 依然是200 不是 301
>
> 状态码在 非 200~299 时，检查对象 res.ok属性 true 则请求成功

### 语法

```
fetch(url, options)
```

<!--more-->

### 示例

- post提交字符串

```js

const response = await fetch(url, {
  method: 'POST',
  headers: {
    "Content-type": "application/x-www-form-urlencoded; charset=UTF-8",
  },
  body: 'foo=bar&lorem=ipsum',
});

const json = await response.json();
```

- 提交表单数据

```javascript
const form = document.querySelector('form');

const response = await fetch('/users', {
  method: 'POST',
  body: new FormData(form)
})
```

- **文件上传**

```js
const input = document.querySelector('input[type="file"]');

const data = new FormData();
data.append('file', input.files[0]);
data.append('user', 'foo');

fetch('/avatars', {
  method: 'POST',
  body: data
});
```

- **直接上传二进制数据**

```js
// 将 Blob 或 arrayBuffer 数据放在body属性里面

let blob = await new Promise(resolve =>   
  canvasElem.toBlob(resolve,  'image/png')
);

let response = await fetch('/article/fetch/post/image', {
  method:  'POST',
  body: blob
});
```



#### 参数配置

```js
options 完整配置
{
  method: "GET",
  headers: {
    "Content-Type": "text/plain;charset=UTF-8"
  },
  body: undefined,
  referrer: "about:client",
  referrerPolicy: "no-referrer-when-downgrade",
  mode: "cors", 
  credentials: "same-origin",
  cache: "default",
  redirect: "follow",
  integrity: "",
  keepalive: false,
  signal: undefined,
}
```

#### Headers 对象

Headers 对象提供了以下方法，用来操作标头。

> - `Headers.get()`：根据指定的键名，返回键值。
> - `Headers.has()`： 返回一个布尔值，表示是否包含某个标头。
> - `Headers.set()`：将指定的键名设置为新的键值，如果该键名不存在则会添加。
> - `Headers.append()`：添加标头。
> - `Headers.delete()`：删除标头。
> - `Headers.keys()`：返回一个遍历器，可以依次遍历所有键名。
> - `Headers.values()`：返回一个遍历器，可以依次遍历所有键值。
> - `Headers.entries()`：返回一个遍历器，可以依次遍历所有键值对（`[key, value]`）。
> - `Headers.forEach()`：依次遍历标头，每个标头都会执行一次参数函数。



#### Response 对象

`Response`对象根据服务器返回的不同类型的数据，提供了不同的读取方法。

> - `response.text()`：得到文本字符串。
> - `response.json()`：得到 JSON 对象。
> - `response.blob()`：得到二进制 Blob 对象。
> - `response.formData()`：得到 FormData 表单对象。
> - `response.arrayBuffer()`：得到二进制 ArrayBuffer 对象。

Stream 对象只能读取一次，读取完就没了。这意味着，上面五个读取方法，只能使用一个，否则会报错。

- ### Response.clone()

	创建`Response`对象的副本，实现多次读取

	```js
	const response1 = await fetch('flowers.jpg');
	const response2 = response1.clone();
	
	const myBlob1 = await response1.blob();
	const myBlob2 = await response2.blob();
	
	image1.src = URL.createObjectURL(myBlob1);
	image2.src = URL.createObjectURL(myBlob2);
	```

	

- ### Response.body 属性

	`Response.body`属性是 Response 对象暴露出的底层接口，返回一个 ReadableStream 对象，供用户操作

	```js
	// 分块读取内容，应用之一就是显示下载的进度
	
	const response = await fetch('flower.jpg');
	const reader = response.body.getReader();
	
	// 通过请求头获取文件总大小
	const totals = response.headers.get('Content-length');
	
	let hasDone = 0;
	while(true) {
	    const {done, value} = await reader.read();
	
	    if (done) {
	      break;
	    }
	    hasDone += value.length;
	    console.log(hasDone / totals)
	}
	```

	

#### 参数

- `method`：HTTP 请求的方法，`POST`、`DELETE`、`PUT`都在这个属性设置。

- `headers`：一个对象，用来定制 HTTP 请求的标头。

	

- `body`：POST 请求的数据体。

	> 请求体内容 必须是 Blob、BufferSource、FormData、URLSearchParams、ReadableStream 或 String 的 实例

##### **cache**

`cache`属性指定如何处理缓存。可能的取值如下：

> - `default`：默认值，先在缓存里面寻找匹配的请求。
> - `no-store`：直接请求远程服务器，并且不更新缓存。
> - `reload`：直接请求远程服务器，并且更新缓存。
> - `no-cache`：将服务器资源跟本地缓存进行比较，有新的版本才使用服务器资源，否则使用缓存。
> - `force-cache`：缓存优先，只有不存在缓存的情况下，才请求远程服务器。
> - `only-if-cached`：只检查缓存，如果缓存里面不存在，将返回504错误。

##### **mode**

`mode`属性指定请求的模式。可能的取值如下：

> - `cors`：默认值，允许跨域请求。
> - `same-origin`：只允许同源请求。
> - `no-cors`：请求方法只限于 GET、POST 和 HEAD，并且只能使用有限的几个简单标头，不能添加跨域的复杂标头，相当于提交表单所能发出的请求。

##### **credentials**

`credentials`属性指定是否发送 Cookie。可能的取值如下：

> - `same-origin`：默认值，同源请求时发送 Cookie，跨域请求时不发送。
> - `include`：不管同源请求，还是跨域请求，一律发送 Cookie。
> - `omit`：一律不发送。

跨域请求发送 Cookie，需要将`credentials`属性设为`include`。

> ```javascript
> fetch('http://another.com', {
>   credentials: "include"
> });
> ```

##### **signal**

`signal`属性指定一个 AbortSignal 实例，用于取消`fetch()`请求，详见下一节。

**keepalive**

`keepalive`属性用于页面卸载时，告诉浏览器在后台保持连接，继续发送数据。

一个典型的场景就是，用户离开网页时，脚本向服务器提交一些用户行为的统计信息。这时，如果不用`keepalive`属性，数据可能无法发送，因为浏览器已经把页面卸载了。

> ```javascript
> window.onunload = function() {
>   fetch('/analytics', {
>     method: 'POST',
>     body: "statistics",
>     keepalive: true
>   });
> };
> ```

##### **redirect**

`redirect`属性指定 HTTP 跳转的处理方法。可能的取值如下：

> - `follow`：默认值，`fetch()`跟随 HTTP 跳转。
> - `error`：如果发生跳转，`fetch()`就报错。
> - `manual`：`fetch()`不跟随 HTTP 跳转，但是`response.url`属性会指向新的 URL，`response.redirected`属性会变为`true`，由开发者自己决定后续如何处理跳转。

**integrity**

`integrity`属性指定一个哈希值，用于检查 HTTP 回应传回的数据是否等于这个预先设定的哈希值。

比如，下载文件时，检查文件的 SHA-256 哈希值是否相符，确保没有被篡改。

> ```javascript
> fetch('http://site.com/file', {
>   integrity: 'sha256-abcdef'
> });
> ```

##### **referrer**

`referrer`属性用于设定`fetch()`请求的`referer`标头。

这个属性可以为任意字符串，也可以设为空字符串（即不发送`referer`标头）。

> ```javascript
> fetch('/page', {
>   referrer: ''
> });
> ```

##### **referrerPolicy**

`referrerPolicy`属性用于设定`Referer`标头的规则。可能的取值如下：

> - `no-referrer-when-downgrade`：默认值，总是发送`Referer`标头，除非从 HTTPS 页面请求 HTTP 资源时不发送。
> - `no-referrer`：不发送`Referer`标头。
> - `origin`：`Referer`标头只包含域名，不包含完整的路径。
> - `origin-when-cross-origin`：同源请求`Referer`标头包含完整的路径，跨域请求只包含域名。
> - `same-origin`：跨域请求不发送`Referer`，同源请求发送。
> - `strict-origin`：`Referer`标头只包含域名，HTTPS 页面请求 HTTP 资源时不发送`Referer`标头。
> - `strict-origin-when-cross-origin`：同源请求时`Referer`标头包含完整路径，跨域请求时只包含域名，HTTPS 页面请求 HTTP 资源时不发送该标头。
> - `unsafe-url`：不管什么情况，总是发送`Referer`标头。

####   中断请求

   

```js
// Fetch API 支持通过 AbortController/AbortSignal 对中断请求。

let abortController = new AbortController(); 

fetch('../resource/test.pdf', { signal: abortController.signal }) 

.catch(() => console.log('中断请求 aborted!')); 

// 10 毫秒后中断请求 

setTimeout(() => abortController.abort(), 10); 

// 已经中断
```



> fetch 不能实现上传进度这部分