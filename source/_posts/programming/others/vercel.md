---
title: vercel 部署koa项目
date: 2023-04-21 08:23:10
tags: vercel
categories: other
---

> vercel 不仅能够部署静态网站，还能部署node项目。这次就部署koa项目作为尝试。



### 项目目录

> 新建koa项目，目录如下

![image-20230421090353337](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230421090353337.png)

#### 入口文件

> 入口文件一定要是index.js,如果改写其他如 app.js,main.js 等等 vercel 均无法识别，这是一个大坑.
>
> koa 监听端口不能是 3000 ,因为vercel 默认是 3000 端口

```js
const Koa = require('koa');
const app = new Koa();

// 跨域处理
const cors = require('koa-cors');
app.use(cors());

// 设置静态资源
const static = require('koa-static');
app.use(static(__dirname+ '/static'))


// 设置响应格式
const routerResponse = require('./middleware/routerResponse');
app.use(routerResponse({
    code:200,
}))

// 获取post请求参数
const bodyparser = require('koa-bodyparser');
app.use(bodyparser())

// 注册路由
const controller = require('./middleware/controllers');
app.use(controller());


app.listen(3030,()=>{
	console.log('port 3030 is running!')
});
```

#### 路由文件

> 因为没有数据库，路由中使用axios请求了接口获取数据

```js
const http = require("../utils/http");
var fn_index = async (ctx, next) => {
    ctx.response.body = `<h1>Index</h1>
        <form action="/signin" method="post">
            <p>Name: <input name="name" value="koa"></p>
            <p>Password: <input name="password" type="password"></p>
            <p><input type="submit" value="Submit"></p>
        </form>`;
};

var fn_signin = async (ctx, next) => {
    var name = ctx.request.body.name || '',
        password = ctx.request.body.password || '';
    if (name === 'koa' && password === '12345') {
        ctx.success({
            ok:'xxxxx'
        })
    } else {
        ctx.response.body = `<h1>Login failed!</h1>
        <p><a href="/">Try again</a></p>`;
    }
};

var fetchData = async (ctx, next) => {
    let rs = await http.get("https://www.fastmock.site/mock/cc1aeeec1b278c3c30ec60eeaf462247/front/getPicList")
    ctx.success({
        data: rs
    })
}

module.exports = {
    'GET /login': fn_index,
    'GET /getData': fetchData,
    'POST /signin': fn_signin
};
```

> koa项目搭建好后可以在本地运行，访问静态资源，接口没有问题后。就开始改造

### 部署准备

> 部署到 vercel 需要做一些配置

1. package.json 新增 script

	```js
	# vercel 部署时会用到
	"build": "node index.js"
	```

2. 安装 @vercel/node

	```shell
	# 必备
	yarn add @vercel/node
	```

3. 新建vercel.json

	```js
	# 覆盖 vercel 默认行为
	{
	    "version": 2,
	    "builds": [
	      {
	        "src": "index.js",
	        "use": "@vercel/node"
	      }
	    ]
	  }
	```

4. 安装vercel 并登录

	```shell
	# 安装 vercel
	npm i vercel -g
	# 登录 Vercel 账号。
	vercel login
	
	
	------------------其他命令----------------------
	# 本地开启服务。
	vercel dev
	# 本地开启服务并打印日志。
	vercel dev --bug
	# 部署本地资源到 Vercel 上。
	vercel
	# 更新本地网页。
	vercel --prod
	
	vercel 可以用 vc 来代替，vc 是 Vercel 的缩写。
	```

### 部署

```shell
vercel
```

![vercel](https://s9.51cto.com/oss/202205/10/d78f532685481bcc147866bb9c8a2276277074.png)

```js
# 更新资源
vercel --prod
```

> [此处参考文章](https://www.51cto.com/article/708495.html)
>
> 此处操作vercel生成vercel-koa2 但是操作后代码并没有上传至 vercel, 还是得将代码提交到 github 再关联到 vercel

### 访问

```shell
# 访问地址，默认会访问到 static 中得 index,而不是根目录得index.html
https://vercel-koa-three.vercel.app/

# 查看获取数据得接口
https://vercel-koa-three.vercel.app/getData
```

> 注意接口 注册时不要使用 /, 因为/ 会默认访问 static/index.html
>
> [项目源码](https://github.com/wyf195075595/koa-vercel)

