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