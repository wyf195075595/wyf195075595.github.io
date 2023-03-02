---
title: jsconfig.json
date: 2023-02-08 22:11:10
tags: js
categories: js
---

### [vue项目](https://so.csdn.net/so/search?q=vue项目&spm=1001.2101.3001.7020)中 jsconfig.json 文件是什么?有啥用？

> 当您在工作空间中有一个定义项目上下文的jsconfig.json文件时，JavaScript体验会得到改进。

目录中存在tsconfig.json文件表明该目录是 TypeScript 项目的根目录。该tsconfig.json文件指定编译项目所需的根文件和编译器选项。

JavaScript 项目可以使用jsconfig.json文件来代替，它的作用几乎相同，但默认启用了一些与 JavaScript 相关的编译器标志。

> jsconfig.json源于tsconfig.json，是TypeScript的配置文件。
>
>  jsconfig.json相当于tsconfig.json的“allowJs”属性设置为true

<!--more-->



```js

/* 主要作用是vscode导入使用别名时可以自动化提示文件路径 */
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@/*": ["src"],
      "components/*": ["src/components/*"],
      "pages/*": ["src/pages/*"],
      "assets/*": ["src/assets/*"]
    },
    // 解决 prettier 对装饰器语法警告
    "allowSyntheticDefaultImports": true
  },
  "exclude": ["node_modules", "dist"]
}
```

![image-20230208222816154](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230208222816154.png)