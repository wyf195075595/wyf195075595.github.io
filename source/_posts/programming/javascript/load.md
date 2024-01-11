---
title: jq的$.load()方法
date: 2022-06-17 08:23:10
tags: js
categories: js
---

>
> ## 通过jQuery load 方法动态加载HTML，然后动态加载 js脚本，实现动态加载页面功能

<!--more-->

1、主页面加载

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
        // load 方法 加载html
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
    // 移除上个页面异步加载的js脚本
    var scripts = document.getElementsByTagName("script");
    for (var index = 0; index < scripts.length; index++) {
        if (scripts[index].getAttribute("data-is-dynamic") === "true") {
            document.body.removeChild(scripts[index]);
        }
    }
    // 加载新的脚本
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    script.setAttribute("data-is-dynamic", "true"); //判断是否是新增script
    document.body.appendChild(script);
}
```



### 示例

```html
<body>
    <div class="page-title">算法识别</div>
    <div class="page-container">
        <div class="iframe-centent" style="height: 100%;width:100%;"></div>
    </div>
</body>
```

```js
$(function () {
  var src = "";
  getIframePage();
  function getIframePage() {
    src = "http://" + ip + ":17030";
    var lists =
      '<iframe id="iriframe" src="' +
      src +
      '" style="width:100%;height:100%" frameborder="0" ></iframe>';
      // 加载iframe
      $(".iframe-centent").html(lists);
      // iframe 加载完毕后，使用postMessage 向 iframe 页面发送 token 
    document.getElementById("iriframe").addEventListener("load", function () {
      document.getElementById("iriframe").contentWindow.postMessage(
        {
          token: common_token,
        },
        "*"
      );
    });
  }
});

```

