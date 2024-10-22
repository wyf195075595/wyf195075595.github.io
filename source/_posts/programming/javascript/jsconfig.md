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

`jsconfig.json` 文件在 JavaScript 项目中为 VS Code 提供了强大的配置能力，能够提升开发体验，包括更智能的代码提示、类型检查、路径别名支持等。通过合理配置该文件，开发者可以在大型项目中更高效地管理代码和项目结构。

```js

/* 主要作用是vscode导入使用别名时可以自动化提示文件路径 */
{
  "compilerOptions": {
    "target": "es6",  // 指定 ECMAScript 版本
    "module": "commonjs",  // 使用的模块系统
    "checkJs": true,  // 启用 JavaScript 文件的类型检查
    "baseUrl": "./",// 基础目录，用于解析非相对模块名
    "paths": {
      "@/*": ["src"],// 路径别名配置
      "components/*": ["src/components/*"],
      "pages/*": ["src/pages/*"],
      "assets/*": ["src/assets/*"]
    },
    // 解决 prettier 对装饰器语法警告
    "allowSyntheticDefaultImports": true
  },
  // 用于排除不需要处理的文件或目录，如 node_modules、构建输出目录等。
  "exclude": ["node_modules", "dist"],
  "include": ["src/**/*"]  // 包含 src 目录下的所有文件  
  "files": ["src/index.js"],// 用于明确指定要包含的文件，而 exclude 是明确要排除的
  // 用于配置自动获取类型定义文件（.d.ts），常用于纯 JavaScript 项目中启用对 TypeScript 的类型支持
  "typeAcquisition": {
    "enable": true,  // 启用类型自动获取
    "include": ["jquery"],  // 包含特定库的类型
    "exclude": ["node"]  // 排除特定库的类型
  }
}
```

![image-20230208222816154](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230208222816154.png)