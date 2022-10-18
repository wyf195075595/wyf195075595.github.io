---
title: 第二章 HTML中的javaScript
date: 2022-06-17 08:23:10
tags: 红宝书笔记
categories: js
---

> 如何在HTML页面中引入JavaScript。最早在Netsape Navigator2 实现 script 元素

``` js
script元素的属性
1、 async :可选。HTML5新增。异步执行脚本。与defer属性类似。只适用于外部脚本。告诉浏览器立即下载。与defer不同的是，标记 async 的脚本不能保证按照他们出现的顺序执行。异步脚本保证会在页面的 load 事件前执行，但可能会在 DOMContentLoaded 之前或之后。

2、defer：可选。推迟执行脚本。延迟至整个页面解析完毕后执行，执行时不改变页面结构，DOMContentLoaded事件前，按顺序执行。

3、charset:可选。使用src属性指定的代码字符集。基本没啥用。

4、crossorigin：可选。配置相关请求的CORS（跨源资源共享）设置。默认不使用CORS。

5、integrity：可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性(SRI)。

6、language：废弃。最初用于表示代码块中的脚本语言（如"JavaScript"、"JavaScript）

7、src：可选。表示包含要执行的代码的外部文件。

8、type:代替language。表示脚本语言的内容类型（MIME类型）。值为 module 时，代码会被当成ES6模块。

<!--more-->

```
在HTML中使用

``` html
<!DOCTYPE html>
<html>
	<head>
		
		<script src="./test2.js" defer></script>
		<script src="./test2.js" async></script>
	</head>
	<body>
		
		<script>
			function loadScript(path){
                let script = document.createElement('script');
                script.src = path ;
                // script.async = false;//这种方式添加的脚本默认async为true，但有些浏览器不支持 async
                document.body.append(script);
            }
            //提高浏览器预加载资源队列优先级
            function addPreload(path,type){
                let link = document.createElement('link');
                link.rel = 'preload';
                link.href = path;
                link.as = type||'script';
                document.head.append(link);
            }
		</script>
		<script src="./test1.js"></script>
	</body>
</html>

使用 async、defer标签，会使脚本资源队列优先级下降


拓展：

-资源提升使用场景：
	1、网站现在有多个首屏图像，但它们并具有相同的优先级，比如在轮播图中只有第一张图需要更高的优先级。

	2、将 <script> 声明为 async 或 defer 可以告诉浏览器异步加载它们。但是，根据我们上面的分析，这些 <script> 也被分配了 “低” 优先级。但是你可能希望在确保浏览器异步下载它们的同时提高它们的优先级，尤其是一些对用户体验至关重要脚本。

    3、浏览器为 JavaScript fetch API 异步获取资源或数据分配了高优先级，但是某些场景下你可能不希望以高优先级请求所有资源。

    4、浏览器为 CSS、Font 分配了同样的高优先级，但是并不是所有 CSS 和 Font 资源都是一样重要的，你可能需要更具体的指定它们的优先级。
    


-importance 属性
	你可以使用一个 importance 属性来更细力度的控制资源加载的优先级，包括 link、img、script 和 iframe 这些标签。

	- importance 属性可以指定三个值：

        - high：你认为该资源具有高优先级，并希望浏览器对其进行优先级排序。
        - low：你认为该资源的优先级较低，并希望浏览器降低其优先级。
        - auto：采用浏览器的默认优先级。
        
     <!-- We don't want a high priority for this above-the-fold image -->
    <img src="/images/in_viewport_but_not_important.svg" importance="low" alt="I'm an unimportant image!">

    <!-- We want to initiate an early fetch for a resource, but also deprioritize it -->
    <link rel="preload" href="/js/script.js" as="script" importance="low">

    <script>
      fetch('https://example.com/', {importance: 'low'}).then(data => {
        // Trigger a low priority fetch
      });
    </script>

    <!-- The third-party contents of this iframe can load with a low priority -->
    <iframe src="https://example.com" width="600" height="400" importance="low"></iframe>


- 降低首屏图片的优先级
	使用 importance 属性降低可能不重要的首屏图片的优先级
    <ul class="carousel">
      <img src="img/carousel-1.jpg" importance="high">
      <img src="img/carousel-2.jpg" importance="low">
      <img src="img/carousel-3.jpg" importance="low">
      <img src="img/carousel-4.jpg" importance="low">
    </ul>
- 降低预加载资源的优先级
	想要阻止预加载资源和其他关键资源的竞争，可以降低其优先级：
	
	<!-- Lower priority only for non-critical preloaded scripts -->
    <link rel="preload" as="script" href="critical-script.js">
    <link rel="preload" href="/js/script.js" as="script" importance="low">

    <!-- Preload CSS and hero images without blocking other resources -->
    <link rel="preload" as="style" href="theme.css" importance="low" onload="this.rel=stylesheet">

- 脚本的优先级
	如果页面上有一些必要的交互脚本，但不需要阻塞其他资源，你可以把它们标记为具有高优先级，同时异步加载它们
	
    <script src="async_but_important.js" async importance="high"></script>


- fetch 
	浏览器默认会以高优先级执行 fetch 请求，你可以降低不太关键的数据请求的优先级
    <script>
        // Important validation data (high by default)
        let authenticate = await fetch('/user');

        // Less important content data (suggested low)
        let suggestedContent = await fetch('/content/suggested', {importance: 'low'});
    </script>
```
> noscript 标签
>
> <noscript>元素可以包含任何可以出现在<body>中的 HTML 元素，<script>除外。在下列两种 情况下，浏览器将显示包含在<noscript>中的内容：
>  浏览器不支持脚本； 
>  浏览器对脚本的支持被关闭.
>
> 