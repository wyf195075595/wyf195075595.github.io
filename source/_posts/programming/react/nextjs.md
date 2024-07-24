---
title:  nextjs基础
date: 2024-06-25 08:23:10
tags: 服务端渲染
categories: react
---

​	Next.js是一个基于React的开源JavaScript框架，旨在简化React应用程序的开发流程，并提供更好的性能和开发体验。

​	它的诞生背景是基于开发者和企业对构建更快、更优化的Web应用程序的需求，特别是在服务端渲染（SSR）和搜索引擎优化（SEO）方面。

[官方文档](https://nextjs.org/docs)

<!--more-->

## 目录结构

```jsx
#顶级文件夹 用于组织应用程序的代码和静态资产。
app	应用路由器
pages	页面路由器
public	要服务的静态资产
src	可选应用程序源文件夹

#顶级文件 用于配置您的应用程序、管理依赖项、运行中间件、集成监控工具以及定义环境变量。
next.config.js	Next.js 的配置文件
package.json	项目依赖项和脚本
instrumentation.ts	OpenTelemetry 和 Instrumentation 文件
middleware.ts	Next.js 请求中间件
.env	环境变量
.env.local	本地环境变量
.env.production	生产环境变量
.env.development	开发环境变量
.eslintrc.json	ESLint 的配置文件
.gitignore	要忽略的 Git 文件和文件夹
next-env.d.ts	Next.js 的 TypeScript 声明文件
tsconfig.json	TypeScript 的配置文件
jsconfig.json	JavaScript 的配置文件
```

## 路由约定

### app路由

放置在app文件夹下的路由文件，此模式在nextjs13+版本支持

#### [路由文件](https://nextjs.org/docs/getting-started/project-structure#routing-files)

|                                                              |                     |                  |
| ------------------------------------------------------------ | ------------------- | ---------------- |
| [`layout`](https://nextjs.org/docs/app/api-reference/file-conventions/layout) | `.js` `.jsx` `.tsx` | 布局             |
| [`page`](https://nextjs.org/docs/app/api-reference/file-conventions/page) | `.js` `.jsx` `.tsx` | 页               |
| [`loading`](https://nextjs.org/docs/app/api-reference/file-conventions/loading) | `.js` `.jsx` `.tsx` | 加载界面         |
| [`not-found`](https://nextjs.org/docs/app/api-reference/file-conventions/not-found) | `.js` `.jsx` `.tsx` | 未找到用户界面   |
| [`error`](https://nextjs.org/docs/app/api-reference/file-conventions/error) | `.js` `.jsx` `.tsx` | 错误用户界面     |
| [`global-error`](https://nextjs.org/docs/app/api-reference/file-conventions/error#global-errorjs) | `.js` `.jsx` `.tsx` | 全局错误用户界面 |
| [`route`](https://nextjs.org/docs/app/api-reference/file-conventions/route) | `.js` `.ts`         | API 端点         |
| [`template`](https://nextjs.org/docs/app/api-reference/file-conventions/template) | `.js` `.jsx` `.tsx` | 重新渲染布局     |
| [`default`](https://nextjs.org/docs/app/api-reference/file-conventions/default) | `.js` `.jsx` `.tsx` | 并行路由回退页面 |

页面结构

```
-app
--testPage
---layout
---page
...
--otherPage
---layout
---page
...
```

#### [嵌套路由](https://nextjs.org/docs/getting-started/project-structure#nested-routes)

文件夹嵌套方式自动形成路由

|                                                              |            |
| ------------------------------------------------------------ | ---------- |
| [`folder`](https://nextjs.org/docs/app/building-your-application/routing#route-segments) | 航段       |
| [`folder/folder`](https://nextjs.org/docs/app/building-your-application/routing#nested-routes) | 嵌套路线段 |

#### [动态路线](https://nextjs.org/docs/getting-started/project-structure#dynamic-routes)

|                 |              |
| --------------- | ------------ |
| `[folder]`      | 动态路由     |
| `[...folder]`   | 捕获所有路由 |
| [`[...folder]`] | 可选的路由   |

1. 第一种动态路由：

	页面：app/testPage/[id].tsx

	路径： /testPage/15

	```tsx
	# params {"id":"1"}
	export default function TestPage({ params }: {params: { id: string|number }}) {
	  return (
	    <div>Blog {params.id}</div>
	  )
	}
	```

	

2. 第一种动态路由： 

	页面：app/testPage/[...id].tsx

	路径： /testPage/15/16/17

	```tsx
	# params  {"id":["1","2","3","4"]}
	export default function TestPage({ params }: {params: { id: string|number }}) {
	  return (
	    <div>Blog {params.id}</div>
	  )
	}
	```

3. 第三种动态路由： 

	页面：app/testPage/[[...id]].tsx

	路径： /testPage、 /testPage/15、 /testPage/15/16/17 都能匹配

	```json
	{ } // GET `/testPage` (empty object)
	{ "id": ["15"] } // `GET /testPage/15` (single-element array)
	{ "id": ["15", "16"，"17"] } // `GET /testPage/15/16/17` (multi-element array)
	```

	

#### [路由组和私人文件夹](https://nextjs.org/docs/getting-started/project-structure#route-groups-and-private-folders)

|                                                              |                              |
| ------------------------------------------------------------ | ---------------------------- |
| [`(folder)`](https://nextjs.org/docs/app/building-your-application/routing/route-groups#convention) | 不影响路由的分组路由         |
| [`_folder`](https://nextjs.org/docs/app/building-your-application/routing/colocation#private-folders) | 选择文件夹和所有子段退出路由 |

1. 分组路由 会生成根路由，不受主路由、主布局文件影响，一般用于多页面路由配置

#### [平行和拦截路由](https://nextjs.org/docs/getting-started/project-structure#parallel-and-intercepted-routes)

|                                                              |              |
| ------------------------------------------------------------ | ------------ |
| [`@folder`](https://nextjs.org/docs/app/building-your-application/routing/parallel-routes#slots) | 命名槽       |
| [`(.)folder`](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes#convention) | 拦截同级别   |
| [`(..)folder`](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes#convention) | 拦截高一级   |
| [`(..)(..)folder`](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes#convention) | 拦截两级以上 |
| [`(...)folder`](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes#convention) | 从根目录拦截 |



### [`pages`路由约定](https://nextjs.org/docs/getting-started/project-structure#pages-routing-conventions)

以下文件约定用于定义[`pages`路由器](https://nextjs.org/docs/pages)中的路由。

#### [特殊文件](https://nextjs.org/docs/getting-started/project-structure#special-files)

|                                                              |                     |                |
| ------------------------------------------------------------ | ------------------- | -------------- |
| [`_app`](https://nextjs.org/docs/pages/building-your-application/routing/custom-app) | `.js` `.jsx` `.tsx` | 自定义应用程序 |
| [`_document`](https://nextjs.org/docs/pages/building-your-application/routing/custom-document) | `.js` `.jsx` `.tsx` | 自定义文档     |
| [`_error`](https://nextjs.org/docs/pages/building-your-application/routing/custom-error#more-advanced-error-page-customizing) | `.js` `.jsx` `.tsx` | 自定义错误页面 |
| [`404`](https://nextjs.org/docs/pages/building-your-application/routing/custom-error#404-page) | `.js` `.jsx` `.tsx` | 404 错误页面   |
| [`500`](https://nextjs.org/docs/pages/building-your-application/routing/custom-error#500-page) | `.js` `.jsx` `.tsx` | 500 错误页面   |

#### 路由文件

| **文件夹约定**                                               |                     |          |
| ------------------------------------------------------------ | ------------------- | -------- |
| [`index`](https://nextjs.org/docs/pages/building-your-application/routing/pages-and-layouts#index-routes) | `.js` `.jsx` `.tsx` | 主页     |
| [`folder/index`](https://nextjs.org/docs/pages/building-your-application/routing/pages-and-layouts#index-routes) | `.js` `.jsx` `.tsx` | 嵌套页面 |
| **文件约定**                                                 |                     |          |
| [`index`](https://nextjs.org/docs/pages/building-your-application/routing/pages-and-layouts#index-routes) | `.js` `.jsx` `.tsx` | 主页     |
| [`file`](https://nextjs.org/docs/pages/building-your-application/routing/pages-and-layouts) | `.js` `.jsx` `.tsx` | 嵌套页面 |

#### 动态路由

规则与app路由类似

| **文件夹约定**       |                     |                  |
| -------------------- | ------------------- | ---------------- |
| [folder\]/index      | `.js` `.jsx` `.tsx` | 动态路线段       |
| [...folder\]/index   | `.js` `.jsx` `.tsx` | 捕获所有路线段   |
| [[...folder\]]/index | `.js` `.jsx` `.tsx` | 可选的综合路线段 |
| **文件约定**         |                     |                  |
| [file\]              | `.js` `.jsx` `.tsx` | 动态路线段       |
| [...file\]           | `.js` `.jsx` `.tsx` | 捕获所有路线段   |
| [[...file\]]         | `.js` `.jsx` `.tsx` | 可选的综合路线段 |



### [元数据文件约定](https://nextjs.org/docs/getting-started/project-structure#metadata-file-conventions)

#### [应用程序图标](https://nextjs.org/docs/getting-started/project-structure#app-icons)

|                                                              |                                     |                           |
| ------------------------------------------------------------ | ----------------------------------- | ------------------------- |
| [`favicon`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons#favicon) | `.ico`                              | 网站图标文件              |
| [`icon`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons#icon) | `.ico` `.jpg` `.jpeg` `.png` `.svg` | 应用程序图标文件          |
| [`icon`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons#generate-icons-using-code-js-ts-tsx) | `.js` `.ts` `.tsx`                  | 生成的应用程序图标        |
| [`apple-icon`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons#apple-icon) | `.jpg` `.jpeg`，`.png`              | 苹果应用程序图标文件      |
| [`apple-icon`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons#generate-icons-using-code-js-ts-tsx) | `.js` `.ts` `.tsx`                  | 生成的 Apple 应用程序图标 |

#### [Open Graph 和 Twitter 图片](https://nextjs.org/docs/getting-started/project-structure#open-graph-and-twitter-images)

|                                                              |                              |                        |
| ------------------------------------------------------------ | ---------------------------- | ---------------------- |
| [`opengraph-image`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image#opengraph-image) | `.jpg` `.jpeg` `.png` `.gif` | 打开 Graph 图像文件    |
| [`opengraph-image`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image#generate-images-using-code-js-ts-tsx) | `.js` `.ts` `.tsx`           | 生成的 Open Graph 图像 |
| [`twitter-image`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image#twitter-image) | `.jpg` `.jpeg` `.png` `.gif` | Twitter 图像文件       |
| [`twitter-image`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image#generate-images-using-code-js-ts-tsx) | `.js` `.ts` `.tsx`           | 生成的 Twitter 图像    |

#### [搜索引擎优化](https://nextjs.org/docs/getting-started/project-structure#seo)

|                                                              |             |                    |
| ------------------------------------------------------------ | ----------- | ------------------ |
| [`sitemap`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap#sitemap-files-xml) | `.xml`      | 网站地图文件       |
| [`sitemap`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap#generating-a-sitemap-using-code-js-ts) | `.js` `.ts` | 生成的网站地图     |
| [`robots`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots#static-robotstxt) | `.txt`      | 机器人文件         |
| [`robots`](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots#generate-a-robots-file) | `.js` `.ts` | 生成的 Robots 文件 |



### loading状态

组件数据加载时，展示loading状态

```tsx
# app/loading.tsx
export default function Loading() {
  // You can add any UI inside Loading, including a Skeleton.
  return (<div>loading...</div>)
}


# app/layout.tsx
import Loading from './loading';
import { Suspense} from 'react';
export default function RootLayout({
  children,
}: {
  children: React.ReactNode	
}) {
  return (
    <html lang="en">
      <body >
      <Suspense fallback={<Loading />}>
        root layout
        {children}
        </Suspense>
      </body>
    </html>
  )
}

```

### 错误处理

​	组件页面中的错误捕获【只能处理客户端错误】，错误组件可以使用该`reset()`函数提示用户尝试从错误中恢复。执行时，该函数将尝试重新渲染错误边界的内容。如果成功，则后备错误组件将替换为重新渲染的结果。

app/testPage/error.tsx

```tsx
'use client' // Error components must be Client Components
 
import { useEffect } from 'react'
 
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])
 
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={
          // Attempt to recover by trying to re-render the segment
          () => reset()
        }
      >
        重试
      </button>
    </div>
  )
}
```

根`app/error.js`边界不会**捕获**根`app/layout.js`或`app/template.js`组件中引发的错误。

为了专门处理这些根组件中的错误，请使用位于根目录中`error.js`的变体。`app/global-error.js``app`

```tsx
'use client'
 
export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    <html>
      <body>
        <h2>Something went wrong!</h2>
        <button onClick={() => reset()}>Try again</button>
      </body>
    </html>
  )
}
```

与根`error.js`不同，`global-error.js`错误边界包裹**整个**应用程序，其后备组件在活动时替换根布局。因此，需要注意的是`global-error.js` **必须**定义自己的`<html>`和`<body>`标签。

- `global-error.js`仅在生产环境中启用。在开发环境中，将显示我们的错误覆盖。

**[处理服务器错误](https://nextjs.org/docs/app/building-your-application/routing/error-handling#handling-server-errors)**

如果服务器组件内部抛出错误，Next.js 会将一个`Error`对象（在生产中去除敏感错误信息）`error.js`作为`error`prop 转发到最近的文件。

### 平行路由

并行路由允许您同时或有条件地在同一布局中渲染一个或多个页面。它们对于应用程序中高度动态的部分非常有用，例如仪表板和社交网站上的提要。

**并行路由使用命名插槽**创建。插槽按照惯例定义`@folder`。例如，以下文件结构定义了两个插槽：`@analytics`和`@team`：

app/@analytics/page.tsx

app/@team`/page.tsx

```tsx
export default function RootLayout({
  children,
  team,
  analytics,
}: {
  children: React.ReactNode
  analytics: React.ReactNode
  team: React.ReactNode
}) {
  return (
    <>
      {children}
      {team}
      {analytics}
    </>
  )
}
```

**插槽 default.tsx** 

如果访问的路由页面没有layout来继承 RootLayout ,则新页面无处安放插槽导致页面404，需要配置default,tsx

```tsx
export default function Default() {
    return null;
}
```



插槽路由不会影响其他路由

![包含两个子页面和一个布局的分析槽](https://nextjs.org/_next/image?url=%2Fdocs%2Flight%2Fparallel-routes-tab-groups.png&w=3840&q=75)

app/analytics/layout 中可以切换 页面 page-view, visitory 不影响其他视图

```jsx
import Link from 'next/link'
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <nav>
        <Link href="/page-views">Page Views</Link>
        <Link href="/visitors">Visitors</Link>
      </nav>
      <div>{children}</div>
    </>
  )
}
```



### 拦截路由

例如，当点击 feed 中的照片时，您可以在模态框中显示该照片，覆盖 feed。在这种情况下，Next.js 会拦截路由`/photo/123`、屏蔽 URL，并将其覆盖在 feed 上`/feed`。

![拦截路线软导航](https://nextjs.org/_next/image?url=%2Fdocs%2Flight%2Fintercepting-routes-soft-navigate.png&w=3840&q=75)

路由拦截与插槽可以实现弹窗效果

### 路由处理与中间件

这是 nextjs 内置的请求工具，与axios 功能类似



## 渲染

### 静态渲染



### 动态渲染