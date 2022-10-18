---
title: 第一章 什么是JavaScript
date: 2022-06-17 08:23:10
tags: 红宝书笔记
categories: js
---

> 1995年，JavaScript问世。它的用途是代替Perl等服务器端语言处理输入验证。
>
> 应为js问世前，验证表单是否已填、是否有效都需要与服务端一次通信。缓慢的网速导致用户体验极差
>
> 可以想象一下，你填写完表单，单击“提交”按钮，等 30 秒处理，然后看到一条消息， 告诉你有一个必填字段没填。
>
> 随着时间推移，现在的js已不仅仅是用于数据验证，而是渗透到浏览器窗口及其内容的方方面面。JavaScript 已被公认为主流的编程语言，能 够实现复杂的计算与交互，包括闭包、匿名（lambda）函数，甚至元编程等特性。不仅是桌面浏览器， 手机浏览器和屏幕阅读器也支持 JavaScript，其重要性可见一斑。就连拥有自家客户端脚本语言 VBScript 的微软公司，也在其 Internet Explorer（以下简称 IE）浏览器最初的版本中包含了自己的 JavaScript 实现。
>

## 1、js与浏览器的历史

<!--more-->

> 1991年，WordWideWeb 第一个浏览器诞生。
>
> 1992年，Erwise 世界上第一个图形网页浏览器。
>
> [更多历史]: http://www.360doc.com/content/14/0910/15/17799864_408415463.shtml
>
> 1993年，美国伊利诺大学的 国家超级计算机应用中心（NCSA）发表了名为 ‘Mosaic’的浏览器，当时大受欢迎
>
> 1994年，Mosaic开发中心人员 马克安德森 和硅图（SGI）公司创始人吉姆克拉克 创建了 Mosaic公司。成立后，重写浏览器代码。因为 NCSA 拥有 Mosaic 的商标权，且伊利诺大学已将技术转让给了 望远镜娱乐公司（Spyglass）。同年10月13日，发布了新的浏览器 **Mosaic NetScape** 0.9 bate版。当时最热门的浏览器。为避免和 NCSA 商标拥有权问题，公司更名 网景通信公司（**Netscape Communications Corporation**），12月5日，软件名改为 网景导航者(**Netscape Navigator**)。很快占领市场首位
>
> 1995年，网景公司一位名叫 Brendan Eich 的工程师，开始为即将发布的 Netscape Navigator 2 开发一 个叫 Mocha（后来改名为 LiveScript）的脚本语言。当时的计划是在客户端和服务器端都使用它，它在 服务器端叫 LiveWire。为赶上发布时间，与Sun公司合作，并在 Netscape Navigator 2 发布前 把 **LiveScript** 改名为 **javaScript**.(为了蹭热度，因为当时Sun公司的Java编程语言风靡世界)，使其稳居市场领导者位置。同年8月16号， 微软发布了 **IE浏览器** 进军浏览器市场，IE浏览器的技术取自望远镜娱乐公司（Spyglass）的授权，也就是在Mosaic的源码基础上开发的
>
> 1996年，网景公司的Navigator浏览器所占有的浏览器市场份额达86%。微软公司开始将IE浏览器整合到OS(操作系统)中，同年8月发布自己的脚本语言JScript与IE3.0（避免与网景公司冲突）。双方激烈的竞争--Navigator和Internet Explorer浏览器大战的序幕（到2002年IE完胜，占据全世界96%的市场份额）。javaScript作为一门语言也向前迈进了一大步
>
> 1997年，因为网景的JavaScript与微软的JScript的差异，但是又没有规范其语法的标准，差异问题日益严重业界堪忧。欧洲计算机制造协会（Ecma）,制定了ECMA-262（浏览器脚本语言标准）。
>
> 1998 年，国际标准化组织（ISO）和国际电工委员会（IEC）也将 ECMAScript 采纳为标准（ISO/ IEC-16262）。自此以后，各家浏览器均以 ECMAScript 作为自己 JavaScript 实现的依据，虽然具体实现 各有不同。

## 2、javaScript实现

>  核心（ECMAScript）  文档对象模型（DOM）  浏览器对象模型（BOM）
>

### 		2.1、ECMAScript

> 脚本语言规范
>
>  语法  类型  语句  关键字  保留字  操作符  全局对象

### 		2.2、DOM

> 文档对象模型是一个应用编程接口（api），通过创建表示文档的树，让开发者可以随心所欲地控制网页的内容和结构。使用 DOM API， 可以轻松地删除、添加、替换、修改节点。
>
> DOM 拥有 Level 1，2，3,W3C规范
>
> 支持 DOM 是浏览器厂商的重中之重，每个版本发布都会改进支持度。下表展示了主流浏览器支持 DOM 的情况。 
>
>![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/dom.png)



### 		2.3、BOM

> 提供了浏览器对象模型（BOM） API，用于支持访问和操作浏览器的窗 口。使用 BOM，开发者可以操控浏览器显示页面之外的部分。但是没有唯一相关标准。HTML5改变了这个局面，这个版 本的 HTML 以正式规范的形式涵盖了尽可能多的 BOM 特性。
>