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



<!--more-->

### xlsx

支持预览，读取，导出等excel操作

[api文档](https://docs.sheetjs.com/docs/)

[参考文档](https://www.cnblogs.com/fdxjava/p/17040470.html)

xlsx 导出 el-table 中的数据为 excel

```js
// 表格数据写入excel，并导出为Excel文件
exportToExcel(data, name = "表格数据") {
  // 为啥用 require 应为低版本 import 得到的为null
  const XLSX = require("xlsx");
  // 使用 this.$nextTick 是在dom元素都渲染完成之后再执行
  this.$nextTick(function() {
    // 设置导出的内容是否只做解析，不进行格式转换     false：要解析， true:不解析
    const xlsxParam = { raw: true };
    // tableId 为 el-table 的 id
    const wb = XLSX.utils.table_to_book(
      document.getElementById(data.tableId),
      xlsxParam
    );
    // 导出excel文件名
    let fileName = name + new Date().getTime() + ".xlsx";
    const wbout = XLSX.write(wb, {
      bookType: "xlsx",
      bookSST: true,
      type: "array"
    });
    try {
      // 下载保存文件，此处也可以使用a标签下载
      FileSaver.saveAs(
        new Blob([wbout], { type: "application/octet-stream" }),
        fileName
      );
    } catch (e) {
      console.warn(e, wbout);
    }
    return wbout;
  });
}
```

### [html-docx-js](http://evidenceprime.github.io/html-docx-js/)

​	将富文本内容导出为word文件

```js
import htmlDocx from 'html-docx-js/dist/html-docx';
import saveAs from 'file-saver';
export default {
  methods: {
    exportClick() {
      var content = ` <h1>This is an about page</h1>
      <h2>This is an about page</h2>`
      var page = '<!DOCTYPE html><html><head><meta charset="UTF-8"></head><body>' + content + '</body></html>'
      var converted = htmlDocx.asBlob(page);
      // 用 FielSaver.js里的保存方法 进行输出
      saveAs(converted, 'test.docx');
    }
  }
}
```

注意 脚本中 with(obj||{}){ } 语法会报错 With statements cannot be used with the "esm" output format due to strict mode

要将 with 语法 替换 掉

原来

with的作用，可以使 obj 中的属性在with作用域中直接使用

```js
module.exports = function(obj){
    var __t,__p='',__j=Array.prototype.join,print=function(){__p+=__j.call(arguments,'');};
    with(obj||{}){
    __p+='------=mhtDocumentPart\nContent-Type: '+
    ((__t=( contentType ))==null?'':__t)+
    '\nContent-Transfer-Encoding: '+
    ((__t=( contentEncoding ))==null?'':__t)+
    '\nContent-Location: '+
    ((__t=( contentLocation ))==null?'':__t)+
    '\n\n'+
    ((__t=( encodedContent ))==null?'':__t)+
    '\n';
    }
    return __p;
};
```

新的

```js
module.exports = function(obj){
    var __t,__p='',__j=Array.prototype.join,print=function(){__p+=__j.call(arguments,'');};
    if(obj){
    __p+='------=mhtDocumentPart\nContent-Type: '+
    ((__t=( obj.contentType ))==null?'':__t)+
    '\nContent-Transfer-Encoding: '+
    ((__t=( obj.contentEncoding ))==null?'':__t)+
    '\nContent-Location: '+
    ((__t=( obj.contentLocation ))==null?'':__t)+
    '\n\n'+
    ((__t=( obj.encodedContent ))==null?'':__t)+
    '\n';
    }
    return __p;
};
```

### DocRaptor

> **DocRaptor：** `DocRaptor` 是一个第三方服务，提供了通过 API 将 HTML 或 Markdown 内容转换为 Word 文档的功能。它可以轻松地集成到前端或后端代码中，并且支持更复杂的文档格式和样式。

### [**mammoth.js**](https://github.com/mwilliamson/mammoth.js?tab=readme-ov-file#document-transforms)

>  `mammoth.js` 是一个 JavaScript 库，**用于将 Word 文档转换为 HTML 格式**。虽然它的主要功能是从 Word 文档提取内容，但**它也提供了一些功能用于将 HTML 内容转换为 Word 文档。**

### pdfjs](https://github.com/mozilla/pdf.js)

> 预览pdf，支持很多功能，搜索，定位，高亮，菜单等,不能使用 npm 安装，手动去官网下载dist包，放置在 public 使用
>
> [参考链接](https://blog.csdn.net/IAIPython/article/details/134525898)
>
> [参考链接](https://www.qianduange.cn/article/1646.html)
>
> **文章目录**
>
> - - 一、pdf.js介绍
> 	- 二、实现pdf预览的两种方式
> 	- - 1、使用viewer.html
> 		- 2、将PDF文件渲染成Canvas
> 	- 三、viewer.js的使用形势下的一些方法及技巧
> 	- - 1、实现外部操作跳转到具体的某一页
> 		- - 法1）、修改viewer.js源码，添加一个可供页面跳转的参数page
> 			- 法2）、修改pdf.js里面的页码
> 		- 2、获取pdf.js里面的页码
> 		- 3、根据pdf.js内置的postMessage函数、findBar函数实现外部文本的搜索
> 		- 4、在pdfjs-3.7.107-dist版本中，给pdf.js传参的处理
> 		- 5、在pdfjs-3.7.107-dist版本中，修改convertToRegExpString方法，更换匹配方法（可匹配到带有空格的文档）
> 		- 6、清除pdf缓存
> 		- 7、pdf.js实现分片加载
> 		- 8、如果后端返回的是流的形式，就用此方法转一下
> 		- 9、 下载
> 	- 四、原理

核心就是将 iframe 的 src 属性设置为 pdfjs 的地址，然后将 pdf 文件的地址作为参数传递给 pdfjs

```html
// 例如：http://localhost:8080/pdfjs-4.0.189-dist/web/viewer.html?file=http%3A%2F%2Flocalhost%3A8080%2Fpdf%2Ftest.pdf

<iframe :src="pdfUrl" ref="pdfViewRef" width="100%" height="100%"></iframe>
```

**[对于老版本中可能遇见的问题](https://www.jb51.net/javascript/327521ngl.htm) **

1. pdfjs 3.11.174 以上的版本，js资源是 .mjs 后缀，低版本浏览器会加载资源报错

	自己改 文件后缀不行，得配置 nginx ，或者 降级插件

	找到 Nginx 文件夹下的 mime.types ，我的mimetype文件路径为  /usr/local/nginx/mime.types, 再类型后面加上 mjs

	```nginx
	application/javascript                 js mjs;
	```

	

### **[Docxtemplater](https://docxtemplater.com/demo/#/categories)**

>  `Docxtemplater` 是一个用于生成 Word 文档的 JavaScript 库，它通过填充模板来生成 Word 文档。您可以在模板中使用变量和条件语句，并通过 JavaScript 代码来填充模板。虽然它是一个商业库，但提供了强大的功能和灵活的定制选项
>
>  [参考连接](https://juejin.cn/post/7205181884589031485)
>
>  [解决方案参考链接](https://blog.csdn.net/CRMEB/article/details/133278226)
>
>  [在线示例](https://codepen.io/wyf195075595/pen/JjVxmpW)

```js
import Docxtemplater from "docxtemplater";
import { saveAs } from "file-saver";
import JSZipUtils from "jszip-utils";
import PizZip from "pizzip";

// path 模板文件的路径
// 填充模板的数据
// 下载的文件名称
export function downloadWithTemplate(path, data, fileName) {
    JSZipUtils.getBinaryContent(path, (error, content) => {
        if (error) throw error;

        const zip = new PizZip(content);
        const doc = new Docxtemplater().loadZip(zip);
        doc.setData({
            ...data,
        });

        try {
            doc.render();
        } catch (error) {
            const e = {
                message: error.message,
                name: error.name,
                stack: error.stack,
                properties: error.properties,
            };
            ElMessage.error("文件格式有误!" + e);
            throw error;
        }
        const out = doc.getZip().generate({
            type: "blob",
            mimeType:
                "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
        });
        saveAs(out, fileName);
    });
}

```

```js
downloadWithTemplate("审计底稿1.dotx", {
	time: '2024.1.22',
    number: 'xx',
    sj_content: 'xx',
    ...
    
}, "模板word.docx");
```

![image-20240424162500232](C:/Users/19507/AppData/Roaming/Typora/typora-user-images/image-20240424162500232.png)

### [vue-html2pdf](https://github.com/kempsteven/vue-html2pdf)

pdf导出的方案.[vue3版本分支](https://github.com/raiblaze/vue3-html2pdf)，



### [docx-preview](https://github.com/VolodymyrBaydalka/docxjs)

比 mammoth.js 效果更好，支持跟多复杂样式

```js
import * as docx from 'docx-preview';

// 预览
const goPreview = async () => {
  const { data } = await service({
    method: 'get',
    responseType: 'blob'
  })
  docx.renderAsync(data, refFile.value)
}
```



### [docx.js](https://docx.js.org/)

> docxjs是一款较为强大的docx格式文档处理的js库，基本可以处理我们遇到的所有有关于docx的文档问题。支持内置方式的word**生成、解析、格式渲染**。除此之外，我们还可以通过自定义方式直接通过xml方式渲染word包括但不限于**字体样式、字体大小、段落样式、间距、颜色**等
>
> [参考教程文章1](https://www.enjoytoday.cn/2022/09/15/%e5%a6%82%e4%bd%95%e4%bd%bf%e7%94%a8docxjs%ef%bc%9f/)
>
> [参考教程文章2](https://blog.csdn.net/baijiafan/article/details/126779192)

### [fecs](http://fecs.baidu.com/)

代码检查 / 格式化，从未如此简单



### Pinia

```
Pinia 是 Vue.js 的轻量级状态管理库，最近很受欢迎。它使用 Vue 3 中的新反应系统来构建一个直观且完全类型化的状态管理库。

Pinia体积约1KB,轻量级，简单的项目应用项目性能有优化
```

### [PizZip](https://github.com/open-xml-templating/pizzip)

> 一个用于使用 Javascript 创建、读取和编辑.zip文件的库，具有可爱而简单的 API。

```js
var zip = new PizZip();
// 编辑
zip.file("Hello.txt", "Hello World\n");

var img = zip.folder("images");
img.file("smile.gif", imgData, { base64: true });

var content = zip.generate({ type: "blob" });

// see FileSaver.js
saveAs(content, "example.zip");
```

###  [jszip 优秀的前端压缩库](https://github.com/Stuk/jszip)

```js
const zip = new JSZip();

zip.file("Hello.txt", "Hello World\n");

const img = zip.folder("images");
img.file("smile.gif", imgData, {base64: true});

zip.generateAsync({type:"blob"}).then(function(content) {
    // see FileSaver.js
    saveAs(content, "example.zip");
});

/*
Results in a zip containing
Hello.txt
images/
    smile.gif
*/
```



### [zip-a-folder](https://github.com/maugenst/zip-a-folder)

> 创建 zip,tar 文件。将指定目录压缩

```js
import { zip } from 'zip-a-folder';

class TestMe {

    static async main() {
        await zip('/path/to/the/folder', '/path/to/archive.zip');
    }
}

TestMe.main();
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

### [BPMN.IO](https://bpmn.io/toolkit/bpmn-js/)

> 经典的流程图绘制插件
>
> [中文教程][https://github.com/LinDaiDai/bpmn-chinese-document/blob/master/LinDaiDai/%E5%85%A8%E7%BD%91%E6%9C%80%E8%AF%A6bpmn.js%E6%95%99%E6%9D%90-%E5%9F%BA%E7%A1%80%E7%AF%87.md]

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

### [Inquirer.js](https://github.com/SBoudrias/Inquirer.js?tab=readme-ov-file) 与交互式命令行工具

> 可以制作交互式工具命令，类似于 vue 脚手架创建项目时的操作

eg:

旧版

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
inquirer.prompt(questions).then(abswer=> {
    console.log('您输入的所有答案如下：')
    console.log(answer)
})
```

新版

```js
import { select, Separator } from '@inquirer/prompts';
// Or
// import select, { Separator } from '@inquirer/select';

const answer = await select({
  message: 'Select a package manager',
  choices: [
    {
      name: 'npm',
      value: 'npm',
      description: 'npm is the most popular package manager',
    },
    {
      name: 'yarn',
      value: 'yarn',
      description: 'yarn is an awesome package manager',
    },
    new Separator(),
    {
      name: 'jspm',
      value: 'jspm',
      disabled: true,
    },
    {
      name: 'pnpm',
      value: 'pnpm',
      disabled: '(pnpm is not available)',
    },
  ],
});
console.log(answer); // npm
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
// upload.js
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

### [jTopo]([www.jtopo.com/](https://link.zhihu.com/?target=http%3A//www.jtopo.com/))

> 简介：jTopo（Javascript Topology library）是一款完全基于 HTML5 Canvas 的关系、拓扑图形化界面开发工具包。
>
> 背景：个人开发者、国产

### [和风天气插件](https://widget.qweather.com/)

> 和风天气插件产品，免费、跨终端。适配你的网站、APP、公众号…,
>
> 插件完全免费，并且没有流量限制

### [mark.js](https://markjs.io/)

> 针对每个用例的搜索词突出显示。可以与纯JavaScript一起使用，也可以作为jQuery插件使用。
>
> 它可用于动态标记搜索词或自定义正则表达式，并为您提供内置选项，如音调符号支持、单独的单词搜索、自定义同义词、iframe 支持、自定义过滤器、准确性定义、自定义元素、自定义类名等。
>
> [示例](https://jsfiddle.net/julmot/hdyLpy37/)

### [Pictode](https://github.com/JessYan0913/pictode)

> 一个开源的网页绘图编辑器,基于konva.js。可以通过npm包方式引入项目

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bce6230c53464d1fb917a31c5e1f33c2~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1920&h=1054&s=2552077&e=gif&f=629&b=fbfafd)

### [excalidraw](https://github.com/excalidraw/excalidraw)

> 与Pictode类似，但是更强,支持集成到项目中但确是整体引入，不能自定义。github star 75k+

![](https://camo.githubusercontent.com/ddb3b5442d70e4dc28bf57c16f772be3ebe1ac3768ffbacaca3606013bf696a4/68747470733a2f2f657863616c69647261772e6e7963332e63646e2e6469676974616c6f6365616e7370616365732e636f6d2f67697468756225324670726f647563745f73686f77636173652e706e67)

### [recorder](https://github.com/xiangyuecn/Recorder)

> Recorder用于html5录音
>
> 支持在大部分已实现`getUserMedia`的移动端、PC端浏览器麦克风录音、实时处理，主要包括：Chrome、Firefox、Safari、iOS 14.3+、Android WebView、腾讯Android X5内核(QQ、微信、小程序WebView)、uni-app(App、H5)、大部分2021年后更新的Android手机自带浏览器

### [EasyPlayer](https://github.com/tsingsee/EasyPlayer.js/tree/master)

> ## About
>
> EasyPlayer.js H5播放器，是一款免费的能够同时支持HTTP、RTMP、HTTP-FLV、HLS（m3u8）直播与点播等多种协议，支持H.264、H.265、AAC等多种音视频编码格式，支持mse、asm、wasm等多种解码方式，支持Windows、Linux、Android、iOS全平台终端的H5播放器。
>
> [在线演示](https://www.tsingsee.com/easyplayer/)

### [h265web.js](https://github.com/numberwolf/h265web.js/wiki/%E3%80%90%E8%AF%B4%E6%98%8E%E3%80%91%E5%88%9B%E5%BB%BA%E6%92%AD%E6%94%BE%E5%99%A8)

> HEVC/H.265 网页直播/点播播放器 支持硬解！ 支持H.265的HttpFLV/HLS/MP4/TS/FLV/M3U8/Websocket播放
>
> [在线演示](http://v.yuveye.com/h265web/)

### [TinyLD](https://github.com/komodojp/tinyld)

> 一个 JS 库，用来判断一段文字是什么语言（汉语、英语、日语等等）。

### [VineJS](https://github.com/vinejs/vine)

> 一个 Node.js 的表单验证库，只用于后端，支持大量验证规则，验证速度快。

### antv-x6

> 流程图编辑器，流程图绘制
>
> [示例1](https://wforguo.github.io/daily-code/vue2-app/#/X6) [示例代码](https://github.com/wforguo/daily-code/tree/master/vue-app/vue2-app) [示例文档](https://forguo.cn/f2e/%E5%8F%AF%E8%A7%86%E5%8C%96/Antv-X6%E5%9B%BE%E7%BC%96%E8%BE%91%E5%99%A8%E7%9A%84%E5%BA%94%E7%94%A8%E2%80%94%E2%80%94%E6%B5%81%E7%A8%8B%E5%9B%BE%E5%AE%9E%E7%8E%B0.html#%E9%A1%B9%E7%9B%AE%E6%95%88%E6%9E%9C%E5%B1%95%E7%A4%BA)
>
> [示例2](https://tian-jing-ruo-feng.github.io/logic-flow/) [示例代码](https://github.com/tian-jing-ruo-feng/logic-flow)

[logic-flow](https://github.com/didi/LogicFlow)

> 专注于业务自定义的流程图编辑框架，支持实现脑图、ER图、UML、工作流等各种图编辑场景。比x6使用更简单，更强
>
> [官方文档](https://site.logic-flow.cn/examples)

### 导出pdf

[打印的5中姿势](https://juejin.cn/post/7262656196855038008?searchId=202312201409479A9BB809BBF1D3632BDA#heading-9)

1. html2canvas + jsPDF 生成pdf

	有点不清晰，无论怎么调整 scale 和 dpr 都有点，但是还是能接受

2. jsPDF手写内容生成pdf

	第一个问题就是要下载字体（不支持中文），第二个排版要计算，贼麻烦，但是清晰度是最高的

3. css 媒体查询

	```css
	@media print {
	  .mnav.c-font-normal.c-color-t {
	    color: green !important; 
	  }
	}
	```

	配合 window.print(), 超链接颜色可能会发生变化

4. iframe 打开

	```js
	 function printElement(e) {
	    var ifr = document.createElement('iframe');
	    ifr.style = 'height: 0px; width: 0px; position: absolute'
	    document.body.appendChild(ifr);
	    ifr.contentDocument.body.appendChild(e.cloneNode(true))
	    ifr.contentWindow.print();
	
	    ifr.parentElement.removeChild(ifr);
	}
	var style = document.createElement('style')
	style.innerText = '* { color: red; }'
	var img = document.createElement('img')
	img.height = 300; 
	img.width = 300;
	img.src = 'https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png'
	var div = document.createElement('div')
	div.innerText = "这是百度网站"
	div.appendChild(style)
	div.appendChild(img)
	printElement(div)
	
	```

	

### [localforage](https://github.com/localForage/localForage)

> 本地存储的最佳方案，localForage 是一个快速而简单的 JavaScript 存储库。localForage 通过将异步存储（IndexedDB 或 WebSQL）与简单的类似 `localStorage` 的 API 结合使用来改善 Web 应用的脱机体验。
>
> [中文文档](https://localforage.docschina.org/#api-driver)

### [VisActor](https://visactor.io/)

> 一个字节出品的前端数据可视化解决方案，分成图表库 [VChart](https://visactor.io/vchart) 和表格库 [VTable](https://visactor.io/vtable)。
>
> vchart 有播放动画，可以绘制动态数据随时间增长的动画

### [heatmap.js](http://www.patrick-wied.at/static/heatmapjs/)

> Heatmap.js V2.0 是目前网络上最先进的热图可视化库。新的2.0版本 Heatmap.js 更快，拥有更强的渲染模块，使用更方便，因此您可以快速掌握和扩展自定义功能。
>
> [参考文章](https://blog.csdn.net/cungudafa/article/details/102869566)

### [Echo UI](https://echoui.dev/zh/)

> 一款专为 Web Audio API 设计的 UI 库，适合用来搭建基于 Web 的音频应用。
>
> Light 指示灯
>
> Oscilloscope 示波器
>
> Spectrogram 频谱图
>
> VU Meter 音量表
>
> Waveform 波形图
>
> ...

### [Animotion](https://cssanimotion.pages.dev/)

> 一个网页 CSS 动画生成器，可视化设定动画，自动生成代码。

### [Inpaint-web](https://github.com/lxfater/inpaint-web)

> 要加载30M大小的包，开源的图片修复和超分辨率工具, 纯浏览器端实现。

[KaTeX](https://katex.org/)

>  LaTeX 数学渲染,在web页面展示复杂数学公式，使用 [LateX](https://baike.baidu.com/item/LaTeX/1212106) 语法。
>
>  配合这个[手写公式识别为LateX语法](https://webdemo.myscript.com/) 六的一批

### [音视频处理](https://hughfenghen.github.io/WebAV/guide)

> WebAV 项目包含三个模块，使用 TypeDoc 生成 API 文档
>
> - @webav/av-cliper
> 	- 提供音视频数据处理的基础 API
> 	- 包括：音视频解码、合成、拼接、裁剪等功能等等
> - @webav/av-recorder
> 	- 录制 `MediaStream`，输出 MP4 视频
> 	- 在浏览器中，可以中 canvas、video、摄像头、麦克风、分享屏幕等 API 获取 `MediaStream` 对象
> - @webav/av-canvas
> 	- 用户或代码可操作的“画布”，能添加、控制各种素材，支持输出 `MediaStream`
> 	- 素材包括：摄像头、麦克风、屏幕、音视频文件、图片、文字
> 	- 输出 `MediaStream` 意味着能将“画布”内容推流至服务器或录制为本地视频
>
> `@webav/av-cliper` 的 API 相对多一些，建议先阅读[基础概念](https://hughfenghen.github.io/WebAV/_api/av-cliper/#md:basic-concepts-基础概念)，有助于快速理解 DEMO 源码与其他 API

### [nginx代管理器](https://nginxproxymanager.com/guide/)

> 这个项目是一个预构建的 docker 映像，使您能够**轻松地转发**到在家中或其他地方运行的**网站**，包括**免费 SSL**，而**无需对 Nginx 或 Letsencrypt 了解太多**。

### [mermaid.js](https://github.com/mermaid-js/mermaid?tab=readme-ov-file)

> Mermaid 是一个基于 Javascript 的图表绘制工具，通过解析类 Markdown 的文本语法来实现图表的创建和动态修改。Mermaid **诞生的主要目的是让文档的更新能够及时跟上开发进度。**
>
> 可以快速实现 流程图、序列图、类图、状态图、实体关系图、用户旅程图、甘特图、饼图、象限图、需求图、git图、C4图、思维导图、时间线、桑基图、方框图...。但是**可自定义性差**
>
> [在线演示](https://mermaid.live/edit#pako:eNp1Uk1vwjAM_SuRuYBUUPqxlkYT0kavO-02skNoDFSkSdWmAob470tbOrFJ88nPfn7xU3yF3EgEBltl8uN8i1ZwTVzkRrWlbkg4QGnyFYfM5G2J2nL4ZPdGU4kcXfuk_ecNB-Jaq2kHZ0OL647XqzMch4ZQjq9w16k9lssphxdyKiQSo5EUmtgDkrKQUiGH2SO1dgp1sT-MEqjl362Cf7bqTW037q3s1an-9jOC7C60HvlkPl-RbEDrHkhhcSsaHAe62p3e2Ityi5NdoRSbyFh6ja3NEdkkDMN7Pnc-7YFF1XmYAQ9KrEtRSPcn167GwdkvnXPmUinqIweub44nWmveLzoHthOqQQ_aqlsmK8S-FuVPtRL6wxiHbd0OENgVzsCScOGnEaVRHCSUpnTpwQVYkNIFpX6UJEHq-0EShDcPvnoBuojDOArT5VNMl0ka-W4CZWFN_TbcUH9Kt28H1K5A)
>
> 类似得库：Plantuml、Graphviz

html中使用

```shell
yarn add mermaid
```

```html
<pre class="mermaid">

  flowchart LR
    A[班组长] -->|开始| B(班主任)
    B --> C(教导主任)
    B --> D(教务处)
    D --> F(校长)
    C --> F
 </pre>

<script>
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
mermaid.initialize({ startOnLoad: true });
</script>
```

```vue
<template>
  <div class="container">
    <pre class="mermaid">
      graph LR
        A[立项] --> B[需求分析]
        B --> C[设计]
        C --> D[开发]
        D --> E[测试]
        E--> F[发布]
        F --> G[维护]
    </pre>
  </div>
</template>
<script>
import mermaid from "mermaid"; //引用mermaid
export default {
  mounted() {
    mermaid.initialize({ startOnLoad: true });
    mermaid.init();
  },
}
</script>
```

手动渲染

```js
let mermaidDiv = document.getElementById("mermaid")
// 设置 Mermaid 图表代码  
mermaid.initialize({ startOnLoad: false });
// mermaid.init();
mermaid.render('mermaid', mermaidDiv.textContent, (svgCode) => {
    console.log("svgCode", svgCode);
    mermaidDiv.innerHTML = svgCode;
});
```



### [jsmind](https://github.com/hizzgdev/jsmind)

> jsMind 是一个显示/编辑思维导图的纯 javascript 类库。

```html
<html>
    <head>
        <link
            type="text/css"
            rel="stylesheet"
            href="//cdn.jsdelivr.net/npm/jsmind@0.8.3/style/jsmind.css"
        />
        <script
            type="text/javascript"
            src="//cdn.jsdelivr.net/npm/jsmind@0.8.3/es6/jsmind.js"
        ></script>
    </head>
    <body>
        <div id="jsmind_container"></div>

        <script type="text/javascript">
            var mind = {
                // 3 data formats were supported ...
                // see documents for more information
            };
            var options = {
                container: 'jsmind_container',
                theme: 'orange',
                editable: true,
            };
            var jm = new jsMind(options);
            jm.show(mind);
        </script>
    </body>
</html>
```

### 示意图利器  [D2](https://d2lang.com/)

> 根据代码，生成图片，非常适合放入代码仓库，进行版本管理。
>
> 它有一个线上生成器 [play.d2lang.com](https://play.d2lang.com/)（下图），把代码贴进去，按下"compile"按钮，马上生成图片。

可以绘制复杂得图

![img](https://cdn.beekka.com/blogimg/asset/202412/bg2024120614.webp)

### [`whatsapp-web.js`](https://github.com/pedroslopez/whatsapp-web.js?utm_source=gold_browser_extension)

> `whatsapp-web.js` 主要用于编写自动化脚本，用于自动化处理 **WhatsApp** Web 客户端的各种操作，例如自动回复、消息监控、群组管理等。这个库提供了一种灵活的方式来与 WhatsApp Web 进行交互，并且可以根据需要进行定制和扩展。
>
> 有点类似于socket.io ,两个库都涉及到在 Node.js 环境中的通信，但它们的使用场景和目的完全不同。`whatsapp-web.js` 用于控制 **WhatsApp** Web 客户端，而 `Socket.IO` 用于实现实时通信功能

### [image-coversion](https://github.com/WangYuLue/image-conversion)

> 图片压缩插件，使用场景，上传高质量图片前进行压缩。减少图片大小。详情查看文档。
>
> 依赖原生 fetch、promise ,不支持IE11

```js
// 指定压缩图片到200kb
async function view() {
  const file = document.getElementById('demo').files[0];
  console.log(file);
  const res = await imageConversion.compressAccurately(file,200)
  console.log(res);
}
```

[canvas 弹幕库](https://fly-barrage.netlify.app/guide/quick-start.html)

> **功能完善**
>
> 支持滚动弹幕、顶部弹幕、底部弹幕、高级弹幕
>
> **文字、图片混搭**
>
> 弹幕内容支持混入图片
>
> **自定义渲染**
>
> 提供自定义渲染接口，高度定制化弹幕渲染
>
> **不局限于特定框架**
>
> 原生 Html、Vue、React 均可使用，不局限上层应用所使用的框架

### [koodo](https://github.com/koodo-reader/koodo-reader?tab=readme-ov-file)

> Koodo Reader 是一个开源免费的电子书阅读器，支持多达15种主流电子书格式， 内置笔记、高亮、翻译功能，助力高效书籍阅读和学习。支持 Windows、macOS、Linux 和网页版

### [vconsole](https://github.com/Tencent/vConsole)

> 
>
> 一个轻量、可拓展、针对手机网页的前端开发者调试面板。
>
> vConsole 是框架无关的，可以在 Vue、React 或其他任何框架中使用。
>
> 现在 vConsole 是微信小程序的官方调试工具。
>
> [其他拓展](https://juejin.cn/post/7134219358301077517?searchId=20240222174257D00581DF83F659312EE4&utm_source=gold_browser_extension#heading-3)

### [pear-rec](https://027xiguapi.github.io/pear-rec/)

> 本插件提供了录屏、录音、录像、预览图片、预览视频功能。

### [blossom](https://gitee.com/blossom-editor/blossom?utm_source=gold_browser_extension)

> 个支持私有部署的云端存储双链笔记软件，你可以将你所有的笔记，图片，个人计划安排保存在自己的服务器中，并在任意设备之间实时同步。同时还是一个个人博客。

![home](https://gitee.com/blossom-editor/blossom/raw/dev/doc/imgs/device.png)

### [whistle](https://juejin.cn/post/7330151205770412095?searchId=20240321152558EDA551202F6D8B7D2C7F)

> 好多人认为whistle是抓包工具，殊不知抓包只是whistle能力的冰山一角。除了抓包外，它还能修改请求与响应、真机调试h5移动端、解决跨域、域名映射等等等。总而言之，使用whistle能够提升我们的开发效率，改善开发体验。
>
> whistle一出手就是抓包工具的极限，那年它双手插兜不知道什么是对手（doge）。

### [Superjson](https://github.com/blitz-js/superjson)

> 一个 JavaScript 模块，用于字符串和 JSON 数据的互相转换，支持多种 JSON 不支持的数据格式。
>
> 如：

| JSON        | Superjson |      |
| ----------- | --------- | ---- |
| `undefined` | ❌         | ✅    |
| `bigint`    | ❌         | ✅    |
| `Date`      | ❌         | ✅    |
| `RegExp`    | ❌         | ✅    |
| `Set`       | ❌         | ✅    |
| `Map`       | ❌         | ✅    |
| `Error`     | ❌         | ✅    |
| `URL`       |           |      |



### [LunarLink](https://github.com/tahitimoon/LunarLink)

> 一个基于 Web 的接口自动化测试平台，可以快速编写和运行接口自动化测试用例。

### [driver.js](https://juejin.cn/post/7262542750405804091?searchId=20240329160649886A99DDEED7CE8D9245)

> driver.js 是一款用原生 js 实现的页面引导库，上手非常简单，体积在 gzip 压缩下仅仅 5kb。
>
> [官方文档](https://driverjs.com/)

### [simple-mind-map](https://github.com/wanglin2/mind-map)（思绪思维导图）

> 一个开源的 Web 思维导图，试用 [Demo](https://wanglin2.github.io/mind-map/)。
>
> 本项目包含两部分：
>
> 1.一个 js 思维导图库，不依赖任何框架，可以使用它来快速完成 Web 思维导图产品的开发。
>
> 开发文档：https://wanglin2.github.io/mind-map/#/doc/zh/。
>
> 2.一个 Web 思维导图，基于思维导图库、Vue2.x、ElementUI 开发，可以操作电脑本地文件，可以当做一个在线版思维导图应用使用，也可以自部署和二次开发。
>
> 在线地址：https://wanglin2.github.io/mind-map/。
>
> 此外也提供了客户端可供下载使用，支持`Windows`、`Mac`及`Linux`，下载地址：
>
> Github：[releases](https://github.com/wanglin2/mind-map/releases)。百度云盘：[地址](https://pan.baidu.com/s/1huasEbKsGNH2Af68dvWiOg?pwd=3bp3)。

### [markmap](https://markmap.js.org/)

> 可以与markdown语法结合，绘制思维导图

```markdown

# 开源小分队
 
## 节点1
- 节点1.1
  - 节点1.1.1
 
## 节点2
## 节点3
## 节点4

```

![图片](https://img-blog.csdnimg.cn/img_convert/d254647485f4203fb5d4370902e007ba.gif)

代码中使用

```shell
pnpm add markmap-lib
pnpm add markmap-view
```



```vue
<template>
  <div class="flex-1">
    <textarea class="w-full h-full border border-gray-400" v-model="value" />
  </div>
  <svg class="flex-1" ref="svgRef" />
</template>

<script lang="ts" setup>
import { ref, onMounted, onUpdated } from 'vue';
import { Transformer } from 'markmap-lib';
import { Markmap } from 'markmap-view/dist/index.esm';

const transformer = new Transformer();
const initValue = `# 思维导图
1. 标题1
 - 子标题1
 - 子标题2
3. 标题2
4. 标题3
`;
const mm = ref()
const value = ref('')
const svgRef = ref()
const update = () =>{
	 const { root } = transformer.transform(value.value);
      mm.value.setData(root);
      mm.value.fit();
}

watch(()=>value.value,(n)=>{
	// 监听输入变化更新思维导图
	update()
})
onMounted(()=>{
	// 初始化markmap思维导图
	mm.value = Markmap.create(svgRef.value);
	value.value = initValue.value
	// 更新思维导图渲染
    update();
})
</script>
```





[blog-cells](https://github.com/rameshvarun/blog-cells)

> 这个工具可以在网页插入互动式区块，用来展示和执行 JavaScript 代码，类似于 Jupyter。
>
> 

[Hyphen](https://github.com/00000o1/-)

> 一个 Web 组件的基类，你可以在它的基础上定义自己的 Web Component。类似的工具还有 [Cami.js](https://github.com/kennyfrc/cami.js)。

### [stokado](https://github.com/KID-joker/stokado)

> 浏览器存储对象（比如 localStorage、IndexDB）的包装库，提供统一的 API，以及一些便利的功能（比如过期时间）。

### [Newcar](https://github.com/dromara/newcar)

> 一个 JS 语言的前端动画引擎，基于 Skia 的WebAssembly 版本，在 Canvas 画布上生成动画。

### [x-crawl](https://github.com/coder-hxl/x-crawl)

> 一个 AI 辅助的爬虫库，基于 Node.js，抓取网页后，可以用文字描述所要的操作。

### [条码生成 JsBarcode](https://lindell.me/JsBarcode/)

> 易于使用，但功能强大的条形码生成器，适用于网页和Node.js

### [音频可视化](https://wavesurfer.xyz/)

> 音波线条展示音频

```js
<script type="module">
import WaveSurfer from 'https://cdn.jsdelivr.net/npm/wavesurfer.js@7/dist/wavesurfer.esm.js'

const wavesurfer = WaveSurfer.create({
  container: '#waveform',
  waveColor: '#4F4A85',
  progressColor: '#383351',
  url: '/audio.mp3',
})

wavesurfer.on('interaction', () => {
  wavesurfer.play()
})
</script>
```

### [howlerjs](https://howlerjs.com/)

> howler.js 使在所有平台上使用 JavaScript 中的音频变得简单可靠。api支持几乎所有浏览器，支持所有浏览器音频格式，0依赖，完全控制

### [打字机效果](https://www.typeitjs.com/docs/vanilla/quick-start/)

> 快速简单的实现打字机效果

```js
<html>
  <head></head>
  <body>
    <!-- A root element for TypeIt to target. -->
    <span id="myElement"></span>

    <!-- The script itself, loaded AFTER your root element. -->
    <script src="https://unpkg.com/typeit@@{TYPEIT_VERSION}/dist/index.umd.js"></script>
    <script>
      new TypeIt("#myElement", {
        strings: "This is what will be typed!",
      }).go();
    </script>
  </body>
</html>
```

[敏感词过滤库](https://github.com/ZhelinCheng/mint-filter)

> 基于Aho–Corasick算法，更轻巧的JavaScript敏感词过滤库，更好的容错性，适用Node及浏览器环境

```js
// CommonJS导入
const { Mint } = require('mint-filter')
// TypeScript / ES Module引用
import Mint from 'mint-filter'
const mint = new Mint(['敏感词数组'])

// 基本使用
mint.filter('需要验证的文本')
```

[pinyin-pro](https://pinyin-pro.cn/use/pinyin.html)

> 一个识别准确、性能优异的汉字拼音转换库.About
>
> 中文转拼音、拼音音调、拼音声母、拼音韵母、多音字拼音、姓氏拼音、拼音匹配

[自由画板](https://songlh.top/paint-board/)

> 只支持iframe嵌套，不能直接集成到项目里面

> 

### [SimpleMindMap](https://wanglin2.github.io/mind-map/#/doc/zh/introduction)

> `simple-mind-map`【中文名：思绪思维导图】是一个简单&强大的Web思维导图库，不依赖任何特定框架。可以帮助你快速开发思维导图产品。
>
> 如果你只是想使用思维导图，你也完全可以把本项目的demo作为一个普通的在线思维导图工具使用。[在线示例](https://wanglin2.github.io/mind-map/#/)

> 

### [页面引导](https://github.com/usablica/intro.js?tab=readme-ov-file)

> 介绍页面亮点功能

![](https://raw.githubusercontent.com/usablica/intro.js/gh-pages/img/introjs-demo.png)

### [中国区域数据]([china-area-data](https://github.com/airyland/china-area-data))

> 中国各地区及地区编码数据

### [rrweb](https://github.com/rrweb-io/rrweb/blob/master/guide.zh_CN.md)

> 录制，回放 操作过程. 不是视频录制回放。而是复制网页及操作过程回放，回放会触发浏览器接口调用。就是录制全部用户在网页里的所有点击操作，出问题随时回放排查
>
> [rrweb](https://codepen.io/ansike/pen/qBXQQxm)
>
> 相似的工具 [TimeCat](https://timecatjs.com/docs/quick-start) [参考文章](https://zhuanlan.zhihu.com/p/670386173)
>
> 

### [dom purify](https://github.com/cure53/DOMPurify)

> 将用户输入的html转化为可信任的html.DOMPurify 将去除所有包含危险 HTML 的内容，从而防止 XSS 攻击和其他肮脏的东西。

注意，默认情况下，我们允许 HTML、SVG 和 MathML。如果您只需要 HTML，这可能是一个非常常见的用例，您也可以轻松设置它：

```js
const clean = DOMPurify.sanitize(dirty, { USE_PROFILES: { html: true } });
```



### [editorAnnotate](https://gitee.com/doc_wei01/editorAnnotate/tree/master/)

> skyeyeAnnotate是一款**基于jQuery**封装的**HTML文章批注插件**，帮助开发者轻松实现**类似word的文本批注**。我们需要在一个或多个网页中添加批注信息的时候这个插件可以简化我们许多工作。使用者可以随意选择文本进行批注以及批注回复，支持批注历史查看等功能。支持客户合同批注、试卷批阅等多种场景。

### [ElysiaJS](https://elysiajs.com/)

> JS 语言的 Web 框架，专门为 Bun 运行环境开发。 与nodejs 一样作为 js引擎开发的后端语言

### [IMaker 创客](https://github.com/slince-zero/IMaker)

> 一款开源的封面设计工具，基于 JS 的 Web 应用，有[试用 Demo](https://img-maker.vercel.app/)。

### [VideoSubtitleGenerator](https://github.com/buxuku/VideoSubtitleGenerator)

> 一个命令行工具，通过语音识别，批量为本地的视频文件生成字幕，并支持翻译。

### [vmr](https://github.com/gvcgo/version-manager)

> 一个跨平台的通用版本管理器，目前支持40多种编程语言和工具。另有一个类似工具 [vfox](https://github.com/version-fox/vfox)。
>
> 就像 nodejs 版本管理器 nvm, 这个管理器支持多种编程语言包管理器，如 java,js,python,php...

### [web-tracing](https://github.com/M-cheng-web/web-tracing)

> 为前端项目提供【 埋点、行为、性能、异常、请求、资源、路由、曝光、录屏 】监控手段

### [二维码生成](https://jsfiddle.net/lachlan/r8qWV/)

> 不是开箱即用， 可以在项目中集成使用。这是一个demo页面

### [QrIt](https://qrit.chesko.dev/)

![img](https://cdn.beekka.com/blogimg/asset/202408/bg2024081405.webp)

可以更改配色的二维码生成器。

### [widget-qrcode](https://github.com/mumuy/widget-qrcode)

> 一个网页二维码的 web component 组件，支持自定义二维码风格模板、前景、背景、logo等。

![](https://cdn.beekka.com/blogimg/asset/202405/bg2024052802.webp)

### [autocomplete](https://github.com/algolia/autocomplete)

> Algolia 公司推出的 JS 库，实现搜索关键字的自动补全。

![](https://cdn.beekka.com/blogimg/asset/202306/bg2023060904.webp)

### [JavaScript 语法问题](https://github.com/lydiahallie/javascript-questions/blob/master/zh-CN/README-zh_CN.md)

> 这个仓库收集了100多个 JavaScript 的语法选择题，可以试试你是否真的了解这门语言。

### [在线海报设计](https://github.com/palxiao/poster-design)

> ### 特点
>
> - 丝滑的页面操作体验，丰富的交互细节，基础功能完善
> - 采用服务端生成图片，确保多端出图统一性，支持各种 HTML5 特性
> - 简易 AI 抠图工具，上传图片一键去除背景
> - 技术栈：Vue3 、Vite5 、Pinia 、ElementPlus
> - 图片生成：Puppeteer、Express
>
> ### 支持功能
>
> - 导入 PSD 文件解析成模板、在线导出图片下载。
> - 元素拖拽、组合、缩放、层级调整、对齐等操作。
> - 图片素材插入、替换、裁剪，图片容器等功能。
> - SVG 素材颜色、透明度编辑，文字花字组合。
> - 支持图层管理、多画板管理、自适应画布。
> - 吸附对齐、辅助引导线、标尺功能。
> - 键盘快捷键、右键菜单快捷操作，复制删除等常用操作。
> - 风格二维码编辑，支持单色、渐变、自定义 logo 等。
> - 颜色调色板，原生级取色器颜色吸管（Chrome）。

![](https://camo.githubusercontent.com/07e814b51eb2cc138741b647d8e01e93ba00c000885f21b16e850f421be0db38/68747470733a2f2f78702e70616c78702e636e2f696d616765732f323032332d372d31362d313638393530303131323639342e676966)

### [flowchart](https://github.com/adrai/flowchart.js)

> flowchart.js 是在浏览器和终端中运行的流程图 DSL 和 SVG 渲染. 与 **mermaid.js** 有点类似

```
st=>start: Start:>http://www.google.com[blank]
e=>end:>http://www.google.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: linear or polynomial :>http://www.google.com
io=>inputoutput: catch something...
para=>parallel: 3 possibilities

st->op1->cond
cond(true@linear)->io->e
cond(false@polynomial)->sub1(right)
sub1(right)->para
para(path1@an1, top)->cond
para(path2@an2, right)->op1
para(path3@an3, bottom)->e
```

[![img](https://user-images.githubusercontent.com/1086194/137810516-0d7d7307-fc55-466f-b06d-a6ca9f6b8785.png)](https://user-images.githubusercontent.com/1086194/137810516-0d7d7307-fc55-466f-b06d-a6ca9f6b8785.png)

### [QuickJS](https://github.com/sebastianwessel/quickjs)

> 一个 npm 模块，使用 WebAssembly 建立了一个沙箱，让 JS 代码在沙箱中运行。

```js
import { quickJS } from '@sebastianwessel/quickjs'

// General setup like loading and init of the QuickJS wasm
// It is a ressource intensive job and should be done only once if possible 
const { createRuntime } = await quickJS()

// Create a runtime instance each time a js code should be executed
const { evalCode } = await createRuntime({
  allowFetch: true, // inject fetch and allow the code to fetch data
  allowFs: true, // mount a virtual file system and provide node:fs module
  env: {
    MY_ENV_VAR: 'env var value'
  },
})


const result = await evalCode(`
import { join } as path from 'path'

const fn = async ()=>{
  console.log(join('src','dist')) // logs "src/dist" on host system

  console.log(env.MY_ENV_VAR) // logs "env var value" on host system

  const url = new URL('https://example.com')

  const f = await fetch(url)

  return f.text()
}
  
export default await fn()
`)

console.log(result) // { ok: true, data: '<!doctype html>\n<html>\n[....]</html>\n' }
```

### [color4bg.js](https://github.com/winterx/color4bg.js)

![img](https://cdn.beekka.com/blogimg/asset/202407/bg2024071103.webp)

生成动态、抽象的背景图的工具。 动态效果的背景。可在网站生成js代码再使用	

### [MathLive](https://github.com/arnog/mathlive)

![img](https://cdn.beekka.com/blogimg/asset/202407/bg2024071409.webp)

一个 Web 组件，用于在网页输入数学公式。菜单是英文，没有汉化

### [web端公式输入](https://blog.csdn.net/qq_44278289/article/details/134926322)

这是通过页面嵌入、弹出层等方式实现的类似于富文本编辑器的效果。可集成到wangEditor 编辑器，~~但是有点问题 vue3继承时会出现弹出层位置不合适的现象~~ 【已解决】。此插件公式编辑是借用的 wangEditor 官方公式插件，将代码渲染成 官方插件元素 **formula**

![](https://img-blog.csdnimg.cn/direct/18c6b9a0022447b6aeaa9e5d45275448.gif)

[gitee代码地址](https://gitee.com/guo-xiaxue/mathematical-formula)

vue3 中使用与 vue2 不同，上边代码示例是vue2版本。

**kityformula.js**

将此菜单注册到

```js
import $ from "jquery";
// import { formulaIcon } from "../assets/icons/svg-icon.ts";

class MyKityFormulaMenu {
  constructor() {
    this.title = "编辑公式";
    // this.iconSvg = formulaIcon;
    this.tag = "button";
    this.showModal = true;
    this.modalWidth = 900;
    this.modalHeight = 400;
  }

  // 菜单是否需要激活（如选中加粗文本，“加粗”菜单会激活），用不到则返回 false
  isActive(editor) {
    return false;
  }

  // 获取菜单执行时的 value ，用不到则返回空 字符串或 false
  getValue(editor) {
    return "";
  }

  // 菜单是否需要禁用（如选中 H1 ，“引用”菜单被禁用），用不到则返回 false
  isDisabled(editor) {
    return false;
  }
  // 点击菜单时触发的函数
  exec(editor, value) {
    // Modal menu ，这个函数不用写，空着即可
  }

  // 弹出框 modal 的定位：1. 返回某一个 SlateNode； 2. 返回 null （根据当前选区自动定位）
  getModalPositionNode(editor) {
    return null; // modal 依据选区定位
  }

  // 定义 modal 内部的 DOM Element
  getModalContentElem(editor) {
    // panel 中需要用到的id
    const inputIFrameId = "kityformula_" + Math.ceil(Math.random() * 10);
    const btnOkId = "kityformula-btn" + Math.ceil(Math.random() * 10);

    const $content = $(`
    <div>
      <iframe id="${inputIFrameId}" class="iframe" height="400px" width="100%" frameborder="0" scrolling="no" src="/kityformula/index.html"></iframe>
    </div>`);
    const $button = $(
      `<button id="${btnOkId}" class="right" style='margin: 5px 0'>
        确认插入
      </button>`
    );
    $content.append($button);

    $button.on("click", () => {
      // 执行插入公式
      const node = document.getElementById(inputIFrameId);
      const kfe = node.contentWindow.kfe;

      kfe.execCommand("get.image.data", function (data) {
        // 获取base64
        // console.log(data.img);
      });

      let latex = kfe.execCommand("get.source");
      latex = latex.replace(/\s/g, ""); // 去掉空格

      const formulaNode = {
        type: "paragraph",
        children: [
          {
            type: "formula",
            value: latex,
            children: [
              {
                text: "",
              },
            ],
          },
        ],
      };
      editor.restoreSelection(); // 恢复选区
      editor.insertNode(formulaNode);
        editor.insertBreak();
        editor.move(1);
      editor.hidePanelOrModal();
    });

    return $content[0]; // 返回 DOM Element 类型

    // PS：也可以把 $content 缓存下来，这样不用每次重复创建、重复绑定事件，优化性能
  }
}
const menuConf = {
  key: "kityFormula", // menu key ，唯一。注册之后，需通过 toolbarKeys 配置到工具栏
  factory() {
    return new MyKityFormulaMenu();
  },
};

export default menuConf;

```

**/kityformula/index.html**

这个地址是 public 中放置的 公式编辑静态页面路由地址，vue2中可以直接使用静态资源路径。vue3版本中需要注册路由，否者路径将重定向到首页

```json
{
  path: '/kityformula',
  name: 'kityformula',
  component: () => import('@/pages/mathView/index.vue'),
 }
```

```vue
<template>
  <div class="">
    <iframe src="/kityformula/index.html" width="100%" height="100%" style="border: none" frameborder="0"></iframe>
  </div>
</template>

<script setup>
import {} from "vue"
</script>

<style lang="scss" scoped></style>

```

解决弹出层位置问题

设置 toolbarConfig.modalAppendToBody: true, 让弹出层dom在body中。让后监听弹出层事件，动态修改样式

```js
const handleCreated = (editor) => {
	window.editor = editor;
	editorRef.value = editor; // 记录 editor 实例，重要！
	editor.on('modalOrPanelShow', modalOrPanel => {
      if (modalOrPanel.type !== 'modal') return
      const { $elem } = modalOrPanel // modal element
      const width = $elem.width()
      const height = $elem.height()
      // set modal position z-index
      $elem.css({
        left: '50%',
        top: '50%',
        marginLeft: `-${width / 2}px`,
        marginTop: `-${height / 2}px`,
        zIndex: 1000
      })
    })
};
```



### [fetch event source](https://github.com/Azure/fetch-event-source#readme)

> 对比 浏览器 原生的 eventSource，更加强大好用

```shell
npm install @microsoft/fetch-event-source
```

支持更过参数

```js
const ctrl = new AbortController();
fetchEventSource('/api/sse', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        foo: 'bar'
    }),
    signal: ctrl.signal,
});
```

更好的错误处理

```js
class RetriableError extends Error { }
class FatalError extends Error { }

fetchEventSource('/api/sse', {
    async onopen(response) {
        if (response.ok && response.headers.get('content-type') === EventStreamContentType) {
            return; // everything's good
        } else if (response.status >= 400 && response.status < 500 && response.status !== 429) {
            // client-side errors are usually non-retriable:
            throw new FatalError();
        } else {
            throw new RetriableError();
        }
    },
    onmessage(msg) {
        // if the server emits an error message, throw an exception
        // so it gets handled by the onerror callback below:
        if (msg.event === 'FatalError') {
            throw new FatalError(msg.data);
        }
    },
    onclose() {
        // if the server closes the connection unexpectedly, retry:
        throw new RetriableError();
    },
    onerror(err) {
        if (err instanceof FatalError) {
            throw err; // rethrow to stop the operation
        } else {
            // do nothing to automatically retry. You can also
            // return a specific retry interval here.
        }
    }
});
```

**eventSource 请求地址 与普通http请求区别**

```json
'Content-Type': 'text/event-stream',  
'Cache-Control': 'no-cache',  
'Connection': 'keep-alive'  
```

- 响应的 `Content-Type` 必须是 `text/event-stream`。
- 响应数据必须遵循特定的格式，即以 `data:` 开头，后跟数据内容，最后以两个换行符 `\n\n` 结束。
- 客户端通过监听 EventSource 对象的事件（如 `message`、`open`、`error`）来处理接收到的数据。



### [在线协同文档](https://docs.etherpad.org/)

实时编辑，聊天。页面支持多语言。docker 部署。js、ts编写

[在线演示地址](https://etherpad.wikimedia.org/)



### [selenium](https://www.selenium.dev/zh-cn/documentation/webdriver/actions_api/mouse/)

​	Selenium 通过使用 *WebDriver* 支持市场上所有主流浏览器的自动化。 WebDriver 是一个 API 和协议，它定义了一个语言中立的接口，用于控制 web 浏览器的行为。 每个浏览器都有一个特定的 WebDriver 实现，称为驱动程序。 驱动程序是负责委派给浏览器的组件，并处理与 Selenium 和浏览器之间的通信。

Selenium脚本支持 **Java，Python，CSharp，Ruby，JavaScript，Kotlin**



### [tagui](http://www.tagui.com.cn/)

​	TagUI 是一款免费、开源、跨平台（Windows & Linux & macOS）的 RPA 工具，可帮助您轻松完成自动化桌面、Web、鼠标和键盘操作。下面请来体验一下

### [hash-wasm](https://github.com/Daninet/hash-wasm)

一个轻量级的哈希函数库，提供了二十几种常见的哈希算法，比如 MD5 和 SHA-1，以及更安全的 BLAKE3。

### [WebUI](https://github.com/webui-dev/webui)

![img](https://cdn.beekka.com/blogimg/asset/202401/bg2024010704.webp)

一个跨平台的桌面应用的打包程序，允许你直接将浏览器作为桌面应用的前端，比 Electron 要轻量化很多，同时又避免使用 WebView。

### [canvas-confetti](https://github.com/catdad/canvas-confetti)

![img](https://cdn.beekka.com/blogimg/asset/202404/bg2024042508.webp)

在网页上抛洒五彩纸屑的 JS 库。

### **fues.js** [fues.js Fuzzy Search 前端模糊搜索](https://github.com/krisk/Fuse)

```js

const Fuse = require('fuse.js');
const list = [
    {
    "title": "Old Man's War",
    "author": {
      "firstName": "John",
      "lastName": "Scalzi"
    }
  },
  ...
];

const fuseOptions = {
	// isCaseSensitive: false,
	// includeScore: false,
	// shouldSort: true,
	// includeMatches: false,
	// findAllMatches: false,
	// minMatchCharLength: 1,
	// location: 0,
	// threshold: 0.6,
	// distance: 100,
	// useExtendedSearch: false,
	// ignoreLocation: false,
	// ignoreFieldNorm: false,
	// fieldNormWeight: 1,
	keys: [
		"title",
		"author.firstName"
	]
};

const fuse = new Fuse(list, fuseOptions);

// Change the pattern
const searchPattern = "b"

return fuse.search(searchPattern)
```

[演示页面](https://www.fusejs.io/demo.html)

### [Text Search Engine](https://github.com/cjinhuo/text-search-engine/blob/master/docs/README_zh.md)

一个 JS 的模糊搜索库，具有中文拼音的模糊搜索等多种功能。

### [k-colors.js](https://github.com/ppzreboot/k-colors.js)

一个从图片提取主要颜色的 JS 库，底层是作者自己实现的 [K-means 算法库](https://github.com/ppzreboot/k-means-pp.js)。



### [json-schema-library](https://github.com/sagold/json-schema-library)

json格式校验

```js
import { Draft2019, JsonSchema, JsonError } from "json-schema-library";

const myJsonSchema: JsonSchema = {
    type: "object",
    additionalProperties: false
};

const jsonSchema = new Draft2019(myJsonSchema);
const errors: JsonError[] = jsonSchema.validate({ name: "my-data" });
```

### [Zerox OCR](https://github.com/getomni-ai/zerox)

![img](https://cdn.beekka.com/blogimg/asset/202409/bg2024092303.webp)

一个 JS/Python 库，使用 OpenAI 对 PDF 文件进行文字识别

### [MiKaPo](https://github.com/AmyangXYZ/MiKaPo)

![img](https://cdn.beekka.com/blogimg/asset/202409/bg2024092808.webp)

一个纯前端的二次元动作捕捉方案，在网页上，从视频/图片/摄像头提取动作、表情来控制动画角色

### [[faces.js](https://github.com/zengm-games/facesjs)

![img](https://cdn.beekka.com/blogimg/asset/202404/bg2024040701.webp)

一个生成随机卡通头像的 JS 库。

### markdown 语法编辑器

1. **[vditor](https://github.com/Vanessa219/vditor)**

	此插件不支持vue3语法，可以使用无封装版本

	![vditor](https://b3log.org/vditor/images/wysiwyg.gif)

2. [tui.editor](https://github.com/nhn/tui.editor)

	![tui editor](https://user-images.githubusercontent.com/37766175/121808381-251f5000-cc93-11eb-8c47-4f5a809de2b3.png)

### [ProseMirror](https://prosemirror.xheldon.com/)

ProseMirror是一个开源的JavaScript库，专注于构建强大而可定制的富文本编辑器。 **tui.editor** 就是基于此开发的, WangEditor的某些版本（如v5）是基于**slate.js**开发的

### [Quill](https://github.com/slab/quill)

一款很强的富文本编辑器，提供了很多api 拓展操作，可惜对表格支持不好

### [slate](https://github.com/ianstormtaylor/slate)

markdown 编辑器， [`slate-react`](https://github.com/ianstormtaylor/slate/blob/main/packages/slate-react) 支持 react 拓展

![img](https://github.com/ianstormtaylor/slate/raw/main/docs/images/preview.png)

### [CheerpJ](https://labs.leaningtech.com/blog/cheerpj-3.0)

![img](https://cdn.beekka.com/blogimg/asset/202402/bg2024021302.webp)

### [PhysicsJS](https://gitcode.com/gh_mirrors/ph/PhysicsJS)

浏览器里面的 JVM 编译器，让 Java 程序可以在浏览器运行。

 一、**项目概述**

- **项目地址**：https://gitcode.com/gh_mirrors/ph/PhysicsJS
- **主要编程语言**：JavaScript
- **特点**：模块化设计、可扩展性、易于使用

**二、功能与应用**

- **核心功能**：PhysicsJS包含了碰撞检测、刚体动力学、约束模拟等多种功能，能够满足开发者在物理模拟方面的需求。
- **应用场景**：
	- **游戏开发**：PhysicsJS可用于实现真实的物体碰撞、重力感应等效果，提升游戏的真实感和互动性。
	- **交互式设计**：在网页或应用中，通过PhysicsJS可以创建具有物理效果的交互元素，增强用户体验。
	- **模拟应用**：在教育软件中，PhysicsJS可用于模拟物理实验，帮助学生更好地理解和掌握知识。

[xterm.js](https://xtermjs.org/)

Build terminals in the browser. 

xterm.js + node-pty + ws 实现 web-terminal

[用 xterm.js 实现一个简易的 web-terminal ！](https://juejin.cn/post/6918911964009725959)



### [barba.js](https://barba.js.org/)

![img](https://cdn.beekka.com/blogimg/asset/202403/bg2024033002.webp)

一个 JS 库，让网站**页面的切换**产生**平滑的动画 ** [效果](https://codepen.io/wyf195075595/pen/ogNgZPJ)。	

### [Superdiff](https://github.com/DoneDeal0/superdiff)

![img](https://cdn.beekka.com/blogimg/asset/202410/bg2024101201.webp)

一个 JS 库，对两个对象或数组进行 diff 操作，返回差异的部分。