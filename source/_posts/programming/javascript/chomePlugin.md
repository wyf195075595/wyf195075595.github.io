---
title: chrome 插件
date: 2024-07-19 15:45:10
tags: chrome
categories: js
---
Chrome插件是一个用Web技术开发、用来增强浏览器功能的软件，它其实就是一个由HTML、CSS、JS、图片等资源组成的一个[.crx](https://developer.chrome.com/extensions/crx)后缀的压缩包.

v2版本已经逐渐不在被支持，以v3语法为基础的简单笔记

![](http://image.haoji.me/201707/20170710_222547_735_9922.png)

<!--more-->

demo目录结构

```lua
demo
├── css -- 样式资源
│   ├── ...
├── image  -- 图片资源
│   ├── ...
├── js   -- 脚本资源
│   ├── content-script.js  -- 内容脚本
│   ├── injected-script.js  --  动态注入脚本
│   ├── popup.js  --  弹窗脚本
│   └── service_worker.js  -- 后台脚本
├── popup.html   -- 弹窗页面
└── manifest.json  -- 配置文件
```

### 配置文件

```json
{
	"manifest_version": 3,// 配置文件v3版本写死3，2版本逐渐放弃支持
	"name": "Chrome插件demo",// 插件名称
	"version": "1.0",// 插件版本
	"description": "最简单的Chrome插件demo，需要快速做一个简单的插件时可以基于这个仓库开发",// 描述信息
	"author": "sxei",// 作者信息
	"icons": // 图标icon
	{
		"48": "image/icon.png",
		"128": "image/icon.png"
	},
	"background":// 常驻后台js
	{
		"service_worker": "js/service_worker.js",
		"type": "module"// 只有使用 ES module （使用 import 关键字）时，才需要 "type" 字段。其值将始终为 "module"。
	},
	"action": // 浏览器上显示的图标及浮窗
	{
		"default_icon": "image/icon.png",
		"default_popup": "popup.html"
	},
	"content_scripts": // 需要直接注入页面的JS
	[
		{
             // "<all_urls>" 表示匹配所有地址
			"matches": ["http://localhost:5173/*"],
             // 多个JS按顺序注入
			"js": ["js/content-script.js"],
             // css 注入，优先级很高需仅次于浏览器默认样式，注意 
             "css": ["css/custom.css"],
             // 运行的时机
			"run_at": "document_start"
		}
	],	
    // 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的
	"web_accessible_resources": 
	[
		{
			"resources": [
				"js/injected-script.js"
			],
			"matches": [
			  "*://*/*"
			]
		}
	]
}
```



### popup.html

页面在点击插件图标时显示，一般做临时交互用，此页面引用popup脚本

![博客园网摘插件popup效果](https://image.haoji.me/201706/20170619_161102_335_9254.png)

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8"/>
	<title>popup</title>
</head>
<body style="width:500px;min-height:100px;">
	这里是popup。
	<script type="text/javascript" src="js/popup.js"></script>
</body>
</html>
```

### popup.js

​	在权限上，它和 background 非常类似，它们之间最大的不同是生命周期的不同，popup 中可以直接通过 chrome.extension.getBackgroundPage() 获取 background 的 window 对象。

```js
console.log('你好，我是popup！', globalThis);
```

与 background 通信

```js
// 获取background的window对象。
chrome.extension.getBackgroundPage()
```



### content-script.js

**content-scripts** **和原始页面共享 DOM，但是不共享JS**，如要访问页面JS（例如某个JS变量），只能通过 injected js 来实现。

```js
(function() {
	// 向页面注入JS
	function injectCustomJs(jsPath)
	{
		jsPath = jsPath || 'js/injected-script.js';
		var temp = document.createElement('script');
		temp.setAttribute('type', 'text/javascript');
		// 获得的地址类似：chrome-extension://ihcokhadfjfchaeagdoclpnjdiokfakg/js/inject.js
		temp.src = chrome.runtime.getURL(jsPath);
		temp.onload = function()
		{
			// 放在页面不好看，执行完后移除掉
			this.parentNode.removeChild(this);
		};
		document.head.appendChild(temp);
	}

	document.addEventListener('DOMContentLoaded', function()
	{
		console.log('这是 simple-chrome-plugin-demo 的content-script！', globalThis);
		// 注入脚本，注入脚本还需配置 web_accessible_resources 允许外部页面访问此脚本资源
		injectCustomJs();
	});
})();
```

与 background的 service-work.js，pupup.js(与background权限相同，仅生命周期不同) 通信 

```js
// 与 backgoundrd.js 通信
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {

    console.log('content-script收到来自background.js的消息：', request, sender, sendResponse);
    // 收到消息后回复
    // sendResponse()
})

// 发送消息给 background.js
chrome.runtime.sendMessage({ type: "getURL", value: 'js/injected-script.js' }, function(response) { 
	console.log("content-script 主动发送消息响应：", response); 
});

```



### injected-script.js

​	content-script 有一个很大的“缺陷”，也就是无法访问页面中的JS，虽然它可以操作DOM，但是DOM却不能调用它，也就是无法在DOM中通过绑定事件的方式调用content-script中的代码

此脚本是在 content-script.js 内容脚本动态注入的，它可以访问DOM和js ，可以通过 postMessage 方法 与 content-script 在同一页面通信

```js
console.log("注入脚本成功:", globalThis);
```

postMessage 通信

```js
// injected-script中：
window.postMessage({"test": '你好！'}, '*');

// content script中：
window.addEventListener("message", function(e)
{
	console.log(e.data);
}, false);
```

 

### service-worker.js

 background 的权限非常高，几乎可以调用所有的 Chrome扩展API（除了devtools），而且它可以无限制跨域，也就是可以跨域访问任何网站而无需要求对方设置`CORS`。

```js
console.log('你好，我是service_worker！', globalThis);

```

与contentscript通信

```js
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => { 
    console.log('service_worker收到消息：', request, sender, sendResponse);
    
});
// 发送消息给content_script
chrome.tabs.query({active: true, currentWindow: true}, function(tabs) { 
    chrome.tabs.sendMessage(tabs[0].id, {greeting: "你好，我是service_worker！"}, function(response) {
        console.log('service_worker 主动发送消息响应：', response)
    }); 
});
```

与popup.js 通信

```js
// 获取background的window对象。
chrome.extension.getBackgroundPage()
```





### 互相通信概览

注：`-`表示不存在或者无意义，或者待验证。

|                 | injected-script                       | content-script                              | popup-js                                          | background-js                                     |
| --------------- | ------------------------------------- | ------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- |
| injected-script | -                                     | window.postMessage                          | -                                                 | -                                                 |
| content-script  | window.postMessage                    | -                                           | chrome.runtime.sendMessage chrome.runtime.connect | chrome.runtime.sendMessage chrome.runtime.connect |
| popup-js        | -                                     | chrome.tabs.sendMessage chrome.tabs.connect | -                                                 | chrome.extension. getBackgroundPage()             |
| background-js   | -                                     | chrome.tabs.sendMessage chrome.tabs.connect | chrome.extension.getViews                         | -                                                 |
| devtools-js     | chrome.devtools. inspectedWindow.eval | -                                           | chrome.runtime.sendMessage                        | chrome.runtime.sendMessage                        |

> [参考文章](https://blog.haoji.me/chrome-plugin-develop.html#injected-script)
>
> [v2->v3版本变化](https://segmentfault.com/a/1190000044555740#item-2-2)
>
> [代码示例](https://github.com/GoogleChrome/chrome-extensions-samples?tab=readme-ov-file)

