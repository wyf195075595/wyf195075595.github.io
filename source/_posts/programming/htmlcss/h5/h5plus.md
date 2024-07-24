---
title: h5开发
date: 2024-05-28 15:40:10
categories: h5
---

最近开发了app项目。使用的h5项目然后套壳成app。记录下过程

h5项目采用的 vite+vue3+vant

[h5文档](https://www.html5plus.org/doc/zh_cn/webview.html)

套壳方式：

1、hbuildX 云打包

`window.plus` 是 HBuilderX 和 uni-app 提供的 API 对象，用于访问设备的原生功能。

```js
if(window.plus){ 
    plusReady(); 
}else{ 
    document.addEventListener( "plusready", plusReady, false ); 
} // 扩展API准备完成后要执行的操作 
function plusReady(){ 
    plus.android.requestPermissions(['android.permission.RECORD_AUDIO'], function(e) {
        if(e.deniedAlways.length>0){	//权限被永久拒绝
            // 弹出提示框解释为何需要定位权限，引导用户打开设置页面开启
            console.log('限被永久拒绝!!! '+e.deniedAlways.toString());
        }
        if(e.deniedPresent.length>0){	//权限被临时拒绝
            // 弹出提示框解释为何需要定位权限，可再次调用plus.android.requestPermissions申请权限
            console.log('权限被临时拒绝!!! '+e.deniedPresent.toString());
        }
        if(e.granted.length>0){	//权限被允许
            //调用依赖获取定位权限的代码
            console.log('权限被允许!!! '+e.granted.toString());
        }
    }, function() {

    })
    
}
```

2、[hbuildX本地打包](https://blog.csdn.net/lf21qp/article/details/133277438)

3、capacitorjs + Android Studio 打包

4、[cordova](https://cordova.yoqi.me/docs/zh-cn/9.x/guide/cli/index.html) 打包

<!--more-->

套壳方式1的话就是按正常的h5项目开发。打包借助HbuildX工具就行了[参考](https://blog.csdn.net/dhp1994/article/details/134128719)



方式2引入 capacitorjs 

```
yarn add @capacitor/core
yarn add -D @capacitor/cli
```

初始化配置文件

```
npx cap init
```

修改配置文件 webDir 就是h5项目路径文件夹

```
{
  "appId": "com.example.app",
  "appName": "my-vue-capacitor-app",
  "webDir": "dist"
}

```

引入Android / ios

```
yarn add @capacitor/android
yarn add @capacitor/ios

npx cap add android
npx cap add ios
```

打开项目用 andriod studio

```
npx cap  open android
```

在 Android studio 中构建 apps



### 安装 Android studio 

1. [下载](https://developer.android.google.cn/studio?hl=zh-cn)

	开发工具免费使用，不需要破解

2. 安装中文插件

	查看安装的编辑器版本信息，AI-**222**.4459.24.2221.10121639,built on May 12,2023...,关键信息就是 222

	![](https://img-blog.csdnimg.cn/direct/b19fde22dc514ac0ae9d24cfd60fa5dd.png)

	[汉化包地址](https://plugins.jetbrains.com/plugin/13710-chinese-simplified-language-pack----/versions) 根据关键信息下载对应版本 zip 包。

	然后打开设置 install Plugin from Disk....,选择我们下载的包，重启即可

> 不想安装到默认位置C盘，[参考](https://www.cnblogs.com/progress2022/p/16648008.html)

