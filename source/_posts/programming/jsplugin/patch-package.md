---
title: patch-package
date: 2022-07-18 14:27:10
tags: 插件
categories: js
---

> **patch-package** 这个库来解决这类问题。一方面，它能记录第三方库代码的改动，另一方面也能将改动同步到团队每个成员。

```
npm install patch-package -D
```

> 注意: 要改动的包在 package.json 中必须声明确定的版本，不能有~或者^的前缀。

<!--more-->

进入第三库进行修改

```
//这里我修改的是 sass 库
npx patch-package sass
```

现在根目录会多出patches目录记录第三方包内容的更改，随后我们在package.json的scripts中增加如下内容：

```
{
  "scripts": {
    "postinstall": "patch-package"
  }
}
```

> 每次安装依赖的时候都会通过 postinstall 脚本自动应用 patches 的修改，解决了团队协作的问题。