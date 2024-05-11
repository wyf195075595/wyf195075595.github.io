---
title: 页面 rem 适配
date: 2022-07-21 21:54:10
tags: js
categories: js
---

> rem 自适应方案

### 宽度 1920 时, 1rem 等于 100px.方便换算

```js
(function(doc, win) {
    var docEl = doc.documentElement,
        resizeEvt = "orientationchange" in window ? "orientationchange" : "resize",
    recalc = function() {
        var width = docEl.clientWidth / 19.20;
        docEl.style.fontSize = width + "px";
        scaleNum = width;
    };
    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener("DOMContentLoaded", recalc, false);
})(document, window);
```

<!--more-->

### 使用开发工具 计算rem

> 默认 1920 下16px，插件 px to rem 默认配置 16px

```js
window.addEventListener('resize', () => {
    window.getclientSize();
    this.$bus.emit('window-resize', null)
})


getclientSize(){
  let clientWidth = document.documentElement.clientWidth || document.body.clientWidth //可视区域宽度
  // let clientHeight = document.documentElement.clientHeight || document.body.clientHeight //可视区域高度
  let clientWidth_base = 1920

  clientWidth_base = clientWidth;
  document.documentElement.style.fontSize = clientWidth_base / 1920 * 16 + 'px'
  // $('html').css("font-size", clientWidth_base / 1920 * 16 + 'px');
  window.radioBase = clientWidth_base / 1920
}
```



### vue+amfe-flexible开发移动端

> (手淘移动端适配方案)

```
cnpm install amfe-flexible -S
cnpm install postcss-pxtorem -S
```

配置main.js

```
//引入
import 'amfe-flexible/index'
```

配置 vue.config.js

```json
module.exports = {
    css: {
        loaderOptions: {
            postcss: {
                plugins: [
                    require('postcss-pxtorem')({ // 把px单位换算成rem单位
                        rootValue: 75, // 换算的基数(设计图750的根字体为75,若是设计图为640:则rootValue=64)
                        propList: ['*']
                    })
                ]
            }
        }
    },
    configureWebpack: config => {
        if (process.env.NODE_ENV === 'production') {
            // 为生产环境修改配置...
        } else {
            // 为开发环境修改配置...
        }
    }
}

// 或者

"postcss-pxtorem": {
   "rootValue": 75,
   "propList": ['*','!font','!font-size']
}

// 或者

"css": {
    loaderOptions: {
      less: {},
      postcss: {
        postcssOptions: {
          plugins: {
            autoprefixer: {},
            "postcss-pxtorem": {
              rootValue: 75,
              propList: [
                "*",
                "!font-size",
                "!border*",
                "!letter-spacing",
                "!text-indent",
              ],
              mediaQuery: false,
              replace: true,
              unitPrecision: 5,
              exclude: /node_modules/i,
            },
          },
        },
      },
    },
}
```

修改 index.html

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
```

此插件也可直接于页面引入

```js
  <script src="<%= BASE_URL %>static/amfe-flexible.js"></script>
```



> *在CSS样式中可直接书写750PX,既表明 整屏的宽度,若是设计图不是750的,可在vue.config.js中 rootValue属性,修改成设计图宽度便可,详见vue.config.js注释说明**css*



postcss-pxtorem 缺陷

内联样式，行内样式 px单位不会自动转换，需要手动处理

```js
function px2rem(px){
  if(/%/ig.test(px)){ // 有百分号%，特殊处理，表述pc是一个有百分号的数，比如：90%
    return px
  }else{
    return (parseFloat(px) / 37.5) + 'rem'
  }
}
```

```vue
<Dialog
    v-model="isShow"
    :title="dialogTitle"
    width="35%"
    :height="px2rem('300px')"
    @close="closeDialog(addFormRef)"
/>
<el-form
    label-position="left"
    :label-width="px2rem(120)"
    :model="addForm"
    ref="addFormRef"
    class="dialog-form"
>
```


