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