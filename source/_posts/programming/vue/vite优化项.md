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

vite.config.js

```json
css: {
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
	    default: ''
	  },
	})
	const iconName = computed(() => `#icon-${props.iconClass}`)
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
	font-size: 15px;
	margin-right: 12px;
	position: relative;
	}
	
	.svg-icon {
	width: 1em;
	height: 1em;
	position: relative;
	fill: currentColor;
	vertical-align: -2px;
	}
	</style>
	
	```

5. 使用

	```vue
	<svg-icon icon-class="bug"></svg-icon>
	```

### 字典通用解决方案

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

