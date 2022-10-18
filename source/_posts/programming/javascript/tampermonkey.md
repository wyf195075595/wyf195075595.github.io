---
title: Tampermonkey脚本
date: 2022-06-17 08:23:10
tags: 拓展
categories: js
---

## 1、基本格式

```js
// ==UserScript==
// @name         脚本的名称
// @namespace    可以写个人的网站什么的
// @version      版本号
// @description  脚本的描述
// @author       作者
// @match        匹配站点，在那个网页中启用该脚本，可使用正则进行匹配
// @match        http://119.36.55.224:6850/zentao/my/
// @match        http://www.iqiyi.com/v_*
// @grant        需要什么权限（GM_addStyle添加css样式权限）
// @grant        GM_addStyle
// @require		 引用外部js
// @require		 http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js
// ==/UserScript==
$(function() {
  //在这里编写我们的脚本
})();
```

## 2、基本语法

<!--more-->

| 键                                   | 示例                                                         | 备注                                                         |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| @name                                | [@name](https://www.52pojie.cn/home.php?mod=space&uid=170990) 脚本名称 | 脚本的名称。该项将显示在页面的标题以及链接内容，必填项。     |
| @description                         | @description 脚本功能描述                                    | 脚本功能的描述，显示在脚本标题下面，必填项。                 |
| @namespace                           |                                                              | [@namespace](https://www.52pojie.cn/home.php?mod=space&uid=467642) 及 @name 这两个属性将帮助用户脚本管理器判断是否已安 装该脚本。 |
| @version                             | @version 0.0.1                                               | 脚本的版本标记将使用 Mozilla 版本格式 并显示于脚本的简介页面，必填 项。 |
| @include @exclude @match             | @match *://www.52pojie.cn/*                                  | 描述脚本将执行的页面。该列表会被分析并展示到脚本的简介页面，以及 用于脚本分类。 |
| @require                             | @require http://cdn.bootcss.com/jquery.min.js                | 引用外部脚本到您的脚本                                       |
| @updateURL @installURL, @downloadURL |                                                              | 告知用户脚本管理器应该在哪个地址获取脚本更新。               |
| @license                             |                                                              | 脚本所使用的许可协议名称或地址，该协议需包含用户是否允许二次分发 或修改 脚本的权利。不提供许可协议则表示用户仅允许个人使用且不得 二次分发；该协 议将在脚本的简介页面显示。 |
| @supportURL                          |                                                              | 用户可获得该脚本技术支持的链接地址 (如：错误反馈系统、论坛、电子 邮件)，该链接将显示在脚本的反馈页面。 |
| @contributionURL                     |                                                              | 用于捐赠脚本作者的链接，该链接将显示在脚本的反馈页面。       |
| @contributionAmount                  |                                                              | 建议捐赠金额，请配合 @contributionURL 使用。                 |
| @compatible                          |                                                              | 标记此脚本与某个浏览器兼容，兼容性信息将显示在脚本的简介页面上。 |
| @incompatible                        |                                                              | 标记此脚本与某个浏览器不兼容，兼容性信息将显示在脚本的简介页面 上。 |