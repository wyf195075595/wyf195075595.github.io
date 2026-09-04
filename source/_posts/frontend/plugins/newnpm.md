---
title: 开发自己的js plugin
date: 2023-03-22 18:53:10
tags: npm
categories: plugins
---

> 工欲善其事，必先利其器。首先去找适合写插件的项目包管理工具。对比了webpack,rollup,gulp,parcel
>
> 经过一通对比就选中了 **rollup**，具体请自行了解。简单说一下我们熟知的**vue**,**react**,**taro-ui** 等 就是使用的 rolluojs 打包的
>
> 自己写了一个很简单的插件上传发布到npm。具体步骤如下：

<!--more-->

1. 首先 新建一个文件夹 npm init 初始化 得到package.json

2. 引入 rollup

	```shell
	# 使用了 yarn 因为 yarn 打包 可以自己找到 (yarn rollup) 命令可执行文件
	yarn add rollup -D
	```

3. 增加配置文件 rollup.config.js, 跟package.json 同级

	```js
	// 支持 导入 josn 文件
	import json from 'rollup-plugin-json'
	// 支持导入 npm 第三方模块
	import resolve from 'rollup-plugin-node-resolve'
	// 兼容 commonJs 模块
	import commonjs from 'rollup-plugin-commonjs'
	// 大小写转换
	import changeCase from 'change-case'
	// 创建作者，日期，库名等注释
	import createBanner from 'create-banner'
	// 语法降级,@babel/preset-env,@babel/core
	import { babel } from '@rollup/plugin-babel'
	import pkg from './package.json' assert { type: "json" }
	
	let name = changeCase.pascalCase(pkg.name)
	const banner = createBanner({
	    data: {
	      name: `${name}.js`,
	      year: '2023-present',
	    },
	  });
	
	console.log(name);
	export default {
	    // 入口文件
	    input: "src/index.js",
	    // input: ["src/index.js", "src/album.js"],
	    // input: {
	    //     foo: "src/index.js",
	    //     bar: "src/album.js"
	    // },
	    // 输出路径
	    output: [
	        {
	            file: `dist/${pkg.name}.amd.js`,
	            banner,
	            format: "amd"
	        },
	        {
	            banner,
	            name,
	            file: `dist/${pkg.name}.js`,
	            format: 'umd',
	        },
	        {
	            banner,
	            file: `dist/${pkg.name}.common.js`,
	            format: 'cjs',
	            exports: 'auto',
	        },
	        {
	            banner,
	            file: `dist/${pkg.name}.esm.js`,
	            format: 'esm',
	        },
	        {
	            banner,
	            file: `dist/${pkg.name}.iife.js`,
	            format: 'iife',
	        }
	    ],
	    plugins: [
	        json(),
	        // resolve(),
	        babel({
	            extensions: ['.js'],
	            babelHelpers: 'bundled',
	            "presets": [
	                [
	                    "@babel/preset-env",
	                    {
	                    "modules": false
	                    }
	                ]
	            ],
	            "env": {
	                "test": {
	                    "plugins": [
	                        "istanbul"
	                    ]
	                }
	            }
	        }),
	        commonjs(),
	    ]
	}
	```

4. 新建 src 文件夹放置源码，dist 文件夹放置打包文件，src/index.js

	```js
	// index.js
	class Progress extends HTMLElement {
	    constructor() {
	        // 调用超类的构造器
	        super();
	        // 创建一个影子 DOM 树并将其附加到这个元素
	        // 设置为 this.shadowRoot 的值
	        this.attachShadow({mode:'open'});
	        
	        // 克隆模板，定义自定义组件的后代及样式
	        // 然后把内容追加到影子根节点
	        this.createTemplate();
	        this.shadowRoot.append(this.template.content.cloneNode(true));
	        
	        // 内容模板
	        this.contentTemplate = `正在安装 #percentage %`;
	
	        // 取得对影子DOM中重要元素的引用
	        this.progress = this.shadowRoot.querySelector('.progress');
	        this.progressContent = this.shadowRoot.querySelector('.progress-content');
	        this.progressWrapper = this.shadowRoot.querySelector('.progress-wrapper');
	        
	
	        // 初始化进度
	        this.start = 0;
	        this.max = 100;
	    }
	    // 钩子函数： 自定义元素增加、删除、修改属性时 ，调用。
	    // 触发此回调函数，必须监听变化的属性static get observedAttributes() ,返回监听的属性。
	    attributeChangedCallback(name, oldValue, newValue) {
	        console.log(name, oldValue, newValue);
	    }
	    // 创建一个 template 元素，用于保存样式和元素树,可以在每个 元素的实例中使用它门
	    createTemplate() {
	        let template = document.createElement('template');
	        template.innerHTML = `
	            <style>
	              .progress-wrapper {
	                position: relative;
	                overflow: hidden;
	                width: 300px;
	                height: 20px;
	                text-align: center;
	                border-radius: 10px;
	                background-color: #cdeee3;
	              }
	        
	              .progress {
	                width: 0%;
	                height: 100%;
	                transition: all 200ms;
	                background-color: #3da985;
	              }
	        
	              p {
	                font-size: 12px;
	                font-weight: bolder;
	                position: absolute;
	                z-index: 10;
	                top: 0;
	                left: 0;
	                width: 100%;
	                height: 100%;
	                margin: 0;
	                transition: all 200ms;
	                background-image: linear-gradient(to right, #cdeee3 0%, #3da985 0%);
	                -webkit-background-clip: text;
	                -webkit-text-fill-color: transparent;
	                display: flex;
	                align-items: center;
	                justify-content: center;
	              }
	            </style>
	            <div class="progress-wrapper">
	                <div class="progress"></div>
	                <p class="progress-content">正在安装 0%</p>
	            </div>
	        `
	        this.template = template;
	    }
	    // 更新进度条
	    updateProgress(percentage) {
	        this.progress.style.width = percentage + "%";
	        this.progressContent.innerText = this.contentTemplate.replace("#percentage", percentage);
	        this.progressContent.style.backgroundImage = `linear-gradient(to right, #cdeee3 ${percentage}%, #3da985 ${percentage}%)`;
	    }
	    // 常用属性读写
	    get percentage() { return this.start }
	    get width() { return this.progressWrapper.style.width }
	    get height() { return this.progressWrapper.style.height }
	    get fontSize() { return this.progressContent.style.fontSize }
	    set percentage(pt) { 
	        if(String(pt).length) {
	            if(pt >= this.max) pt = this.max;
	            this.start = pt;
	            this.updateProgress(pt);
	            this.setAttribute("data-percentage", pt);
	        }
	    }
	    set height(val) {
	        if(val) {
	            this.progressWrapper.style.height = val;
	            let fontSize = parseInt(1/3 * (+String(val).replace("px", "")))
	            this.fontSize = fontSize + "px";
	        }
	    }
	    set width(val) {
	        if(val) {
	            this.progressWrapper.style.width = val;
	        }
	    }
	    set fontSize(val) {
	        if(val) {
	            this.progressContent.style.fontSize = val;
	        }
	    }
	}
	// 这个静态属性对 attributeChangedCallback 方法是必须的，只有在这个数组中列出的属性名才会触发对该方法的调用
	Progress.observedAttributes = ['innerText', 'value','data-percentage'];
	customElements.define('my-progress', Progress);
	
	export default Progress;
	
	
	```

5. 运行打包

	```shell
	# -c 表示使用配置文件，不加默认不使用
	yarn rollup -c
	```

6. 上传至npm

	> 准备：注册npm账号
	>
	> ```shell
	> # 取消淘宝镜像，设置npm官方地址，否则发布不了
	> 
	> # 查看npm配置
	> npm config list
	> 
	> # 如果设置了淘宝镜像，执行以下命令,一定要是https 否则会报错
	> npm config set registry=https://registry.npmjs.org
	> 
	> # 登录 npm 账户，提示🔗跳转至网页登录
	> npm adduser
	> 
	> # 检查是否登录，登录成功会显示你的用户名
	> npm who am i
	> 
	> # 发布, 此处如果报错403，重名错误。修改package.json name 属性。应为全网name不能重复。提交时可以增加 忽略文件 .gitignore
	> npm publish
	> ```
	>
	> 



> 结语： 插件写的很拉，主要是体验一把过程
