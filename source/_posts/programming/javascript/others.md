---
title: 性能优化篇
date: 2022-11-11 08:23:10
tags: 性能优化
categories: 
---



### 缓存你的 CORS

> 在您的浏览器发出任何跨源请求（例如 example.com 到 api.example.com）之前，如果它不是一个[简单的请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests)，那么浏览器首先发送一个预检请求，并在发送真正的请求之前等待成功的响应。
>
> 这个预检请求是对服务器的一个 OPTIONS 请求，描述了浏览器要发送的请求，并首先请求权限。
>
> 实际上，几乎所有跨域 API 请求都需要这些预检请求，特别是包括：
>
> - 任何带有 JSON 或 XML 正文的请求
> - 任何请求，包括凭据
> - 任何不是 GET、POST 或 HEAD 的请求
> - 流式传输请求或响应正文的任何交换
> - `Accept`使用除、`Accept-Language`和以外的任何`Content-Language`标题`Content-Type`

要在浏览器中缓存 CORS 响应，只需将此标头添加到您的预检响应中：

```text
Access-Control-Max-Age: 86400
```

要在浏览器和 API 服务器之间的 CDN 和其他代理中缓存 CORS 响应，请添加：

```text
Cache-Control: public, max-age=86400
Vary: origin
```

在nodejs中缓存 CORS

```js
app.use(cors({
    // Set the browser cache time for preflight responses
    maxAge: 86400,
    preflightContinue: true // Allow us to manually add to preflights
}));

// Add cache-control to preflight responses in a separate middleware:
app.use((req, res, next) => {
    if (req.method === 'OPTIONS') {
        res.setHeader('Cache-Control', 'public, max-age=86400');
        // No Vary required: cors sets it already set automatically
        res.end();
    } else {
        next();
    }
});
```

使用 Java Spring 缓存 CORS

```java
@Component
public class AddPreflightCacheControlWebFilter implements WebFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {
        if (CorsUtils.isPreFlightRequest(exchange.getRequest())) {
            exchange.getResponse()
                .getHeaders()
                .add("Cache-Control", "public, max-age=86400");
        }
        return chain.filter(exchange);
    }
}
```

[参考文章](https://httptoolkit.com/blog/cache-your-cors/)



### onclick 事件中传递对象参数

```js
var user = {id:1, name:'zs', age:20};

var ele = '<a οnclick="edit(' + JSON.stringify(user).replace(/"/g, '&quot;') + ');">修改</a>';

或者

var ele = '<a οnclick="edit(\'' + JSON.stringify(user).replace(/"/g, '&quot;') + '\');">修改</a>';

前者取到的是json对象，后者取到的是json字符串。
```

> 如果不行把 onclick 用单引号

### 文本域实现 ctrl+enter 换行，enter 发送

处理事件的时候对按 shift 键做特殊处理，换行时，还要考虑光标在输入的文本段落中时处理

```vue
<el-input
    type="textarea"
    :rows="5"
    placeholder="请输入问题"
    v-model="inputMsg"
    @keydown.enter.native.prevent="handleKeyDown"
></el-input>

<script>
// 处理键盘事件
handleKeyDown(event) {
  console.log(event);
  if (!event.shiftKey) {
    // 发送文本
  } else if (event.shiftKey) {
    this.insertLineBreak(event.target);
  }
},
// 插入换行字符
insertLineBreak(textarea) {
  var startPos = textarea.selectionStart;
  var endPos = textarea.selectionEnd;
  var text = textarea.value;
  textarea.value =
    text.substring(0, startPos) +
    "\n" +
    text.substring(endPos, text.length);
  textarea.selectionStart = textarea.selectionEnd = startPos + 1;
},
</script>
```

优化方法

```js
// 插入换行字符
insertLineBreak(textarea) {
  var startPos = textarea.selectionStart;
  var endPos = textarea.selectionEnd;
  textarea.setRangeText("\n", startPos, endPos, "end");
},

```



### marked + highlight.js 实现代码高亮

在仿gpt对话项目中使用到，对话接口返回如下数据

```js
{
  data: {
    code: 200,
    msg:
      '以下是一个简单的Python脚本，用于获取当前月份的最后一天：\n\n```python\nfrom datetime import datetime, timedelta\n\n# 获取当前日期\ncurrent_date = datetime.now()\n\n# 计算当前月份的最后一天\n# 首先获取下一个月的第一天，然后减去一天即为当前月的最后一天\nnext_month_date = current_date.replace(day=28) + timedelta(days=4)  # 这样可以确保跳过当前月的天数，到达下个月的某一天\nlast_day_of_current_month = next_month_date - timedelta(days=1)\n\n# 打印结果\nprint("当前月份的最后一天是:", last_day_of_current_month.strftime("%Y-%m-%d"))\n```\n\n这个脚本使用了Python的`datetime`模块来处理日期和时间。首先，它获取当前日期，然后通过一些简单的日期计算来确定当前月份的最后一天。这里使用了一个小技巧，即将当前日期的日设置为28，然后加上4天（这样可以确保跳过当前月的天数），接着再减去一天，就得到了当前月的最后一天。\n最后，脚本使用`strftime`方法将日期格式化为`"年-月-日"`的格式，并打印出来。',
    history: [],
    data: "xxx"
  }
}
```

将数据中代码块部分渲染成html代码块形式

~~~text
```python\nfrom datetime import datetime, timedelta\n\n# 获取当前日期\ncurrent_date = datetime.now()\n\n# 计算当前月份的最后一天\n# 首先获取下一个月的第一天，然后减去一天即为当前月的最后一天\nnext_month_date = current_date.replace(day=28) + timedelta(days=4)  # 这样可以确保跳过当前月的天数，到达下个月的某一天\nlast_day_of_current_month = next_month_date - timedelta(days=1)\n\n# 打印结果\nprint("当前月份的最后一天是:", last_day_of_current_month.strftime("%Y-%m-%d"))\n```
~~~

marked 用于将 上边 markdown 语法字符串转化为 html。在使用 highlight.js 使代码部分高亮

```js
var rendererMD = new marked.Renderer();
marked.setOptions({
  renderer: rendererMD,
  highlight: function(code) {
    return hljs.highlightAuto(code).value;
  },
  pedantic: false,
  gfm: true,
  tables: true,
  breaks: false,
  sanitize: false,
  smartLists: true,
  smartypants: false,
  xhtml: false
});
// 将字符串设置到DOM innerHTML 中，后边一句替换是使代码块有背景颜色样式
let htmlDomStr = marked(this.msg).replace(/<pre>/g, "<pre class='hljs'>");

```

依赖样式引入

```js
// 代码背景主题
// import "highlight.js/styles/paraiso-light.css";
import "highlight.js/styles/a11y-dark.css";
// import "highlight.js/styles/ascetic.css";

import marked from "marked";
import hljs from "highlight.js";
```

版本问题，不同版本兼容不同，这里用的是

```json
"highlight.js": "9.18.1",
"marked": "^0.8.0",
```

[highlight.js 样式选择](https://highlightjs.org/examples)

如果要**使用最新版本marked**，写法稍有不同

```json
"highlight.js": "^11.9.0",
"marked": "^12.0.2",
"marked-highlight": "^2.1.1",
```

```vue
<script setup>
import { Marked } from "marked";
import { markedHighlight } from "marked-highlight";
import hljs from 'highlight.js';
import 'highlight.js/styles/a11y-dark.css';
import { ref } from "vue"

let code = ref("")
const marked = new Marked(
  markedHighlight({
    langPrefix: 'hljs language-',
    highlight(code, lang, info) {
      const language = hljs.getLanguage(lang) ? lang : 'plaintext';
      let rs = hljs.highlight(code, { language }).value
      let toolBar = `<p class="code-bar"><span>复制</span></p>`
      return toolBar + `<div class="mycode">${rs}</div>`;
    }
  })
);

code.value = marked.parse('以下是一个简单的**Python脚本**，用于获取当前月份的最后一天：\n\n```python\nfrom datetime import datetime, timedelta\n\n# 获取当前日期\ncurrent_date = datetime.now()\n\n# 计算当前月份的最后一天\n# 首先获取下一个月的第一天，然后减去一天即为当前月的最后一天\nnext_month_date = current_date.replace(day=28) + timedelta(days=4)  # 这样可以确保跳过当前月的天数，到达下个月的某一天\nlast_day_of_current_month = next_month_date - timedelta(days=1)\n\n# 打印结果\nprint("当前月份的最后一天是:", last_day_of_current_month.strftime("%Y-%m-%d"))\n```\n\n这个脚本使用了Python的`datetime`模块来处理日期和时间。首先，它获取当前日期，然后通过一些简单的日期计算来确定当前月份的最后一天。这里使用了一个小技巧，即将当前日期的日设置为28，然后加上4天（这样可以确保跳过当前月的天数），接着再减去一天，就得到了当前月的最后一天。\n最后，脚本使用`strftime`方法将日期格式化为`"年-月-日"`的格式，并打印出来。');
console.log(code.value)
</script>


```





### [服务器实时消息获取的技术方案](https://rxdb.info/articles/websockets-sse-polling-webrtc-webtransport.html)（英文）

**长轮询**

​	请求失败后会定时再次请求，直到请求成功为止

```js
// long-polling in a JavaScript client
function longPoll() {
    fetch('http://example.com/poll')
        .then(response => response.json())
        .then(data => {
            console.log("Received data:", data);
            longPoll(); // Immediately establish a new long polling request
        })
        .catch(error => {
            /**
             * Errors can appear in normal conditions when a 
             * connection timeout is reached or when the client goes offline.
             * On errors we just restart the polling after some delay.
             */
            setTimeout(longPoll, 10000);
        });
}
longPoll(); // Initiate the long polling
```

**websocket**

​	虽然 WebSocket API 的基础知识易于使用，但它在生产中已被证明是相当复杂的。套接字可能会松开连接，必须相应地重新创建。特别是检测连接是否仍然可用，可能非常棘手。大多数情况下，您会添加一个乒乓球检测信号，以确保打开的连接不会关闭。这种复杂性就是为什么大多数人在 WebSocket 之上使用一个库，比如 **Socket.IO** 可以处理所有这些情况，甚至在需要时提供长轮询的回退。

```js
// WebSocket in a JavaScript client
const socket = new WebSocket('ws://example.com');

socket.onopen = function(event) {
  console.log('Connection established');
  // Sending a message to the server
  socket.send('Hello Server!');
};

socket.onmessage = function(event) {
  console.log('Message from server:', event.data);
};
```

**Server-Sent-Events**

​	服务器发送事件 （SSE） 提供了一种通过 HTTP 将服务器更新推送到客户端的标准方法。与 WebSocket 不同，SSE 专为服务器到客户端的单向通信而设计，非常适合实时新闻提要、体育比分等场景，或者任何需要实时更新客户端而无需向服务器发送数据的情况。

```js
// Connecting to the server-side event stream
const evtSource = new EventSource("https://example.com/events");

// Handling generic message events
evtSource.onmessage = event => {
    console.log('got message: ' + event.data);
};
```

​	后端需要支持EventStream格式，即**Content-Type为text/event-stream**。当客户端通过EventSource向指定的服务器端URL发送HTTP请求时，服务器端需要返回符合这种格式的响应。这种响应是一个文本流，其中包含了一个或多个以特定格式编码的事件。

express 为例

```js
import express from 'express';
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/events', (req, res) => {
    res.writeHead(200, {
        'Content-Type': 'text/event-stream',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
    });

    const sendEvent = (data) => {
        // all message lines must be prefixed with 'data: '
        const formattedData = `data: ${JSON.stringify(data)}\n\n`;
        res.write(formattedData);
    };

    // Send an event every 2 seconds
    const intervalId = setInterval(() => {
        const message = {
            time: new Date().toTimeString(),
            message: 'Hello from the server!',
        };
        sendEvent(message);
    }, 2000);

    // Clean up when the connection is closed
    req.on('close', () => {
        clearInterval(intervalId);
        res.end();
    });
});
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
```

**WebTransport API**

​	WebTransport 是一个尖端的 API，旨在实现 Web 客户端和服务器之间的高效、低延迟通信。它利用 HTTP/3 QUIC 协议来实现各种数据传输功能，例如以可靠和不可靠的方式通过多个流发送数据，甚至允许无序发送数据。这使得 WebTransport 成为需要高性能网络的应用程序（如实时游戏、实时流媒体和协作平台）的强大工具。但是，需要注意的是，WebTransport 目前是一个工作草案，尚未被广泛采用。截至目前（2024 年 3 月），**WebTransport 处于工作草案中，尚未得到广泛支持**。您还不能在 Safari 浏览器中使用 WebTransport，并且 Node.js 中也没有本机支持。这限制了它在不同平台和环境中的可用性。

**WebRTC**

略



### chat-gpt 回答展示代码块切换编辑

展示代码块功能就是上述方式实现的，但是后来需求增加点击代码块右下方编辑按钮切换代码编辑，点击右下方发送按钮实现发送功能。

代码编辑采用的方案是 **"monaco-editor": "0.30.1"**. 

思路：在marked转换 代码块时添加 dom div.code-edit-box，然后通过显示隐藏完成切换功能。

交互：在点击编辑时才初始化代码编辑器，初始化完成后通过定位 zIndex 控制显示哪个

**关键代码**

```js
case "代码":
  formatChatMsg.type = "6";
  try {
    let rendererMD = new marked.Renderer();
    marked.setOptions({
      renderer: rendererMD,
      highlight: function(code, language) {
        console.log(code, language);
        let cd = hljs.highlightAuto(code).value;
        let codeEdit = `<div class="code-edit-box"></div>`;
        let footBar = `<div class="code-foot-box">
              <button class="code-btn-edit">编辑</button>
              <button class="code-btn-start">发送</button>
          </div>`;
        return codeEdit + `<div class="code-box">${cd}</div>` + footBar;
      },
      pedantic: false,
      gfm: true,
      tables: true,
      breaks: false,
      sanitize: false,
      smartLists: true,
      smartypants: false,
      xhtml: false
    });
    console.log(data);
    formatChatMsg.msg = marked(data.msg).replace(
      /<pre>/g,
      "<pre class='hljs myCode' >"
    );
    console.log(formatChatMsg);
  } catch (error) {
    console.warn("格式化代码出错", error);
    formatChatMsg.msg = data.msg;
  }
  break;
```

```css
/deep/.myCode {
  position: relative;
  overflow: visible;
  .code-edit-box {
    width: calc(100% - 25px);
    height: calc(100% - 10px);
    position: absolute;
    z-index: -1;
    overflow: hidden;
  }
  .code-foot-box {
    position: absolute;
    bottom: -20px;
    right: 0;
    display: flex;
    z-index: 99;
  }
}
```

事件委托监听所有代码块按钮点击事件

```js
// chatContent 为父级元素
this.$refs.chatContent.addEventListener("click", e => {
  let tar = e.target;
  // 只有点击到代码块区域时才能获取到代码块父级dom
  let parent = tar.closest("code");
  let codeEditBox = null;
  if (parent) {
    codeEditBox = parent.querySelector(".code-edit-box");
  }
  // 点击编辑按钮
  if (tar.className === "code-btn-edit") {
    // 判断按钮编辑器实例是否初始化，是则进行切换显示层级展示源码和编辑代码
    if (codeEditBox.monacoEditor) {
      // 通过编辑器容器的zIndex判断当前展示的内容，编辑器层级默认-1 不显示
      let index = codeEditBox.style.zIndex == 9 ? -1 : 9;
      return (codeEditBox.style.zIndex = index);
    }
    // 获取源码通过源码dom innerText 属性获取
    let codeStr = parent.querySelector(".code-box").innerText;
    // 初始化编辑器
    this.initCodeEdit(codeStr, codeEditBox, parent.className.split("-")[1]);
    // 显示代码编辑器
    codeEditBox.style.zIndex = 9;
  } else if (tar.className === "code-btn-start") {
    let codeStr = parent.querySelector(".code-box").innerText;
    // 通过编辑器实例获取编辑后的代码
    let editCode = codeEditBox.monacoEditor.getValue();
    let status = codeEditBox.style.zIndex === 9; // false 非编辑 true 编辑
    console.log("当前编辑状态：", status);
    console.log("发送原代码：", codeStr);
    console.log("发送修改后代码：", editCode);
  }
});
```

初始化编辑器

```js
initCodeEdit(code, container, language) {
  // 初始化container的内容，销毁之前生成的编辑器
  container.innerHTML = "";
  // 生成编辑器配置
  let editorOptions = Object.assign(
    {
      value: code, // 编辑器的值
      theme: "vs-dark", // 编辑器主题：vs, hc-black, or vs-dark，更多选择详见官网
      roundedSelection: true, // 右侧不显示编辑器预览框
      autoIndent: true, // 自动缩进
      language: language
    },
    {}
  );
  // 生成编辑器对象赋值到编辑器容器 monacoEditor 属性中
  container.monacoEditor = monaco.editor.create(container, editorOptions);
  // 编辑器内容发生改变时触发
  // container.monacoEditor.onDidChangeModelContent(() => {
  //   let codeValue = container.monacoEditor.getValue();
  //   container.codeValue = codeValue;
  // });
}
```

#### [200 行 JavaScript 的虚拟 DOM](https://lazamar.github.io/virtual-dom/#/completed)

在这篇文章(英文)中，我将用 200 多行 JavaScript 介绍虚拟 DOM 的完整实现。

```js
// VirtualNode
//    = { tag : string
//      , properties : { property: string }
//      , children : [VirtualNode]
//      }
//    | { text : string }
//
// Diff
//    = { replace : VirtualNode }
//    | { remove : true }
//    | { create : VirtualNode }
//    | { modify : { remove :: string[], set :: { property : value }, children :: Diff[] } }
//    | { noop : true }
//
const SMVC = (function () {

function assert(predicate, ...args) {
  if (!predicate) {
    console.error(...args);
    throw new Error("fatal");
  }
}

const props = new Set([ "autoplay", "checked", "checked", "contentEditable", "controls",
  "default", "hidden", "loop", "selected", "spellcheck", "value", "id", "title",
  "accessKey", "dir", "dropzone", "lang", "src", "alt", "preload", "poster",
  "kind", "label", "srclang", "sandbox", "srcdoc", "type", "value", "accept",
  "placeholder", "acceptCharset", "action", "autocomplete", "enctype", "method",
  "name", "pattern", "htmlFor", "max", "min", "step", "wrap", "useMap", "shape",
  "coords", "align", "cite", "href", "target", "download", "download",
  "hreflang", "ping", "start", "headers", "scope", "span" ]);

function setProperty(prop, value, el) {
  if (props.has(prop)) {
    el[prop] = value;
  } else {
    el.setAttribute(prop, value);
  }
}

function listener(event) {
  const el = event.currentTarget;
  const handler = el._ui.listeners[event.type];
  const enqueue = el._ui.enqueue;
  assert(typeof enqueue == "function", "Invalid enqueue");
  const msg = handler(event);
  if (msg !== undefined) {
    enqueue(msg);
  }
}

function setListener(el, event, handle) {
  assert(typeof handle == "function", "Event listener is not a function for event:", event);

  if (el._ui.listeners[event] === undefined) {
    el.addEventListener(event, listener);
  }

  el._ui.listeners[event] = handle;
}

function eventName(str) {
  if (str.indexOf("on") == 0) {
    return str.slice(2).toLowerCase();
  }
  return null;
}

// diff two virtual nodes
function diffOne(l, r) {
  assert(r instanceof VirtualNode, "Expected an instance of VirtualNode, found", r);
  const isText = l.text !== undefined;
  if (isText) {
    return l.text !== r.text
      ? { replace: r }
      : { noop : true };
  }

  if (l.tag !== r.tag) {
    return { replace: r };
  }

  const remove = [];
  const set = {};

  for (const prop in l.properties) {
    if (r.properties[prop] === undefined) {
      remove.push(prop);
    }
  }

  for (const prop in r.properties) {
    if (r.properties[prop] !== l.properties[prop]) {
      set[prop] = r.properties[prop];
    }
  }

  const children = diffList(l.children, r.children);
  const noChildrenChange = children.every(e => e.noop);
  const noPropertyChange =
        (remove.length === 0) &&
        (Array.from(Object.keys(set)).length == 0);

  return (noChildrenChange && noPropertyChange)
    ? { noop : true }
    : { modify: { remove, set, children } };
}

function diffList(ls, rs) {
  assert(rs instanceof Array, "Expected an array, found", rs);
  const length = Math.max(ls.length, rs.length);
  return Array.from({ length })
    .map((_,i) =>
      (ls[i] === undefined)
      ? { create: rs[i] }
      : (rs[i] == undefined)
      ? { remove: true }
      : diffOne(ls[i], rs[i])
    );
}

function create(enqueue, vnode) {
  assert(vnode instanceof VirtualNode, "Expected an instance of VirtualNode, found", vnode);

  if (vnode.text !== undefined) {
    const el = document.createTextNode(vnode.text);
    return el;
  }

  const el = document.createElement(vnode.tag);
  el._ui = { listeners : {}, enqueue };

  for (const prop in vnode.properties) {
    const event = eventName(prop);
    const value = vnode.properties[prop];
    (event === null)
      ? setProperty(prop, value, el)
      : setListener(el, event, value);
  }

  for (const childVNode of vnode.children) {
    const child = create(enqueue, childVNode);
    el.appendChild(child);
  }

  return el;
}

function modify(el, enqueue, diff) {
  for (const prop of diff.remove) {
    const event = eventName(prop);
    if (event === null) {
      el.removeAttribute(prop);
    } else {
      el._ui.listeners[event] = undefined;
      el.removeEventListener(event, listener);
    }
  }

  for (const prop in diff.set) {
    const value = diff.set[prop];
    const event = eventName(prop);
    (event === null)
      ? setProperty(prop, value, el)
      : setListener(el, event, value);
  }

  assert(diff.children.length >= el.childNodes.length, "unmatched children lengths");
  apply(el, enqueue, diff.children);
}

function apply(el, enqueue, childrenDiff) {
  const children = Array.from(el.childNodes);

  childrenDiff.forEach((diff, i) => {
    const action = Object.keys(diff)[0];
    switch (action) {
      case "remove":
        children[i].remove();
        break;

      case "modify":
        modify(children[i], enqueue, diff.modify);
        break;

      case "create": {
        assert(i >= children.length, "adding to the middle of children", i, children.length);
        const child = create(enqueue, diff.create);
        el.appendChild(child);
        break;
      }

      case "replace": {
        const child = create(enqueue, diff.replace);
        children[i].replaceWith(child);
        break;
      }

      case "noop":
        break;

      default:
        throw new Error("Unexpected diff option: " + Object.keys(diff));
    }
  });
}

class VirtualNode {
  constructor(any) { Object.assign(this, any) }
}

// Create an HTML element description (a virtual node)
function h(tag, properties, children) {
  assert(typeof tag === "string", "Invalid tag value:", tag);
  assert(typeof properties === "object", "Expected properties object. Found:", properties);
  assert(Array.isArray(children), "Expected children array. Found:", children);
  return new VirtualNode({ tag, properties, children });
}

// Create a text element description (a virtual text node)
function text(content) {
  return new VirtualNode({ text: content });
}

// Start managing the contents of an HTML element.
function init(root, initialState, update, view) {
  let state = initialState; // client application state
  let nodes = []; // virtual DOM nodes
  let queue = []; // msg queue

  function enqueue(msg) {
    queue.push(msg);
  }

  // draws the current state
  function draw() {
    let newNodes = view(state);
    apply(root, enqueue, diffList(nodes, newNodes));
    nodes = newNodes;
  }

  function updateState() {
    if (queue.length > 0) {
      let msgs = queue;
      queue = [];

      msgs.forEach(msg => {
        try {
          state = update(state, msg, enqueue);
        } catch (e) {
          console.error(e);
        }
      });

      draw();
    }

    window.requestAnimationFrame(updateState);
  }

  draw();
  updateState();

  return { enqueue };
}

return { init, h, text };
})();

if (typeof define !== 'undefined' && define.amd) { // AMD
  define([], function () { return SMVC })
} else if (typeof module !== 'undefined' && module.exports) { // CommonJS
  module.exports = SMVC
} else if (typeof window !== 'undefined') { // Script tag
  window.SMVC = SMVC
}
```



### js判断用户大小写锁定输入

```js
document.querySelector('input[type=password]').addEventListener('keyup', function (keyboardEvent) {
    // 获取大小写输入模式
    const capsLockOn = keyboardEvent.getModifierState('CapsLock');
    if (capsLockOn) {
        // Warn the user that their caps lock is on?
    }
});
```

### [js调试命令](https://segmentfault.com/a/1190000012957199)

console 的 不同用法

```js
console.group("中奖了");
console.log(`%c 1 2 3 4 5`, `color: #9E9E9E; font-weight: bold`);
console.log(`%c 1 2 3 4 5`, `color: #03A9F4; font-weight: bold`);
console.log(`%c 1 2 3 4 5`, `color: #4CAF50; font-weight: bold`);
console.groupEnd();
```




