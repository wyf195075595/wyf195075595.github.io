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