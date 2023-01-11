---
title: 微信小程序拓展
date: 2022-06-17 18:23:10
tags: 微信小程序
categories: 微信小程序小程
---
### 小程序框架

#### 1：WeUI 小程序–使用教程

https://weui.io/

官方介绍：WeUI 是一套同微信原生视觉体验一致的基础样式库，由微信官方设计团队为微信内网页和微信小程序量身设计，令用户的使用感知更加统一。小程序开发中最常用到的一款框架，受广大开发人员的欢迎。

[图片上传失败...(image-5dd2d3-1654073247786)]

#### 2：美团小程序框架mpvue

Github：https://github.com/Meituan-Dianping/mpvue 官网： http://mpvue.com/

官方介绍：mpvue是一个使用 Vue.js开发小程序的前端框架。框架基于 Vue.js核心，mpvue修改了 Vue.js的 runtime和 compiler实现，使其可以运行在小程序环境中，从而为小程序开发引入了整套Vue.js开发体验。

#### 3：组件化开发框架wepy

Github地址: https://github.com/Tencent/wepy 官网地址： https://tencent.github.io/wepy

官方介绍：组件化开发，完美解决组件隔离，组件嵌套，组件通信等问题,支持使用第三方 npm 资源，自动处理 npm 资源之间的依赖关系，完美兼容所有无平台依赖的 npm 资源包.

[图片上传失败...(image-f7e73e-1654073247786)]

#### 4：官方框架MINA

地址：https://developers.weixin.qq.com/miniprogram/dev/framework/MINA.html

官方介绍：框架提供了自己的视图层描述语言 WXML 和 WXSS，以及基于 JavaScript 的逻辑层框架，并在视图层与逻辑层间提供了数据传输和事件系统，可以让开发者可以方便的聚焦于数据与逻辑上。

#### 5：Tina.js

 一款轻巧的渐进式微信小程序框架

Tina.js 开源框架地址： https://github.com/tinajs/tina

官方介绍：是一款轻巧的渐进式微信小程序框架，保留 MINA (微信小程序官方框架) 的大部分 API 设计；无论你有无小程序开发经验，都可以轻松过渡上手。

#### 6：前端框架weweb

地址: https://github.com/wdfe/weweb

官方介绍：weweb是一个兼容小程序语法的前端框架，你可以用小程序的写法，来写web应用。如果你已经有小程序了，通过它你可以将你的小程序运行在浏览器中。

#### 7：微信UI组件库 iView Weapp

https://weapp.iviewui.com/

介绍：iView Weapp 提供了与 iView 一致的 UI 和尽可能相同的接口名称，大幅度降低了学习成本，是一套一套高质量的微信小程序 UI 组件库。

#### 8：ZanUI-WeApp --

 一个颜值高、好用、易扩展的微信小程序 UI 库

https://cnodejs.org/topic/589d625a5c8036f7019e7a4a

官方介绍：ZanUI-WeApp结合了微信的视觉规范，为用户提供更加统一的使用感受。 包含 badge、btn、等共计 17 类组件或元素。

#### 9：uniApp 

-- 一个多端混合开发的好用的框架，熟悉一个适用性超强
框架地址： https://uniapp.dcloud.io/collocation/pages 文档地址： https://uniapp.dcloud.io/README 相适应的ide：Hbuilder 可以直接使用该ide打包相应所需的端代码 官方介绍：开发一次，覆盖多端



### 小程序容器

> **所谓"小程序容器"，就是一个小程序的运行环境。** App 只要加载这个运行环境，就具备了运行小程序的能力。说得直白一点，它让你拥有了微信那样的小程序引擎，但是不必自己开发。
>
> 以前的方案是将小程序代码打包成适配各种平台的小程序运行环境。小程序容器则是可以适配各种小程序框架的代码，不需要额外打包成其他格式，直接运行

#### 小程序容器 FinClip

> FinClip 提供一套 SDK，不仅支持手机（iPhone、Android），还支持桌面（Windows、Linux、macOS）、手表、车机等各种平台。你的 App 只要加载了这套 SDK，就能直接运行微信小程序（以及小游戏），一行代码也不用修改。
>
> 也就是说，它完全支持微信小程序的 WXML 语法，无需二次开发，体验与微信端保持一致。你就可以把你的小程序，在微信运行一份，在自己的 App 也运行一份。

对于小型开发者，这个产品是免费使用，只有企业级应用才需要付费。