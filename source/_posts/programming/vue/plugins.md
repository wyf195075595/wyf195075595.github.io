---
title:  插件合集
date: 2022-09-23 08:23:10
tags: vue
categories: vue
---

### 1、VueUse 

> ​	VueUse 是 **Anthony Fu  大佬**的一个开源项目，它为Vue的开发者提供了大量用于 Vue2 和Vue3 的基本 **Composition API** 实用工具函数。
>
> 它有几十个用于常见开发人员用例的解决方案，如跟踪`ref`更改，检测元素可见性，简化常见Vue模式，键盘/鼠标输入等。 这是真正节省开发时间的好方法，因为我们不必自己亲手添加所有这些标准功能，拿来主义，用就对了(再次感谢大佬的付出)。
>

<!--more-->

> 我喜欢VueUse库，因为它在决定提供哪些实用工具时真正把开发者放在第一位，而且它是一个维护良好的库，因为它与Vue的当前版本保持同步。
>
> - Animation(动画) - 包含易于使用的过渡、超时和计时功能
>
> - Browser (浏览器) - 可以用于不同的屏幕控件、剪贴板、首选项等等
>
> - Component (组件) - 为不同的组件方法提供简写
>
> - Sensors (传感器)- 用来监听不同的DOM事件、输入事件和网络事件
>
> - State (状态) - 管理用户状态（全局，本地存储，会话存储）
>
> - Utility （实用方法）--不同的实用方法，如`getters`、`conditionals`、`ref synchronization`等。
>
> - Watch --更高级的观察器类型，如可暂停的观察器、放弃的观察器和条件观察器
>
> - 其它 - 事件、WebSockets和 Web workers  的不同类型的功能
>
> 	VueUse 的最大特点之一是，它只用一个包就能兼容 Vue2 和 Vue3!

### 2、vue-print-nb

> 用于代替浏览器的打印

```html
//安装
npm i vue-print-nb

// 注册
import Print from 'vue-print-nb'
Vue.use(Print);

//使用, 指令方式 id为要打印元素的id

<el-row type="flex" justify="end">
          <el-button v-print="printObj" size="small" type="primary">打印</el-button>
</el-row>

<script>
	new Vue({
        data: function(){
            return {
                printObj: {
                    id: 'myPrint'
                }
            }
        }
    })
</script>

```

### 3、qrcode

> 用于生成二维码。如果内容是地址URL 就跳转到该地址。如果是其他内容就直接展示内容

```html
npm i qrcode

//局部引入使用
QrCode.toCanvas(dom, info)


// 页面使用
 <el-dialog title="二维码" 
 	:visible.sync="showCodeDialog" 
	@opened="showQrCode" 
	@close="imgUrl=''"
 >
  <el-row type="flex" justify="center">
    <canvas ref="myCanvas" />
  </el-row>
</el-dialog>


<script >
	 showQrCode(url) {
      // url存在的情况下 才弹出层
      if (url) {
        this.showCodeDialog = true // 数据更新了 但是我的弹层会立刻出现吗 ？页面的渲染是异步的！！！！
        // 有一个方法可以在上一次数据更新完毕，页面渲染完毕之后
        this.$nextTick(() => {
          // 此时可以确认已经有ref对象了
          QrCode.toCanvas(this.$refs.myCanvas, url) // 将地址转化成二维码
          // 如果转化的二维码后面信息 是一个地址的话 就会跳转到该地址 如果不是地址就会显示内容
        })
      } else {
        this.$message.warning('该用户还未上传头像')
      }
    }
</script>
```

### mitt

> vue3中取消了 this.$on, this.$off, this.$emit 事件，推荐使用外部插件 mitt库.来实现发布订阅功能

```js
// 安装
npm install mitt

// 引入
mybus.js
import mitt from 'mitt'
export default mitt() // 此为公用实例

// 使用
import mybus from './mybus.js'

发送
mybus.emit('自定义事件名称','数据');

接收
mybus.on('自定义事件名称',data=>{
    console.log(data);//接收到的数据
})

移除
mybus.off('自定义事件名称');

```

### vue-countTo

> 实现数字滚动效果的无依赖轻量级插件，vue3-count-to 是 vue3版本

```js
安装
npm i vue-count-to


eg:
<template>
  <countTo :startVal='startVal' :endVal='endVal' :duration='3000'></countTo>
</template>

<script>
  import countTo from 'vue-count-to';
  export default {
    components: { countTo },
    data () {
      return {
        startVal: 0,
        endVal: 2017
      }
    }
  }
</script>
```

文档

| Property  | Description            | type     | default |
| --------- | ---------------------- | -------- | ------- |
| startVal  | 开始值                 | Number   | 0       |
| endVal    | 结束值                 | Number   | 2017    |
| duration  | 持续时间，以毫秒为单位 | Number   | 3000    |
| autoplay  | 自动播放               | Boolean  | true    |
| decimals  | 要显示的小数位数       | Number   | 0       |
| decimal   | 十进制分割             | String   | .       |
| separator | 分隔符                 | String   | ,       |
| prefix    | 前缀                   | String   | ''      |
| suffix    | 后缀                   | String   | ''      |
| useEasing | 使用缓和功能           | Boolean  | true    |
| easingFn  | 缓和回调               | Function | —       |

** 注意：当autoplay：true时，它将在startVal或endVal更改时自动启动**

回调钩子函数

|  Function Name  |   Description    |
| :-------------: | :--------------: |
| mountedCallback | 挂载以后返回回调 |
|      start      |     开始计数     |
|      pause      |     暂停计数     |
|      reset      |   重置countTo    |

### vuex-persist

> 在vue项目中使用vuex本地持久化能更方便我们对数据的管理，不需要写localStorage或cookie等集合方法,统一用store来管理全局数据。

```js
eg1:
import Vue from 'vue'
import Vuex from 'vuex'
import floodPrevention from '@/store/floodPrevention/floodPrevention'
import VuexPersistence from 'vuex-persist'

Vue.use(Vuex)

const store = new Vuex.Store({
    state: {
        userInfo: {
            name: 'xxx',
            depId: '51515645165',
            sex: '男'
        }
    },
    mutations: {
        setUserInfo(state, payload) {
            state.userInfo = {
                ...state.userInfo,
                ...payload
            }
        }
    },
    actions: {
        
    },
    getters: {
        getUserInfo: (state) => state.userInfo
    },
    modules: {
        floodPrevention
    },
    plugins: [
        new VuexPersistence({
            storage: window.sessionStorage
        })
    ]
})
export default store
```

属性文档

| 属性         | 类型                               | 描述                                                         |
| :----------- | :--------------------------------- | :----------------------------------------------------------- |
| key          | string                             | 将状态存储在存储中的键。默认: 'vuex'                         |
| storage      | Storage (Web API)                  | 可传localStorage, sessionStorage, localforage 或者你自定义的存储对象. 接口必须要有get和set. **默认是: window.localStorage** |
| saveState    | function (key, state[, storage])   | 如果不使用存储，这个自定义函数将保存状态保存为持久性。       |
| restoreState | function (key[, storage]) => state | 如果不使用存储，这个自定义函数处理从存储中检索状态           |
| reducer      | function (state) => object         | 将状态减少到只需要保存的值。默认情况下，保存整个状态。       |
| filter       | function (mutation) => boolean     | 突变筛选。看mutation.type并返回true，只有那些你想坚持写被触发。所有突变的默认返回值为true。 |
| modules      | string[]                           | 要持久化的模块列表。                                         |

> 同类型插件 vuex-persistedstate 但是21年作者就不维护了

### Vuetify

> 一个前端基于 Material Design 的UI库

### NUXTJS

> vuejs+nodejs 的最佳实践

### vuepress

> 方便文档编写，支持markdown语法

### vue-mate

> 基于vue2.0插件，用于管理html head标签，支持ssr. 头部信息双向绑定便于管理

### vue-charts .js

> vue 图表

### vue grid layout

> 通用vuejs栅格布局系统
> 1、可拖拽
> 2、调整大小
> 3、可序列化，还原布局
> 4、自动化 RTL 支持
> 5、支持响应式

### vue draggable

> 优秀的vue拖拽组件

### [vue-draggable-plus](https://github.com/Alfred-Skyblue/vue-draggable-plus)

> Vue2 & Vue3 的拖拽组件。

### vee validate

> 数据类型验证

### vue Toastification

> 轻量级通知组件

### vue Tour

> 轻巧简单的可自定义新手指引插件

### Trois (语法为three) vue3库



### vue-presistedstate

> 在页面重新加载之间保持并补充您的[Vuex](https://vuex.vuejs.org/)状态。此项目已经不维护了

用法：

```js
import { createStore } from "vuex";
import createPersistedState from "vuex-persistedstate";

const store = createStore({
  // ...
  plugins: [createPersistedState()],
});

```

Vux 模块示例

```js
/* module.js */
export const dataStore = {
  state: {
    data: []
  }
}

/* store.js */
import { dataStore } from './module'

const dataState = createPersistedState({
  paths: ['data']
})

export new Vuex.Store({
  modules: {
    dataStore
  },
  plugins: [dataState]
})
```

### axios

> ajax 请求工具，[看文档](http://www.axios-js.com/zh-cn/docs/#axios-create-config)

```js
class HttpRequest {
    constructor() {
        this.baseURL = process.env.NODE_ENV === 'development'? 'https://www.fastmock.site': 'https://www.fastmock.site'
        this.timeout = 15000
        this.queue = 0 // 请求队列
        this.cache = []
    }
    setInterceptor(instance, url) {
        instance.interceptors.request.use((config)=> {
            // 开启 loading
            if(!this.queue) {
                // open loading
                context.$bus.emit('showLoading')
            }

            // 统一增加token
            // const token = store.getters['user/token']
            config.headers['token'] = 'tokentest'

            // 记录请求的取消函数
            let CancelToken =  axios.CancelToken
            config.cancelToken = new CancelToken((c)=> { // 存入 vux,组件销毁时执行
                // c 就是取消请求的token
                this.cache.push(c)
            })
            this.queue++

            return config
        }, (err)=> {
            // 响应就从队列删除 
            this.queue--
            if(this.queue == 0) {
                // close loading
                context.$bus.emit('closeLoading')
            }
            this.$message.error(err);

            return Promise.reject(err)
        })
        instance.interceptors.response.use((res)=> {
            this.queue--
            if(this.queue == 0) {
                // close loading
                context.$bus.emit('closeLoading')
                console.log('关闭 loading');
            }
            // 这里判断要依据后端返回格式而定
            if(res.data.code == 400200) {
                return res.data
            } else {
                return Promise.reject(res.msg)
            }
        }, (err)=> {
            this.queue--
            if(this.queue == 0) {
                // close loading
                context.$bus.emit('closeLoading')
            }
            this.$message.error(err);
            return Promise.reject(err)
        })
    }
    request(options) {
        let instance = axios.create()
        let config = {
            baseURL: this.baseURL,
            timeout: this.timeout,
            // validateStatus: function (status) {
            //     console.log('当前请求状态:', status);
            //     return status >= 200 && status < 300; // default
            // },
            ...options
        }
        this.setInterceptor(instance, config.url)
        return instance(config) // 返回 promise 对象
    }
    get(url, data) {
        return this.request({
            url,
            method: 'get',
            params: data
        })
    }
    post(url, data, config = {}) {
        return this.request({
            url,
            method: 'post',
            data,
            ...config
        })
    }
}
```

### eventemitter3

> EventEmitter3 是一个高性能的 EventEmitter。它已经针对各种代码路径进行了微优化，使其成为 Node.js 和浏览器可用的最快 EventEmitter 之一。该模块的 API 与 Node.js 默认附带的 EventEmitter 兼容，但有一些细微差别：
>
> - 域支持已被删除。
> - `throw`当您发出`error`事件并且没有人在听时，我们不会出错。
> - 和事件已被删除`newListener`，`removeListener`因为它们仅在一些不常见的用例中有用。
> - 、`setMaxListeners`和方法不可用 `getMaxListeners`。`prependListener``prependOnceListener`
> - 支持事件的自定义上下文，因此无需使用`fn.bind`.
> - 该`removeListener`方法删除所有匹配的侦听器，而不仅仅是第一个。
>
> 它是现有 EventEmitters 的替代品，但速度更快。免费表演，谁不想呢？EventEmitter 是用 EcmaScript 3 编写的，因此它可以在您需要支持的最旧的浏览器和节点版本中工作。

```js
npm install --save eventemitter3
```

```js
var EE = new EventEmitter()
  , context = { foo: 'bar' };

function emitted() {
  console.log(this === context); // true
}

EE.once('event-name', emitted, context);
EE.on('another-event', emitted, context);
EE.removeListener('another-event', emitted, context);

```

EventEmitter在其原型上添加了如下方法：

> eventNames、listeners、listenerCount、emit、on / addListener、once、off / removeListener、removeAllListeners

![preview](https://raw.githubusercontent.com/wyf195075595/images/main/blog/v2-1d23d0fcb5c423d69da86237d6201af7_r.jpg)



### amfe-flexible

> 手淘 rem适配方案，为html、body添加font-size，窗口调整时候重新设置font-size

### postcss-pxtorem

> 通过postcss-pxtorem来实现自动渲染px至rem的开发与生产工作
> 而不需要再手动计算。将px转换为px

### [GSAP（GreenSock）](https://greensock.com/)

> 最健全的web动画库之一

### ESLint

> 代码规范检查，建议使用文件配置得方式来编写配置。
>
> 安装 ESlint 后，通过 eslint --init  初始化配置文件

配置规则如下：

```js
rules: {
	"规则名": [规则值, 规则配置]
}
// 规则值： "off"/0 表示关闭， “warn"/1 表示警告，"error"/2 表示错误
```

官方提供的可选检测规则多达200条，我们可以从中筛选出自己需要的规则。还可以通过配置项文件得 extends 字段来启用官方推的或者开源的规则集。

```js
{
	"extends": "eslint:recommended"
}
```

如果在某些特殊场景中，需要有针对性地避开某些检测规则（而不是对整个工程禁用某项检测），则可以使用下面的语法在源代码中进行注释：

```js
// 禁止检查这一行
alert('foo'); /* eslint-disable-line no-alert*/

// 禁止检查下一行
/*eslint-disable-next no-alert*/
alert('foo')
```

> ESLint和Prettier是非常好的搭档，尽管有时候因为配置的问题可能会在检查项上出现冲突，这种时候根据实际要求关闭其中一个的检查项即可。总之，ESLint更倾向于发现和修复语言本身相关的漏洞或风险，而Prettier关注的则是格式和排版风格方面的一致性，两者各司其职。

### Requirejs 与 模块化推演

- 代码隔离

	第一代模块化解决方案，立即执行函数（IIFE）

	```js
	;(function(win, undefined){
	    ...
	})(window)
	```

	我们知道“undefined”在JavaScript语言中是一个关键字，不仅如此，它还是全局对象的一个属性，它的值被定义为“undefined”。在低版本的浏览器中，它是可以被赋值修改的，一旦有人恶意修改了“undefined”这个属性的值，那么你写在代码里的所有针对“undefined”的判断逻辑就会混乱。由于立即执行函数中的最后一个形参没有对应于任何值，因此其会被自动赋值为真正的“undefined”，以避免上述风险。



### Loadsh.js

> 它是一个具有一致性、模块化且高性能的JavaScript实用工具库



### 静态类型检查

> 两种流行的静态类型检查方案Flow.js和TypeScript的使用方法。 js 是一门动态类型语言，只有在运行时才会发现错误，而静态类型检查可以帮我们避免很多不必要得错误和快速定位错误

Flow.js

优势无侵入性是Flow.js最有吸引力的特点之一，当你希望尝试它时，可以轻松地将它引入项目中，当你的项目不再需要使用它时，使用“babel”或“flow-remove-types”命令可以非常轻松地将类型信息从源代码中去除，进而得到一份纯净的源代码，它就像一个自带卸载程序的辅助工具。相较于TypeScript的学习曲线，Flow.js的上手难度几乎低到可以忽略不计。无论如何，建议尝试使用它，将它作为TypeScript或是Java的预备知识也是非常好的。

TypeScript 

学习成本相对较高，但是功能更加强大。



### Ramda.js

为开发者提供了许多实用的工具原子函数

### babel-plugin-transform-remove-console

> 通过配置在生产环境打包时自动去除 console.log

```js
// bable.config.js
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



### [FullCalendar](https://fullcalendar.io/docs)

> 最早是jQuery插件，后来支持vue2|3.是非常使用的日程管理插件。下面是vue2中使用的简单案例

安装依赖

```json
"@fullcalendar/core": "^6.1.8",
"@fullcalendar/daygrid": "^6.1.8",
"@fullcalendar/interaction": "^6.1.8",
"@fullcalendar/timegrid": "^6.1.8",
"@fullcalendar/vue": "^6.1.8",
```

使用

```vue
<template>
  <div>
    <h1>Demo App</h1>
    <div class="box1">
        <FullCalendar :options='calendarOptions' />
    </div>
  </div>
</template>

<script>
import FullCalendar from '@fullcalendar/vue'
import dayGridPlugin from '@fullcalendar/daygrid'
import timegridPlugin from '@fullcalendar/timegrid'

export default {
  components: {
    FullCalendar // make the <FullCalendar> tag available
  },
  data: function() {
    return {
      calendarOptions: {
        plugins: [dayGridPlugin, timegridPlugin],
        initialView: 'dayGridMonth',
        weekends: true,
        headerToolbar: {
          left: 'prev,next,today',
          center: 'title',
          right: 'dayGridDay,dayGridWeek,dayGridMonth'
        },
        buttonText: {
          today: '今天',
          dayGridMonth: '月',
          dayGridWeek: '周',
          dayGridDay: '日'
        },
        locale:'zh-cn',// 切换语言，当前为中文
        firstDay: 1, // 设置一周中显示的第一天是哪天，周日是0，周一是1，类推  new Date().getDay()当前天
        eventMouseover: function ({event, jsEvent, view}) { //鼠标移上事件
            console.log(event);
        },
        eventClick: function ({event, jsEvent, view}) { //单击日程
            console.log("=====",event)
            
        },
        events: [
          { title: 'Meeting', start: new Date() }
        ]
      }
    }
  }
}
</script>

```

效果

![image-20230713102224150](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230713102224150.png)

> 在自适应方案 autofit.js 下出现 内容宽高错位现象，解决方案，resize 时js 重新设置内容宽高,外部拖拽事件上下偏移注意 autofit dh 参数 为930.
>
> 建议使用 pxtorem 做适配
>
> ```js
> let calendarBox = ref(null);
> const resetHeight = ()=> {
>  if(!calendarBox.value) return
>  const tbody = document.querySelector(".fc-scrollgrid tbody");
>  let height = tbody.clientHeight - 2;
>  let width = tbody.clientWidth;
>  document.querySelector('.fc-scrollgrid-sync-table').style.height = height + 'px'
>  document.querySelector('.fc-scrollgrid-sync-table').style.width = width + 'px'
>  document.querySelector('.fc-col-header').style.width = width + 'px'
>  fullCalendar.value.getApi().setOption('height', calendarBox.value.clientHeight);
> }
> ```
>
> 

### [vue-treeselect](https://vue-treeselect.js.org/)

> 若依框架在使用的 vue 下拉插件，支持下拉树，多选，搜索，懒加载等功能

[Equal UI](https://equal-ui.github.io/Equal/)

> 基于 Tailwind CSS 的一套 Vue 3 组件库。

[AI Code Translator](https://aicodeconvert.com/)

> 该网站可以将一种语言的程序，转换成另一种语言。用户也可以描述想要什么程序，它来生成代码。

### [PrettyPolly](https://www.prettypolly.app/app)

> 学习外语时，最好有一个对话环境，可以练习口语，这个 AI 应用就解决了这个问题。
>
> 它目前提供26种语言（包括中文、日文和韩文），你在网页上选择一种，就可以与 AI 进行口语练习了。感觉以后国内的外语培训产业，都要被 AI 取代了。

### [JavaScript 框架的历史](https://programmingsoup.com/history-of-javascript-frameworks)

> 一本英文小册子，介绍 JavaScript 的发展历史，从最早的 Prototype.js、Dojo、Mootools、YUI 一直到最新的微前端框架、Islands 框架。

### [NixOS 与 Flakes 新手指南](https://nixos-and-flakes.thiscute.world/zh/)

> 中文的开源书籍，帮助新人快速上手使用 NixOS。

### [element-ui-verify](https://aweiu.com/documents/element-ui-verify/)

> 提升 element-ui 原生表达验证体验

```vue
<template>
  <el-form label-width="100px" :model="model">
    <el-form-item label="年龄" prop="age" verify int :gt="0">
      <el-input v-model="model.age"></el-input>
    </el-form-item>
  </el-form>
</template>
<script>
  export default {
    data() {
      return {
        model: {
          age: '',
        },
      }
    },
  }
</script>
```

### [normalize.css](http://necolas.github.io/normalize.css/)

> **现代的、支持 HTML5 的 CSS 重置替代方案**

```js
npm i normalize.css

// main.js
import 'normalize.css/normalize.css'
```



### [vue-amap](https://github.com/ElemeFE/vue-amap)

> [基于 Vue 2.x 与高德的地图组件](https://elemefe.github.io/vue-amap/#/?id=vue-amap-基于-vue-2x-与高德的地图组件)

### [vue放大镜插件](https://mater1996.github.io/vue-photo-zoom-pro/demo/image.html)

> [VuePhotoZoomPro](https://mater1996.github.io/vue-photo-zoom-pro/)

### [pc表单/表格组件](https://vxetable.cn/#/table/start/theme)

> 功能十分强大，一个基于 [vue](https://www.npmjs.com/package/vue) 的 PC 端表单/表格组件，支持增删改查、虚拟列表、虚拟树、懒加载、快捷菜单、数据校验、打印导出、表单渲染、数据分页、弹窗、自定义模板、渲染器、JSON 配置式...

### [面板拆分组件](https://antoniandre.github.io/splitpanes/)

> codepen 中那种 可调整的大小面板的功能[示例](https://codepen.io/antoniandre/pen/XybPKP)

### [bytemd](https://github.com/bytedance/bytemd)

> 支持[Svelte](https://github.com/bytedance/bytemd#svelte)，react,vue,[Vanilla JS](https://github.com/bytedance/bytemd#vanilla-js) 的 markdown 编辑器

![image-20231103095727302](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20231103095727302.png)

### [tinymce](https://github.com/tinymce/tinymce)

> 富文本编辑器。安装命令：pnpm add tinymce @tinymce/tinymce-vue
>
> 安装成功后，在框架 /public 目录下创建 tinymce 文件夹，并将 /node_modules/tinymce 目录下的 langs 和 skins 文件夹复制到 /public/tinymce 目录下。
>
> [tynymce中文网](http://tinymce.ax-z.cn/)
>
> [富文本编辑器选型](https://juejin.cn/post/7434373084747333658?utm_source=gold_browser_extension)

![富文本](https://camo.githubusercontent.com/703496cf0c2806ff6aba376c72b0a50e016329d68ccc4b92d0bbbbdf51347fd0/68747470733a2f2f7777772e74696e792e636c6f75642f73746f726167652f6769746875622d726561646d652d696d616765732f74696e796d63652d656469746f722d36782e706e67)

```shell
安装命令：
pnpm add bytemd @bytemd/vue-next @bytemd/plugin-gfm
```

### [过渡动画](https://animate.style/)

> 过渡动画 样式

```
pnpm add animate.css
```

### [vue-esign](https://github.com/JaimeCheng/vue-esign)

> 电子签名

### [CodeMirrorr](https://github.com/codemirror/CodeMirror)

> json 编辑器，Json-Editor is base on [CodeMirrorr](https://github.com/codemirror/CodeMirror). Lint base on [json-lint](https://github.com/codemirror/CodeMirror/blob/master/addon/lint/json-lint.js).

### [vue-tree](https://github.com/wsfe/vue-tree?tab=readme-ov-file)

> 一款高性能 vue 虚拟树控件，支持搜索，定位，拖拽等。该控件是在公司业务的基础上不断打磨出来的，提供了十分丰富强大的 API，几乎能够满足你对树控件的所有需求。该控件同时支持 vue2 以及 vue3。
>
> element-plus 树未作优化，加载大量数据时会卡死

### [简洁的微信 Markdown 编辑器](https://github.com/doocs/md?utm_source=gold_browser_extension)

> 支持 Markdown 语法、色盘取色、多图上传、一键下载文档、自定义 CSS 样式、一键重置等特性

### [生成element表单](https://jakhuang.github.io/form-generator/#/)

> Element UI表单设计及代码生成器，可将生成的代码直接运行在基于Element的vue项目中；也可导出JSON表单，使用配套的解析器将JSON解析成真实的表单。
>
> 这是一个单独的项目，生成表单json后可通过**JSON解析器**解析成新的表单

### [vue-flow](https://github.com/bcakmakoglu/vue-flow)

> **一个高度可定制的 Vue 3 流程图组件**
>
> Vue Flow 使用了 Vue 3 独有的功能，因此对 Vue 2 没有支持，以后也不会有任何支持，对不起。

### [机器人拼图验证](https://github.com/javaLuo/vue-puzzle-vcode?tab=readme-ov-file)

> 我知道有第 3 方的很好用,比如 GEETEST 的拼图验证，但要引入 SDK 跟后台配合，还有接口交互。
> 太麻烦了，纯前端校验
>
> 支持vue2/3版本

### [弹幕交互](https://github.com/hellodigua/vue-danmaku/tree/master)

> 支持vue2/3版本

![](https://camo.githubusercontent.com/4dfee4ab6ff2e18882943ef0d5845ff7df599faed57fbdc997911b02caf97f6f/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f68656c6c6f64696775612f63646e2f696d672f7675652d64616e6d616b752e77656270)

### [vue-types](https://dwightjack.github.io/vue-types/)

> 简化了 props 参数声明，灵感源自 react

```js
import VueTypes from 'vue-types'

export default {
  props: {
    id: VueTypes.number.def(10),
    name: VueTypes.string.isRequired,
    age: VueTypes.integer,
    nationality: VueTypes.string,
  },
  methods: {
    // ...
  },
}
```

### [vue-json-pretty](https://github.com/leezng/vue-json-pretty)

> 页面展示json对象，[在线演示](https://leezng.github.io/vue-json-pretty/)

### [瀑布流组件](https://github.com/heikaimu/vue-waterfall-plugin)

> vue 瀑布流插件，支持 PC 和移动端，支持 animate 的所有动画效果，支持图片懒加载.
>
> 有vue2/3版本

[pptlist](https://github.com/pipipi-pikachu/PPTist/blob/master/README_zh.md)

> 一个基于 Vue3.x + TypeScript 的在线演示文稿（幻灯片）应用，还原了大部分 Office PowerPoint 常用功能，支持 文字、图片、形状、线条、图表、表格、视频、音频、公式 几种最常用的元素类型，每一种元素都拥有高度可编辑能力，同时支持丰富的快捷键和右键菜单，力求还原桌面应用级体验。支持导出本地 PPTX 文件，支持移动端基础编辑和预览。您可以在此基础上搭建自己的在线幻灯片应用。
>
> 

### [vue-tippy](https://github.com/KABBOUCHI/vue-tippy)

> vue提示组件，支持vue组件，指令，h 函数 三种使用方式

### [Vue I18n](https://kazupon.github.io/vue-i18n/zh/started.html#html)

> vue 国际化组件,支持vue2/3

```js
// 准备翻译的语言环境信息
const messages = {
  en: {
    message: {
      hello: 'hello world'
    }
  },
  ja: {
    message: {
      hello: 'こんにちは、世界'
    }
  }
}

// 通过选项创建 VueI18n 实例
const i18n = new VueI18n({
  locale: 'ja', // 设置地区
  messages, // 设置地区信息
})


// 通过 `i18n` 选项创建 Vue 实例
new Vue({ i18n }).$mount('#app')
```

### [VContextmenu](https://cybernika.net/v-contextmenu/)

> 通用的鼠标右键浮窗菜单

### [vue-cropper](https://github.com/xyxiao001/vue-cropper?tab=readme-ov-file)

一个优雅的图片裁剪插件

### [动态表单](https://view.form-create.com/)

> 强大的低代码动态表单组件，通过数据驱动表单渲染，适配移动端，支持可视化设计。提高开发者对表单的开发效率。目前在政务系统、OA系统、ERP系统、电商系统、流程管理等系统中已稳定应用。
>
> **支持的UI框架**
>
> - element-plus
> - ant-design-vue
> - naive-ui
> - arco-design
> - tdesign
> - vant

### [TvT.js](https://github.com/hawk86104/icegl-three-vue-tres)

![img](https://cdn.beekka.com/blogimg/asset/202410/bg2024100803.webp)

一个基于 ThreeJS + Vue + TresJS 封装的框架，用 Vue 组件的方式写 3D 可视化项目。

将px单位转换为视口单位的 (vw, vh, vmin, vmax) 的 [PostCSS](https://github.com/postcss/postcss) 插件.

### [postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport?tab=readme-ov-file)

如果你的样式需要做根据视口大小来调整宽度，这个脚本可以将你CSS中的px单位转化为vw，1vw等于1/100视口宽度。

postcss.config.js

```js
module.exports = {
  plugins: {
     // 使用 cnjm-postcss-px-to-viewport 规避 postcss.plugin was deprecated 警告
    "cnjm-postcss-px-to-viewport": {
      viewportWidth: 375, // 根据设计稿设定
      unitPrecision: 4, // 转化精度，转换后保留位数
      viewportUnit: "vmin", // 转换后的单位
      fontViewportUnit: "vmin", // 字体单位
      unitToConvert: "px" // 需要转换的单位
    },
  }
}

```

输入

```css
.class2 {
  padding-top: 10px; /* px-to-viewport-ignore */
  /* px-to-viewport-ignore-next */
  padding-bottom: 10px;
  /* Any other comment */
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 20px;
  line-height: 30px;
}
```

输出

```css
.class2 {
  padding-top: 10px;
  padding-bottom: 10px;
  /* Any other comment */
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 6.25vw;
  line-height: 9.375vw;
}
```

[EchoShare](https://github.com/echoshare/echoshare)

![img](https://cdn.beekka.com/blogimg/asset/202503/bg2025031706.webp)

开源的基于 WebRTC 的在线屏幕共享工具，允许与他人共享屏幕、摄像头和音频。使用vue+ts开发的项目