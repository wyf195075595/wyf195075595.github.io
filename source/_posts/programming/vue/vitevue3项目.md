---
title:  vite + vue3 + tailwindcss
date: 2022-06-17 08:23:10
tags: vite
categories: vue
---

中前台项目 开发

项目架构

![image-20221208154045915](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221208154045915.png)

## 

## 开发使用到的技术及开发插件

### vite

> 以速度运行制胜 webpack. 兼容 rollup 几乎所有插件，生态已经支持企业项目开发

创建项目：

```js
npm init vite@latest
```

没有安装vite@latest会提示勾选安装

### vue3 

> vue 最新版，搭配 vite 使用更佳

### tailwindcss

> 前端 css 四种模式
>
> 1. 行内样式，自由度极高但复用性极差
> 2. 通用组件库，风格固定，自定义性差
> 3. 原子样式， tailwindcss 众多类名，每个类名都对应一个 css 样式，类似于boostrap
> 4. 经典模式， class 类名方式，但是媒体查询需要重写

安装

```
yarn add tailwindcss postcss@8.4.8 autoprefixer@10.4.2 -D
```

报错缺少依赖 sass

```
yarn add sass -D
```

生成配置文件 tailwind.config.js

```
npx tailwindcss init -p
```

配置文件 

```js
// purge: [], purge在3.0 更改为 content
{
    purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
    ...
}
    
// 最新
module.exports = {
  // purge: [], purge在3.0 更改为 content tailwind 应用范围
  content: [
    './index.html', 
    './src/**/*.{vue,js,ts,jsx,tsx}'
  ],
  theme: {
    extend: {
      height: {
        header: '72px',
        main: 'calc(100vh - 72px)'
      },
      fontSize: {
        xs: ['0.25rem', '0.35rem'],
        sm: ['0.35rem', '0.45rem'],
        base: ['0.42rem', '0.52rem'],
        lg: ['0.55rem', '0.65rem'],
        xl: ['0.65rem', '0.75rem']
      },
      boxShadow: {
        'l-white': '-10px 0 10px white',
        'l-zinc': '-10px 0 10px #18181b'
      },
      colors: {
        main: '#f44c58',
        'hover-main': '#f32836',
        'success-100': '#F2F9EC',
        'success-200': '#E4F2DB',
        'success-300': '#7EC050',
        'warn-100': '#FCF6ED',
        'warn-200': '#F8ECDA',
        'warn-300': '#DCA550',
        'error-100': '#ED7456',
        'error-200': '#f3471c',
        'error-300': '#ffffff'
      },
      backdropBlur: {
        '4xl': '240px'
      },
      variants: {
        scrollbar: ['dark']
      }
    },
  },
  plugins: [],
}

```

在src下新建styles文件夹， 增加 index.scss

```css
/* ./your-css-folder/styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

然后在mian.js中引用这个index.scss.tailwind CSS就引入成功了,

```js
import '@/styles/index.scss'
```

tailwind CSS 依赖scss,可能需要手动安装

```js
yarn add sass@1.54.4 -D
```

<!--more-->

### vscode 插件

#### prettier-code formater

> 对代码做格式化，统一代码风格

​	在根目录之下创建 .prettierrc, 配置如下

```css
{
	// 代码结尾不加分号
	"semi": false,
	// 优先单引号
	"singleQuote": true,
	// 不添加结尾逗号
	"trailingComma": "none"
}
```

配置默认使用

vscode 中 右键代码格式化，配置默认值为 prettier



设置保存自动生效

到 设置 中搜索 save,找到 Edit：Format OnSave选项 勾选保存时自动格式化文件

#### Tailwind CSS intelliSense

> 进行 Tailwind  类名提示， 在class 中按空格可提示类名

#### Vue language Featrues

> vue3官方推荐的插件



### 使用 vueuse 中的 useWindowSize 判断 当前PC端还是手机端

> 它为 Vue 开发人员提供了大量适用于 Vue 2 和 Vue 3 的基本 Composition API 实用程序函数。 
> 它为常见的开发人员用例提供了数十种解决方案，例如，跟踪引用更改、检测元素可见性、简化常见的 Vue 模式、键盘/鼠标输入等。这是真正节省开发时间的好方法，因为你不必自己添加所有这些标准功能。 

```js
import { computed } from "vue"; 
import { PC_DEVICE_WIDTH } from "@/constants";
import { useWindowSize } from '@vueuse/core'
const { width } = useWindowSize()

/**
 * 判断是否为移动设备， 依据为屏幕宽度是否小于一个指定宽度（1280）
 */

export const isMobileTerminal = computed(()=> {
    return width.value < PC_DEVICE_WIDTH
})
```

通过 上述 判断 动态切换 PC 端 合手机端 路由

在 router 的 index.js 中

```
const routes = [
  isMobileTerminal? mobileRoutes: pcRouter
]
```

### 安装axios 挂载全局并在子组件引用

挂载

```js
import axios from '@/utils/request'

const app = createApp(App);
app.use(store);
app.use(router);
app.config.globalProperties.$axios = axios;  //配置axios的全局引用
app.mount('#app');
```

使用

```js
// 方法一 start

import { getCurrentInstance } from 'vue'
const currentInstance = getCurrentInstance()
const { $axios } = currentInstance.appContext.config.globalProperties

$axios.get('https://www.fastmock.site/mock/a75253fac8aad5461c1f262f1515fc63/api/test')
    .then(res=> {
        console.log(res);
    })

// 方法二 start
  const { proxy } = getCurrentInstance()

  function getData() {
    proxy.$http({
      url: '/api/v1/posts/list'
    }).then(res=>{
      let { data } = res.data
      console.log(data)
    })
  }
```

### 环境变量设置

通过创建 .env.[mode] 文件， 可设置变量。在不同mode 下运行将读取不同的文件

开发环境

.env.developmenet

```
VITE_BASE_API = /api/
```

生产环境

.env.production

```
VITE_BASE_API = /prod-api/
```

读取变量

```
import.meta.env.VITE_BASE_API
```

### 开发代理设置

```js
server: {
    proxy: {
      '/api': {
        target: 'https://api.imooc-front.lgdsunday.club/',
        changeOrigin: true,
        // rewrite: 
      }
    }
  }
```

### 将 svg 注册成全局组件

vite 通过 使用 vite-plugin-svg-icons 插件使用 svg 图片

```
yarn add vite-plugin-svg-icons -D
```

在main.js 中

```
import 'virtual:svg-icons-register'
```

配置 vite.config.js

```

//import path,{ resolve } from 'path'
import path from 'path'
import {createSvgIconsPlugin} from 'vite-plugin-svg-icons'
 
export default defineConfig((command) => {
 return {
    plugins: [
      createSvgIconsPlugin({
        // 指定要缓存的文件夹
        iconDirs: [path.resolve(process.cwd(), 'src/assets/icons')],
        // 指定symbolId格式
        symbolId: '[name]'
      })
    ],
}
 
})
```

新增组件 icon-svg

```vue
<template>
  <svg aria-hidden="true">
    <use :class="fillClass" :href="symbolId" :xlink:href="symbolId" :fill="color" />
  </svg>
</template>

<script setup>
import {computed} from 'vue'

const props = defineProps({
    // 显示得SVG 图片名称
    name: {
        type: String,
        required: true
    },
    // 直接指定 SVG 图表颜色
    color: {
        type: String,
    },
    // 指定 svg 颜色的类名
    fillClass: {
        type: String
    }
})

const symbolId = computed(()=> `#icon-${props.name}`)
</script>
```

全局注册 组件， 使用

```js
import { defineAsyncComponent } from 'vue'
export default {
  install(app) {
    // 获取当前路径任意文件夹下的 index.vue 文件
    const components = import.meta.glob('./*/index.vue')
    // 遍历获取到的组件模块
    for (const [key, value] of Object.entries(components)) {
      // 拼接组件注册的 name
      const componentName = 'm-' + key.replace('./', '').split('/')[0]
      // 通过 defineAsyncComponent 异步导入指定路径下的组件
      app.component(componentName, defineAsyncComponent(value))
    }
  }
}
```

```
<m-svg-icon class="w-1.5 h-1.5" name="hamburger"></m-svg-icon>
```

### 全局指令注册

> 文件目录结构
>
> -directives
>
> --modules
>
> ---lazy.js
>
> --index.js

```js
// index.js

import {
    defineAsyncComponent
} from 'vue'

export default {
    install(app) {
        const directives =
            import.meta.globEager('./modules/*.js')
        for (const [key, value] of Object.entries(directives)) {
            // 拼接指令注册的 name
            console.log(key);
            const name = key.split('/')[2].replace('.js', '')
            // 通过 defineAsyncComponent 异步导入指定路径下的组件
            app.directive(name, defineAsyncComponent(value))
        }
    }
}
```



### 第三方平台登录

> QQ，微信。通过扫码授权等方式，用户同意之后会获取到第三方的用户信息。然后就需要在本平台进行判定
>
> 该第三方用户是否在本平台注册？因为我们拿到的第三方数据没有关键的用户名，密码，手机号。
>
> 1、当前用户已注册，就直接登录
>
> 2、当前用户未注册，执行注册。获取用户唯一标识，如手机号，不重复的用户名等
>
> 核心步骤：
>
> 1、对接第三方平台，获取第三方平台用户信息
>
> 2、利用该童虎信息，完成本应用的注册

#### qq

> [qq互联](https://connect.qq.com/manage.html#/) 需要qq登录认证开发者，创建应用。两者都需要认证。扫码登录时后跳转至一个新的页面，这个页面获取到用户数据后要传回之前的页面。要使用跨页面传输方式。 为保证兼容性使用 BroadcastChannel + localStroage + window.onstorage 两种方式兼容写法

Broadcast.js

```js
// 频道名
const LOGIN_SUCCESS_CHANNEL = "LOGIN_SUCCESS_CHANNEL";
class BroadcastUtils {
  constructor() {
    if (window.BroadcastChannel) {
      this.broadcastChannel = new BroadcastChannel(LOGIN_SUCCESS_CHANNEL);
    }
  }
  wait(callback) {
    if (this.broadcastChannel) {
      this.broadcastChannel.onmessage = (e) => {
        callback && callback(e.data);
      };
    } else {
      window.onstorage = (e) => {
        if (e.key === LOGIN_SUCCESS_CHANNEL) {
          callback && callback(JSON.parse(e.newValue));
        }
      };
    }
  }
  send(data) {
    if (this.broadcastChannel) {
      this.broadcastChannel.postMessage(data);
    } else {
      localStorage.setItem(LOGIN_SUCCESS_CHANNEL, JSON.stringify(data));
    }
  }
  clear() {
    if (this.broadcastChannel) {
      this.broadcastChannel.close();
    }
    localStorage.removeItem(LOGIN_SUCCESS_CHANNEL);
  }
}
```

> 从 第三方数据获取：
>
> 完整代码：
>
> ```js
> // QQ 登录的 URL
> const QQ_LOGIN_URL =
>   'https://graph.qq.com/oauth2.0/authorize?client_id=101998494&response_type=token&scope=all&redirect_uri=https%3A%2F%2Fimooc-front.lgdsunday.club%2Flogin'
> 
> 
> /**
>  * 处理 QQ 登录视窗
>  */
> const openQQWindow = async () => {
>   window.open(
>     QQ_LOGIN_URL,
>     'oauth2Login_10609',
>     'height=525,width=585, toolbar=no, menubar=no, scrollbars=no, status=no, location=yes, resizable=yes'
>   )
>   // 打开视窗之后开始等待
>   brodacast.wait().then(async (oauthObj) => {
>     // 登录成功,关闭通知
>     brodacast.clear()
>     // 执行登录操作
>     oauthLogin(LOGIN_TYPE_QQ, oauthObj)
>   })
> }
> 
> /**
>  * 登录按钮事件
>  */
> const onQQLogin = () => {
>   openQQWindow()
> }
> 
> // QQ 登录挂起
> onMounted(() => {
>   QC.Login(
>     {
>       btnId: 'qqLoginBtn' //插入按钮的节点id
>     },
>     // 登录成功之后的回调，但是需要注意，这个回调只会在《登录回调页面中被执行》
>     // 登录存在缓存，登录成功一次之后，下次进入会自动重新登录（即：触发该方法，所以我们应该在离开登录页面时，注销登录）
>     (data, opts) => {
>       console.log('QQ登录成功')
>       // 1. 注销登录，否则在后续登录中会直接触发该回调
>       QC.Login.signOut()
>       // 2. 获取当前用户唯一标识，作为判断用户是否已注册的依据
>       const accessToken = /access_token=((.*))&expires_in/.exec(
>         window.location.hash
>       )[1]
>       // 3. 拼接请求对象
>       const oauthObj = {
>         nickname: data.nickname,
>         figureurl_qq_2: data.figureurl_qq_2,
>         accessToken
>       }
>       // 4. 完成跨页面传输
>       brodacast.send(oauthObj)
> 
>       // 针对于 移动端而言：通过移动端触发 QQ 登录会展示三个页面，原页面、QQ 吊起页面、回调页面。并且移动端一个页面展示整屏内容，且无法直接通过 window.close() 关闭，所以在移动端中，我们需要在当前页面继续进行后续操作。
>       oauthLogin(LOGIN_TYPE_QQ, oauthObj)
>       // 5. 在 PC 端下，关闭第三方窗口
>       window.close()
>     }
>   )
> })
> ```
>
> 要获取的数据
>
> ```js
> const oauthObj = {
>     nickname: data.nickname,// 昵称
>     figureurl_qq_2: data.figureurl_qq_2,// 头像
>     accessToken // accessToken qq登录返回的唯一标识
>   }
> ```
>
> 继而访问登录接口，接口返回值返回状态标识用户是否注册。没注册则跳转至注册页面

**移动端没有窗口的概念，所以我们直接在回调页面执行登录**

针对于 移动端而言：通过移动端触发 QQ 登录会展示三个页面，原页面、QQ 吊起页面、回调页面。并且移动端一个页面展示整屏内容，且无法直接通过 window.close() 关闭，所以在移动端中，我们需要在当前页面继续进行后续操作。