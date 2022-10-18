---
title: jq的$.load()方法
date: 2022-06-17 08:23:10
tags: js
categories: js
---


## 1、主页面加载
<!--more-->

```html
//
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
</head>

<body>

<header>
    
</header>

<div class="index-main">
    <div id="main">

    </div>
</div>
<footer class="footer">
    
</footer>
</body>

<script>
    if(location.search.split('=')[0] == '?aim'){
        $("#main").html("");
        $("#main").load(`html${location.search.split('=')[1]}.html?version=${version}`, function () {
            loadScript(`js${location.search.split('=')[1]}.js?version=${version}`);
        });
    }
    else{
        $("#main").html("");
        $("#main").load(`html/module/index.html?version=${version}`, function () {
            loadScript(`js/module/index.js?version=${version}`);
        });
    }
</script>
</html>

```

## 2、动态加载html及其js文件

```js
//页面切换
window.localtion.href = 'index.html?aim=/xxxx/xxxxx'
// 动态加载js脚本文件
function loadScript(url) {
    var scripts = document.getElementsByTagName("script");
    for (var index = 0; index < scripts.length; index++) {
        if (scripts[index].getAttribute("data-is-dynamic") === "true") {
            document.body.removeChild(scripts[index]);
        }
    }
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    script.setAttribute("data-is-dynamic", "true"); //判断是否是新增script
    document.body.appendChild(script);
}
```

