---
title: 如何在页面下载json数据
date: 2022-06-17 18:23:10
categories: htmlcss
---

1. 利用 DataURL 进行下载
2. 转化为 Object URL 进行下载

```js
function download(url, name) {
  const a = document.createElement("a");
  a.download = name;
  a.rel = "noopener";
  a.href = url;
  // 触发模拟点击
  a.dispatchEvent(new MouseEvent("click"));
  // 或者 a.click()
}

const json = {
  a: 3,
  b: 4,
  c: 5,
};
const str = JSON.stringify(json, null, 2);

// 方案一：Text -> DataURL
const dataUrl = `data:,${str}`;
download(dataUrl, "demo.json");

// 方案二：Text -> Blob -> ObjectURL
const url = URL.createObjectURL(new Blob(str.split("")));
download(url, "demo1.json");
```

> 方法一有 2 个缺点：
> 1、无法保留缩进
> 2、字符串里面的空格会被删除