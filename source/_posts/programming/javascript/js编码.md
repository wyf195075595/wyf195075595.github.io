---
title: js编码解码
date: 2022-06-17 08:23:10
tags: js
categories: js
---

1. 编码 encodeURI和encodeURIComponent

   > 它们都是编码URL，唯一区别就是编码的字符范围了：
   >
   > encodeURI方法**不会**对下列字符编码 ASCII字母、数字、~!@#$&*()=:/,;?+'
   >
   > encodeURIComponent方法**不会**对下列字符编码 ASCII字母、数字、~!*()'
   >
   > 所以encodeURIComponent比encodeURI编码的范围更大。
   >
   > **当你要编码整个URL，使用这个URL，那么用encodeURI。**
   >
   > **当你要编码URL中的参数的时候，那么encodeURIComponent是最好方法。**

2. 解码 decodeURI()和decodeURIComponent()函数

   > - decodeURIComponent()定义和用法：decodeURIComponent() 函数可对 encodeURIComponent() 函数编码的 URI 进行解码。
   >
   > - decodeURI()定义和用法：decodeURI() 函数可对 encodeURI() 函数编码过的URI 进行解码。