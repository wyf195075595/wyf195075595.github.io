---
title: js/jquery获取其他窗口的元素
date: 2022-06-17 08:23:10
tags: js
categories: js
---
# js/jquery获取其他窗口的元素

### 1、获取 iframe 中的元素

```js
//获取iframe页面中的document
let doc = window.frames['Iframe name 属性'].document;
let doc = document.getElementById('Iframe 的id').contentDocument;
//然后就可通过doc对象获取其中的元素了
【注意，需要在 onload事件中使用】
```

### 2、获取父级窗口元素

<!--more-->

```js
//js
//获取父级窗口的document
let doc = window.parent.document;



//jquery
//获取父级窗口元素
$(``"#父窗口元素ID"``,window.parent.document);

//取父窗口的父窗口的元素就可以用：
$(selector, window.parent.parent.document);

//获取通过windows.open()打开的父窗体：
$(selector, window.opener.document);

//获取通过 showModelDialog()打开的父窗体：
$(selector, window.dialogArguments.document);

//用于刷新父窗口整个页面：
window.parent.location.reload();

//刷新父页面中的id为“4thEditTable”的列表（局部刷新）：
parent.$("#4thEditTable").load(window.parent.location.href+" #4thEditTable");
```

### 页面嵌套

#### 1、 在window对象上共享父级对象

​	效果，iframe 子页面 可以访问父级 window对象上共享的对象
 	问题，如果两个窗口一级域名相同，只是二级域名不同，那么设置上一节介绍的**document.domain**属性，就可以规避同源政策，拿到DOM。

#### 2、片段识别符

> 片段识别符（fragment identifier）.
>
> 片段标识符（fragment identifier）指的是，URL的#号后面的部分，比如http://example.com/x.html#fragment的#fragment。如果只是改变片段标识符，页面不会重新刷新。

 父窗口可以把信息，写入子窗口的片段标识符。

    // 父页面
    window.onhashchange() {
        var message = window.localtion.hash;
        ...
    }
    // 子页面
        parent.location.href = target + '#' + hash
    
    
    
    

#### 3、window.name

> 浏览器窗口有window.name属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。

// 父页面

```js
// 设置 
window.name = data;
// 访问
window.name
```

 // 子页面

```js
// 访问
var data = document.getElementById('myFrame').contentWindow.name

// 设置
window.parent.name = '子页面信息'
```



#### 4、window.postMessage

> 跨文档通信API（Cross-document messaging）
>
> HTML5为了解决这个问题，引入了一个全新的API：跨文档通信 API（Cross-document messaging）。
>
> 这个API为window对象新增了一个window.postMessage方法，允许跨窗口通信，不论这两个窗口是否同源。
>         

父窗口 aa.com

```js
var popup = window.open('http://bbb.com', 'title');
popup 
// 发送内容
popup.postMessage('Hello World!', 'http://bb.com');


// 监听消息与子页面相同
```

子窗口 bb.com

```js
window.addEventListener('message', receiveMessage);
function receiveMessage(e) {
    if(e.origin != 'http://aa.com') return
    if(e.data === 'Hello World!') {
        // 发送给消息源窗口
        e.source.postMessage('hello', event.origin)
    } else {
        console.log(event.data);
    }
}

// 主动发送给父级
// 内容，数据来源
 
window.top.postMessage('我是子页面','*')
```

message事件的事件对象event，提供以下三个属性。

-event.source：发送消息的窗口
-event.origin: 消息发向的网址
-event.data: 消息内容

