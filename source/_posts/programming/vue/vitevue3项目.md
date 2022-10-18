---
title:  vite + vue3 + tailwindcss
date: 2022-06-17 08:23:10
tags: vite
categories: vue
---

中前台项目 开发

## 

## 开发使用到的技术及开发插件

### vite

> 以速度运行制胜 webpack. 兼容 rollup 几乎所有插件，生态已经支持企业项目开发

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

```
{
    purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
    ...
}
```

<!--more-->

### vscode 插件

#### prettier-code formater

​	在根目录之下创建 .prettierrc, 配置如下

```
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

到 设置 中搜索 save 勾选保存时自动格式化文件

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

