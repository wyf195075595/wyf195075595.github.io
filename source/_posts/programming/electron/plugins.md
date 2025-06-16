---
title: electron 插件
date: 2022-06-17 08:23:10
tags: electron
categories: electron
---
## plugins

### @electron/remote

> 用来代替 electron remote 模块

```js
在主进程中

const remoteMain = require('@electron/remote/main');

// 初始化
remoteMain.initialize()

// 开启之后能在子进程访问主进程
remoteMain.enable(win.webContents)

在子进程中
const remote = require('@electron/remote');
```


<!--more-->


### electron-store

> 本地数据持久化的方案，通过文件的形式
>
> 门为Electron设计的，依赖的包很少的，很轻量的数据库，而且它还支持数据加密以防止数据被恶意用户窃取，甚至不需要你指定文件的路径和文件名，就直接把数据保存在用户的userData目录下。

```js
const Store = require('electron-store');
// 创建仓库引用，相同name引用同一个仓库
const fileStore = new Store({name: 'Files Data'});

fileStore.set('key', storeObj)
fileStore.get('key')
fileStore.delete('key')

store.set('foo.bar', true);               //可以级联设置JSON对象的值
console.log(store.get('foo'));            //输出 {bar: true}

```

### [electron-builder](https://github.com/electron-userland/electron-builder)



```js
完整配置项

"build": {
    "productName":"xxxx",//项目名 这也是生成的exe文件的前缀名
    "appId": "com.leon.xxxxx",//包名  
    "copyright":"xxxx",//版权  信息
    "directories": { // 输出文件夹
      "output": "build"
    }, 
    "nsis": {
      "oneClick": false, // 是否一键安装
      "allowElevation": true, // 允许请求提升。 如果为false，则用户必须使用提升的权限重新启动安装程序。
      "allowToChangeInstallationDirectory": true, // 允许修改安装目录
      "installerIcon": "./build/icons/aaa.ico",// 安装图标
      "uninstallerIcon": "./build/icons/bbb.ico",//卸载图标
      "installerHeaderIcon": "./build/icons/aaa.ico", // 安装时头部图标
      "createDesktopShortcut": true, // 创建桌面图标
      "createStartMenuShortcut": true,// 创建开始菜单图标
      "shortcutName": "xxxx", // 图标名称
      "include": "build/script/installer.nsh", // 包含的自定义nsis脚本
    },
    "publish": [
      {
        "provider": "generic", // 服务器提供商 也可以是GitHub等等
        "url": "http://xxxxx/" // 服务器地址
      }
    ],
    // 更新配置
    "files": [
      "dist/electron/**/*"
    ],
    // 其他平台配置
    "dmg": {
      "contents": [
        {
          "x": 410,
          "y": 150,
          "type": "link",
          "path": "/Applications"
        },
        {
          "x": 130,
          "y": 150,
          "type": "file"
        }
      ]
    },
    "mac": {
      "icon": "build/icons/icon.icns"
    },
    "win": {
      "icon": "build/icons/aims.ico",
      "target": [
        {
          "target": "nsis",
          "arch": [
            "ia32"
          ]
        }
      ]
    },
    "linux": {
      "icon": "build/icons"
    }
  }

报错如下：

electron 打包“ERR_ELECTRON_BUILDER_CANNOT_EXECUTE”

错误如下：
 errorOut=ERROR: Can not open output file : ▒ܾ▒▒▒▒ʡ▒ : C:\****\electron-builder\Cache\winCodeSign\384097343\windows-10\ia32\appxpackaging.dll

下载 electron-v9.4.4-win32-ia32.zip
https://npm.taobao.org/mirrors/electron/9.4.4/electron-v9.4.4-win32-ia32.zip
解压放到 C:\Users\Admin\AppData\Local\electron-builder\Cache 目录下

下载SHASUMS256.txt
https://npm.taobao.org/mirrors/electron/9.4.4/SHASUMS256.txt
重命名为SHASUMS256.txt 放到 C:\Users\Admin\AppData\Local\electron\Cache 目录下

下载 winCodeSign
https://npm.taobao.org/mirrors/electron-builder-binaries/winCodeSign-2.6.0/
解压放到C:\Users\Admin\AppData\Local\electron-builder\Cache\winCodeSign\winCodeSign-2.6.0目录下（
没有的话，自己创建winCodeSign-2.6.0）

下载/nsis-3.0.4.1
⨯ Get "https://github.com/electron-userland/electron-builder-binaries/releases/download/nsis-3.0.4.1/nsis-3.0.4.1.7z": dial tcp 52.74.223.119:443: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.

https://npm.taobao.org/mirrors/electron-builder-binaries/nsis-3.0.4.1/
解压放到C:\Users\Admin\AppData\Local\electron-builder\Cache\nsis\nsis-3.0.4.1 目录下（
没有的话，自己创建nsis-3.0.4.1）

最终路径为
--- electron
--Cache
-electron-v12.0.7-win32-x64.zip
-SHASUMS256.txt
---- electron-builder
---Cache
--nsis
-nsis-3.0.4.1
-nsis-resources-3.4.1
--winCodeSign
-winCodeSign-2.6.0
```



### electron-is-dev

> 判断当前环境是否开发环境

```js
const isDev = require('electron-is-dev')

if(isDev) {
    //开发环境
} else {
    //生产环境
}
```

### wait-on

> 执行下一条命令时等待上一条命令执行完.开发中使用 等待本地服务启动再打开electron 即页面不会还没加载，无需等待界面

### concurrently

> 并发运行任务， 如果一个进程失败,其他人仍然继续运行 你甚至不会注意到差别。
>
> 用来替换 npm run task1 && task2 语法

```js
package.json
{
    "electron:start": "concurrently \"npm run start\" \"wait-on http://localhost:3000 && electron .\"",
}
```



### fs-extra

```js
fs-extra库是对原生fs库的一层包装，它除了原封不动地暴露出fs库的所有API外，还额外增加了很多非常实用的API
```

### lowdb

```js
https://github.com/typicode/lowdb

    它是一个基于Lodash开发的小巧的JSON数据库。Lodash是一个非常强大且业内知名的JavaScript工具库，使用它可以快速高效地操作数组、JSON对象。
    
/创建数据访问对象
const low = require('lowdb')
const FileSync = require('lowdb/adapters/FileSync')
const adapter = new FileSync('db.json')
const db = low(adapter)
//查找数据
db.get('posts').find({ id: 1 }).value();
//更新数据
db.get('posts').find({ title: 'low!' }).assign({ title: 'hi!'}).write();
//删除数据
db.get('posts').remove({ title: 'low!' }).write();
//排序数据
db.get('posts').filter({published: true}).sortBy('views').take(5).value();
```

#### SQLite

```js
    SQLite是一个轻型的、嵌入式的SQL数据库引擎，其特点是自给自足、无服务器、零配置、支持事务。它是在世界上部署最广泛的SQL数据库引擎。大部分桌面应用都使用SQLite在客户端保存数据。
    
1、knexjs也是业内知名的数据库访问工具。

let knex = require('knex')({
    client: 'sqlite3',
    connection: { filename: yourSqliteDbFilePath }
});
CRUD操作样例代码：
//查找
let result = await knex('admins').where({id:0});
//排序
let result = await knex('users').orderBy('name', 'desc')
//更新
await knex('admins').where("id", 0).update({ password: 'test' });
//删除
await knex('addresses').whereIn("id", [0,1,2]).del();

2、GUI工具来管理你的SQLite3数据库
	有个人免费版
    SQLite Expert（http://www.sqliteexpert.com/）


【注意】
一个客户端应用直接访问这些数据库是非常危险的，一旦一个客户端被破解或者被嗅探到了数据库用户名和密码，那么你所有用户的数据都将受到威胁。

    一般情况下我们通过Web API来间接地访问这些数据库。所以如果你希望把用户数据保存在网络数据库内，那么你应该让后端开发人员给你提供Web API接口，并和后端开发人员协商好鉴权和身份验证的技术细节，再开始动手开发客户端。
```



#### [Sky/electron-vue-template](https://gitee.com/Zh-Sky/electron-vue-template?_from=gitee_search)

> 这是一个基于electron+vue+axios+elementui的跨平台桌面应用模板，内包含两个分支，作用如其名，无论是新手起步还是老鸟快速开坑，相信本项目都会给您带来最大程度的帮助~

#### [核桃钳/electron-react-quick-start](https://gitee.com/fddi/electron-react-quick-start?_from=gitee_search)

> electron+react 的脚手架项目。提供Electron + React 桌面程序创建，测试，打包的示例，Electron环境下调用DLL的示例。界面使用ant-design。

### [electron-store](https://github.com/sindresorhus/electron-store)

Electron 没有内置方法来保存用户设置和其他数据。此模块会为您处理这些问题，因此您可以专注于构建应用程序。数据保存在 中名为 config.json 的 JSON 文件中[`app.getPath('userData')`](https://electronjs.org/docs/api/app#appgetpathname)。

你可以在主进程和渲染进程中直接使用该模块。如果只在渲染进程中使用，则需要`Store.initRenderer()`在主进程中调用，或者`new Store()`在主进程中创建一个新的 Store 实例 ( )。

### [electron-devtools-installer](https://github.com/MarshallOfSound/electron-devtools-installer)

可以让electron 自动给浏览器安装指定拓展，只需提供拓展程序id ,内置了很多应用id

​	从技术上讲，您可以使用任何您想要的扩展程序。只需找到您要安装的扩展程序的 ChromeStore ID，然后调用`installExtension('YOUR_ID_HERE')`。我们在包内提供了几个扩展程序 ID，因此您可以轻松导入它们进行安装，而无需自己寻找它们。



## [Electron Forge](https://www.electronforge.io/)

用于构建和发布 Electron 应用的完整工具.这个脚手架提供了 vite + js, vite + ts 的版本模板。可直接集成 vue,react,打包等一系列操作，github ⭐6.7k。 下载依赖时记得配置 electron 镜像 

```bash
yarn config set electron_mirror "https://npmmirror.com/mirrors/electron/"
```

