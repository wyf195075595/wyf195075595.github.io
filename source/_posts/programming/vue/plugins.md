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

> 实现数字滚动效果的无依赖轻量级插件

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