---
title: webpack 配置
date: 2022-06-17 18:23:10
tags: webpack
categories: webpack
---



### 初始化项目

```
yarn init -y
```



### 添加 webpack-cli

```
yarn add webpack-cli -D
```

基础配置:(webpack.config.js)

```js
let path = require('path');

module.exports = {
    mode: 'development', // 开发环境，生产环境 production
    entry: './src/index.js', // 入口文件
    output: {
        filename: 'bundle.js', // 输出文件名
        // filename: 'bundle.[hash].js', // 输出文件名，添加hash值
        // filename: 'bundle.[hash:10].js', // 输出文件名，添加10位hash值
        path: path.resolve(__dirname, 'build'), // 输出路径（绝对路径）
    }
}
```

打包命令：

```js
// 可通过 --config 传入参数设置配置文件名称 默认（webpack.config.js）
npx webpack --config webpack.config.js

// 在 package.json 中 设置 scripts 脚本

"script": {
    "build": "webpack"
}

执行：
npm run build
```



### webpack node方式启动项目

```
yarn add webpack-dev-server -D
```

配置文件添加配置：

```
devServer: {
	port: 3000,
	progress: true,
	contentBase: './build'
}
```

添加脚本

```
"script": {
    "dev": "webpack-dev-server "
}
```



### 安装 htmlWebpackPlugin 插件

> 将打包后的js,注入到 html 模板中

```
yarn add html-webpack-plugin -D
```

配置文件：

```js
let htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    ...
    plugins: [
        new htmlWebpackPlugin({
            template: './src/index.html',// 模板的位置
            filename: 'index.html',// 打包后模板的名字
            minify: {
                removeAttributeQuotes: true,// 移出双引号
                collapseWhitespace:  true,// 压缩成一行
                hash: true,// 注入的js 文件名添加hash值
                
            }
        })
    ]
}
```





### 样式处理

> 引入适合的loader，在配置 loader 处理规则
>
> css-loader	处理 @import 这种语法映入的样式
>
> style-loader	把css 插入到 head 标签中
>
> 处理 less:  less-loader
>
> 处理 sass:	node-sass	sass-loader
>
> 处理 stylus:	stylus-loader



配置：

```js
module.exports = {
    ...
    module: {
    	rules: [
    		// loader 使用时顺序从右-> 左, 从 下 -> 上
    		{
        		test: /\.css$/, 
        		use: [
        	       {
                        loader: 'style-loader',
                        options: {
                            // 设置插入到 标签顶部，这样自己在页面添加 style 标签优先级更高
                            insertAt: 'top'
                        }
                    }, 
                     'css-loader'
                  ]
			},
            {test: '/\.less$/',use: [
                {
                    loader: 'style-loader',
                     options: {
                        // 设置插入到 标签顶部，这样自己在页面添加 style 标签优先级更高
                        insertAt: 'top'
                    }
                },
                'css-loader',
                'less-loader' // 把 less -> css
            ]}
    	]
    }
}
```

#### 样式抽离：mini-css-extract-plugin

> 为啥要用这个？
>
> 上述配置 最终都是通过 style-loader 将 样式转化为 style 标签插入到 head 标签中
>
> 而这个插件则是将 css 转化为 link 标签 再 插入 head 中、
>
> 它用于代替 style-loader。

```
yarn add mini-css-extract-plugin -D
```

```js
let htmlWebpackPlugin = require('html-webpack-plugin')
let MiniCssExctractPlugin = require('mini-css-extract-plugin')
module.exports = {
    ...
    plugins: [
        new htmlWebpackPlugin({
            template: './src/index.html',// 模板的位置
            filename: 'index.html',// 打包后模板的名字
            minify: {
                removeAttributeQuotes: true,// 移出双引号
                collapseWhitespace:  true,// 压缩成一行
                hash: true,// 注入的js 文件名添加hash值
                
            }
        }),
        new MiniCssExctractPlugin({
           filename: 'main.css' 
        })
    ],
    module: {
    	rules: [
    		// loader 使用时顺序从右-> 左, 从 下 -> 上
    		{
        		test: /\.css$/, 
        		use: [
        	       MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入 
                     'css-loader'
                  ]
			},
            {test: '/\.less$/',use: [
                MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入,
                'css-loader',
                'less-loader' // 把 less -> css
            ]}
    	]
    }
}
```

#### 给样式添加后缀：autoprefixer, postcss-loader

```
yarn add autoprefixer postcss-loader -D
```

```js
let htmlWebpackPlugin = require('html-webpack-plugin')
let MiniCssExctractPlugin = require('mini-css-extract-plugin')
module.exports = {
    ...
    module: {
    	rules: [
    		// loader 使用时顺序从右-> 左, 从 下 -> 上
    		{
        		test: /\.css$/, 
        		use: [
        	       MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入 
                     'css-loader',
                    'postcss-loader',
                  ]
			},
            {test: '/\.less$/',use: [
                MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入,
                'css-loader',
                'postcss-loader',
                'less-loader' // 把 less -> css
            ]}
    	]
    }
}
```

还需要配置 postcss-loader 使用 autoprefixer 插件

根目录新增 postcss.config.js

```js
module.exports = {
	plugins: [require('autoprefixer')]
}
```

css 压缩：optimize-css-assets-webpack-plugin

js 压缩：uglifyjs-webpack-plugin

```
yarn add optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin -D
```

```js
let htmlWebpackPlugin = require('html-webpack-plugin')
let MiniCssExctractPlugin = require('mini-css-extract-plugin')
// 使用了css 压缩后，默认js压缩失效，需使用 插件压缩 js.
let OptimizeCss = require('optimize-css-assets-webpack-plugin')
let UglifyJsPlugin = require('uglifyjs-webpack-plugin')
module.exports = {
    ...,
    optimization: {// 优化项
    	minimizer: [
    		new UglifyJsPlugin({
    			cache: true,
    			sourceMap: true,// 是否并发打包，压缩多个
    			paraller: true
            }),
    		new OptimizeCss()
        ]
    
    },
    module: {
    	rules: [
    		// loader 使用时顺序从右-> 左, 从 下 -> 上
    		{
        		test: /\.css$/, 
        		use: [
        	       MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入 
                     'css-loader',
                    'postcss-loader',
                  ]
			},
            {test: '/\.less$/',use: [
                MiniCssExctractPlugin.loader, // 将css 以link 标签形式插入,
                'css-loader',
                'postcss-loader',
                'less-loader' // 把 less -> css
            ]}
    	]
    }
}
```



### es高级语法降级处理

> babel 三件套
>
> babel-loader	转化源文件
>
> @babel/core	核心模块有转化方法
>
> @babel/preset-env	转化模块，es6->es5
>
> @babel/plugin-proposal-class-properties	兼容es高级class语法
>
> @babel/plugin-proposal-decorators	兼容装饰器语法
>
> 

```
yarn add babel-loader @babel/core @babel/preset-env -D
```

```js
let htmlWebpackPlugin = require('html-webpack-plugin')
let MiniCssExctractPlugin = require('mini-css-extract-plugin')

module.exports = {
    ...,
    module: {
    	rules: [
    	  {
    		test: '/\.js$/',
    		use: {
    		  loader: 'babel-loader',
    		  options: {
    			// 大插件集合，转换es6-> es5
    			presets: [
    			  '@babel/preset-env'
                ],
                // 小插件，转换 高级 es语法
                plugins: [
                     ["@babel/plugin-proposal-decorators", { "legacy": true }],
                    '@babel/plugin-proposal-class-properties',
                    "@babel/plugin-transform-runtime"
                ]
               }
            },
            // 只匹配 scr目录下js, 不包括 node_modules目录
            include: path.resolve(__dirname, 'src'),
            exclude: /node_modules/
          }
    		
    	]
    }
}
```



### 处理js语法及校验

> 生成器，promise 等一些内置方法，可能需要额外配置
>
> @babel/plugin-transform-runtime
>
> @babel-polyfill	兼容更高es语法,

```js
// 生产开发都需要
yarn add @babel/plugin-transform-runtime @babel-polyfill
```

```js
// "./app/js" 为js 入口
entry: ["@babel/polyfill", "./app/js"],module.exports = {
  entry: ["@babel/polyfill", "./app/js"],
};
```

js语法校验： eslint

> [官网可视化配置](https://eslint.org/play/)，配置完后可直接下载配置文件 .eslintrc.json

```js
let htmlWebpackPlugin = require('html-webpack-plugin')
let MiniCssExctractPlugin = require('mini-css-extract-plugin')

module.exports = {
    ...,
    module: {
    	rules: [
    	  {
    		test: /\.js$/,
    		use: {
    			loader: 'eslint-loader',
    			options: {
                    enforce: 'pre', // 在从下往上执行规则中 先使用它，  post 后使用它
                }
            },
            
          },
    	  {
    		test: '/\.js$/',
    		use: {
    		  loader: 'babel-loader',
    		  options: {
    			// 大插件集合，转换es6-> es5
    			presets: [
    			  '@babel/preset-env'
                ],
                // 小插件，转换 高级 es语法
                plugins: [
                     ["@babel/plugin-proposal-decorators", { "legacy": true }],
                    '@babel/plugin-proposal-class-properties',
                    "@babel/plugin-transform-runtime"
                ]
               }
            },
            // 只匹配 scr目录下js, 不包括 node_modules目录
            include: path.resolve(__dirname, 'src'),
            exclude: /node_modules/
          }
    		
    	]
    }
}
```





### 引入全局变量

> 例如：将 jQuery 暴露到全局

在代码中通过内联loader 将 jQuery 以 $ 暴露到 全局

```
import $ from 'expose-loader?$!jquery'
```

在配置项中引入，这样

```
module.exports = {
	...,
    module: {
        rules: [
		test: require.resolve('jquery'),
		use: 'expose-loader?$'
        ]
    }
}

// 使用,需要手动引入
import $ from 'jquery'
```

每个模块自动注入$

```
let webpack = require('webpack')
module.exports = {
	...,
	plugin:s: [
	  new webpack.ProvidePlugin({
	    $: 'jquery'
	  })
	]
}
```

还可以在index.html 模板 用 script 标签引用 jquery CDN

> 通过MDN引入的依赖，需要设置不需要打包选项
>
> 通过 externals 使webpack 不处理某些依赖库

```
module.exports = {
	...,
	externals: {
		jquery: '$'
	}
}
```



### 图片处理

> 图片的引入方式
>
> 1、在 js 中创建 图片引入
>
> 2、在css引入 background('url')
>
> 3、<img src="" />

```js
import url from './logo.png'
let image = new Image();

image.src = url

document.append(image)
```

使用 file-loader 处理

> file-loader 默认在内部生成一张图片到 build 目录，把生成图片的名字返回来
>
> background 中的 图片路径 css-loader 回去处理

```
yarn add file-loader -D
```

```
module.exports = {
	...,
    module: {
        rules: [
		test: /\.(png|jpg|jpeg|gif)$/,
		use: 'file-loader'
        ]
    }
}
```

处理页面 img 标签中的 图片 url

```
yarn add html-withimg-loader -D
```

```
module.exports = {
	...,
    module: {
        rules: [
		test: /\.html$/,
		use: 'html-withimg-loader'
        ]
    }
}
```

url-loader

> url-loader 内部实现了 file-loader. 一般代替file-loader处理图片，将体积小的图片转成base64位，减少请求次数.
>
> 体积大的图片再用file-loader 处理

```js
module.exports = {
	...,
    module: {
        rules: [
		test: /\.(png|jpg|jpeg|gif)$/,
		use: {
			loader: 'url-loader',
			options: {
				limit: 200*1024,// 小于200K 转 base64
				outputPath: 'img/', //将图片打包到 build/img 文件夹中
			}
		}
        ]
    }
}
```



### 打包文件分类

> 就是将打包的文件放置指定的文件，使打包文件具有层次

```js
module.exports = {
    output: {
        filename: 'bundle.js', // 输出文件名
        // filename: 'bundle.[hash].js', // 输出文件名，添加hash值
        // filename: 'bundle.[hash:10].js', // 输出文件名，添加10位hash值
        path: path.resolve(__dirname, 'build'), // 输出路径（绝对路径）
        publicPath: './'
    }
	...,
    plugins: [
        new htmlWebpackPlugin({
            template: './src/index.html',// 模板的位置
            filename: 'index.html',// 打包后模板的名字
            minify: {
                removeAttributeQuotes: true,// 移出双引号
                collapseWhitespace:  true,// 压缩成一行
                hash: true,// 注入的js 文件名添加hash值
                
            }
        }),
        new MiniCssExctractPlugin({
           filename: '/css/main.css' 
        })
    ],
    module: {
        rules: [
		test: /\.(png|jpg|jpeg|gif)$/,
		use: {
			loader: 'url-loader',
			options: {
				limit: 200*1024,// 小于200K 转 base64
				outputPath: '/img/', //将图片打包到 build/img 文件夹中
            	 publicPath: 'www.xxx.com',// 给图片单独设置 publicPath
			}
		}
        ]
    }
}
```



### 多页应用打包

> 多入口，多出口. webpack 会打包多个入口js.
>
> 多个html 模板， 需要借助 html-webpack-plugin 插件

```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
	entry: {
        home: './src/index.js',
        other: './src/other.js'
    },
    output: {
        // [name] 代表 home,other
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
    // 默认打包，home,other 都会在两个html中引入
    // 通过 chunks 设置只引入需要的入口js
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            filename: 'home.html',
            chunks: ['home']
        }),
        new HtmlWebpackPlugin({
            template: './other.html',
            filename: 'other.html'
            chunks: ['other']
        })
    ]
}
```



### 配置 source-map



```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    ...,
    // 源码映射 用于调试，打包后的js 被压缩处理，看不见源码
    // 增加映射文件，标识错误行列信息
    devtool: 'source-map',// 
    // 不会产生单独文件，但是可以显示错误行信息
    //devtool: 'eval-source-map',
    // 不会产生行列信息，但是是一个单独的映射文件
    //devtool: 'cheap-module-source-map',
    // 不会产生文件,集成在打包文件中，不会产生列信息,有行信息
    //devtool: 'cheap-module-eval-source-map' 

}
```



### webpack 实时打包

> 代码修改已保存就更新页面

```js
module.exports = {
    ...,
    watch: true,
    watchOptions: {
    	poll: 1000,// 每秒问我 1000次
    	aggreatementTimeout: 500 // 防抖，500毫秒内只打包一次
    	ignored: /node_modules/,// 不需要监控
    }
}
```



### webpack 小插件

> 实用常用的插件

1. cleanWebpackPlugin

	每次打包时清除上次的dist目录

	```
	yarn add clean-webpack-plugin -D
	```

	```js
	const CleanWebpackPlugin = require('clean-webpack-plugin')
	module.exports = {
	    ...,
	    plugins: [
	    	// 每次打包 清除 dist 文件夹
	    	new CleanWebpackPlugin('./dist')
	    ]
	}
	```

	

2. copyWebpackPlugin

	可以将指定文件或文件夹复制到指定文件

	```
	yarn add copy-webpack-plugin -D
	```

	```js
	const copyWebpackPlugin = require('copy-webpack-plugin')
	module.exports = {
	    ...,
	    plugins: [
	    	// 每次打包 复制 文件/夹 到指定目录。可指定多个动作
	    	new copyWebpackPlugin([
	    		{
	                from: './doc',
	                to: './dist'
	            }
	        ])
	    ]
	}
	
	```

	

3. bannerPlugin(wenpack内置)

	版权声明，打包后的文件最顶端显示版权声明注释

	```js
	const webpack = require('webpack')
	
	module.exports = {
	    ...,
	    plugins: [
	    	new webpack.bannerPlugin('make on 2022 by xxx')
	    ]
	}
	```

	

### webpack  跨域配置

​	前端开发测试时可配置 proxy 解决跨域问题

```js
// 比如 页面服务是 localhost 8080, 接口服务localhost 3000
module.exports = {
    ...,
    devServer: {
    	//1、 如果后端是用express 写的，可以用中间件（webpack-dev-middleware）读取前端配置，实现前后端运行在一个端口	
    
    	before(app): {//2、 此处可模拟服务请求数据, [express 服务]
    		app.get('/user', (req, res)=> {
                res.json({name: 'xxx'})
            })
        },
    	//3、 把localhost:8080/api	开头的请求转发到 把localhost:3000
    	// localhost:8080/api/user -> localhost:3000/user
    	proxy: {
    		"/api": {
    			target: 'http://localhost:3000',
    			pathRewrite: {'/api', ''}
    		}
    	}
    }
}
```



### resolve 解析

​	对第三方模块的优化处理

```js
module.exports = {
    ...,
    resolve: { // 解析第三方模块 common
    	// 从当前路径查找依赖，不要去父级及其他路径去找了
    	module: [path.resolve('node_modules')],
        // 第三方模块的package.json 文件 main 字段 默认入口，可设置先找 style，没有在找 main
        mainFilds: ['style', 'main'],
        // 配置别名
        alias: {
            '@': path.resolve(__dirname, 'src')
        },
        // 省略后缀的处理, 先视为js 文件，没有就找 css 最后找 json 文件
        extensions: ['.js', '.css', '.json']
    }
}
```



### 注入环境变量区分 开发环境



```js
const webpack = require('webpack')
module.exports = {
    ...,
    plugins: [
    	new webpack.DefinedPlugin({
    		DEV: "'dev'"// JOSN.stringif('dev')
        })
    ]
}

访问： 
console.log(DEV)
```

## 优化项

### noParse  不解析某些包

​	我们在 require 某个依赖时，webpack 默认会去解析这个依赖是否还依赖其他包，有的话就一起打包

noParse   则可设置不去检查这个依赖了

```
module.exports = {
    ...,
    module: {
    	noParse: /jquery/, // 不去解析 jQuery 依赖库
    }
}

```



### 排除，忽略某些文件

在进行某个规则可具体优化。

在引用momentjs 实践处理库时，moment 默认引入了所有语言包，导致文件打包很大，可以设置忽略语言包

```js
// 设置语言包忽略后，如果要设置中文，需要手动引入中文包
import 'moment/local/zh-cn';
moment.locale('zh-cn')

module.exports = {
    ...,
    plugins: [
    	new webpack.IgnorePlugin(/\./locale//, /moment/)
    ],
    module: {
    	{
    		test: /\.js$/,
    		exclude: /node_modules/, // 排除
    		include: path.resolve('src'),// 只找 src 下js
    		use: ...
    	}
    }
}

```



###  动态链接库

开发中，有些不会变的模块可以提前打包成库，后边直接使用打包好的库。不需要每次都打包,下次打包体积就变小了

例如：react, react-dom 模块

新增打包配置文件 webpack-config.react.js

```js
let path = require('path')
let webpack = require('webpack')
module.exports = {
	mode: 'devlopment',
    entry: {
        react: ['react', 'react-dom'],
    },
    output: {
        filename: '_dll_[name].js',
        path: path.resolve(__dirname, 'dist'),
        library: '_dll_[name]',// 打包后的模块名
        // libraryTarget: 'val' // commonj var this ..., 模块化模式 CMD，AMD，Commonjs
    },
    plugins: [
        new webpack.DLLPlugin({
            // name == library
            name: '_dll_[name]',
            // 清单, 模块名映射关系
            path: path.resolve(__dirname, 'dist', 'minifest.json')
        })
    ]
}
```

webpack.config.js 配置引用

```js
let path = require('path')
let webpack = require('webpack')
module.exports = {
	...,
    plugins: [
        new webpack.DLLReferencePlugin({
            manifest: path.resolve(__dirname, 'dist', 'manifest.json')
        })
    ]
}
```

在index.html 中 使用打包好的库

```html
<script src="/_dll_react.js" ></script>
```



### 多线程打包 进程

使用 第三方模块 happypack, 项目体积大的时候会使打包速度变快



```js
let path = require('path')
let HappyPack = require('happypack')
module.exports = {
	...,
    module: {
    	{
    		test: /\.js$/,
    		exclude: /node_modules/, // 排除
    		include: path.resolve('src'),// 只找 src 下js
    		use: 'HappyPack/loader?id=js'
    	},
        {
            test: /\.css$/, 
            use: 'HappyPck/loader?id=css'
        },
    }
    plugins: [
     new HappyPack({
         id: 'js',
         use: [
             {
               loader: 'babel-loader',
    		  options: {
    			presets: [
    			  '@babel/preset-env'
                ],
                plugins: [
                     ["@babel/plugin-proposal-decorators", { "legacy": true }],
                    '@babel/plugin-proposal-class-properties',
                    "@babel/plugin-transform-runtime"
                ]
             }
         ]
     }),
      new HappyPack({
         id: 'js',
         use: [
           {
                loader: 'style-loader',
                options: {
                    insertAt: 'top'
                }
            }, 
             'css-loader'
          ]
        ]
     }),
     
}
```



### webpack 自带的优化



tree-shaking  

生产环境下打包会去除没用的代码, 但只对 import 语法有效。通过 require 方式引入的模块不会优化



scope hosting

作用域提升

```js
let a = 1;

let b = 2;

let c = 3;

let d = a + b + c;
console.log(d)


代码会被编译为：
console.log(6)
```



### 抽离公共代码

多入口环境下才能配置

页面 都引用了 a/b.js. 所以缓存 a/b.js

```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode: 'production',
    optimization: {
      splitChunks: {// 分割代码块
          cacheGroups: {
              common: {// 符合条件抽离成公共模块
                  chunks: 'initial', // 入口处开始分析
                  minSize: 500*1024,// 体积大于500kb
                  minChunks: 2,// 至少引用了2次
              },
              // 抽离第三方模块为公共
              vendor: {
                  priorty: 1, // 权重，优先抽离公共模快
                  test: /node_modules/,// 抽离出来
                  chunks: 'initial', // 入口处开始分析
                  minSize: 500*1024,// 体积大于500kb
                  minChunks: 2,// 至少引用了2次
              }
          }
      }  
    },
	entry: {
        home: './src/index.js',
        other: './src/other.js'
    },
    output: {
        // [name] 代表 home,other
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
    // 默认打包，home,other 都会在两个html中引入
    // 通过 chunks 设置只引入需要的入口js
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            filename: 'home.html',
        }),
        new HtmlWebpackPlugin({
            template: './other.html',
            filename: 'other.html'
        })
    ]
}
```

### webpack懒加载

es6 草案语法 , 不管是 vue,还是react 都是靠这个语法实现的懒加载

```
import('/xxx.js').then(data=> {})
```



### webpack热更新



```js
module.exports = {
    ...,
    devServer: {
    	port: 3000,
    	open: true,
    	contentBase: './dist',
    	hot: true, // 开启热更新
    },
    plugins: [
        // 可打印提示哪个模块更新了，
        new webpack.NameModulePlugin(),
        // 热更新插件
        new webpack.HotModuleReplacementPlugin()
    ]
}
```

监听data模块变化

```js
import str from './data';

if(module.hot) {
    module.hot.accept('./data', ()=> {
        let str = require('./data');
        console.log(str)
    })
}
```



### 图片压缩方案

> [image-minimizer-webpack-plugin](https://github.com/webpack-contrib/image-minimizer-webpack-plugin/)

vue-cli 项目中 vue.config.js

```json
const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");

module.exports = {
  configureWebpack: {
    plugins: [
      new ImageMinimizerPlugin({
        minimizerOptions: {
          plugins: [
            ["gifsicle", { interlaced: true }],
            ["jpegtran", { progressive: true }],
            ["optipng", { optimizationLevel: 5 }],
            [
              "svgo",
              {
                plugins: [
                  {
                    removeViewBox: false
                  }
                ]
              }
            ]
          ]
        }
      })
    ]
  }
};

```

