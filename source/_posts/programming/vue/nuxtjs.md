---
title:  nuxtjs 基础
date: 2022-06-17 18:23:10
tags: nuxtjs
categories: vue
---

> 服务端渲染解决方案，视图层渲染基于vue.js

### 目录结构

> assets，components，layouts，middleware，pages，plugins，static，store，nuxt.config.js 文件，package.json 文件
>
> [详情参考官网1](https://www.nuxtjs.cn/guide/directory-structure)
>
> [详情参考官网2](https://www.nuxt.com.cn/docs/getting-started/seo-meta)

### nuxt.config.js 配置文件

> ### build，css，dev，env，generate，head，loading，modules，modulesDir，plugins，rootDir，router，server，srcDir，dir，transition
>
> [详情参考官网](https://www.nuxtjs.cn/guide/configuration)

<!--more-->

### 组件

> components 文件夹下的vue文件会自动注册为全局组件

```vue
<!--
	header
-->
<template>
  <div class="">header</div>
</template>
<!--
	footer
-->
<template>
  <div class="">footer</div>
</template>
```



### 路由

> nuxt 会根据 pages 文件夹自动生成路由。 

```vue
<!--
	app.vue
	
	NuxtPage: 类似于 router-view 路由出口
--> 
<div class="page">
    <Header />
    <NuxtPage />
    <Footer />
</div>
```



#### 路由跳转

```vue
<!--
	pages/index.vue: 此页面就是app.vue主路由出口的组件
	/about: pages/about.vue
	/about/us: pages/about/us.vue
-->
<NuxtLink to="/about">about page</NuxtLink><br/>
<NuxtLink to="/about/us">about us page</NuxtLink><br/>
```

#### 子路由嵌套

```vue
<!--
	pages/about.vue
	/post-38: pages/post-[id].vue
-->
<template>
  <div class="container">
    <h2>about us</h2>
    <NuxtLink to="/post-38">id page</NuxtLink>
    <!-- 子路由出口 -->
    <NuxtPage />
  </div>
</template>

<!--
	pages/about/us.vue
-->
<template>
    <NuxtLayout name="aside">
        <template #icon>myIcon</template>
        us page
    </NuxtLayout>
</template>
```

#### 动态路由

```vue
<NuxtLink to="/post-38">id page</NuxtLink>

<!--
	pages/post-[id].vue

    NuxtLayout: 使用模板	
-->
<template>
    <NuxtLayout name="aside">
        <template #icon>myIcon</template>
        us page
    </NuxtLayout>
</template>
```

#### 404路由

> pages/_.vue

### layouts 布局 模板

> 可以复用相同的组件，支持插槽

```vue
<!--
	layouts/aside.vue
-->
<template>
  <div class="aside">
    <slot name="icon">icon</slot>
    <p>name</p>
    <p>age</p>
    <p>sex</p>
    <slot />
    end
  </div>
</template>
```

### assets,public 静态文件

```html
<!-- assets 中图片 -->
<img src="~/assets/action1.jpg" alt="action1.jpg">
<!-- public 中图片 -->
<img src="/imgs/playgame.jpg" alt="playgame.jpg">
```



### 处理公共样式

> [参考文档](https://www.nuxt.com.cn/docs/getting-started/assets)
>
> 

样式文件放在 assets/_colors.scss

```scss
$primary: #49240F;
$secondary: #E4A79D;

.page-enter-active,
.page-leave-active {
  transition: all 0.4s;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  filter: blur(1rem);
}

.layout-enter-active,
.layout-leave-active {
  transition: all 0.4s;
}
.layout-enter-from,
.layout-leave-to {
  filter: grayscale(1);
}

.rotate-enter-active,
.rotate-leave-active {
  transition: all 0.4s;
}
.rotate-enter-from,
.rotate-leave-to {
  opacity: 0;
  transform: rotate3d(1, 1, 1, 15deg);
}

.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: all 0.2s;
}
.slide-left-enter-from {
  opacity: 0;
  transform: translate(50px, 0);
}
.slide-left-leave-to {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-enter-from {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-leave-to {
  opacity: 0;
  transform: translate(50px, 0);
}
```

配置 nuxt.config.js

```js
export default defineNuxtConfig({
  vite: {
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: '@use "@/assets/_colors.scss" as *;'
        }
      }
    }
  }
})

```



### 过渡

> 过渡动画。页面过渡，布局过渡

全局页面设置在过渡

```js
export default defineNuxtConfig({
    app: {
        pageTransition: { name: 'page', mode: 'in-out' },
        layoutTransition: { name: 'layout', mode: 'in-out' }
    }
})

```

#### 页面单独设置过渡

> 此设置会覆盖全局配置

```js
definePageMeta({
  pageTransition: {
    name: 'slide-right',
    mode: 'out-in'
  }
})
```

#### 页面动态设置过渡效果

> 通过中间件 监听路由，动态设置 过渡动画

```js
definePageMeta({
  pageTransition: {
    name: 'slide-right',
    mode: 'out-in'
  },
  middleware (to, from) {
    to.meta.pageTransition.name = +to.params.id > +from.params.id ? 'slide-left' : 'slide-right'
  }
})
```



### 路由中间件

> 所有中间件都放在 middleware 文件夹下

```js
// auth.js	路由中间件
export default defineNuxtRouteMiddleware((to, from) => {
  // 判断组件校验是否通过
  if (isAuthenticated() === false) {
    return navigateTo('/')
  }
})

// 停止导航
abortNavigation()
// 重定向
navigateTo()
// 页面路由鉴权结果
isAuthenticated()


```

在页面中判断是否通过校验

> 通过 页面参数 validate ,返回 true 进入，返回false 执行 navigateTo('/') 重定向到首页

```js
definePageMeta({
  validate: async (route) => {
    const nuxtApp = useNuxtApp();
    console.log(nuxtApp);
    // 检查是否 有id
    return /^\d+$/.test(route.params.id)
  },
  
})
```

单页面中间件

```js
// 只在当前页面生效
definePageMeta({
    middleware: ['middleware 中定义的中间件']
    middleware:(to,from) => {
    	// 匿名中间件
    }
})

```





### 公共方法

> **composables** 文件夹下所有第一层文件暴露的 方法都会被放到 公共方法 注入到所有页面，可直接使用。
>
> 如果此名录有文件夹，nuxt只会读取 第二层文件夹中的 index.js



### 获取数据

> nuxt 提供了 4种获取数据方法。底层其实是封装了axios
>
> 1. useFetch **√**
> 2. useAsyncData**√**
> 3. useLazyFetch , 相当于在上面方法加参数 lazy: true
> 4. useLazyAsyncData  , 相当于在上面方法加参数 lazy: true
>
> [详情查看文档](https://www.nuxt.com.cn/docs/getting-started/data-fetching)

```vue
<script setup>
	const { data: count } = await useFetch('/api/count')
</script>
<template>
  Page visits: {{ count }}
</template>

--------------------------------------------------------------
<script setup>
    const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>
<template>
  Page visits: {{ data }}
</template>
```

