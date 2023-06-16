---
title: koa 基础语法
date: 2022-06-17 18:23:10
tags: koa
categories: koa
---

## 一、创建koa项目

```js
//1、新建node项目
新建文件夹 进入目录 执行
npm init
生成package.json文件
//2、项目引入koa
npm i koa -S
//3、新建app.js
const Koa = require('koa');
const app = new Koa();

app.listen(3000,()=>{
	console.log('port 3000 is running!')
});
//运行 node app.js
```

## 二、中间件

<!--more-->

```js
const Koa = require('koa');
const app = new Koa();

// 对于任何请求，app将调用该异步函数处理请求：
app.use(async (ctx, next) => {
    await next();
    ctx.response.type = 'text/html';
    ctx.response.body = '<h1>Hello, koa2!</h1>';
});
//但是为什么要调用await next()？
//原因是koa把很多async函数组成一个处理链，每个async函数都可以做一些自己的事情，然后用await next()来调用下一个async函数。
app.listen(3000);
```

### 实例运用

```js
//统一接口数据返回格式
//routerResponse.js
 function routerResponse(option = {}){
    return async function (ctx , next ){
        ctx.success = function(data){
            ctx.type = option.type || 'json';
            ctx.body = {
                code : option.successCode || 200,
                msg : option.successMsg || 'success',
                data : data
            }
        }
        ctx.fail = function (msg,code){
            ctx.type = option.type || 'json';
            ctx.body = {
                code : code ||option.failCode || 0,
                msg : msg || option.successMsg || 'fail'
            }
        }
        await next();
    }
}

module.exports = routerResponse
//app.js引用
const routerResponse = require('./middleware/routerResponse')
app.use(routerResponse({
    code:200,
    ...
}))
```



## 三、常用的中间件

### koa-router 路由管理

```js
//koa-router 路由管理
npm i koa-router -S
//引用
const router = require('koa-router')();
//添加路由
router.post('/sigin', async(ctx,next)=>{
    ...
})
router.get('/getUserInfo', async(ctx,next)=>{
    ...
})
app.use(router.routes());
```

#### 路由管理实例

```js
//实例目录结构
--KoaTest
	--controllers
    	--login.js
	--maddleware
    	--controllers.js
	--app.js
-------------------------------------------------------------------------------------------------------
//login.js 被注册的路由
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

module.exports = {
    'GET /': fn_index,
    'POST /signin': fn_signin
};

//controllers.js 动态扫描文件注册路由
const fs = require('fs');
const path = require('path')
function addMapping(router, mapping) {
    for (var url in mapping) {
        if (url.startsWith('GET ')) {
            var path = url.substring(4);
            router.get(path, mapping[url]);
        } else if (url.startsWith('POST ')) {
            var path = url.substring(5);
            router.post(path, mapping[url]);
        } else {
            console.log(`invalid URL: ${url}`);
        }
    }
}
function addControllers(router) {
    var files = fs.readdirSync(path.join(__dirname,'../','/'+controllers_dir));
    var js_files = files.filter((f) => {
        return f.endsWith('.js');
    });
    for (var f of js_files) {
        console.log(`process controller: ${f}...`);
        let mapping = require(path.join(__dirname,'../','/'+controllers_dir+'/') + f);
        addMapping(jwt,router, mapping);
    }
}
module.exports = function (dir) {
    let controllers_dir = dir || 'controllers', // 如果不传参数，扫描目录默认为'controllers'
        router = require('koa-router')();
    addControllers(router, controllers_dir);
    return router.routes();
};

//app.js
const controller = require('./middleware/controllers');
app.use(controller());
```



### koa-static 静态资源目录

```js
//koa-static 静态资源目录
npm i koa-static -S
//引用
const static = require('koa-static');
//可创建多个
app.use(static((__dirname + '/static')));
```

### koa-bodyparser  获取post请求参数

```js
//koa-bodyparser 获取post请求参数
npm i koa-bodyparser -S
//引用
const bodyparser = require('koa-bodyparser');
app.use(bodyparser())
//使用
ctx.request.body.password //即可获取post传值
```

### koa-cors 跨域资源处理

```js
const cors = require('koa-cors');
app.use(cors());
```

### koa-send 文件下载

```js
const send = require('koa-send');

var fn_downLoadTest = async (ctx, next) => {
    const name = '0.4669277812174879.doc';
    const dir = path.join(__dirname,'../','/static/upload/');
	ctx.attachment(name);//下载文件名称
    try {
        await send(ctx, name, {root: dir})
    } catch (error) {
      ctx.throw(404, '文件不存在')
    }
};
```

### archiver 文件打包下载

```js
const router = require('koa-router')();
 
const send = require('koa-send');
 
const archiver = require('archiver');
 
router.post('/downloadAll', async (ctx){
// 将要打包的文件列表
    const list = [{name: '1.txt'},{name: '2.txt'}];
    const zipName = '1.zip';
    const zipStream = fs.createWriteStream(zipName);
    const zip = archiver('zip');
    zip.pipe(zipStream);
    for (let i = 0; i < list.length; i++) {
        // 添加单个文件到压缩包
        zip.append(
            fs.createReadStream(list[i].name), 
            { name: list[i].name }
        )
    }
    await zip.finalize();
    ctx.attachment(zipName);
    await send(ctx, zipName);
 
})
```

如果直接打包整个文件夹，则不需要去遍历每个文件 append 到压缩包里

```js
const zipStream = fs.createWriteStream('1.zip');
const zip = archiver('zip');
zip.pipe(zipStream);
// 添加整个文件夹到压缩包
zip.directory('upload/');
zip.finalize();
```

> 注意：打包整个文件夹，生成的压缩包文件不可存放到该文件夹下，否则会不断的打包。
>
> [参考文档](https://blog.csdn.net/qq_39197547/article/details/81316856)



### koa-body文件上传

```js
const {koaBody } = require('koa-body');
//处理上传文件
app.use(koaBody({
    multipart: true,//多文件
    formidable: {
        maxFileSize: 200*1024*1024	// 设置上传文件大小最大限制，默认2M
    }
}))


var fn_uploadTest = async (ctx, next) => {
    const file = ctx.request.files.file;	// 获取上传文件
	const reader = fs.createReadStream(file.path);	// 创建可读流
    const ext = file.name.split('.').pop();	// 获取上传文件扩展名
    const reletivePath = `/${(Math.random()*(10**16)).toString()}.${ext}` 
    const filePath = path.join(__dirname,'../', '/static/upload') + reletivePath;
	const upStream = fs.createWriteStream(filePath);// 创建可写流
	reader.pipe(upStream);	// 可读流通过管道写入可写流
    ctx.success({
        data:'上传成功',
        path:reletivePath,
    })
};
```

### koa-range  解决网络视频地址不能拖动进度条

```js
const range = require('koa-range');

app.use(range);
```

### koa-jwt  + webjsontoken 用户权鉴

```js
//引入
const jwt = require('koa-jwt')({secret:config.secret});

//登录生成token
const {sign} = require("jsonwebtoken");

const token = sign({username, test: 'testok'}, secret, {expiresIn: '1h'});
        ctx.body = {
            mssage: 'GET TOKEN SUCCESS',
            code: 1,
            token
        }
//路由权鉴
router.get('/viplist', jwt, async (ctx, next) => {
    console.log(ctx.state)
    ctx.body = 'check ok'
})

app
    .use(router.routes())
    .use(router.allowedMethods())

//前端传入token
// 请求拦截器
service.interceptors.request.use((config) => {
    const token = localStorage.getItem('userState')?JSON.parse(localStorage.getItem('userState')).token:'';
    config.headers.authorization = 'Bearer ' + token;
	return config
}, (error) => {
	// 错误抛到业务代码
    error.data = {}
    error.data.msg = '服务器异常，请联系管理员！'
    return Promise.resolve(error)
})
```



## 五、使用mongoose连接mongodb

```js
const mongoose = require('mongoose');
const config = require('../config_key');

mongoose
	//('路径',{关闭某些提示})
    .connect(config.mongoURI,{useNewUrlParser:true,useUnifiedTopology:true})
    .then(()=>{
        console.log('Mongodb Connected...')
    })
    .catch(err=>{
        console.log(err)
    })
module.exports = mongoose;

//数据库交互与express不同
//koa中
let data = {}
data= await userModel.findOne({name});
...
ctx.response.body = {
    code:200,
    msg:'xxx',
    data:xxx
}
//express中
userModel.findOne({name},function(err,data){
    ...
    res.json({
        code:200,
        msg:'xxx',
        data:xxx
    })
})
```



## koa 获取参数

1、获取 query (问好后面的内容)

```js
ctx.query
```

2、获取路由参数 （/user/:id 的 id）

```
ctx.param
```

3、获取请求体参数

> 需要借助中间件 koa-bodyparser

4、获取header

```
ctx.header.content-type
```

