---
title:  react 插件
date: 2022-06-17 08:23:10
tags: 插件
categories: react
---
### react-loadable

React 项目打包时，如果不进行异步组件的处理，那么所有页面所需要的 js 都在同一文件中（bundle.js），整个js文件很大，从而导致首屏加载时间过长。

------

所有，可以对组件进行异步加载处理，通常可以使用 `React-loadable`。

<!--more-->

```js
一个高阶组件与动态加载组件进口。
// loadable.js

import React from 'react';
import Loadable from 'react-loadable';

export default (loader) => {
    return Loadable({
        loader,
        loading() {
            return <div>正在加载</div>
        },
    });
}

// app.js

import loadable from './utils/loadable';
import Header from './common/header'; 

<!--异步加载组件-->
const Home = loadable(() => import('./pages/home'));
const Detail = loadable(() => import('./pages/detail'));
const Login = loadable(() => import('./pages/login'));
const Write = loadable(() => import('./pages/write'));
  
class App extends Component {

  render() {
    return (
      <Provider store={store}>
        <Fragment> 
          <BrowserRouter>
            <div>
              <Header />
              <Switch>
                <Route
                  exact
                  path="/"
                  render={() => <Redirect to="/home" />}
                />
                <Route path='/home' exact component={Home}></Route>
                <Route path='/login' exact component={Login}></Route>
                <Route path='/write' exact component={Write}></Route>
                <Route path='/detail/:id' exact component={Detail}></Route>
                <Route render={() => <div>Not Found</div>} />
              </Switch>
            </div>
          </BrowserRouter>
        </Fragment>
      </Provider>
    )
  }
}

export default App;
```

### history

```js
实现非 React 组件在页面路由跳转，

import {createHashHistory} from 'history'
let history = createHashHistory()

export const goto = (path) => {
    history.push(path)
}

// 全局挂载方法
let history = createHashHistory()
React.$goto = function(url) {
  history.push(url)
}
const

5.20 版本可能有Bug跳转不了
```

### babel-plugin-import

```js
按需加载

安装 antd 但只用到了部分组件，实现样式按需加载。

yarn add babel-plugin-import -D

// package.json
{
    ...
    "babel": {
        "presets": [
            "react-app"
        ],
        "plugin": [//+
            [//+
                "import",//+
                {
                    "librayName": "antd",//+
                    "style": "css"//+
                }//+
            ]//+
        ]//+
    }
}
```

### antd

```js
//1、 自定义antd 主体颜色

修改 config/webpack.config.js (暴露配置后 npm run eject)

if(preProcessor) {
    // +
    let preProcessorOptions = {
        sourceMap: true
    }
    if(preProcessor == "less-loader") {
        preProcessorOptions = {
            sourceMap: true,
            lessOptions: {
                modifyVars: {
                    // 自定义颜色
                    "primary-cololr": '#65C25A'
                },
                javascriptEnabled: true
            }
        }
    }
    // +
    loaders.push(
    	{
            loader: require.resolve('resolve-url-loader'),
            options: {
                sourceMap: isEnvProduction && shoudUseSourceMap
            }
        },
        {
            loader: require.resolve.resolve(preProcessor),
            //options: {
            //    sourceMap: true
            //},
            options: preProcessorOptions
        }
    )
}

// 修改package.json
"plugin": [
    [
        "import",
        {
            "librayName": "antd",
            "style": true
        }
    ]
]


// 2、设置 中文，antd 默认英文

import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
// 设置默认语言为中文
import {ConfigProvider} from 'antd' 
import zhCN from 'antd/es/locale/zh_CN'
const antdConfig = {
  locale: zhCN
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <ConfigProvider {...antdConfig}>
      <App />
  </ConfigProvider>
);
```

### @craco/craco

```js
在不暴露 react 配置项的情况下修改 webpack 配置项

// craco.config.js

const { when, whenDev, whenProd, whenTest, ESLINT_MODES, POSTCSS_MODES } = require("@craco/craco");

module.exports = {
    reactScriptsVersion: "react-scripts" /* (default value) */,
    style: {
        modules: {
            localIdentName: ""
        },
        css: {
            loaderOptions: { /* Any css-loader configuration options: https://github.com/webpack-contrib/css-loader. */ },
            loaderOptions: (cssLoaderOptions, { env, paths }) => { return cssLoaderOptions; }
        },
        sass: {
            loaderOptions: { /* Any sass-loader configuration options: https://github.com/webpack-contrib/sass-loader. */ },
            loaderOptions: (sassLoaderOptions, { env, paths }) => { return sassLoaderOptions; }
        },
        postcss: {
            mode: "extends" /* (default value) */ || "file",
            plugins: [require('plugin-to-append')], // Additional plugins given in an array are appended to existing config.
            plugins: (plugins) => [require('plugin-to-prepend')].concat(plugins), // Or you may use the function variant.
            env: {
                autoprefixer: { /* Any autoprefixer options: https://github.com/postcss/autoprefixer#options */ },
                stage: 3, /* Any valid stages: https://cssdb.org/#staging-process. */
                features: { /* Any CSS features: https://preset-env.cssdb.org/features. */ }
            },
            loaderOptions: { /* Any postcss-loader configuration options: https://github.com/postcss/postcss-loader. */ },
            loaderOptions: (postcssLoaderOptions, { env, paths }) => { return postcssLoaderOptions; }
        }
    },
    eslint: {
        enable: true /* (default value) */,
        mode: "extends" /* (default value) */ || "file",
        configure: { /* Any eslint configuration options: https://eslint.org/docs/user-guide/configuring */ },
        configure: (eslintConfig, { env, paths }) => { return eslintConfig; },
        pluginOptions: { /* Any eslint plugin configuration options: https://github.com/webpack-contrib/eslint-webpack-plugin#options. */ },
        pluginOptions: (eslintOptions, { env, paths }) => { return eslintOptions; }
    },
    babel: {
        presets: [],
        plugins: [],
        loaderOptions: { /* Any babel-loader configuration options: https://github.com/babel/babel-loader. */ },
        loaderOptions: (babelLoaderOptions, { env, paths }) => { return babelLoaderOptions; }
    },
    typescript: {
        enableTypeChecking: true /* (default value)  */
    },
    webpack: {
        alias: {},
        plugins: {
            add: [], /* An array of plugins */
            add: [
                plugin1,
                [plugin2, "append"],
                [plugin3, "prepend"], /* Specify if plugin should be appended or prepended */
            ], /* An array of plugins */
            remove: [],  /* An array of plugin constructor's names (i.e. "StyleLintPlugin", "ESLintWebpackPlugin" ) */
        },
        configure: { /* Any webpack configuration options: https://webpack.js.org/configuration */ },
        configure: (webpackConfig, { env, paths }) => { return webpackConfig; }
    },
    jest: {
        babel: {
            addPresets: true, /* (default value) */
            addPlugins: true  /* (default value) */
        },
        configure: { /* Any Jest configuration options: https://jestjs.io/docs/en/configuration. */ },
        configure: (jestConfig, { env, paths, resolve, rootDir }) => { return jestConfig; }
    },
    devServer: { /* Any devServer configuration options: https://webpack.js.org/configuration/dev-server/#devserver. */ },
    devServer: (devServerConfig, { env, paths, proxy, allowedHost }) => { return devServerConfig; },
    plugins: [
        {
            plugin: {
                overrideCracoConfig: ({ cracoConfig, pluginOptions, context: { env, paths } }) => { return cracoConfig; },
                overrideWebpackConfig: ({ webpackConfig, cracoConfig, pluginOptions, context: { env, paths } }) => { return webpackConfig; },
                overrideDevServerConfig: ({ devServerConfig, cracoConfig, pluginOptions, context: { env, paths, proxy, allowedHost } }) => { return devServerConfig; },
                overrideJestConfig: ({ jestConfig, cracoConfig, pluginOptions, context: { env, paths, resolve, rootDir } }) => { return jestConfig },
            },
            options: {}
        }
    ]
};

/* package.json */

"scripts": {
-   "start": "react-scripts start",
+   "start": "craco start",
-   "build": "react-scripts build",
+   "build": "craco build"
-   "test": "react-scripts test",
+   "test": "craco test"
}
```

### react-photo-view

```
react-photo-view 拥有无与伦比的预览交互体验：从打开图像开始，每一帧的动画、细节和交互都经过了精心设计与反复调试，媲美原生图片预览的效果。
```

### styled-components

> css in js
>
> - 贯彻React的 everything in JS理念，降低js对css文件的依赖
> - 组件的样式和其他组件完全解耦，有效避免了组件之间的样式污染

- 安装：

	```
	npm install --save styled-components
	```

	

- 全局样式

	```
	// style.js
	
	import { createGlobalStyle } from 'styled-components'
	
	export const GlobalStyle = createGlobalStyle`
	  html, body, div, span, applet, object, iframe,
	  h1, h2, h3, h4, h5, h6, p, blockquote, pre,
	  a, abbr, acronym, address, big, cite, code,
	  del, dfn, em, img, ins, kbd, q, s, samp,
	  small, strike, strong, sub, sup, tt, var,
	  b, u, i, center,
	  dl, dt, dd, ol, ul, li,
	  fieldset, form, label, legend,
	  table, caption, tbody, tfoot, thead, tr, th, td,
	  article, aside, canvas, details, embed, 
	  figure, figcaption, footer, header, hgroup, 
	  menu, nav, output, ruby, section, summary,
	  time, mark, audio, video {
	    margin: 0;
	    padding: 0;
	    border: 0;
	    font-size: 100%;
	    font: inherit;
	    vertical-align: baseline;
	  }
	  /* HTML5 display-role reset for older browsers */
	  article, aside, details, figcaption, figure, 
	  footer, header, hgroup, menu, nav, section {
	    display: block;
	  }
	  body {
	    line-height: 1;
	  }
	  ol, ul {
	    list-style: none;
	  }
	  blockquote, q {
	    quotes: none;
	  }
	  blockquote:before, blockquote:after,
	  q:before, q:after {
	    content: '';
	    content: none;
	  }
	  table {
	    border-collapse: collapse;
	    border-spacing: 0;
	  }
	`;
	```

	然后在项目的入口文件(index.js)中
	将其放在render函数的第一个html标签内部

```js
import React, { Fragment } from 'react';
import ReactDOM from 'react-dom';
import { GlobalStyle} from './style';
import App from './App';

ReactDOM.render(
  <Fragment>
    <GlobalStyle/>
    <App/>
  </Fragment>,
  document.getElementById('root')
);
```

- 局部样式

	对于一个特定的组件，我们可以事先在render函数中，用组件的命名方式替换原本的div等标签

	```js
	import React, {Component} from 'react'
	import {
	  HeaderWrapper,
	  Nav,
	  NavItem
	} from './style'
	
	class Header extends Component {
	  render() {
	    return (
	      <HeaderWrapper>
	        <Nav>
	          <NavItem className='left active'>首页</NavItem>
	          <NavItem className='left'>下载App</NavItem>
	          <NavItem className='right'>登陆</NavItem>
	          <NavItem className='right'>
	            <span className='iconfont'>&#xe636;</span>
	          </NavItem>
	        </Nav>       
	      </HeaderWrapper>
	    )
	  }
	}
	
	export default Header
	```

	然后在同目录下的style.js中编写具体的CSS样式，以组件名的形式导出

```
import styled from 'styled-components'

export const HeaderWrapper = styled.div`
  position: relative;
  margin: 0;
  padding: 0;
  height: 58px;
  border-bottom: 1px solid #f0f0f0
`;

export const Nav = styled.div`
  width: 945px;
  height: 100%;
  padding-right: 70px;
  box-sizing: border-box;
  margin: 0 auto;
`

export const NavItem = styled.div`
line-height: 56px;
padding: 0 15px;
font-size: 17px;
color: #333;
`
```

### nprogress

> 页面顶部进度条，用来代替加载中动画。使用时记得引入样式，否则进度条不显示。

### react-router-dom 与 react-router-dom 区别

> 都是路由管理的插件，它拓展了一下在浏览器环境下运行的一些功能。
>
> react-router-dom 依赖 react-router 。安装了前者就不用再安装后者
>
> x



Router 默认是模糊匹配, Switch 只能匹配其中第一个

```js

<Routes>
  <Route exact path="/fuzzySearch" element={<FuzzySearch />}></Route>
  <Route exact path="/entitySearch" element={<EntitySearch />}></Route>
  <Route exact path="/entityRelSearch" element={<EntityRelSearch />}></Route>
  <Switch>
     <Route path='/' component={Home} exact></Route>
     <Route  component={NoMatch} ></Route>
  </Switch>
  <Route path="/" element={<HomeWrapped />}></Route>
</Routes>
```



### redux-persist

> redux 数据持久化

### ahooks

> [ahooks](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Falibaba%2Fhooks) 是一套开源的 React Hooks 库，封装了大量好用的 Hooks。在当前 React 项目研发过程中，一套好用的 React Hooks 库是必不可少的，希望 ahooks 能成为您的选择。