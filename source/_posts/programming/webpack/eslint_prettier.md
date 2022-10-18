---
title:  ESLint + Prettier规范项目代码
date: 2022-06-23 18:23:10
tags: eslint
categories: webpack
---

> 为了项目代码风格统一，但又不会使人难受。自动化检测格式化代码方案

- Prettier

	> 一个流行的代码格式化工具的名称,它能够解析代码,使用你自己设定的规则来重新打印出格式规范的代码。

	

- Eslint

> ESLint是一个用来识别 ECMAScript 并且按照规则给出报告的代码检测工具，使用它可以避免低级错误和统一代码的风格。

<!--more-->

1. 安装依赖

	都是开发依赖

	```
	npm install eslint prettier --save-dev
	
	npm install eslint-config-prettier eslint-plugin-prettier --save-dev
	```

2. vscode 插件安装

	> 安装 vscode 插件 [ESLint](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Ddbaeumer.vscode-eslint) 和 [Prettier - Code formatter](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Desbenp.prettier-vscode)

	需要每次保存时就自动格式化代码，添加以下配置

	- 项目下创建 .vscode/settings.json 配置 (也可以配置到全局)

	- 配置每次报错自动ESLint检查规则并格式化

		```js
		{
		 "editor.codeActionsOnSave": {
		   "source.fixAll.eslint": true
		 }
		}
		```

	

3. 添加配置文件

	- 添加配置文件 .eslintrc.js

		```js
		// .eslintrc.js
		module.exports = {
		  root: true, 
		  plugins: ['prettier'],  // prettier 一定要是最后一个，才能确保覆盖
		  rules: {
		    'prettier/prettier': 'error',
		  },
		  extends: ['plugin:prettier/recommended'],
		};
		
		```

		上边配置运行报错就用下边的

		```js
		// .eslintrc.js
		module.exports = {
		  plugins: ['prettier'],
		  parser: "vue-eslint-parser",
		  parserOptions: {
		    sourceType: 'module',
		    ecmaVersion: 2020,
		    parser: 'babel-eslint',
		  },
		  rules: {
		    'prettier/prettier': 'error',
		    "parser": 0
		  },
		  extends: ['plugin:prettier/recommended'],
		};
		
		```

	- 添加配置文件 .prettierrc.js

		```js
		//.prettierrc.js
		module.exports = {
		  // 一行最多 120 字符
		  printWidth: 120,
		  // 使用 2 个空格缩进
		  tabWidth: 2,
		  // 不使用 tab 缩进，而使用空格
		  useTabs: false,
		  // 行尾需要有分号
		  semi: true,
		  // 使用单引号代替双引号
		  singleQuote: true,
		  // 对象的 key 仅在必要时用引号
		  quoteProps: 'as-needed',
		  // jsx 不使用单引号，而使用双引号
		  jsxSingleQuote: false,
		  // 末尾使用逗号
		  trailingComma: 'all',
		  // 大括号内的首尾需要空格 { foo: bar }
		  bracketSpacing: true,
		  // 箭头函数，只有一个参数的时候，也需要括号
		  arrowParens: 'always',
		  // 每个文件格式化的范围是文件的全部内容
		  rangeStart: 0,
		  rangeEnd: Infinity,
		  // 不需要写文件开头的 @prettier
		  requirePragma: false,
		  // 不需要自动在文件开头插入 @prettier
		  insertPragma: false,
		  // 使用默认的折行标准
		  proseWrap: 'preserve',
		  // 根据显示样式决定 html 要不要折行
		  htmlWhitespaceSensitivity: 'css',
		  // 换行符使用 lf
		  endOfLine: 'lf',
		};
		
		```

		

4. 添加命令

	在 package.json 中的 scripts 中添加

	```js
	"lint": "eslint --ext .js,.vue src",  // 一键检查项目是否符合eslint规则
	    
	"prettier": "prettier --write .",     // 一键格式化所有文件
	```

	

5. 忽略文件

	某些文件不需要格式检查或代码格式化的，可以添加到配置

	- **eslint忽略文件**	**.eslintignore**

		```
		/*.json
		/*.js
		dist
		node_modules
		```

		

	- **prettier忽略文件**  **.prettierignore**

		```
		node_modules
		.DS_Store
		dist
		dist-ssr
		*.local
		node_modules
		.DS_Store
		dist
		dist-ssr
		*.local
		```

> 结语：
>
> 参考链接：
>
> https://juejin.cn/post/7095689037889372191
>
> https://www.cnblogs.com/Yellow-ice/p/15127392.html

