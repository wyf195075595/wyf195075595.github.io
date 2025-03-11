---

title:  vue-cli 实践与优化
date: 2023-06-15 10:23:10
tags: vue-cli
categories: vue
---

> 收集一些常见的基于vue-cli的优化项
>
> vue-cli 配置项结构
>
> ```js
> const { defineConfig } = require("@vue/cli-service");
> module.exports = defineConfig({
> 	...
> })
> ```
>
> 

### 设置全局样式

<!--more-->

依赖插件 style-resources-loader, vue-cli-plugin-style-resources-loader

```js
pluginOptions: {
    "style-resources-loader": {
      preProcessor: "less",
      patterns: [path.resolve(__dirname, "./src/styles/variable.less")],
    },
},
```



###  css样式抽取

> vue-cli5 默认配置 生产环境抽取组件的 style 样式为css文件放在 dist/css 文件夹中，还可以设置

```js
css: {
    // 是否生成映射文件，方便调试
    sourceMap: isProduction ? false : true,
    extract: {
      // 输出的每个 CSS 文件的名称
      filename: `css/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.css`,
      // 非入口的 chunk 文件名称 
      chunkFilename: `css/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.css`,
    },
}
```

> [关于 chunkFilename](https://www.thinbug.com/q/54267580)  作用：不在`output.entry`中的文件，但是需要单独打包的文件名配置。（
> 设置使用`require.ensure`或者`import`异步加载模块打包后的名称。）

### js组件打包

> vue-cli5 默认组件打包后输出到 /dist/js下， 文件名格式 `文件夹名-文件名.hash值.后缀`, 也可以手动配置。
>
> eg:defectControlConfiguration-consumptionUnit.2e496dd8.js

```js
1、方式1
chainWebpack: (config) => {

	config.output.filename(
      `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`
    )
    .chunkFilename(
      `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`
    );
}

2、方式2
configureWebpack: (config) => {
    config.output.filename = `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`;
    config.output.chunkFilename = `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`;
}

```

### 资源压缩

对静态资源的压缩，一般只在生产模式下配置

```js
configureWebpack: (config) => {
  config.plugins.push(
    new CompressionPlugin({
      test: /\.(js|css|jpeg|jpg|png|svg|gif|json|txt|ico)$/i,
      threshold: 10240, // 单位字节 只有大小大于10k的资源会被处理
      minRatio: 0.8, // 只有压缩率小于这个值的资源才会被处理
      deleteOriginalAssets: false, // 删除原文件
    })
  );
}
```



### 开启gzip压缩

vue.config.js

安装压缩插件，注意：不用安装太高版本，不用安装太高版本，不用安装太高版本，最新版本安装后打包回出错，我这是选择5.0.2。可能跟你项目的webpack版本有关吧

```css
npm install --save-dev compression-webpack-plugin@5.0.2
```

```js
const CompressionWebpackPlugin = require('compression-webpack-plugin')
chainWebpack(config) {
  // 生产模式下启用gzip压缩 需要配置nginx支持gzip
    if (process.env.NODE_ENV === 'production') {
      config.plugin('CompressionWebpackPlugin').use(CompressionWebpackPlugin, [
        {
          filename: '[path][base].gz',
          algorithm: 'gzip',
          test: new RegExp('\\.(js|css)$'),
          // 只处理大于xx字节 的文件，默认：0
          threshold: 10240,
          // 示例：一个1024b大小的文件，压缩后大小为768b，minRatio : 0.75
          minRatio: 0.8, // 默认: 0.8
          // 是否删除源文件，默认: false
          deleteOriginalAssets: false
        }
      ])
    }
}
```

nginx 配置

```nginx
server {
    listen       80;
    server_name  localhost;
    gzip on;
    gzip_static on;    
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_proxied  any;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    
    //找到nginx的这个文件 /usr/local/nginx/conf/nginx.conf 然后配置
    server {
        listen 4300;
        server_name localhost;
        location / {
            root /home/static/web/wechat;
            index /index.html;
            try_files $uri $uri/ /index.html;
            gzip_static on; #开启压缩
        }
    }

}
```

### 公共代码抽离

将第三方模块,样式抽离成单个文件

```js
configureWebpack: (config) => {
    config.optimization = {
      splitChunks: {
        chunks: "all",
        minChunks: 2,
        minSize: 1024 * 5,
        automaticNameDelimiter: "~",
        cacheGroups: {
          vue: {
            name: "vue",
            test: /[\\/]node_modules[\\/]vue[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          vuex: {
            name: "vuex",
            test: /[\\/]node_modules[\\/]vuex[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          "vue-router": {
            name: "vue-router",
            test: /[\\/]node_modules[\\/]vue-router[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          "element-ui": {
            name: "element-ui",
            test: /[\\/]node_modules[\\/]element-ui[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          echarts: {
            name: "echarts",
            test: /[\\/]node_modules[\\/]echarts[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          dayjs: {
            name: "dayjs",
            test: /[\\/]node_modules[\\/]dayjs[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          jquery: {
            name: "jquery",
            test: /[\\/]node_modules[\\/]jquery[\\/]/,
            priority: 10,
            minSize: 0,
            minChunks: 1,
          },
          "chunk-vendors": {
            name: "chunk-vendors",
            test: /[\\/]node_modules[\\/]/,
            priority: 1,
            minChunks: 1,
          },
          styles: {
            name: "styles",
            test: /\.(le|c)ss$/,
            priority: 0,
          },
        },
      },
    };
}
```

```js
//代码拆分功能
config.optimization.splitChunks = {
  chunks: "all", // 表示选择哪些 chunks 进行分割，可选值有：async，initial和all
  maxInitialRequests: 10, // 
  minSize: 30000, // 依赖包超过30KB将被单独打包
  automaticNameDelimiter: "-",// 抽取出来的文件的自动生成名字的分割符，默认为 ~；
  cacheGroups: {
    vendor: { // vendors用于提取所有node_modules中符合条件的模块
      test: /[\\/]node_modules[\\/]/,
      name(module) {
        const packageNames = module.context.match(
          /[\\/]node_modules[\\/](.*?)([\\/]|$)/
        );
        if(packageNames) {
          return `chunk.${packageNames[1].replace("@", "")}`;
        }
      },
      priority: 10 // 当一个模块同时符合多个cacheGroups时，则根据其中的priority配置项确定优先级
    }
  }
};
```



### 性能提示优化

但项目越来越大，资源越来越多时，打包可能会报错。此时可能需要调整性能提示。

> 原因： node编译的[内存溢出](https://so.csdn.net/so/search?q=内存溢出&spm=1001.2101.3001.7020)了。原因就是打包文件过大，超出了内存的限制大小造成了编译中断。

```js
configureWebpack: (config) => {
    config.performance = {
      hints: "warning",
      // 入口起点的最大体积
      maxEntrypointSize: 30000000,
      // 生成文件的最大体积
      maxAssetSize: 30000000,
      // 只给出 js 文件的性能提示
      assetFilter: function (assetFilename) {
        return assetFilename.endsWith(".js");
      },
    };
}
```

package.json

```json
{
    "scripts": {
        "serve2": "node --max_old_space_size=4096 node_modules/@vue/cli-service/bin/vue-cli-service.js serve --open",
    "build2": "node --max_old_space_size=4096 node_modules/@vue/cli-service/bin/vue-cli-service.js build",
    }
}
```

> [相关解决方法](https://blog.csdn.net/Tiger_lin1/article/details/125634867)



### 路由懒加载

> 这样就优雅了不少

普通懒加载，打包后名称

chunk_59de86678e4fdfa6_1.0.0_1686820955003.js

```js
{
  path: "/pmsLogin",
  name: "pmsLogin",
  meta: {
    keepAlive: false,
    transitionName: "opacity-scale",
  },
  component: () =>
    import("@/views/pmsLogin/index.vue"),
},
```

升级版, 这种方式注册的路由打包后名称

pmsLogin_59de86678e4fdfa6_1.0.0_1686820955003.js

```js
{
  path: "/pmsLogin",
  name: "pmsLogin",
  meta: {
    keepAlive: false,
    transitionName: "opacity-scale",
  },
  component: () =>
    import(/* webpackChunkName: "pmsLogin" */ "@/views/pmsLogin/index.vue"),
},
```

#### 懒加载动态路由打包时的问题

```js
export const loadView = (view) => {
  if (process.env.NODE_ENV === 'development') {
    return (resolve) => require([`@/views/${view}`], resolve)
  } else {
    // 使用 import 实现生产环境的路由懒加载 
    // import(`/* webpackChunkName: "${name}" */ @/views/${view}.vue`), 这种写法不行
    // 从 webpack 2.6.0 开始，占位符 [index] 和 [request] 被支持为递增的数字或实际解析的文件名。
    return () => import(/* webpackChunkName: "[request]" */ `@/views/${view}.vue`)
  }
}
```



### 使用cdn加速

```js
let cdn = {
  css: [
    //雪碧图样式
    "https://**/**/icon.css",
  ],
  js: [
    'https://cdn.bootcdn.net/ajax/libs/vue/3.2.33/vue.cjs.js',
    'https://cdn.bootcdn.net/ajax/libs/vuex/4.0.2/vuex.cjs.min.js',
    'https://cdn.bootcdn.net/ajax/libs/vue-router/4.0.14/vue-router.cjs.min.js',
    'https://cdn.bootcdn.net/ajax/libs/axios/0.26.1/axios.js',
    'https://cdn.bootcdn.net/ajax/libs/core-js/3.22.0/minified.js'
  ],
}
//判断是否是生产环境
const isProd = process.env.NODE_ENV === "production"
cdn = isProd ? cdn : {css:[],js:[]};

module.exports = {
	configureWebpack: {
		 // 采用cdn的方式,所以将以下这些文件配置不打包
    	externals:{
	       vue:"Vue",
	      'vue-router': 'VueRouter',
	       vuex: 'Vuex',
	       axios:'axios',
	      'core-js':'core-js',
	       vant:'vant'
    	},
	}
	chainWebpack:config => {
		 // 注入cdn
   		 config.plugin("html").tap(args => {
	        args[0].cdn = cdn
	        return args
     	 })
	}
}

```

index.html 模板

```html
<!DOCTYPE html>
<html lang="zh-Hans">
  <head>
    <!-- 使用CDN的CSS文件 -->
    <% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.css) { %>
      <link href="<%= htmlWebpackPlugin.options.cdn.css[i] %> " rel="stylesheet">
    <% } %>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but app doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- 使用CDN的JS文件 -->
    <% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.js) { %>
    <script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>" defer></script>
    <% } %>
  </body>
</html>

```

### 查看性能分析报告

通过查看性能分析报告确定哪些大模块使用cdn加速

```json
//package.json
{
	"scripts": {
    	"report": "vue-cli-service build --report --mode production"
 	 },
}

```



### 去除 console.log

1、方式1

npm i terser-webpack-plugin -D

```js
module.exports = {
 chainWebpack:config => {
    if(process.env.NODE_ENV === "production"){
      // 去除console.log输出
      config.optimization
      .minimizer('terser')
      .tap(args => {
        Object.assign(args[0].terserOptions.compress, { 
          // 生产模式去除console.log
          pure_funcs: ['console.log']
        })
        return args
      })
    }
 }
}

```

2、方式2

安装  babel-plugin-transform-remove-console

.babel.config.js

```js
const plugins = [];
const isProduction = process.env.NODE_ENV === "production";
if (isProduction) {
  plugins.push(["transform-remove-console", { exclude: ["error", "warn"] }]);
}

module.exports = {
  presets: ["@vue/cli-plugin-babel/preset"],
  plugins: plugins,
};
```

### 浏览器js兼容性配置

1. 配置 `browserslist`

	**在根目录创建 `.browserslistrc` 文件** 或者在 `package.json` 中配置 `browserslist` 字段。

	```txt
	# .browserslistrc
	> 0.5%
	last 2 versions
	not dead
	```

	```json
	{
	  "browserslist": [
	    "> 0.5%",
	    "last 2 versions",
	    "not dead"
	  ]
	}
	```

	

2. 启用自动 Polyfill

	```js
	// vue.config.js
	module.exports = {
	// 默认情况下 babel-loader 会忽略所有 node_modules 中的文件。你可以启用本选项，以避免构建后的代码中出现未转译的第三方依赖。
	
	// 不过，对所有的依赖都进行转译可能会降低构建速度。如果对构建性能有所顾虑，你可以只转译部分特定的依赖：给本选项传一个数组，列出需要转译的第三方包包名或正则表达式即可。
	  transpileDependencies: ['core-js', 'regenerator-runtime'], // 确保这些库被正确转译
	};
	```

	

3. 使用 Polyfill 方式

	- `useBuiltIns: 'usage'`（按需按文件加载 polyfill）

	Babel 会根据每个文件中使用的 JavaScript 特性，自动选择需要 polyfill 的部分，避免将所有 polyfill 引入到每个文件中。此时不需要在入口文件中显式地引入 polyfill。此模式下main.js不需要引入东西

	```js
	module.exports = {
	  presets: [
	    [
	      '@babel/preset-env',
	      {
	        useBuiltIns: 'usage', // (按需按文件加载 polyfill)
	        corejs: 3,          // 使用 core-js 版本 3
	      },
	    ],
	  ],
	};
	
	```

	- `useBuiltIns: 'entry'`

	Vue CLI 会根据 `browserslist` 自动引入所需的 polyfill，并且你需要在项目的入口文件（如 `src/main.js`）中手动引入 `core-js`：

	```js
	// src/main.js
	import 'core-js/stable';
	import 'regenerator-runtime/runtime';
	```

4. 配置 Vue CLI 来启用自动 Polyfill

	```js
	// vue.config.js
	module.exports = {
	  transpileDependencies: ['core-js', 'regenerator-runtime'], // 确保这些库被正确转译
	  chainWebpack: (config) => {
	    config.module
	      .rule('js')
	      .test(/\.js$/)
	      .use('babel-loader')
	      .loader('babel-loader')
	      .options({
	        presets: [
	          [
	            '@babel/preset-env',
	            {
	              useBuiltIns: 'entry', // 按需加载 polyfills
	              corejs: 3,             // 使用 core-js 版本 3
	            },
	          ],
	        ],
	      });
	  },
	};
	
	```

	### 浏览器 css 兼容性配置

Vue CLI 支持通过 **postcss.config.js** 来配置 **PostCSS** 插件或**package.json**。你可以在项目根目录创建 `postcss.config.js` 文件，或者修改该文件以确保样式兼容。

3选1

```js
// postcss.config.js
module.exports = {
  plugins: {
    autoprefixer: {
      overrideBrowserslist: [
        'last 2 versions', // 支持最新两个版本的浏览器
        'IE 10',           // 支持 IE10 及以上版本
        'Safari >= 9',      // 支持 Safari 9 及以上版本
      ],
    },
  },
};

```

```js
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      postcss: {
        plugins: [
          require('autoprefixer')({
            overrideBrowserslist: [
              'last 2 versions',
              'IE 10',
              'Safari >= 9',
            ],
          }),
        ],
      },
    },
  },
};

```

package.json 中配置browserslist后会自动触发 css后缀样式添加，如果配置了 js垫片添加，css可以不用配置了

```js
"browserslist": [
  "last 2 versions",
  "> 1%",
  "not dead"
]
```



### webpack-chain 语法

> [参考链接](https://blog.51cto.com/feng/5289791)

### 完整示例参考

```js
/*
 * @Description: 打包配置
 * @Author:  wyf
 * @Date: 2023-07-03 08:48:44
 * @LastEditTime: 2023-08-29 11:01:04
 * @LastEditors:  
 */
const { defineConfig } = require('@vue/cli-service');
const { type } = require('os');
// 是否为生产环境
const isProduction = process.env.NODE_ENV === "production";
// 时间戳
const timestamp = new Date().getTime();
const path = require('path');
// const { config } = require('process');
// console.log("process.config", config);
module.exports = defineConfig({
  transpileDependencies: true,
  publicPath: isProduction ? "/" : "/",
  outputDir: "dist",
  productionSourceMap: false,
  devServer: {
    proxy: {
      '/api': {
        // target: "http://192.18.1.12:8080/", // 我们要代理的真实接口地址
        target: "http://60.173.195.163:18000/",
        changeOrigin: true,
        ws: true,
        pathRewrite: { "^/api": "" },
      }
    }
  },
  configureWebpack: (config) => {
    config.performance = {
      hints: "warning",
      // 入口起点的最大体积
      maxEntrypointSize: 1024*1024*1,
      // 生成文件的最大体积
      maxAssetSize: 1024*1024*1,
      // 只给出 js 文件的性能提示
      assetFilter: function (assetFilename) {
        return assetFilename.endsWith(".js");
      },
    }
  },
  chainWebpack: (config) => {
    config.plugins.delete('preload') // TODO: need test
    config.plugins.delete('prefetch') // TODO: need test

    if(isProduction){
      // 去除console.log输出
      config.optimization
      .minimizer('terser')
      .tap(args => {
        Object.assign(args[0].terserOptions.compress, { 
          // 生产模式去除console.log
          pure_funcs: ['console.log']
        })
        return args
      })
    }
    // set svg-sprite-loader
    config.module
      .rule('svg')
      .exclude.add(path.resolve('src/assets/icons'))
      .end()
    config.module
      .rule('icons')
      .test(/\.svg$/)
      .include.add(path.resolve('src/assets/icons'))
      .end()
      .use('svg-sprite-loader')
      .loader('svg-sprite-loader')
      .options({
        symbolId: 'icon-[name]'
      })
      .end()

    config.optimization.splitChunks({
      chunks: "all", // 表示选择哪些 chunks 进行分割，可选值有：async，initial和all
      maxInitialRequests: 10, // 
      minSize: 30000, // 依赖包超过30KB将被单独打包
      automaticNameDelimiter: "-",// 抽取出来的文件的自动生成名字的分割符，默认为 ~；
      cacheGroups: {
        vendor: { // vendors用于提取所有node_modules中符合条件的模块
          test: /[\\/]node_modules[\\/]/,
          name(module) {
            const packageNames = module.context.match(
              /[\\/]node_modules[\\/](.*?)([\\/]|$)/
            );
            if(packageNames) {
              return `chunk.${packageNames[1].replace("@", "")}`;
            }
          },
          priority: 10 // 当一个模块同时符合多个cacheGroups时，则根据其中的priority配置项确定优先级
        }
      }
    });

    config.output
      .filename(
        `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`
      )
      .chunkFilename(
        `js/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.js`
      );
    
    // 系统名称
    config.plugin('html').tap(args => {
        args[0].title = "智慧营业厅管理平台"
        return args
    })
  },
  css: {
    sourceMap: isProduction ? false : true,
    extract: {
      filename: `css/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.css`,
      chunkFilename: `css/[name]_[hash]_${process.env.VUE_APP_VERSION}_${timestamp}.css`,
    },
  },
  pluginOptions: {
    "style-resources-loader": {
      preProcessor: "less",
      patterns: [path.resolve(__dirname, "./src/styles/variable.less")],
    },
  },
})

```

