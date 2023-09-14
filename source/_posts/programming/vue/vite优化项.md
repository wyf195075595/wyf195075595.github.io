---
title:  vite 打包优化项
date: 2022-06-17 08:23:10
tags: vite
categories: vite
---



### 配置别名

```json
resolve: {
    alias: {
        '@/': `${pathSrc}/`,
    },
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

