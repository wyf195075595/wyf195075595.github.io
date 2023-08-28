---
title: 插件集合
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---
### js读取excel文件

```js
1、js读取excel文件 xlsx.js
	

const datas={};
    $('#doc-form-file').on('change', function(e) {
        var fileNames = '';
        $.each(this.files, function() {
            fileNames += '<span class="am-badge">' + this.name + '</span> ';
        });
        $('#file-list').html(fileNames);
        //获取json，同时进行json数据校验
        var files=e.target.files;
        var file=files[0];
        var fileNames=file.name;
        //判断上传的文件是不是excel格式文件
        console.log(fileNames)
        var postfix = fileNames.substr(fileNames.lastIndexOf(".")).toUpperCase();//得到的是后缀名,且转换为大写
        var type=[".XLSX",".XLS"];
        if(!type.includes(postfix)){
            toast('文件格式不是excel')
        }else{
            //进行数据校验
            //console.log(XLSX);
            //读取文件
            var reader=new FileReader();
            reader.onload=function (e) {
                var data=e.target.result;
                var workbook=XLSX.read(data,{
                    type:'binary'
                })
                var sheetNames = workbook.SheetNames;
                var worksheet = workbook.Sheets[sheetNames[0]];
                var  supplement=XLSX.utils.sheet_to_json(worksheet)
                datas.object=supplement
            };
            reader.readAsBinaryString(file);
        }
    });
```

### fecs

<!--more-->

```js
代码检查 / 格式化，从未如此简单

http://fecs.baidu.com/
```

### Pinia

```
Pinia 是 Vue.js 的轻量级状态管理库，最近很受欢迎。它使用 Vue 3 中的新反应系统来构建一个直观且完全类型化的状态管理库。

Pinia体积约1KB,轻量级，简单的项目应用项目性能有优化
```



### JSON Server 

```js
 json-server 作为本地服务器，它的好处是只要有一个 JSON 数据文件，就能自动生成 RESTful 接口。
 
1、全局安装
 npm install -g json-server

2、在package.json 同级创建  db.json 文件
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}

3、设置启动脚本命令
{
    "script": {
        "json:server": "json-server --watch db.json"
    }
}


4、调用
 http://localhost:3000/posts/1

{ "id": 1, "title": "json-server", "author": "typicode" }

增 POST
var newData = {
    "name": "新加数据",
    "phone": "123456789",
    "email": "11357097537@qq.com",
    "age": "45",
    "id": 5,
    "companyId": 3
};
$.ajax({
    type: "post",
    url: "http://localhost:3000/users",
    data: newData,
    success: function(data) {
        console.log(data)
        alert("添加成功")
    },
    error: function(data) {
        console.log(data)
        alert("已存在，不可重复添加")
    }
})

删 DELETE
var delUserId = 5;
$.ajax({
    type: "DELETE",
    url: "http://localhost:3000/users/" + delUserId,
    dataType: "json",
    success: function(data) {
        console.log(data)
        alert("删除成功")
    },
    error: function(err) {

        alert("不可重复删除")
    }
})

改　PUT
var updateuser = {
    "name": "王小婷要修改一下哦",
    "phone": "123456789",
    "email": "11357097537@qq.com",
    "age": "20",
    "id": 1,
    "companyId": 1
};
$.ajax({
    type: "PUT",
    url: "http://localhost:3000/users/1",
    data: updateuser,
    success: function(data) {
        console.log(data)
        alert("修改成功")
    },
    error: function(err) {
        alert("修改失败")
    }
})

查　GET
 //查询id=1
$.ajax({
    type: "get",
    url: "http://localhost:3000/users/1",
    dataType: "json",
    success: function(data) {
        console.log(data)
        alert("查询成功")

    },
    error: function(data) {
        console.log(data, '请求失败')
        alert("查询失败")
    }
})

5、其他操作
过滤
	GET /posts?title=json-server&author=typicode
    GET /posts?id=1&id=2
    GET /comments?author.name=typicode
分页
	GET /posts?_page=7
    GET /posts?_page=7&_limit=20
排序
	GET /posts?_sort=views&_order=asc
    GET /posts/1/comments?_sort=votes&_order=asc
    
    -多字段格式
    GET /posts?_sort=user,views&_order=desc,asc
分片
    GET /posts?_start=20&_end=30
    GET /posts/1/comments?_start=20&_end=30
    GET /posts/1/comments?_start=20&_limit=10
```

### 文本域高度自适应内容

```js
/**
        * @author
        * @description textarea 高度自适应 [注意：不要使用过渡，会造成计算错误]
        * @param { HTMLElement } elem 要自适应的文本 dom
        * @param { number } extra 可选 文本下边距空白
        * @param { number } maxHeight 可选 可自适应的最大高度
        * @param { Boolean } isCancle 可选 优化项(如果更新长度一致，就不进行操作)
        * @param { string } parentScrollTopDomName 父滚动容器 dom 名称
        * @param {function} scrollCallback 滚动值发生改变后的回调
        * @example 可重复调用，副作用已解决
        * textareaAutoHeight("textarea || .className || #id")
    */
    function textareaAutoHeight(elem, extra, maxHeight, isCancle = true, parentScrollTopDomName, scrollCallback) {
        let doms = $(elem)
        if(doms.length <= 0) return

        let changeTextarea = function(dom,extra,maxHeight){
            extra = extra || 0;
            let isFirefox = !!document.getBoxObjectFor || 'mozInnerScreenX' in window,
                isOpera = !!window.opera && !!window.opera.toString().indexOf('Opera'),
                minHeight = (dom[0].minHeight || dom[0].minHeight === 0) ? dom[0].minHeight : dom.height();

            dom.css({ resize: 'none'})
            let change = function () {
                    let scrollTop,
                        height,
                        padding = 0;
                    if(isCancle){
                        if (dom[0]._length === dom[0].value.length) return;
                        dom[0]._length = dom[0].value.length;
                    }

                    if (!isFirefox && !isOpera) {
                            padding = parseFloat(dom.css("paddingTop")) + parseFloat(dom.css("paddingBottom"))
                    };
                    // scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
                    scrollTop = $(parentScrollTopDomName)[0] && $(parentScrollTopDomName)[0].scrollTop || 0;

                    dom.css({ height: minHeight + 'px' })
                    if (dom[0].scrollHeight > minHeight) {
                            if (maxHeight && dom[0].scrollHeight > maxHeight) {
                                    dom.css({ overflowY : 'auto'})
                                    height = maxHeight - padding;
                            } else {
                                    dom.css({ overflowY: 'hidden'})
                                    height = dom[0].scrollHeight - padding;
                            };
                            dom.height(height + extra)
                            scrollTop += dom.height() - (dom[0].currHeight || 0)
                            // document.body.scrollTop = scrollTop;
                            // document.documentElement.scrollTop = scrollTop;
                            scrollCallback && scrollCallback(scrollTop)
                            dom[0].currHeight = dom.height()
                    };
                    dom[0].minHeight = !Number.isNaN(dom[0].minHeight) ? Math.min(dom.height(), dom[0].minHeight) : dom.height();
            };
            dom.off("propertychange").on("propertychange",change)
            dom.off("input").on("input",change)
            dom.off("focus").on("focus",change)
            change();
        }
        doms.each(function(index , item) {
            changeTextarea($(item),extra,maxHeight)
        })
    };]()
```

#### storybook

```
Storybook 是一个开源工具，用于单独构建 UI 组件和页面。它简化了 UI 开发、测试和文档。xxxxxxxxxx 组件开发环境Storybook 是一个开源工具，用于单独构建 UI 组件和页面。它简化了 UI 开发、测试和文档。
```

### Ramda.js

纯粹的JavaScript函数式编程风格库，它在设计上遵从纯函数和不可变数据这两个核心理念，同时它能在内部对所有已经实现了的纯函数自动完成柯里化，在参数次序的安排上，也将要操作的数据项放在了最后，这样的设计风格可以让你在最终传入数据之前更灵活地组装函数管道

函数化的流程控制

- 条件分支函数R.ifElse

	它将if/else判断语句封装在函数体中，并且会接收3个参数，分别作为条件成立时的执行函数、不成立时的执行函数和用于判断if是否成立的表达式，然后返回一个新函数，当向新函数传入数据并调用时，实际上执行的就是if/else条件判断语句。

- 多条件分支函数R.cond

	它可以实现多个if/else判断条件，首先接收一个数组作为参数，数组中的每一项包含一个判断条件和当其成立时需要执行的函数，然后返回一个新函数，向新函数传入数据并调用时，实际上执行的就是多个if/else条件判断语句

- 循环语句函数

	以函数的方式实现循环实际上有多种选择，大多数时候，使用R.map、R.forEach、R.mapObjIndexed和R.forEachObjIndexed就可以实现

- 异步流控函数R.then/R.otherwise

	R.then接收onSuccess函数后会生成一个新函数，新函数的入参是一个promise实例，如果这个promise实例被决议为“fulfilled”，那么预先传入的onSuccess函数就会被作用于该实例被决议时返回的结果（也就是resolve结果）中，新函数仍然会返回一个promise实例。R.otherwise与R.then的用法相似，它会在传入的promise实例被决议为“reject”时作用于决议的返回值，并将结果包装在一个新的promise实例中返回。

对比原生js, lodash.js, ramda.js 实现 TodoList 条件查询功能。假如我是一位管理人员，需从Todo-list 找到与 BillGates一起进行的尚未完成的事情。并从中筛选出 优先级小于3 且 项目收益高于1000W得项,并按截至时间排序返回

原生：

```js
let unSortRs = tasks.filter(task=> {
	if(
        task.partner === "BillGates" && 
        task.priority <= 3 && 
        task.profit > 100000 &&
        !task.complete
    ) {
        return true
    }
    return false
});

let rs = unSortRs.unSortResult.sort((a,b)=> {
    return a.deadline <= b.deadline
})
```

lodash:

```js
let result = _
	.chain(tasks)
	.filter(tsak=> _.eq(task.partner, 'BillGates'))
    .filter(task=> _.lte(task.priority, 3))
	.filter(task=> _.gte(task.profit, 100000))
    .filter(task=> _.eq(task.complete, false))
	.sortBy('deadline')
```

ramda:

```js
// 拼装筛选条件
const condition = {
    partner: R.equals('BillGates'),
    priority: R.lte(R.__, 3),// 第一参数小于第二参数时返回true
    profit: R.gte(R.__, 100000),// 第一参数大于第二参数时返回true
    complete: R.equals(false),
}

// 过滤
const filterByCondition = R.filter(R.where(condition))
// 排序
const sortByDeadline = R.sortBy(R.prop('deadline'))
// 求解
const f = R.compose(sortByDeadline, filterByCondition)
// 执行
let result = f(tasks)
```

### cornerstoneJs

> cornerstone 是一个[开源项目](https://so.csdn.net/so/search?q=开源项目&spm=1001.2101.3001.7020)，目标是提供一个完整的基于 web 的医疗成像平台。
>
> 目前基于 cornerstone 开发 web 端医疗[成像](https://so.csdn.net/so/search?q=成像&spm=1001.2101.3001.7020)平台并不止需要一个 javascript 库，而是需要多个库进行配合，庆幸的是几乎所有需要的库均为 cornerstone 提供，可以理解为已经形成了一套生态系统。



### immer.js

> 不可变[数据结构](https://www.zhihu.com/search?q=数据结构&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1732298410})指的是一个变量，其引用的数据不会发生改变。一旦 `a = { count: 1}` 这个引用关系确立，在任何时候 `a 恒等于 { count: 1}`， 不允许修改 a 引用的数据结构，例如禁止 `a.count = 2` 这种操作。
>
> immer 实现了不可变数据结构

```js
let inittotal = {
  a: 'a',
  b: [1,2,3,4],
  c: {name: 'name', age: 18},
  d: false,
  e: 0,
  f: [{name: '1', id: 1},{name: '2', id: 2}]
}
let {produce} = immer;
const next = produce(inittotal, obj => {
  obj.a = '12346'
})

// next 返回变化之后的对象
console.log(next)
// 初始化的数据不发生改变
console.log(inittotal)
```

原生按约定实现

```js
const state = { count: 1 }

const newState = { ...state, count: 2 };


// 但下面这种情况呢？ 
let [adress, setAdress] = useState({
  phone: 123,
  city: {
    code: '1234',
    area: 'beijing'
  }
})

setAdress({
  ...address,
  city: {
    ...address.city,
    area: "Chengd"
  }
})


// 如果使用 immerjs
setAdress(
  produce((draftState) => {
    draftState.address.city.area = 'JingAn'
    draftState.address.postcode += 10
  })
)
```



### immutable.js 

> 与immer.js 做的是同一件事。
>
> baseState对象中有10000个属性，状态转移函数只修改了其中5个属性的值，但最终实现状态隔离的nextState仍然需要将其他的9995个属性都复制一份，很显然这会造成极大的浪费，因为从信息量的角度来看，未被修改的9995个属性完全是等价且可共享的。Immutable.js的基本原理就是将JavaScript的原生数据类型放入新的数据结构中，从而实现基于共享结构的持久化数据。



Immutable实现了许多新的数据结构，最常见的是用于处理数组的List类型和用于处理对象的Map类型

```js
// 我们规定，所有的 object 都使用 immutable.js 提供的 Map 对象
import { Map, List } from 'immutable.js'

const map = Map({ a: 1, b: 2 });
const newMap = map.set('a', 3);

let list = List([1,2])

```

List，Map API

```js
// 查询 索引为 i
ImmutableData.get(i)

// 查询嵌套索引为i, j的值
ImmutableData.getIn([i,j])

// 修改索引为 i 的值
ImmutableData.set(i, value)

// 修改索引为 i,j的值
ImmutableData.setIn([i,j], value)

// 删除索引为i的值
ImmutableData.delete(i)

// 删除索引为i，j的值
ImmutableData.deleteIn(i,j)
```



### 代替 momentjs 的时间库

> Day.js，	date-fns

day.js

下个季度开始后的第二周星期一早晨10点安排一个会议（进入下个季度后第一个完整的星期为第一周），并且还要能看到当前时间距离该时间点还有多久，要如何实现这样一个功能呢？

```js
const dayjs = require('dayjs')
const quarterOfYear = require('dayjs/plugin/quarterOfYear')
const relativeTime = require('dayjs.plugin/relativeTime')
// 引入语言包
require('dayjs/locale/zh-cn')
// 设置语言
dayjs.locale('zh-cn')
// 加载插件
dayjs.extend(quarterOfYear)
dayjs.extend(relativeTime)

let day = dayjs()
	.endOf('quarter')
	.startOf('week')
	.add(2, 'week')
	.add(1, 'day')
	.add(10, 'hours')
let duration = day.formNow()

console.log(day, duration)
```



### jsplumb.js在网页中绘制流程图

jsplumb.js分为Community（社区版）和Toolkit（正式版）两个不同的版本，社区版是一个免费的开源项目，源代码托管在GitHub平台上，它只包含了核心绘图API、拖放和事件功能，也就是与UI相关的功能，如果你所在的团队具备一定的二次开发能力，则完全可以在此基础上定制自己的绘图工具箱

如果jsplumb.js让你觉得难以上手，也可以选用AntV-X6图编辑引擎，全中文的文档和对现代化前端框架的支持极大地降低了学习难度。

### [图布局引擎viz.js](https://graphviz.org/gallery/)

来实现流程图的自动化布局



### [easel.js](https://www.createjs.com/getting-started/easeljs)

easel.js提供了丰富的语义化的API和交互解决方案，能够优雅地实现对象分层管理、事件机制、链式调用等功能，其作为游戏开发整体解决方案CreateJS的组成部分，所有的API都挂载在createjs命名空间下

容器Stage/Container

图形Shape/Bitmap

定时器Ticker

交互模型EventDispatcher



### 网页 PPT

> [impress.js](https://impress.js.org/)是利用JavaScript来实现幻灯片制作的工具库[文档](https://github.com/impress/impress.js/blob/master/DOCUMENTATION.md)
>
> 它并不是唯一的，Reveal.js[插图]也是非常优秀的工具库，只是它们的侧重点有所不同
>
> ### gossip 一个高效创建和演示炫酷幻灯片的用户界面！ [https://pearmini.github.io/gossip/](https://link.zhihu.com/?target=https%3A//pearmini.github.io/gossip/%5D)

### Velocity.js

> js 操作动画的库

Velocity的主要API只有一个，它会接收两个对象类型的参数，第一个参数properties用于描述对象下一个关键帧中的样式，第二个参数options用于描述动画执行的细节或在事件钩子中触发其他的动作

```js
let element = document.querySelector('#div');
// 全局函数形式调用
Velocity(element, {width: 75});

// 以对象方法的形式调用
element
.velocity({width: 75})
.velocity({
    background：'blue'
}, {
    ducation: 400,// 动画时长
    easing: 'swing',// 缓动函数
    loop: false,// 循环次数
    delay: false,// 延迟时间
    begin: function() {
        // 动画开始
    },
    progress: function() {
        // 动画变化
    },
    complete: function() {
        // 动画触发
    }
    
})
```



### tween.js和jQuery Easing Plugin

> 缓动函数实现库,  例如css 中的贝塞尔曲线，可以控制时间曲线的速度，让动画更有动感

tween.js

是前端动画解决方案CreateJS中的组成部分，需要配合之前介绍过的Canvas操作库easel.js使用，它操作的对象是easel.js定义的图形实例，tween.js提供了完整的逐帧动画、缓动函数以及事件通知机制。tween.js中所有的缓动函数都定义在Ease类中[插图]，它与tween.js的主体部分是解耦的，完全可以拿出来应用在其他动画库中，tween.js的基本语法如下：

```js
Tween.get(target)
.await(500)
.to({y: 200}, 1000, Ease.bounceOut)
.call(handleComplete)
```

 jQuery Easing Plugin

在常规的应用开发中，动画库更多地是使用velocity.js，其官方文档示例中建议集成jQuery Easing Plugin[插图]缓动函数库，并在使用时直接传入名称关键词，参考代码如下：

```js
$element.velocity({width: 50}, "easyOutVounce")
```

如果翻看jQuery Easing Plugin源码中对于easeOutBounce缓动函数的实现，就会发现它与tween.js中实现bounceOut方法的算法是一样的。明白了缓动函数的基本原理后，再将它作为独立模块与其他动画库集成就很容易了，把easel.js中的缓动函数模块换成jQuery Easing Plugin，或是将tween.js中的Ease类集成到velocity.js中来实现缓动效果，都是很容易实现的。

> 一般性的经验和法则是：对于用户交互触发的UI动画，例如视图变换或显示元素的场景，建议采用**快前奏和慢结尾的缓出动画来展示**；而对于由代码触发的UI动画，例如错误或模态框，建议采用**慢前奏和快结尾的缓入动画来展示**。也就是说作为用户期望中的操作反馈时应当尽量满足“快速响应”的要求，而在用户预料之外的提示或是模态框等打断正常使用流程的动画，则应当尽量加入缓入效果来降低动画的“攻击性”。

### JavaScript 构建工具

最早是 Grunt，然后是 Gulp，再后来我们挥手告别 Gulp 复杂配置的地狱，迎来了 Webpack。但是五年后，Webpack 同样是一团糟，变得缓慢、臃肿和混乱。现在，我们的项目又切换到了 esbuild，构建时间从7分钟变为1秒，太棒了，让我们希望这一次会成功。



### 百度语音识别实战

> [参考文章](https://www.cnblogs.com/dashnowords/p/9557355.html)
>
> 在百度AI开放平台找到语音识别标准版的REST AP[I文档](https://ai.baidu.com/ai-doc/SPEECH/Vk38lxily)，可以看到它的使用方式是将一段时长不超过60秒的录音文件发送到对应接口，然后接口会实时返回语音识别的结果。百度语音开发文档中对于语音格式要求的描述具体如下：可识别的语音格式包括pcm、wav、arm以及微信小程序使用的m4a格式，推荐采用采样率为16 000Hz、位深为16bit的单声道pcm文件，百度服务端会将非pcm格式的文件转换为pcm格式，因此会有额外的转换耗时成本。

修改 Recorder.js

默认双声道，修改单声道

```js
// numChannels

function Recoder(source, cfg) {
    ...
    this.config = {
        bufferLen: 4096,
        numChannels: 1,
        mimeType: 'audio/wav'
    }
    ...
    Object.assign(this.config, cfg)
    this.context = source.context
    ...
}
```

采样率

默认44100Hz 改为 16000Hz

```js
// 通过关键字 sampleRate (采样率)

this.worker.postMessage({
    command: 'init',
    config: {
        sampleRate: this.context.sampleRate,
        numChannels: this.config.numChannels
    }
})



function extractSingleChannel(input) {
    // 计算采样的间隔步长，context.sampleRate 44100Hz, customSampleRate 1600Hz
    var step = parsInt(context.sampleRate/customSampleRate, 10)
    var length = Math.cile(input.length / step)
    // 生成新的存储空间用于存储重新采样率数据
    var result = new Float32Array(length)
    var index = 0, inputIndex = 0;
    while(index < length) {
        // 此处关键，算法时原始输入数据点每隔step距离取个点放入result中
        result[index++] = input[inputIndex]
        inputIndex += step
    }
    return result
    
}
```



输入16bit PCM 数据

默认处理函数 floatTo16BitPCM. 在输出wav格式前调用。wav 格式在 PCM 数据前附加44字节，用于描述这段数据的基本信息

```js
// 仿写
function encodePCM(samples) {
    // 位深为16bit,每个数据节点占2字节
    var buffer = new ArrayBuffer(sample.length*2)
    var view = new DataView(buffer)
    // 下面函数的第二个参数在 encodeWAV 中是44，这里不需要设置44字节的偏移量，所以位置0
    floatTo16BitPCM(view,0,samples)
    return view
    
}
```

> “单声道，位深16bit，采样率16 000Hz”这几个条件都满足了

接口提交方式

```js
var formdata = new formDadta()
formdata.set('recorder.wav', blob)
axios({
    url: '',
    method: 'post',
    headers: {
        "Content-Type": "multipart/form-data" // 此处也可赋值为 false
    },
    data: formata
})
```



### jsmpeg.js

> ffmpeg是一款非常好用处理音视频的工具包。那什么是ffmpeg呢？FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序



### Commander.js

> 命令制作工具

新建bin目录，增加test.js

```js
复制代码
// 引入依赖
var program = require('commander');
 
// 定义版本和参数选项
program
  .version('0.1.0', '-v, --version')
  .option('-i, --init', 'init something')
  .option('-g, --generate', 'generate something')
  .option('-r, --remove', 'remove something');
 
// 必须在.parse()之前，因为node的emit()是即时的
program.on('--help', function(){
 console.log('  Examples:');
  console.log('');
  console.log('    this is an example');
  console.log('');
});
 
program.parse(process.argv);
 
if(program.init) {
  console.log('init something')
}
 
if(program.generate) {
  console.log('generate something')
}
 
if(program.remove) {
  console.log('remove something')
}
```

测试：

```js
node bin\test --help
```

结果：

```js
Usage: test [options]
 
  Options:
 
    -v, --version   output the version number
    -i, --init      init something
    -g, --generate  generate something
    -r, --remove    remove something
    -h, --help      output usage information
  Examples:
```

[用commander.js构建自己的脚手架工具](https://www.cnblogs.com/cangqinglang/p/10642891.html)

### Inquirer.js与交互式命令行工具

> 可以制作交互式工具命令，类似于 vue 脚手架创建项目时的操作

eg:

```js
const inquirer = require('inquirer')
const questions = [
    {
        type: 'list',
        name: 'CSSPreProcessor',
        message: '请选择想要使用的css处理器',
        choices: [
            {name: 'sass(dart-sass)', value: 'dartSass'},
            {name: 'sass(node-sass)', value: 'nodeSass'},
            {name: 'less', value: 'less'},
            {name: 'stylus', value: 'stylus'},
        ]
    }
];
imquirer.prompt(questions).then(abswer=> {
    console.log('您输入的所有答案如下：')
    console.log(answer)
})
```



### 自动化脚本

> Windows中使用的bat脚本，Linux中使用Shell脚本。语法不同
>
> Shelljs就是为了解决这个问题而生的，它将Node底层丰富的系统级操作API封装起来，对外提供了与Shell命令一致的语法，借助Node天然的跨平台运行能力，使得前端开发者可以不依赖任何其他工具而直接使用最熟悉的JavaScript来编写Shell脚本并实现跨平台的运行。

常用的Linux命令：

![image-20220905163024223](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220905163024223.png)

> Shell脚本本质上是一份自动化任务清单，开发者将原本需要手动执行的任务记录在Shell脚本中，然后委托给脚本解释器逐行执行。

其他跨平台实用工具

- Cmder

	Cmder[插图]可以算是Windows命令行工具的加强版，它不仅有更加美观的UI样式，还增加了对Linux命令和Tab式多窗口的支持，完整版的Cmder集成了git命令行，它可以让开发者在Windows系统中使用Linux命令。

- cross-env

	用来解决跨平台设置和使用环境变量的问题,

- shelljs

	可以使用JavaScript代码来编写Shell脚本，并且各个平台都可以通过“node [scriptname].js”或是“npm scripts”的方式来运行这个脚本文件，从而解除了跨平台的困扰



### Shelljs+node-ssh 实现自动化部署

上传脚本的编写

在上传脚本upload.js中需要完成以下几项任务，其中每一项都有多种实现方式。

● 将打包后输出的文件夹压缩为zip包（通常是dist文件夹）。

● 连接部署服务器，将zip包发送过去。

● 远程调用服务器端的脚本完成解压和文件替换的工作。

使用archiver模块来制作zip包，接着使用node-ssh包（它的底层是更为通用的SSH2模块，这个模块是一个Promise封装，使用起来更加方便）来建立与服务器的连接，并将压缩包发送至服务器，最后使用node-ssh提供的远程命令调用API来启用放置在服务器上的发布脚本deploy.js，从而完成剩余的工作

```js
/*
 * @Description: 
 * @Author:  
 * @Date: 2022-09-06 15:09:59
 * @LastEditTime: 2022-09-07 09:51:22
 * @LastEditors:  
 */
const path = require('path')
const archiver = require('archiver')
const fs = require('fs')
const {NodeSSH} = require('node-ssh')
const ssh = new NodeSSH()
const srcPath = path.resolve(__dirname, './dist')
// const configs = require('./config')

console.log('开始压缩dist目录')
startZip()
// 开始压缩 dist 目录为 public.zip
function startZip() {
    var archive = archiver('zip', {
        zlib: {level: 5}
    }).on('error', function(err) {
        throw err
    })
    
    var output = fs.createWriteStream(__dirname + '/public.zip')
    .on('close', function(err) {
        // 压缩结束时要先触发close时间，然后开始上传
        if(err) {
            console.log('压缩zip文件异常')
        }
        console.log('已生成zip包')
        console.log('开始上传 public.zip 至远程机器')
        uploadFile()
    })
    
    archive.pipe(output) // 
    
    // 将srcPath 路径对应的内容添加到zip包的 /public 路径中
    archive.directory(srcPath, '/public')
    archive.finalize()
    
}

// 将dist 目录上传至正式环境
function uploadFile() {
    ssh.connect({
        host: '192.168.1.12',
        username: 'jysp',
        password: 'jydw',
        port: 22222 // SSH 默认的连接端口为22
    }).then(function() {
        // 将网址的发布包上传至configs中配置的远程服务器的指定地址
        console.log('上传的文件：', __dirname + '/public.zip');
        ssh.putFile(__dirname + '/public.zip', '/home/jysp/workspace/public.zip')
        .then(function(status) {
            console.log('上传文件成功')
            console.log('开始执行远端脚本')
            startRemoteShell()
        })
        .catch(err=> {
            console.log('文件传输异常1', err)
            process.exit(0)
        })
        , function(err) {
            console.log('文件传输异常1', err)
            process.exit(0)
        }
    })
    .catch(err=> {
        console.log('ssh连接失败：', err)
        process.exit(0)
    })
}

// 执行远端部署脚本
function startRemoteShell() {
    // 在服务器上  cwd 配置的路径下执行 deploy.js 脚本来更新网站文件
    ssh.execCommand('node deploy.js', {cwd: '/home/jysp/workspace'})
    .then(function(result) {
        console.log(result)
        console.log('远程STDOUT输出：',  result.stdout)
        console.log('远程STDERR输出：',  result.stderr)
        if(!result.stderr) {
            console.log('发布成功')
            process.exit(0)
        }
    })
}
```

编写发布的脚本

```js
// deploy.js

const shell = require('shelljs')
let rootPath = __dirname

shell.cd(`${rootPath}/usrl/server/tinyserver`)
// 移出public目录
shell.rm('-rf', 'public')
shell.cd(`${rootPath}/usrl/server`)
shell.exec('unzip public.zip', function(err, stdout) {
    if(err) {
      console.log('unzip error', err)
        process.exit(0)
    } else {
        console.log('unzip success')
        shell.mv('public', './tinyserver')
    }
})


// deploy.sh 脚本
cd /usrl/server/tinyserver
rm -rf public
cd /usrl/server
unzip public.zip
mv public ./tinyserver
```

> 在Windows系统中编写Shell脚本的另一个问题是回车换行符，Windows系统中回车键会输入CRLF（表示回车并换行），而Linux系统中敲回车键会输入LF（表示换行），这虽然看起来是个小问题，但的确有可能导致在Windows系统中编写的Shell脚本在Linux机器上无法正常运行，开发人员在定位问题时需要多加注意

设置打包时自动上传

```js
"scripts": {
    "build": 'vue-cli-service build',
     "postbuild": "npm run upload",
     "upload": "node .scripts/upload.js"
}
```



### 二进制消息格式（protobuf）

> protobuf格式[插图]全称为Protocol Buffers，是Google推出的一种基于二进制编码的跨语言、跨平台、易扩展的数据交换格式，广泛应用于服务端通信等场景，其设计初衷也是为了将结构化的数据转换为序列化的数据，以便在通信传输中使用
>
> protobuf并不是唯一的选择，Facebook推出的Thrift也是一种二进制通信协议，它们都可以在大规模的跨语言服务开发场景中使用
>
> 与Web开发领域常用的表单、XML和JSON等数据交换格式相比，使用二进制编码后的消息体积更小且其编码速度更快，代价就是使用流程比较烦琐且编码后的消息几乎丧失了可读性

![image-20220908093453317](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220908093453317.png)



> 首先将接口声明编写在扩展名为“.proto”的文件中，它拥有自己的语法，接着使用编译器来生成指定语言可以访问的消息读写类，最后在自己的程序中引用对应语言的运行时库和消息读写类来使用编解码函数

假设你现在需要为某个ERP系统开发一个查询入库记录信息的接口，前端在得到查询数据后需要展示为列表，请求时必传的参数包括关键词（keywords）、当前页码（page）和每页条目数（items_per_page），同时还要支持一些可选的查询条件，包括供应商编号（supplier_id）和创建时间（create_at），接口正常响应后会返回响应状态（status）和查询结果（data

proto文件的示例

```js
syntax = 'proto2';// 声明了使用的语法版本
import 'product.proto';// 引用货物接口定义

/* 库存接口查询*/
message SearchRequest {
    required string keywords = 1;// 关键词
    required int32 page = 2;// 页码
    required int32 items_per_page = 3;// 每页展示的数
    
    optional int32 supplier_id = 4;// 供应商 id
    optional string create_at = 5;// 创建时间
}

/* 库存查询响应接口*/
message SearchResponse {
    required int32 status = 1;// 响应状态
    required int32 total = 2;// 总计数量
    required Product data = 3;// 查询结果
    
}
```

第二行的 import 引用了在外部 proto 文件中定义过的消息格式



### 使用protobuf实现跨语言通信

> 需要[下载](https://github.com/protocolbuffers/protobuf/releases)对应操作平台的编译工具 protoc 【win10 下载 protoc-3.13.0-win64.zip】
>
> 接下来将解压后目录中bin文件夹的绝对路径添加到环境变量的Path变量中，之后就可以在命令行工具中使用protoc命令来编译*.proto文件了

编译为js 库

```js
protoc --js_out=import_style=commonjs,binary:. search.proto product.proto
```

。运行成功后，当前目录下会生成两个新的文件，默认命名分别为search_pb.js和product_pb.js

Node.js程序中使用protobuf时，需要引用官方提供的运行时依赖google-protobuf（直接使用npm安装即可

```js
const SearchProto = require('./search_pb')

// 实例化消息体
let message = new SearchProto.SearchRequest();
message.setKeywords('Tony');
message.setPage(31)；
message.setItemsPerPage(20);

// 转换为对象
let obj = message.toObject();
console.log('转为对象：', obj);
console.log('JSON 序列化结果：', JSON.stringify(obj));
console.log('JSON 序列化后的长度：', JSON.stringify(obj).length);
console.log('转为对象：', obj);

// 转换为二进制序列化消息
let bytes = message.serializeBinary();
console.log('序列化结果：', bytes)
console.log('序列化原始buffer：', Buffer.from(bytes.buffer))
console.log('protobuf序列化后的长度：', bytes.length)

// 通过反序列化重建消息内容
let rebuildMessage = SearchProto.SearchRequest.deserializeBinary(bytes);
let rebuildObject = rebuildMessage.toObject();
console.log('反序列化后转对象', rebuildObject)

```



### protobuf.js

> 是一个基于JavaScript二进制数据处理能力实现的运行时框架，这就表示并不需要提前编译proto文件，而是可以在运行时直接加载和使用

代码示例：

```js
const protobuf = require('protobufjs')

// SearchProto 文件对象
let SearchProto = protobuf.loadSync('../proto/search.proto')

// 找到请求消息的类型定义
const SearchRequestMessage = SearchPorto.lookupType('SearchRequest')

// 对象字面量定义载荷
let payload = {
    keywords: 'Tony',
    page: 31,
    itemPerPage: 20
}

// 判断载荷是否合法
let errMsg = SearchRequestMessage.verify(payload)
if(errMsg) {
    throw Error(errMsg)
}

// 如果没有错误则生成消息示例
let message = SearchRequestMessage.create(payload)
console.log('message实例：', message)

// 编码
let buff = SearchRequestMessage.encode(message).finish()
console.log('编码后的消息', buff)

// 解码
let decodeMessage = SearchRequeestMessage.decode.finish()
console.log('编码后的消息:', buff)

// 还原对象
let toObj = SearchRequestMessage.toObject(decodeMessage)
console.log('还原对象:', toObj)
```

编译 proto文件 的过程可以 用 工具 pbjs 来进行预处理，类似于 protoc 将proto 文件转为 .json/.js



### gRPC

> 远程过程调用，这里的远程并不一定是指运行在不同机器上的程序，即使是运行在同一台机器上不同进程里的程序，也会由于资源隔离的机制而无法直接调用另一个进程中定义的函数。而RPC提供的功能就是将本地函数的调用映射为对另一个进程中的同名函数的调用
>
> gRPC是Google在protobuf格式的基础上推出的高性能开源RPC框架，它同样具备跨平台和跨语言的特点



总的来说，protobuf技术与JavaScript结合的优缺点都很明显，真正能够落地的场景也较为有限，但它在服务端的调用中应用较多



### Angular框架中最具特色的技术

> DI（Dependency Injection，依赖注入），了解相关的IOC（Inversion of Control，控制反转）设计模式、AOP（Aspect Oriented Programming，面向切面编程）编程思想以及实现层面的装饰器语法，最后再看看如何使用Inversify.js在自己的代码中实现“依赖注入”





### 入门级物联网“玩具”Arduino

> Arduino作为一个控制单元，能够连接各种各样的传感器来实现感知和数据采集，并通过控制LED灯、马达、舵机等机电元件来提供动力输出或实现控制。



### [xm-select](https://maplemei.gitee.io/xm-select/#/)

> 始于 [layui](https://layui.com/) 的一个多选解决方案。
> 前身 [formSelects](https://github.com/hnzzmsf/layui-formSelects/), 由于渲染速度慢, 代码冗余, 被放弃了
> [xm-select](https://gitee.com/maplemei/xm-select)使用了新的开发方式, 利用preact进行渲染, 大幅度提高渲染速度, 并且可以灵活拓展。 laiui风格使用起来非常nice

### [LeaferJS](https://www.leaferjs.com/)

> 一个国产的 Canvas 2D 图形渲染引擎，追求极致性能。

### 图形库各维度对比

#### [fabric.js](https://github.com/Rookie-Birds/Fabric-Tutorial_zh-CN/blob/master/part-8.md)

> Fabric.js 是一个基于 HTML5 canvas 的开源 JavaScript 库，用于创建交互式的图形设计和编辑应用程序。它提供了一个易于使用的 API，使用户能够轻松地创建并操作多种图形对象，包括文本、图像、路径、形状和组等。Fabric.js 还支持图形转换、滤镜、动画和事件处理等功能，使得用户可以创建出更加生动、丰富的视觉效果。Fabric.js 适用于各种应用场景，例如在线图形编辑器、游戏开发、数据可视化等。

![fabric-demo.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f224b07df53143d4a0c97386741f9158~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### konva.js

> Konva.js是一个HTML5 Canvas库，用于创建交互式的Canvas应用程序。它提供了一个强大的API，使得开发者可以轻松地在Canvas上添加图形、文本、形状、图像、动画等元素，并且可以与这些元素进行交互。Konva.js的特点包括：
>
> 1. 可以创建复杂的动画效果，如移动、旋转、缩放等。
> 2. 支持事件监听，例如鼠标点击、拖拽、滑动等。
> 3. 支持多种图形，包括矩形、圆形、多边形、线条等。
> 4. 可以实现图形的分组、层级管理等。
> 5. 支持图片缓存，可以在Canvas中快速的绘制图片。
> 6. 具有高度的性能和优化，可以在大型项目中使用。

![konva-demo.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ae1bcce1d126428ea9f25d542caa4bcd~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### leaflet.js

> leaflet.js是一个用于创建交互式地图的开源JavaScript库。它提供了易于使用的API，可以轻松地在网页上添加地图、图层、标记、组件和交互元素，并支持各种地图提供商和数据源。它也可以适应不同的设备和分辨率，支持移动设备和触摸交互。它在Web开发中广泛应用于可视化、地理信息系统、位置服务、游戏等领域。

![leaflet-demo.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/017690f085d74827bfa38e460b9b90de~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### pixi.js

> Pixi.js是一个基于WebGL和Canvas的2D渲染引擎，它提供了一种简单、快速的方式来创建交互式图形、动画和游戏。Pixi.js的设计目标是提高Web应用程序的渲染性能，它可以在不同的设备上实现流畅的动画效果，而且非常易于使用。 Pixi.js支持多种渲染器，包括WebGL、Canvas和SVG，可以根据不同的场景选择最适合的渲染器。它还提供了很多实用的功能，例如精灵、文本、遮罩、滤镜、动画等，可以帮助开发者轻松创建各种效果。 Pixi.js的API简单易用，文档详细，社区活跃，拥有大量的插件和扩展，可以满足各种需求。Pixi.js除了适用于游戏开发，还可以用于数据可视化、UI设计、广告制作等领域。

![pixi-demo.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/105d8f8b3abc4627975fbea90a4a4e34~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 总结

|                                 | ** fabric.js**                                               | **konva.js**                                                 | **leaflet.js**                                               | **pixi.js**                                                  |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **简介**                        | Fabric.js是一个开源的JavaScript库，用于创建交互式的Canvas应用程序。 | Konva.js是一个HTML5 Canvas库，用于创建交互式的Canvas应用程序。 | Leaflet.js是一个开源的JavaScript库，用于创建交互式地图。     | Pixi.js是一个快速、轻量级的2D渲染引擎，可用于创建游戏、动画和交互式应用程序。 |
| **github地址**                  | [ github.com/fabricjs/fa…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ffabricjs%2Ffabric.js) | [github.com/konvajs/kon…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fkonvajs%2Fkonva) | [github.com/Leaflet/Lea…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FLeaflet%2FLeaflet) | [github.com/pixijs/pixi…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fpixijs%2Fpixijs) |
| **Star数量**（统计于2023.5.23） | 24.9k                                                        | 9.3k                                                         | 37.6k                                                        | 40k                                                          |
| **更新频率**                    | 约3次/周                                                     | 约1次/周                                                     | 约3次/周                                                     | 约3次/周                                                     |
| **Fork数量**                    | 3.3k                                                         | 809                                                          | 5.6k                                                         | 4.8k                                                         |
| **issue情况**                   | 253 open, 5425 closed, open率4.5%                            | 74 open, 1172 closed, open率5.9%                             | 362 open, 4663 closed, open率7.2%                            | 134 open, 5038 closed,open率2.6%                             |
| **官方文档**                    | [英文文档](https://link.juejin.cn/?target=http%3A%2F%2Ffabricjs.com%2Fdocs%2Ffabric.Polygon.html) | [英文文档](https://link.juejin.cn/?target=https%3A%2F%2Fkonvajs.org%2Fapi%2FKonva.Animation.html)、[中文文档](https://link.juejin.cn/?target=http%3A%2F%2Fkonvajs-doc.bluehymn.com%2Fdocs%2Fselect_and_transform%2FTransform_Events.html) | [英文文档](https://link.juejin.cn/?target=https%3A%2F%2Fleafletjs.com%2Fexamples%2Fextending%2Fextending-1-classes.html)、[中文文档](https://link.juejin.cn/?target=https%3A%2F%2Fleafletjs.cn%2Freference-1.7.1.html) | [英文文档](https://link.juejin.cn/?target=https%3A%2F%2Fpixijs.io%2Fguides%2Fbasics%2Fgetting-started.html)、[中文文档](https://link.juejin.cn/?target=https%3A%2F%2Fpixijs.huashengweilai.com%2Fguide%2Fstart%2F1.introduction.html) |
| **博客分享**                    | 较多                                                         | 一般                                                         | 较少                                                         | 一般                                                         |
| **Fork数量**                    | 3.3k                                                         | 809                                                          | 5.6k                                                         | 4.8k                                                         |
| **参考案例**                    | ① [vue-fabric-editor](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fnihaojob%2Fvue-fabric-editor) ② [react-design-editor](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fsalgum1114%2Freact-design-editor) ③ [新版蓝湖](https://link.juejin.cn/?target=https%3A%2F%2Flanhuapp.com%2F) | [polotno-studio](https://link.juejin.cn/?target=https%3A%2F%2Fstudio.polotno.com%2F) | 暂无                                                         | 暂无                                                         |
| **编辑能力**                    | **编辑能力强**，支持文本、图片、形状等多种图形元素的编辑，可以进行拖拽、缩放、旋转、变换等操作，还支持多种样式和效果。 | **编辑能力强**，支持多种图形元素的编辑，可以进行拖拽、缩放、旋转、变换等操作，但在样式和效果方面略显不足 | **编辑能力一般，** 可以创建交互式地图，具有丰富的地图操作功能，如缩放、平移、标注等，但专注于地图操作和显示，而不是图形编辑。 | **编辑能力较好，** 可以创建高性能的2D图形，具有丰富的渲染功能，可以进行高级的图形设计和编辑，但缺少拖拽、缩放等基础编辑功能。 |
| **性能及适用场景**              | **性能一般**，适用于开发小型应用。                           | **性能较好**，可以处理大量图形元素，适合开发复杂的应用。     | **性能一般**，对于大规模数据的渲染和处理能力较弱。           | **性能优异**，适用于大规模的用户界面和游戏开发，例如创建复杂的用户界面、2D游戏等。 |
| **学习及开发成本**              | 常用功能封装齐全，可参考案例较多，**上手和使用成本一般；** 基于es5开发。可能会影响开发效率。 | 可直接参考的案例较少，暴露的api较少，**学习成本较高；** 对ts的支持比较高，架构设计更灵活，开发成本相对较低。 | **学习成本一般**；具有简单易用的API，开发成本较低。          | 需要开发者具备一定的图形设计能力，**学习成本较高**。         |



> **社区活跃度**：这四个库均创建于2013年-2015年，基本有十年左右的发展历史，github上的star数量都比较高，其中，pixi和leaflet的社区最为活跃；
>
> **技术资料丰富度**：konva和pixi的官方文档更为详细，能有助于帮我们学习如何快速上手、并能快速定位问题或者提前避“坑”，而fabric的官方文档是备受吐槽的；
>
> **编辑能力**：fabric和konva已经封装好了较多基础编辑功能，特别是fabric有不少开源案例直接给我们提供参考，适合用来开发需要快速实现功能的简单应用；
>
> **性能**：由于pixi是基于webgl的，其他三个库都是基于canvas, 所以pixi的性能最优，在处理大量图形方面有很大的优势；
>
> **适用场景**：这四个库有不同的适用场景，技术选型时应根据业务需求确定，例如，需求的复杂度、规模、性能要求等。
>
> [参考文章](https://juejin.cn/post/7243725952789217317?searchId=20230802103316608BF44C06EF117B48D1)

### [Anime.js](https://animejs.com/)

> 一个轻量级 JavaScript 动画库，具有简单但强大的 API。

### [background-removal-js](https://github.com/imgly/background-removal-js)

> 一个去除图片背景的 JS 库。

### [auto-animate](https://github.com/formkit/auto-animate)

> 一个网页动画库，可以为网页应用添加平滑的过渡，支持各种主流框架。

### [Simple Statistics](http://simplestatistics.org/)

> 一个统计学的 JS 库，提供基本的统计函数。