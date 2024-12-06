---
title:  vite 打包优化项
date: 2022-06-17 08:23:10
tags: vite
categories: vite
---



### 配置别名

```json
 resolve: {
    // https://cn.vitejs.dev/config/#resolve-alias
    alias: {
      // 设置路径
      '~': path.resolve(__dirname, './'),
      // 设置别名
      '@': path.resolve(__dirname, './src')
    },
    // https://cn.vitejs.dev/config/#resolve-extensions
    extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue']
  },
```

### 代码分割

<!--more-->

```json
build: {
    outDir: "dist",
    sourcemap: false,
    minify: 'esbuild',
    chunkSizeWarningLimit: 1500,
    emptyOutDir: true,
    rollupOptions: {
      output: {
        // 静态资源打包做处理
        chunkFileNames: 'js/[name]-[hash].js',
        entryFileNames: 'js/[name]-[hash].js',
        assetFileNames: '[ext]/[name]-[hash].[ext]',
        manualChunks(id) {
            if (id.includes('node_modules')) {
                return id.toString().split('node_modules/')[1].split('/')[0].toString();
            }
        },
          // key自定义 value[] 插件同步package.json名称 或 src/相对路径下的指定文件 （自己可以看manualChunks ts类型）
          // manualChunks: {
          //     // vue vue-router合并打包
          //     vue: ['vue'],
          //     vueRouter: ['vue-router'],
          //     axios: ['axios'],
          //     dayjs: ['dayjs'],
          //     qs: ['qs'],
          //     elementPlus: ['element-plus'],
          //     // 两个文件合并成一个helloWorld文件
          //     // helloWorld: ['src/components/HelloWorld.vue','src/components/HelloWorld1.vue'],
          // }
      }
  }
  },
```

#### 1.Vite代码分割方法1

// vite.config.ts

```javascript
build: {
    // rollup 配置
    rollupOptions: {
        output: {
            // key自定义 value[] 插件同步package.json名称 或 src/相对路径下的指定文件 （自己可以看manualChunks ts类型）
            manualChunks: {
                // vue vue-router合并打包
                vue: ['vue', 'vue-router'],
                echarts: ['echarts'],
                lodash: ['lodash'],
                // 两个文件合并成一个helloWorld文件
                helloWorld: ['src/components/HelloWorld.vue','src/components/HelloWorld1.vue'],
                ...
            }
        }
    }
}

```

#### Vite代码分割方法2

// vite.config.ts

```javascript
build: {
    // rollup 配置
    rollupOptions: {
        output: {
            manualChunks(id: any): string {
            	if (id.includes("style.css")) {
                        // 需要单独分割那些资源 就写判断逻辑就行
                        return 'src/style.css';
                }
                if (id.includes("HelloWorld.vue")) {
                        // 单独分割hello world.vue文件
                        return 'src/components/HelloWorld.vue';
                }
                // // 最小化拆分包
                if (id.includes("node_modules")) {
                        return id
                                .toString()
                                .split("node_modules/")[1]
                                .split("/")[0]
                                .toString();
                }
            }
        }
    }
}
```

### 清除代码console和debugger

> terser 配置需要额外安装依赖

```json
build: {
	minify: 'terser',
    terserOptions: {
        // 清除console和debugger
        compress: {
            drop_console: true,
            drop_debugger: true,
        },
    },
}
```

### 配置 px 转 rem 自适应

> npm i postcss-pxtorem@5.1.1 -D
>
> [参考链接](https://juejin.cn/post/7114211754413916167#heading-4)

vite.config.js

```json
css: {
    postcss: {
      plugins: [
        postCssPxToRem({
          propList: ["*"],//匹配CSS中的属性，* 代表启用所有属性  不启用边框，可以写['*'，'!border*']
          replace: true,
          rootValue: 16,// 换算的基数 根元素字体大小
          unitPrecision: 5,//转换成rem后保留的小数点位数
          minPixelValue: 12,//小于12px的样式不被替换成rem
          //忽略一些文件，不进行转换
          exclude: (file) => {
            if (file.indexOf('src\\views\\fontend\\') !== -1) {
                return false;
            }
            return true
          }
        })
      ]
    }
  }
```

main.js

```js
import { useREM } from "@/utils/flexible";

useREM();
```

flexble.js

```js
/**
 * 初始化 rem 基准值
 */
export const useREM = () => {
  // 更新 fontSize
  function resize() {
    // 获取 html 标签
    const html = document.querySelector("html");
    // 获取根元素 fontSize 标准，屏幕宽度 *16 / 1920。
    let fontSize = (window.innerWidth * 16) / 1920;
    // 定义根元素（html）fontSize 的大小 （rem）
    html.style.fontSize = fontSize + "px";
  }
  // 监听 html 文档被解析完成的事件
  document.addEventListener("DOMContentLoaded", resize);
  window.addEventListener("resize", resize);
};
```

### gzip静态资源压缩

```json
import viteCompression from 'vite-plugin-compression'
//在plugins配置数组里添加gzip插件
 plugins: [viteCompression({
    verbose: true,
    disable: false,
    threshold: 10240,
    algorithm: 'gzip',
    ext: '.gz',
  })],
```

### 全局样式注入

全局注入的样式最好是全局变量，非全局变量样式最好在main.js中引入

```js
css: {
    preprocessorOptions: {
      scss: {
          // additionalData: '@use "@/assets/styles/commom.scss" as *;'
          additionalData: '@import "@/assets/styles/commom.scss";'
      },
      less: {
          // 加上 reference 可避免样式重复注入问题，
          additionalData: '@import (reference) "@/assets/styles/commom.less";'
      }
    }
  },
```

### 引入 iconfont 

iconfont 图标文件下载后，放置在 /assets/iconfont 文件夹下

注册icon组件

```vue
<template>
  <span
    class="iconfont"
    :class="[iconName, iconSize]"
    :style="{ color: props.color, fontSize }"
  ></span>
</template>

<script setup>
import { computed } from 'vue'
import { px2rem } from '@/utils/common'
const props = defineProps({
  name: {
    type: String,
    default: ''
  },
  color: {
    type: String,
    default: 'var(--color)'
  },
  size: {
    type: String,
    default: 'default'
  },
  fontSize: {
    type: Number,
    default: 0
  }
})
const iconName = computed(() => `icon-${props.name}`)
const iconSize = computed(() => `size-${props.size}`)
const fontSize = computed(() => `${props.fontSize ? px2rem(props.fontSize) : 'inherit'}`)
</script>

<style lang="scss" scoped>
.iconfont {
  --color: inherit;
  color: var(--color);
}
.size-default {
  font-size: 16px;
}
.size-small {
  font-size: 12px;
}
.size-large {
  font-size: 24px;
}
</style>

```

使用组件

```vue
<m-icon name="fanhui"></m-icon>
```

配合 element 组件使用

```vue
<el-button type="primary" size="large">
  <template #icon>
    <m-icon name="fanhui"></m-icon>
  </template>
  开始
</el-button>
```

### 注册指令

- directives

	- modules

		xxx.js

		```js
		export default  {
		  // 在绑定元素的 attribute 前
		  // 或事件监听器应用前调用
		  created(el, binding, vnode) {
		    // 下面会介绍各个参数的细节
		  },
		  // 在元素被插入到 DOM 前调用
		  beforeMount(el, binding, vnode) {},
		  // 在绑定元素的父组件
		  // 及他自己的所有子节点都挂载完成后调用
		  mounted(el, binding, vnode) {},
		  // 绑定元素的父组件更新前调用
		  beforeUpdate(el, binding, vnode, prevVnode) {},
		  // 在绑定元素的父组件
		  // 及他自己的所有子节点都更新后调用
		  updated(el, binding, vnode, prevVnode) {},
		  // 绑定元素的父组件卸载前调用
		  beforeUnmount(el, binding, vnode) {},
		  // 绑定元素的父组件卸载后调用
		  unmounted(el, binding, vnode) {}
		}
		```

	- index.js

```js
export default {
  install(app) {
    const directives = import.meta.glob('./modules/*.js', { eager: true })
    for (const [key, value] of Object.entries(directives)) {
      // 拼接指令注册的 name
      const name = key.split('/')[2].replace('.js', '')
      console.log(name, value)
      // 通过 defineAsyncComponent 异步导入指定路径下的组件
      app.directive(name, defineAsyncComponent(value))
    }
  },
}

// main.js 注册
import derictives from 'xxx/derictives'
app.use(derictives)
```



### 注册svg组件

1. 安装依赖

	```shell
	yarn add vite-plugin-svg-icons -D
	```

2. 修改vite配置项

	```js
	import { createSvgIconsPlugin } from 'vite-plugin-svg-icons';
	...
	plugins: [
		...,
		createSvgIconsPlugin({
	      iconDirs: [path.resolve(process.cwd(), 'src/assets/icons/svg')],
	      symbolId: 'icon-[name]',
	    })
	]
	```

3. 引入注册

	```js
	// svg图标, 第一行不能少
	import 'virtual:svg-icons-register'
	import SvgIcon from './components/SvgIcon'
	
	// 注册svg-icon组件
	app.component('svg-icon', SvgIcon)
	
	```

4. SvgIcon 组件代码

  ```vue
  <template>
    <svg :class="svgClass" aria-hidden="true">
      <use :xlink:href="iconName" :fill="color" />
    </svg>
  </template>
  
  <script setup>
  import { computed } from 'vue'
  const props = defineProps({
    iconClass: {
      type: String,
      required: true
    },
    className: {
      type: String,
      default: ''
    },
    color: {
      type: String,
      default: 'currentColor'
    },
    disabled: {
      type: Boolean,
      default: false
    }
  })
  const iconName = computed(() => `#icon-${props.iconClass}`)
  const color = computed(() => {
    if (props.disabled) {
      return '#ccc'
    }
    return props.color
  })
  const svgClass = computed(() => {
    if (props.className) {
      return `svg-icon ${props.className}`
    }
    return 'svg-icon'
  })
  </script>
  
  <style scope lang="scss">
  .sub-el-icon,
  .nav-icon {
    display: inline-block;
    font-size: 15px !important;
    margin-right: 2px !important;
    position: relative;
  }
  
  .svg-icon {
    width: 1em;
    height: 1em;
    min-width: 1em;
    position: relative;
    fill: currentColor;
    vertical-align: -2px;
  }
  </style>
  
  ```

5. svg 资源

	path 不需添加 fill="currentColor"，  让图标自适应颜色，如果添加了fill 那么color参数就不生效了

	```svg
	<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1024 1024">
	<path  d="M288 896h448q32 0 32 32t-32 32H288q-32 0-32-32t32-32"/>
	</svg>
	```

	

6. 使用

  ```vue
  <svg-icon icon-class="bug"></svg-icon>
  ```

### 字典通用解决方案

若依参考方案

useDict 直接返回 字典信息，使用时不直接从 store 中取

1. useDict 函数

	```js
	/**
	 * 获取字典数据
	 */
	export function useDict(...args) {
	  const res = ref({});
	  return (() => {
	    args.forEach((dictType, index) => {
	      res.value[dictType] = [];
	      const dicts = useDictStore().getDict(dictType);
	      if (dicts) {
	        res.value[dictType] = dicts;
	      } else {
	        getDicts(dictType).then(resp => {
	          res.value[dictType] = resp.data.map(p => ({ label: p.dictLabel, value: p.dictValue, elTagType: p.listClass, elTagClass: p.cssClass }))
	          useDictStore().setDict(dictType, res.value[dictType]);
	        })
	      }
	    })
	    return toRefs(res.value);
	  })()
	}
	```

2. pinia useDictStore 模块

	```js
	const useDictStore = defineStore(
	  'dict',
	  {
	    state: () => ({
	      dict: new Array()
	    }),
	    actions: {
	      // 获取字典
	      getDict(_key) {
	        if (_key == null && _key == "") {
	          return null;
	        }
	        try {
	          for (let i = 0; i < this.dict.length; i++) {
	            if (this.dict[i].key == _key) {
	              return this.dict[i].value;
	            }
	          }
	        } catch (e) {
	          return null;
	        }
	      },
	      // 设置字典
	      setDict(_key, value) {
	        if (_key !== null && _key !== "") {
	          this.dict.push({
	            key: _key,
	            value: value
	          });
	        }
	      },
	      // 删除字典
	      removeDict(_key) {
	        var bln = false;
	        try {
	          for (let i = 0; i < this.dict.length; i++) {
	            if (this.dict[i].key == _key) {
	              this.dict.splice(i, 1);
	              return true;
	            }
	          }
	        } catch (e) {
	          bln = false;
	        }
	        return bln;
	      },
	      // 清空字典
	      cleanDict() {
	        this.dict = new Array();
	      },
	      // 初始字典
	      initDict() {
	      }
	    }
	  })
	
	export default useDictStore
	
	```

3. 全局注册 useDict 函数

	```js
	import { useDict } from '@/utils/dict'
	
	// 全局方法挂载
	app.config.globalProperties.useDict = useDict
	```

4. 使用

	```js
	const { proxy } = getCurrentInstance();
	const { sys_normal_disable } = proxy.useDict("sys_normal_disable");
	```


自定义方案

**dict.js 定义 pinia 全局状态管理**

```js
import { defineStore } from "pinia";
export default defineStore('dictStore', {
    state: () => {
        return {
            dicts: {
                criticality: [
                    { value: "1", label: "严重" },
                    { value: "2", label: "中等" },
                    { value: "3", label: "轻微" },
                ]
            },
            labels: {
                criticality: {
                    1: "严重",
                    2: "中等",
                    3: "轻微",
                }
            }
        }
    },
    getters: {
        dict(state) {
            return state.dicts;
        },
        label(state) {
            return state.labels;
        }
    },
    actions: {
        setDict( dictName, dict) {
            if(!this.dicts[dictName]) {
                this.dicts[dictName] = [];
                this.labels[dictName] = {};
            }
            this.dicts[dictName] = dict.dict;
            this.labels[dictName] = dict.label;
        },
        delDict( dictName) {
            if(this.dicts[dictName]) {
                delete this.dicts[dictName];
                delete this.labels[dictName];
            }
        }
    },
    // 开启缓存
    persist: {
        enabled: true
    }
})
```

**common.sj 中定义注册字典方法**

如果后端除了标准字典接口之外还有非标准接口可以使用options参数，可自定义 字典label,字典value（字典id使用 value,也可以再加一个字典id），参数取值支持 dataStr (如接口取值 res.rows 传 "rows", res.data.data 传 "data.data", 默认 取值res.data)，request 非标准的字典接口（axios封装后的接口），params (非标准字典接口的参数)

```js
// 加载的字典名称（与接口请求字典类型保持一至），字典store对象，非标准字典加载配置
export const loadDict = async (dictName, store, options = {}) => {
  let { request = null, ...others } = options;
  options = Object.assign({
      labelName: "dictLabel",
      valueName: "dictValue",
      datastr: "",
      params: null
  }, others)
  try {
      if(store.dict[dictName]?.length) return
      let dict = [],label = {};
      let rs = null, list = [];
      if(request) {
          rs = await request(options.params);
      } else {
          rs = await getDictByType(dictName);
      }
      list = rs.data;
      if (options.dataStr) {
          list = options.dataStr.split('.').reduce((rs,key) => {
              return rs[key];
          }, rs)
      }
      dict = list.map(item => {
          label[item[options.valueName]] = item[options.labelName];
          return {
              label: item[options.labelName],
              value: String(item[options.valueName]),
              id: String(item[options.valueName]),
          }
      })
      store.setDict(dictName, {dict, label});
  } catch (error) {
      console.warn(`字典${ dictName }加载失败`, error);
  }
}
```

**使用**

```js
import { loadDict } from '@/utils/common';
import { useRouter } from 'vue-router';
const store = usedictStore();
const initDict = async () => {
	//加载标准字典
    loadDict("fault_solution", store);

    // 加载非标准字典
    loadDict("gates", store, {
        labelName: "type",
        valueName: "logicGateID",
        request: getFaultGatesList,
        dataStr: "rows"
    });
}

// 保证字典加载完成后请求列表
onMounted(async ()=> {
    await initDict();
    getList();
})
```



### 完整的配置参考

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig, loadEnv } from 'vite'
import vue from '@vitejs/plugin-vue'
import postCssPxToRem from 'postcss-pxtorem'
import { visualizer } from 'rollup-plugin-visualizer'
import viteCompression from 'vite-plugin-compression';
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => {
  const { VITE_VERSION = "1.1.0" } = loadEnv(mode, __dirname)
  const manualChunks = [
    { name: "element-plus", test: /[\\/]node_modules[\\/]element-plus[\\/]/ },
    { name: "axios", test: /[\\/]node_modules[\\/]axios[\\/]/ },
    { name: "dayjs", test: /[\\/]node_modules[\\/]dayjs[\\/]/ },
  ]
  return {
    plugins: [
      createSvgIconsPlugin({
        // 要缓存的图标文件夹
        iconDirs: [path.resolve(__dirname, 'src/assets/icons/svg')],
        // 执行 icon name 的格式
        symbolId: 'icon-[name]',
      }),
      vue(),
      visualizer(),
      viteCompression({
        verbose: true, // 是否在控制台输出压缩结果
        disable: false, // 是否禁用
        threshold: 1024, // 
        algorithm: "gzip", // 压缩算法,可选 [ 'gzip' , 'brotliCompress' ,'deflate' , 'deflateRaw']
        ext: ".gz", // 文件后缀
        deleteOriginFile: false, // 是否删除源文件
      })
    ],
    resolve: {
      alias: {
        '@': fileURLToPath(new URL('./src', import.meta.url))
      }
    },
    css: {
      devSourcemap: true,
      postcss: {
        plugins: [
          postCssPxToRem({
            propList: ["*"],
            replace: true,
            rootValue: 16,
            unitPrecision: 5,
          })
        ]
      }
    },
    build: {
      minify: "terser",
      terserOptions: {
        compress: {
          drop_console: true,
          drop_debugger: true
        }
      },
      rollupOptions: {
        output: {
          chunkFileNames: `static/js/[name]-[hash]-${VITE_VERSION}.js`,
          entryFileNames: `static/js/[name]-[hash]-${VITE_VERSION}.js`,
          assetFileNames: `static/[ext]/[name]-[hash]-${VITE_VERSION}.[ext]`,
          manualChunks(id) {
            const flag = manualChunks.some(v => v.test.test(id));
            if (flag) {
              return id.toString().split('node_modules/')[1].split('/')[0].toString();
            }
          }
        }
      }
    }
  }
})

```

### 代码风格配置

> 可以在脚手架初始化项目是添加 eslint 和 prettier, 也可以为已有项目添加，参考如下
>
> [注意]新版本eslit和prettier配置文件后缀都必须是.cjs
>
> **vue3 新版本 脚手架，使用vite构建项目，添加eslit和prettier  就自动设置完成 ** 但是可能还要设置.vscode 文件夹 中的配置
>
> ```json
> // settings.json
> {
>   "explorer.fileNesting.enabled": true,
>   "explorer.fileNesting.patterns": {
>     "tsconfig.json": "tsconfig.*.json, env.d.ts",
>     "vite.config.*": "jsconfig*, vitest.config.*, cypress.config.*, playwright.config.*",
>     "package.json": "package-lock.json, pnpm*, .yarnrc*, yarn*, .eslint*, eslint*, .prettier*, prettier*, .editorconfig"
>   },
>   "editor.codeActionsOnSave": {
>     "source.fixAll": "explicit"
>   },
>   "editor.formatOnSave": true,
>   "editor.defaultFormatter": "esbenp.prettier-vscode"
> }
> 
> ```
>
> ```json
> // extensioins
> {
>   "recommendations": [
>     "Vue.volar",
>     "dbaeumer.vscode-eslint",
>     "EditorConfig.EditorConfig",
>     "esbenp.prettier-vscode"
>   ]
> }
> 
> ```
>
> 

[Vite+vue3+js+eslint+prettierc](https://segmentfault.com/a/1190000041954694)

[Vite+vue3+ts+eslint+prettierc](https://juejin.cn/post/7142815651294511135?searchId=202403141408434480E7D6A2BC567E973B)



### 支持 require 语法

vite 默认不支持 require ，需要引入 "vite-plugin-require-transform"

```json
plugins: [
    ...,
    RequireTransform({
      fileRegex: /^(?!.*node_modules).*\.(js|vue)$/,
    }),
]
```

配置需忽略 node_modules 文件，不然打包可能导致其他第三方包报错。



### 环境变量获取

在 vue-cli 中， 使用的是 webpack 打包，使用

```js
process.env.NODE_ENV === 'production'
```

判断，但是在vite中，没有 process，而是使用

【注意】vite.config.js 配置文件中还是使用 process.env.NODE_ENV 访问

```js
import.meta.env.MODE === "production"
```



### [全局 api 自动引入](https://github.com/unplugin/unplugin-auto-import)

```shell
npm i -D unplugin-auto-import


// vite.config.ts
import AutoImport from 'unplugin-auto-import/vite'

export default defineConfig({
  plugins: [
    AutoImport({ 
      imports: ['vue', 'vue-router'],
      dts: true,
    }),
  ],
})
```

**配置完成后 根目录会新增 auto-imports.d.ts，或一个json 文件，在 eslint.config.js 中配置 忽略即可，不然 eslint 会提示你 引用错误**

使用前

```js
import { computed, ref } from 'vue'

const count = ref(0)
const doubled = computed(() => count.value * 2)
```

使用后

```js
const count = ref(0)
const doubled = computed(() => count.value * 2)
```

### index.html 模板使用 ejs 语法

```shell
yarn add vite-plugin-ejs
```

注入

```js
import {defineConfig} from "vite";
import {ViteEjsPlugin} from "vite-plugin-ejs";

export default defineConfig({
  plugins: [
    // Without Data
    ViteEjsPlugin(),
    
    // With Data
    ViteEjsPlugin({
      domain: "example.com",
      title: "My vue project!"
    }),
    
    // Or With Vite Config
    ViteEjsPlugin((viteConfig) => {
      // viteConfig is the current viteResolved config.
      return {
        root: viteConfig.root,
        domain: "example.com",
        title: "My vue project!"
      }
    }),
  ],
});
```

使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <link rel="icon" href="/favicon.ico"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title><%= domain %> | <%= title %></title>

    <!-- Run Conditions-->
    <% if(isDev){ %>
        <script src="/path/to/development-only-script.js"></script>
    <% } else { %>
        <script src="/path/to/production-only-script.js" crossorigin="anonymous"></script>
    <% } %>
</head>
<body>
<div id="app"></div>
<script type="module" src="/src/main.ts"></script>
</body>
</html>
```

