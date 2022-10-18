---
title: electron 遇到的问题
date: 2022-06-17 08:23:10
tags: electron
categories: electron
---
# Electron结合React，在渲染进程中使用 node 模块

## 问题

将`create-react-app`与`electron`集成在了一个项目中。但是在React中无法使用`electron`。当在React中使用`require('electron')`时就会报`TypeError: fs.existsSync is not a function`的错误。因为React中无法使用Node.js的模块.

## 解决方法1

利用`window.require`引入



```
const electron = window.require('electron')
```

<!--more-->

## 解决方法2

1. 创建`preload.js`文件

在项目目录下新建`preload.js`文件,该文件是预加载的`js`文件，并且在该文件内可以使用所有的`Node.js`的`API`。在`preload.js`中添加



```
global.electron = require('electron')
```

1. 修改`main.js`文件

修改创建浏览器的入口代码,添加`preload`配置项。将`preload.js`作为预加载文件



```
const mainWindow = new BrowserWindow({
        width: 800,
        height: 600,
        // frame: false,
        webPreferences: {
            preload: path.join(__dirname, 'preload.js'),
            nodeIntegration : true,
            contextIsolation: false  //Electron 12.0以上版本需要的额外设置此项
        }
    })
```

或者修改`piblic/index.html`文件

在`<div id="root"></div>`前引入`preload.js`文件



```
<script>require('./preload.js')</script><div id="root"></div>
```

最后在`React`组件中如下使用`electron`



```
const electron = window.electron;
```