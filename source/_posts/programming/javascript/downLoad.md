---
title: 文件下载方式
date: 2022-06-17 08:23:10
tags: js
categories: js
---
# 文件下载方式

1. get方式下载

   > window.location.href = URL
   >
   > window.open(URL)

2. post方式

- form表单

  ```js
  var formObj = document.createElement('form');
            formObj.action = downloadURL;//设置下载路径
            formObj.method = method;//设置请求方式
  		 //formObj.enctype = 'multipart/form-data' 设置Content-Type
            formObj.style.display = 'none';//样式隐藏
            var addFormItem = (formObj,formItemName,formItemValue) => {
              let formItem = document.createElement('input');
              formItem.name = formItemName; // 传参的字段名
              formItem.value = formItemValue; // 传参的值
              formObj.appendChild(formItem)
            }
            addFormItem(formObj,'token',localStorage.getItem('accessToken'))//设置token参数
  		 //设置其他参数
            for(let data_key in data){
              addFormItem(formObj,data_key,data[data_key])
            }
      
            document.body.appendChild(formObj);//加入document
            formObj.submit();//触发submit事件
            document.body.removeChild(formObj);//移除dom
  ```

  

<!--more-->


- XMLHttpRequest2

  > XMLHttpRequest
  >
  > [相关链接]: https://www.html5rocks.com/zh/tutorials/file/xhr2/#toc-cors
  >
  > 

  

  1. ```js
     //下载路径，传参
     downLoadFile（'http://xxxxx',{names:'xxx,yyy,xxx',filename:'xxx',paths:'xxx'}）
     function downLoadFile(downloadURL,data){
         //创建formData对象
         var formObj = new FormData();
         //添加参数函数
         var addFormItem = (formObj,formItemName,formItemValue) => {
             formObj.append(formItemName, formItemValue);
         }
         //添加参数
         for(let data_key in data){
             addFormItem(formObj,data_key,data[data_key])
         }
         console.log(formObj);
         //创建XMLHttpRequest对象
         var xhr = null
         if(window.XMLHttpRequest){
             // code for IE7+, Firefox, Chrome, Opera, Safari
             xhr=new XMLHttpRequest();
         }else if(window.ActiveObject){
             // code for IE6, IE5
             xhr=new ActiveXobject('Microsoft.XMLHTTP');
         }
         //指定响应格式，一般与 response 属性一起使用
         xhr.responseType = 'blob';
         if(method == 'GET'){     
             //不考虑get
             xhr.open(method,`${downloadURL}?token=${localStorage.getItem('accessToken')}&${params}`,true);
             xhr.send(null);
         }else if(method == 'POST'){
             //规定请求的类型、URL 以及是否异步处理请求。
             //open(method,url,async)
             //method：请求的类型；GET 或 POST
             //•url：文件在服务器上的位置
             //•async：true（异步）或 false（同步）
             xhr.open(method,`${downloadURL}`,true);
             //浏览器的原生 <form> 表单
             // xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded");
             //json格式
             // xhr.setRequestHeader("Content-type","application/json;charset=UTF-8");
             //用表单上传文件时
             // xhr.setRequestHeader('Content-Type','multipart/form-data');
             //设置token
             xhr.setRequestHeader("token",localStorage.getItem('accessToken'));
             //将请求发送到服务器。可接受以下任何类型：DOMString、Document、FormData、Blob、File、ArrayBuffer
             xhr.send(formObj);
         }
     	//请求后的响应
         xhr.onload = function () {
             if (this.status === 200 || this.status === 304) {
                 let name = decodeURIComponent(data.filename)
                 let hz = decodeURIComponent(data.names.split('.')[1])
                 let a = document.createElement('a');
                 //FileReader仅用于以安全的方式从用户（远程）系统读取文件内容 它不能用于从文件系统中按路径名简单地读取文件
                 //var fileReader = new FileReader();
                 a.style.display = 'none';
                 console.log('返回值',this.response,name,hz);
                 let blob = null;
                 if(/\./g.test(data.names)){
                     console.log('多文件');
                     // blob = new Blob([this.response], {type: 'application/zip'});
                     //创建一个新的 URL 对象
                     a.href = window.URL.createObjectURL(this.response);
                     a.download = `${name}.zip` || Date.now();
     
                 }else{
                     console.log('单文件');
                     //
                     
                     //fileReader.onload = function(e) {
                     //   a.href = e.target.result;
                     //}
                     a.href = window.URL.createObjectURL(this.response);
                     a.download = `${name}.${hz}` || Date.now();
                 }
     
                 document.body.appendChild(a);
                 a.click();
                 document.body.removeChild(a);
                 //释放内存
                 window.URL.revokeObjectURL(this.response);
             }else{
                 callBackObject.error && callBackObject.error()
             }
         };
     
         }
     }
     ```

  

  

  # 前端下载文件的6种方法的对比

  # 前言

  在前端站点上下载文件，这是一个极其普遍的需求，很早前就已经有各种解决方法了，为什么还写这么老的文章，只是最近在带一个新人，他似乎很多都一知半解，也遇到了我们必经问题之“不能下载txt、png等文件”的典型问题，我就给他总结下下载的几个方式。**顺便分享出来，也许，真有人需要。**

  # form表单提交

  这是以前常使用的传统方式，毕竟那个年代，没那么多好用的新特性呀。

  道理也很简单，为一个下载按钮添加click事件，点击时动态生成一个表单，利用表单提交的功能来实现文件的下载（实际上表单的提交就是发送一个请求）

  来看下如何生成一个表单，生成怎么样的一个表单：

  ```js
  /**
   * 下载文件
   * @param {String} path - 请求的地址
   * @param {String} fileName - 文件名
   */
  function downloadFile (downloadUrl, fileName) {
      // 创建表单
  	const formObj = document.createElement('form');
      formObj.action = downloadUrl;
      formObj.method = 'get';
      formObj.style.display = 'none';
      // 创建input，主要是起传参作用
      const formItem = document.createElement('input');
      formItem.value = fileName; // 传参的值
      formItem.name = 'fileName'; // 传参的字段名
      // 插入到网页中
      formObj.appendChild(formItem);
      document.body.appendChild(formObj);
      formObj.submit(); // 发送请求
      document.body.removeChild(formObj); // 发送完清除掉
  }
  ```

  #### 优点

  - 传统方式，兼容性好，不会出现URL长度限制问题

  #### 缺点

  - 无法知道下载的进度
  - 无法直接下载浏览器可直接预览的文件类型（如txt/png等）

  # open或location.href

  最简单最直接的方式，实际上跟a标签访问下载链接一样

  ```js
  window.open('downloadFile.zip');
  
  location.href = 'downloadFile.zip';
  ```

  当然地址也可以是接口api的地址，而不单纯是个链接地址。

  #### 优点

  - 简单方便直接

  #### 缺点

  - 会出现URL长度限制问题
  - 需要注意url编码问题
  - 浏览器可直接浏览的文件类型是不提供下载的，如txt、png、jpg、gif等
  - 不能添加header，也就不能进行鉴权
  - 无法知道下载的进度

  # a标签的download

  我们知道，a标签可以访问下载文件的地址，浏览器帮助进行下载。但是对于浏览器支持直接浏览的txt、png、jpg、gif等文件，是不提供直接下载（可右击从菜单里另存为）的。

  为了解决这个直接浏览不下载的问题，可以利用download属性。

  download属性是HTML5新增的属性，兼容性可以了解下 can i use download

  总体兼容性算是很好了，基本可以区分为IE和其他浏览。但是需要注意一些信息：

  - Edge 13在尝试下载data url链接时会崩溃。
  - Chrome 65及以上版本只支持同源下载链接。
  - Firefox只支持同源下载链接。

  基于上面描述，如果你尝试下载跨域链接，那么其实download的效果就会没了，跟不设置download表现一致。即浏览器能预览的还是会预览，而不是下载。

  简单用法：

  ```
  <a href="example.jpg" download>点击下载</a>
  ```

  可以带上属性值，指定下载的文件名，即重命名下载文件。不设置的话默认是文件原本名。

  ```
  <a href="example.jpg" download="test">点击下载</a>
  ```

  如上，会下载了一个名叫test的图片

  **监测是否支持download**

  要知道浏览器是否支持download属性，简单的一句代码即可区分

  ```
  const isSupport = 'download' in document.createElement('a');
  ```

  对于在跨域下不能下载可浏览的文件，其实可以跟后端协商好，在后端层做多一层转发，最终返回给前端的文件链接跟下载页同域就好了。

  #### 优点

  - 能解决不能直接下载浏览器可浏览的文件

  #### 缺点

  - 得已知下载文件地址
  - 不能下载跨域下的浏览器可浏览的文件
  - 有兼容性问题，特别是IE
  - 不能进行鉴权

  # 利用Blob对象

  该方法较上面的直接使用a标签download这种方法的优势在于，它除了能利用已知文件地址路径进行下载外，还能通过发送ajax请求api获取文件流进行下载。毕竟有些时候，后端不会直接提供一个下载地址给你直接访问，而是要调取api。

  利用Blob对象可以将文件流转化成Blob二进制对象。该对象兼容性良好，需要注意的是

  - IE10以下不支持。
  - 在Safari浏览器上不能访问Blob Url或Object URL，如下文中通过URL.createObjectURL生成的链接

  第二点是致命的，这就导致这里以下的方案并不合适用于Safari浏览器。希望日后Safari能修复该问题（当前我所测试版本为13.0.4 ）。

  进行下载的思路很简单：发请求获取二进制数据，转化为Blob对象，利用URL.createObjectUrl生成url地址，赋值在a标签的href属性上，结合download进行下载。

  ```js
  /**
   * 下载文件
   * @param {String} path - 下载地址/下载请求地址。
   * @param {String} name - 下载文件的名字/重命名（考虑到兼容性问题，最好加上后缀名）
   */
  downloadFile (path, name) {
      const xhr = new XMLHttpRequest();
      xhr.open('get', path);
      xhr.responseType = 'blob';
      xhr.send();
      xhr.onload = function () {
          if (this.status === 200 || this.status === 304) {
              // const blob = new Blob([this.response], { type: xhr.getResponseHeader('Content-Type') });
              // const url = URL.createObjectURL(blob);
              const url = URL.createObjectURL(this.response);
              const a = document.createElement('a');
              a.style.display = 'none';
              a.href = url;
              a.download = name;
              document.body.appendChild(a);
              a.click();
              document.body.removeChild(a);
              URL.revokeObjectURL(url);
          }
      };
  }
  ```

  该方法不能缺少a标签的download属性的设置。因为发请求时已设置返回数据类型为Blob类型（xhr.responseType = 'blob'），所以target.response就是一个Blob对象，打印出来会看到两个属性size和type。虽然type属性已指定了文件的类型，但是为了稳妥起见，还是在download属性值里指定后缀名，如Firefox不指定下载下来的文件就会不识别类型。

  大家可能会注意到，上述代码有两处注释，其实除了上述的写法外，还有另一个写法，改动一丢丢。如果发送请求时不设置xhr.responseType = 'blob'，默认ajax请求会返回DOMString类型的数据，即字符串。这时就需要两处注释的代码了，对返回的文本转化为Blob对象，然后创建blob url，此时需要注释掉原本的const url = URL.createObjectURL(target.response)。

  #### 优点

  - 能解决不能直接下载浏览器可浏览的文件
  - 可设置header，也就可添加鉴权信息

  #### 缺点

  - 兼容性问题，IE10以下不可用；Safari浏览器不可用

  # 利用base64

  这里的用法跟上面用Blob大同小异，基本上思路是一样的，唯一不同的是，上面是利用Blob对象生成Blob URL，而这里则是生成Data URL，所谓Data URL，就是base64编码后的url形式。

  ```js
  /**
   * 下载文件
   * @param {String} path - 下载地址/下载请求地址。
   * @param {String} name - 下载文件的名字（考虑到兼容性问题，最好加上后缀名）
   */
  downloadFile (path, name) {
      const xhr = new XMLHttpRequest();
      xhr.open('get', path);
      xhr.responseType = 'blob';
      xhr.send();
      xhr.onload = function () {
          if (this.status === 200 || this.status === 304) {
              const fileReader = new FileReader();
              fileReader.readAsDataURL(this.response);
              fileReader.onload = function () {
                  const a = document.createElement('a');
                  a.style.display = 'none';
                  a.href = this.result;
                  a.download = name;
                  document.body.appendChild(a);
                  a.click();
                  document.body.removeChild(a);
              };
          }
      };
  }
  ```

  这里额外提供个方法，该方法作用是，当你知道文件的全名（含后缀名），想要重命名，但是得后缀名一样，来获取后缀名。

  ```js
  function findType (name) {
      const index = name.lastIndexOf('.');
      return name.substring(index + 1);
  }
  ```

  #### 优点

  - 较用Blob URL，这个可在Safari上使用 **（这个结论待验证）**
  - 能解决不能直接下载浏览器可浏览的文件
  - 可设置header，也就可添加鉴权信息

  #### 缺点

  - 兼容性问题，IE10以下不可用

  # 其他

  要能够进行下载文件，后端接口要返回文件流。 简单记录下我成功的例子里，后端返回的header情况：

  ```js
  Access-Control-Allow-Credentials: true
  Access-Control-Allow-Origin: *
  Connection: keep-alive
  Content-Disposition: attachment; filename=_____20200103161450.png; filename*=UTF-8''%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20200103161450.png
  Content-Length: 10673
  Content-Type: application/octet-stream
  ```
  
  # 使用html2canvas和jspdf插件实现
  
  该方式是通过`html2canvas`将HTML页面转换成图片，然后再通过`jspdf`将图片的base64生成为pdf文件。实现步骤如下：
  
  ## 1,下载插件模块
  
  
  
  ```undefined
  npm install html2canvas jspdf --save
  ```
  
  ## 2,定义功能实现方法
  
  在项目工具方法存放文件夹utils中创建`htmlToPdf.js`文件，代码如下：
  
  
  
  ```javascript
  // 导出页面为PDF格式
  import html2Canvas from 'html2canvas'
  import JsPDF from 'jspdf'
  export default{
    install (Vue, options) {
      Vue.prototype.getPdf = function () {
        var title = this.htmlTitle
        html2Canvas(document.querySelector('#pdfDom'), {
          allowTaint: true
        }).then(function (canvas) {
          let contentWidth = canvas.width
          let contentHeight = canvas.height
          let pageHeight = contentWidth / 592.28 * 841.89
          let leftHeight = contentHeight
          let position = 0
          let imgWidth = 595.28
          let imgHeight = 592.28 / contentWidth * contentHeight
          let pageData = canvas.toDataURL('image/jpeg', 1.0)
          let PDF = new JsPDF('', 'pt', 'a4')
          if (leftHeight < pageHeight) {
            PDF.addImage(pageData, 'JPEG', 0, 0, imgWidth, imgHeight)
          } else {
            while (leftHeight > 0) {
              PDF.addImage(pageData, 'JPEG', 0, position, imgWidth, imgHeight)
              leftHeight -= pageHeight
              position -= 841.89
              if (leftHeight > 0) {
                PDF.addPage()
              }
            }
          }
          PDF.save(title + '.pdf')
        }
        )
      }
    }
  }
  ```
  
  ## 3, 全局引入实现方法
  
  在项目主文件`main.js`中引入定义好的实现方法，并注册。
  
  
  
  ```jsx
  import htmlToPdf from '@/components/utils/htmlToPdf'
  // 使用Vue.use()方法就会调用工具方法中的install方法
  Vue.use(htmlToPdf)
  ```
  
  ## 4, 在相关要导出的页面中，点击时调用绑定在Vue原型上的getPdf方法，传入id即可
  
  
  
  ```jsx
  //html
   <div id="pdfDom">
     <!-- 要下载的HTML页面,页面是由后台返回 -->
    <div v-html="pageData"></div>
  </div>
  <el-button type="primary" size="small" @click="getPdf('#pdfDom')">点击下载</el-button>
  
  //js
  export default {
    data () {
        return {
        htmlTitle: '页面导出PDF文件名'
        }
    }
   }
  ```
  
  # 方式二：读取后台返回文件流，利用HTML5中a标签的download属性实现下载
  
  > 该方法需要先请求后台，后台会返回一个文件流，然后解析文件流，再通过HTML5中`<a>`标签的`download`属性实现下载功能。步骤如下：
  
  ## 1,发送请求，获取到后台返回的文件流及文件信息
  
  - 前端发送请求获取下载文件信息：
  
  
  
  ```javascript
  // 引入下载方法
  import {download} from 'utils'
  export default{
    methods: {
      async downloadFile () {
        let res = await axios.get(
          url: 'xxxx/xxxx',
          method: 'GET',
          // 设置返回数据类型，这里一定要设置，否则下载下来的pdf会是空白,也可以是`arraybuffer`
          responseType: 'blob',
          params: {
            id: 'xxxxxx'
          }
        )
        // 获取在response headers中返回的下载文件类型
        let type = JSON.parse(res.headers)['content-type']
        
        /*获取在response headers中返回的下载文件名
          因为返回文件名是通过encodeURIComponent()函数进行了编码，因此需要通过decodeURIComponent()函数解码
        */
        let fileName = decodeURIComponent(JSON.parse(res.headers)['file-name'])
        // 调用封装好的下载函数
        download(res, type, fileName)
      }
    }
  }
  ```
  
  - 后台返回的文件流格式：
  
  	![img](https:////upload-images.jianshu.io/upload_images/4921980-a44fa0f4b6c88c3f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1035/format/webp)
  
  	文件流.png
  
  ## 2, 封装下载方法 - 解析文件流，创建a标签并设置下载相关属性。
  
  
  
  ```javascript
  // utils.js
  export const download = (res, type, filename) => {
    // 创建blob对象，解析流数据
    const blob = new Blob([res], {
      // 如何后端没返回下载文件类型，则需要手动设置：type: 'application/pdf;chartset=UTF-8' 表示下载文档为pdf，如果是word则设置为msword，excel为excel
      type: type
    })
    const a = document.createElement('a')
    // 兼容webkix浏览器，处理webkit浏览器中href自动添加blob前缀，默认在浏览器打开而不是下载
    const URL = window.URL || window.webkitURL
    // 根据解析后的blob对象创建URL 对象
    const herf = URL.createObjectURL(blob)
    // 下载链接
    a.href = herf
    // 下载文件名,如果后端没有返回，可以自己写a.download = '文件.pdf'
    a.download = filename
    document.body.appendChild(a)
    a.click()
    document.body.removeChild(a)
    // 在内存中移除URL 对象
    window.URL.revokeObjectURL(herf)
  }
  ```
  
  ## 3，点击下载按钮，调用下载方法
  
  
  
  ```xml
   <div id="pdfDom">
      <!-- 要下载的页面 -->
      <div v-html="pageData"></div>
  </div>
  <el-button type="primary" size="small" @click="downloadFile">点击下载</el-button>
  ```
  
  
  
  作者：桃花谷主V
  链接：https://www.jianshu.com/p/56680ce1cc97
  来源：简书
  著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
  
  ## 下载时获取文件名

```js
Content-Disposition就是当用户想把请求所得的内容存为一个文件的时候提供一个默认的文件名。

# 前端获取
let content = xhr.getResponseHeader('Content-Disposition');
let oldName = content.match(/filename=(.*)/) ? content.match(/filename=(.*)/)[1] : '';


// 前端可能出现死活获取不到 Content-Disposition， 只能获取 content-type
允许客户端使用服务端自定义的header值，即使用该字段Access-Control-Expose-Headers控制权限

 #详情 https://www.h5w3.com/168504.html

/**
     * 在业务处理器处理请求之前被调用 如果返回false
     * 从当前的拦截器往回执行所有拦截器的afterCompletion(),
     * 再退出拦截器链, 如果返回true 执行下一个拦截器,
     * 直到所有的拦截器都执行完毕 再执行被拦截的Controller
     * 然后进入拦截器链,
     * 从最后一个拦截器往回执行所有的postHandle()
     * 接着再从最后一个拦截器往回执行所有的afterCompletion()
     *
     * @param request
     * @param response
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
//        允许跨域
        response.setHeader(HEADER_ACCESS_CONTROL_ALLOW_ORIGIN.key(), request.getHeader(HEADER_ACCESS_CONTROL_ALLOW_ORIGIN.value()));
        response.setHeader(HEADER_ACCESS_CONTROL_ALLOW_METHODS.key(), HEADER_ACCESS_CONTROL_ALLOW_METHODS.value());
        response.setHeader(HEADER_ACCESS_CONTROL_MAX_AGE.key(), HEADER_ACCESS_CONTROL_MAX_AGE.value());
        response.setHeader(HEADER_ACCESS_CONTROL_ALLOW_HEADERS.key(), HEADER_ACCESS_CONTROL_ALLOW_HEADERS.value());
//        允许session
        response.setHeader(HEADER_ACCESS_CONTROL_ALLOW_CREDENTIALS.key(), HEADER_ACCESS_CONTROL_ALLOW_CREDENTIALS.value());
//      允许客户端在header中使用哪些数值
        response.setHeader(ACCESS_CONTROL_EXPOSE_HEADERS.key(), ACCESS_CONTROL_EXPOSE_HEADERS.value());
        return true;
    }
```



### 使用 axios 封装得下载



```js
/**
 * @description 需要后端支持
 *    服务器的 headers 白名单，让客户端可以访问
 *    在跨域的时，客户端能够访问到一些默认响应的 headers: (Cache-Control,Content-Language,Content-Type,Expires,Last-Modified,Pragma)
 *    Response['Access-Control-Expose-Headers'] = "Content-Disposition,Cache-Control,...";
 *    控制用户请求所得的内容存为一个文件的时候提供一个默认的文件名
 *    Response.addHeader("Content-Disposition", "attachment;filename=" + filename);
 * 
 *    注意：后端若配合，不需要定义 文件名和及文件名后缀，反之，必须定义 文件名和及文件名后缀
 * @param {object} configs 
 * @param {string} fileName 下载的文件名 
 * @returns {Promise}
 */
export function downloadFile(configs = {}, fileName) {
  return new Promise((resolve, reject) => {
    service({
      ...configs,
      responseType: "blob",
    }).then(async response => {
      // 为防止 后台返回错误的 code
      const textResponse = await blobToText(response.data)
      if(textResponse.isJson) {
        // 只要能以文本的形式读取并成功地转为 json 对象，就认为 下载失败（依后端返回的数据或者 code 而定）
        resolve({ ...textResponse, connected: false })
        return
      }

      let fileNameResult = fileName;
      if(response.headers.hasOwnProperty("content-disposition")) {
        let ContentDisposition = response.headers["content-disposition"];
        let defineFileName = ContentDisposition.match(/filename=(.*)/i) ? ContentDisposition.match(/filename=(.*)/i)[1] : '';
        // 需要跟后端沟通,采用解码方式 [decodeURIComponent、decodeURI]
        defineFileName = decodeURIComponent(defineFileName);
        let subFixName = defineFileName.slice(defineFileName.lastIndexOf('.') + 1);
        fileNameResult =  fileName ? `${fileName}.${subFixName}` : defineFileName;
      }
      
      let fileReader = new FileReader();
      fileReader.readAsDataURL(response.data);
      fileReader.onload = function() {
        let download_a = document.createElement('a');
        download_a.style.display = 'none';
        download_a.href = this.result;
        download_a.download = fileNameResult;
        document.body.appendChild(download_a);
        download_a.click();
        document.body.removeChild(download_a);
        resolve({connected: true, success: true, data: "下载成功！"})
      }
      fileReader.onerror = function() {
        resolve({connected: true, success: false, data: "文件读取失败！"})
      }
    }).catch(err => {
      resolve({connected: false, success: false, data: "下载失败！"})
    })
  })
}
```

