---
title: HTML
date: 2022-06-17 18:53:10
tags: HTML
categories: htmlcss
---
## html5

### 属性

- contentEditable 属性

	> 指定该属性的元素可以被编辑

```html

<p contenteditable="true" class="editable"></p>

# 监听可编辑元素变化，通过比较前后两者数据

$('.editable').on('focusin', function() {
    console.log('focus in')
});

$('.editable').on('focusout', function() {
    console.log('focus out')
});

	
```

- designMode 属性

	> 该属性可以指定页面是否可编辑，只能通过js设置该属性
	> 	document.body.designMode = 'off/on'
	> 	IE8出于安全考虑不支持
	> 	IE9允许
	> 	chrome3和Safari，允许内嵌的iframe可编辑
	> 	Firefox和Opera 允许

	

- spellcheck 属性

> 允许 input,textarea,可编辑元素 进行拼写 检验

```
<p contenteditable="true"  spellcheck="true"  style="width:400px;height:150px;"></p>
```

- tabindex 属性

> 通过设置 tabindex的数值，控制每次点击tab键时，页面焦点的切换顺序。通常用于表单





<!--more-->

### 标签

```html
<article></article>//独立内容
<section></section>//分块内容
<aside></aside>//附属信息
<nav></nav>//导航
<header></header>//头部
<hgroup></hgroup>//标题集合
<footer></footer>//尾部
<address></address>//地址  datetime,pubdate属性
<time></time>//时间
<mark max="" min="" low="" high="" optimum="">mark标签</mark> 高亮的引用文字
<meter></meter>//也是进度
<progress value="0.5"></progress>进度栏标签 value值代表进度，0-1
<dialog open>这是打开的对话窗口</dialog>//弹窗， 拥有open属性时自动显示
<small></small>

//figure 表示网页上一块独立的内容。移除后对页面无影响
<figure> </figure>
<figcaption></figcaption>//figure中的标题

eg:
<figure> 
    <img src="" alt="">
    <figcaption>这是一张图片</figcaption>
</figure>
```

### 表单

```html

<form >
  <input type="text" class="test1" pattern="^\d{4}$" required placeholder="请输入4个数字">
  <input type="number" pattern="^\d{4}$" min="0" max="100"  step="5" required placeholder="请输入100内5的倍数">
  <input type="submit" @click="handleSub" value="提交">
</form>

<script>
	const handleSub = (e)=>{
      var num = document.querySelector('.test1');
      let val = num.checkValidity();
      num.setCustomValidity('数字不对啊');
      console.log(val);
    }
</script>

```

> 自定义错误信息
> 	使用js 调用个 input元素的 setCustomValidity 方法来定义
>
> 自动校验
> 点击submit自动给校验
>
> 手动校验
> checkValidity()：判断值是否合法
> 	每个表单元素都有这个方法，返回 Boolean
>
> 取消校验
> 	input元素 formnovalidate 属性 取消当前元素验证
> 	如果对 submit元素使用 formnovalidate 属性，整个表单验证都会失效
> 	

### ol优化

> start 指定开始序号
> reversed 序号反转

```html

<ol start="4" reversed>
    <li>4</li>
    <li>5</li>
    <li>6</li>
</ol>
```

### css计数器

```html
<ul>
  <li>这是啥</li>
  <li>嘻嘻嘻</li>
  <li>哈哈哈</li>
  <li>嘿嘿嘿</li>
  <li>啦啦啦</li>
</ul>

<style>
      ul{
        /* 插入计数器，第一个计数器从0开始累加，第二个计数器从1开始累加 */
        counter-reset:section 0 subsec 1; 
      }
      ul li::before{
        content: counter(subsec);
        /*  对计数器进行累加 */
        counter-increment: subsec;
      }
      ul li::after{
        content: counter(section);
        counter-increment: section; 
      }
</style>

z这玩意儿好像只能在伪类的 content 中使用



```

### 字体相关

```css
text-shadow:文字阴影
	参数 ： length length length color
		   横向距  纵向距 阴影模糊半径 阴影颜色
	可指定多个阴影
	text-shadow：x x x c
                 x x x c
				...
				;
word-break :文本自动换行
	break-all  normal

word-warp  :长单词，url地址自动换行
	break-word

Web Font 与 @font-face:使用服务端字体
	eg:
    @font-face{
        font-family:WebFont; //声明使用服务端字体
        src:url('font/Fontin_Sans_R_45b.otf') format('opentype');//路径 字体文件格式
        font-weight:normal;
        font-variant:'small-caps';//大小写设置
        font-stretch:'wider';//是否伸缩变形
        font-size:12;
    }
	opentype 格式对应文件拓展名 .otf
	truetype 格式对应文件拓展名 .ttf
	.eot 文件不需要 format属性

font-size-adjust:修改字体种类保持字体尺寸不变
	font-size-adjust:0.46; //其中数值为字体的 aspect 值. 计算方式：x-height[小写x的高度]/字体尺寸[即fontSize]
```

### 背景边框相关

```css
background-clip  指定那个背景显示范围
background-orign 指定绘制背景图像时的起点
background-size  指定背景图像尺寸

background-break 指定内联元素的背景图像进行平铺时的循环方式
	bounding-box 整个元素内部平铺
	each-box     每一行
	continuous   下一行
貌似被background-repeat取代了



```

### 轮廓

```css
outline: color style width;
out-offset: 让 outline 外扩内缩
```

### resize属性

> 允许用户修改元素尺寸
> none 不能修改
> both 可以修改宽高
> horizontal 可修改宽
> vertical 可修改高

### 取消元素样式指定

```html
initial 属性值

<style>
    p{
        color:red;
    }
    p2{
        color:initial;//取消样式指定
    }
</style>
<body>
    <p>111</p>
    <p class="p2">111</p>
    <p>111</p>
</body>
```

### 自定义web组件

> 主要包括三种技术模块：自定义元素、影子DOM、HTML模板。
>
> 生命周期回调函数处理一些业务：
>
> - connectedCallback 自定义元素被插入文档DOM时，调用。
> - disconnectedCallback 自定义元素从文档删除时，调用。
> - adoptedCallback 自定义元素被移动到新的文档时，调用。
> - attributeChangedCallback 自定义元素增加、删除、修改属性时 ，调用。
> 	**触发此回调函数，必须监听变化的属性static get observedAttributes() ,返回监听的属性。**



```html
<style>
	search-box:not(:defined) {
            display: flex;
            width: 200px;
            height: 40px;
            background: white;
            align-items: center;
            border-radius: 5px;
            border: 1px solid #333;
            box-sizing: border-box;
            cursor: pointer;
            user-select: none;
        }
        search-box input{
            border: none;
            outline: none;
            width: 70%;
        }
        search-box span:last-child {
            font-size: 2em;
        }
        search-box .icon {
            width: 15%;
            text-align: center;
        }
        inline-cicle {
            display: inline-block;
            width: 1em;
            height: 1rem;
            border-radius: 50%;
            background: white;
            border: 1px solid black;
            vertical-align: bottom;
        }
</style>

<search-box>
    <span slot="left" class="icon">🔍</span>
    <input type="text" placeholder="搜索...">
    <span slot="right" class="icon">×</span>
</search-box>
<p>
        说到中国历史上有关奸臣的典故，很多人会想到<inline-cicle ></inline-cicle>“指鹿为马”的赵高，“路人皆知”的司马昭以及“冲冠一怒”的吴三桂等等。
        其实在他们之中还有几个典故比较有意思，那就是<inline-cicle ></inline-cicle>“莫须有”、<inline-cicle ></inline-cicle>“意有之”和<inline-cicle ></inline-cicle>“水太凉”，有的朋友可能不说就已经知道这三人分别指的是谁了，
        他们分别指的是<inline-cicle ></inline-cicle>害死岳飞的秦桧、<inline-cicle ></inline-cicle>害死于谦的徐有贞、<inline-cicle ></inline-cicle>不清不明的钱谦益（此人身为明朝的官员不为明朝办正事，投降清朝又不为清朝办正事，所以号称“不明不清”）。
        下面我们来看看这三个典故的由来：
</p>

```

### 影子dom

> 要把一个自定义元素转换为真正的Web组件，还需要使用一个强大的封装机制：影子DOM（shadow DOM）。
>     在影子根节点之下定义的样式对该子树是私有的，永远不会影响外部的阳光DOM元素（影子根节点可以为其宿主元素定义默认样式，但这些样式可以被阳光DOM样式覆盖）。
>     影子DOM中发生的某些事件（如“load”）会被封闭在影子DOM中。另外一些事件，像focus、mouse和键盘事件则会向上冒泡、穿透影子DOM。当一个发源于影子DOM内的事件跨过了边界开始向阳光DOM传播时，其target属性会变成影子宿主元素，就好像事件直接起源于该元素一样。
>     要把一个阳光DOM元素转换为影子宿主，只要调用其attachShadow()方法，传入{mode:"open"}这个唯一的参数即可。这个方法返回一个影子根节点对象，同时也将该对象设置为这个宿主的shadowRoot属性的值。这个影子根节点对象是一个DocumentFragment，可以使用DOM方法为它添加内容，也可以直接将其innerHTML属性设置为一个HTML字符串

```js

    
class SearchBox extends HTMLElement {
            constructor() {
                super();// 调用超类的构造器
                // 创建一个影子 DOM 树并将其附加到这个元素
                // 设置为 this.shadowRoot 的值
                this.attachShadow({mode:'open'});
                
                // 克隆模板，定义自定义组件的后代及样式
                // 然后吧内容追加到影子根节点
                this.shadowRoot.append(SearchBox.template.content.cloneNode(true));
                
                // 取得对影子DOM中重要元素的引用
                this.input = this.shadowRoot.querySelector('input');
                let leftSlot = this.shadowRoot.querySelector('slot[name="left"]');
                let rightSlot = this.shadowRoot.querySelector('slot[name="right"]');
                
                // 当内部输入字段获得失去焦点时，设置或移除
                // focused 属性，以便样式表在整个组件显示或隐藏人造的焦点环。注意，blur，focus
                // 现在变量X的值就是0。事件会冒泡，就像起源自<search-box>一样
                console.log('onfocus：',leftSlot);

                this.input.onfocus = ()=> {
                    this.setAttribute('focused', '')
                };
                this.input.onblur = ()=>{
                    this.removeAttribute('focused')
                };
                
                // 如果点击了放大镜，则触发search， 输入时 change 事件也触发这个事件
                // change事件不会影响到影子DOM外边
                leftSlot.onclick = this.input.onchange = (event) =>{
                    event.stopPropagation();// 阻止单击事件冒泡
                    if(this.disabled) return ;// 禁用则啥也不做
                    this.dispatchEvent(new CustomEvent('search', {
                        detail: this.input.value
                    }))
                    
                }
                
                // 单击X，则触发 clear 事件. 处理程序没有调用 preventDefault(), 则清除输入
                rightSlot.onclick = (event) => {
                    event.stopPropagation();// 不让事件向上冒泡
                    if(this.disabled) return;// 如果禁用啥也不做
                    let e = new CustomEvent('clear', {cancelable: true});
                    this.dispatchEvent(e);
                    console.log('这是啥？', e);
                    if(!e.defaultPrevented) {// 如果事件没有被取消
                        this.input.value = '';
                    }
                }
            
            }        
            // 在有些属性被设置或改变时，我们需要设置内部<input >
            // 元素对应的值，这个生命周期方法与下面代码的静态属性
            // observedAttrubutes 相互配合，实现回调
            attributeChangedCallback(name, oldValue, newValue) {
                if(name === 'disabled') {
                    this.input.disabled = (newValue !== null)
                } else if(name === 'placeholder') {
                    this.input.placeholder = newValue
                } else if(name === 'value') {
                    this.input.value = newValue
                }
            }
            
            // 最后，为我们支持的HTML属性定义相应的获取方法和设置方法
            // 获取方法简单的返回属性的值。当某个地方设置修改了值时， 自动调用上边的  attributeChangedCallback
            get placeholder() { return this.getAttribute('placeholder')}
            get size() { return this.getAttribute('size')}
            get size() { return this.getAttribute('value')}
            get disabled() { return this.getAttribute('disabled')}
            get hidden() { return this.getAttribute('hidden')}
            
            set placeholder(val) { return this.setAttribute('placeholder', val)}
            set size(val) { return this.setAttribute('size', val)}
            set size(text) { return this.setAttribute('size', text)}
            set disabled(val) {
                if(val) {
                    this.setAttribute('disabled', val)
                } else {
                    this.removeAttribute('disabled')
                }
            }
            set hidden(val) { 
                if(val) {
                    this.setAttribute('hidden', '')
                } else {
                    this.removeAttribute('hidden')
                }
            }
        }
            
        // 这个静态属性对 attributeChangedCallback 方法是必须的，只有在这个数组中列出的属性名才会触发对该方法的调用
        SearchBox.observedAttributes = ['disabled', 'placeholder', 'size', 'value'];
        
        // 创建一个 template 元素，用于保存样式和元素树
        // 可以在每个 SearchBox 元素的实例中使用它门
        SearchBox.template = document.createElement('template');
        
        // 通过解析HTML字符串模板初始化，通过克隆这个模板中的节点，不需要再次解析HTML
        
        SearchBox.template.innerHTML = `
            <style>
                /*
                *这里的:host选择符引用的是阳光DOM中做的<search-box>元素，这些样式是默认的，
                *<search-box>的使用者可以通过阳光DOM中的样式来覆盖这些样式
                */
                :host {
                    display: inline-block;
                    border: solid black 1px;
                    border-radius: 5px;
                    padding: 4px 6px;
                }
                :host([hidden]) {
                    display: none;
                }

                :host([focused]) {
                    box-shadow: 0 0 2px 2px #6AE;
                }

                input {
                    border-width: 0;outline: none; font: inherit; background: inherit;
                }
                slot {
                    cursor: default; user-select:none;
                }
            </style>
            <div>
                <slot name="left" >\u{1f50d}</slot>
                <input type="text" placeholder="搜索...">
                <slot name="right" >\u{2573}</slot>
            </div>
            
        `;
        // 最后，我们调用 customElements.define()将SearchBox元素，注册为<search-box>标签的实现。自定义元素的标签中必须包含一个连字符
        customElements.define('search-box', SearchBox);
```

## http-equiv（http响应头）

> http-equiv
>
> 相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容，与之对应的属性值为content，content中的内容其实就是各个参数的变量值。

语法：

meta标签的http-equiv属性语法格式是： 

```js
<meta http-equiv=”参数” content=”参数变量值”>；
```

其中http-equiv属性主要有以下几种参数：

1、Expires(期限)

> 可以用于设定网页的到期时间。一旦网页过期，必须到服务器上重新传输。

```html
<meta http-equiv="expires" content="Wed, 20 Jun 2017 12:05:00 GMT">  //必须使用GMT的时间格式。
```

2、Pragma(cache模式)

> 是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开网页就无法从Cache中再调出

```html
<meta http-equiv="Pragma" content="no-cache"> //这样设定，访问者将无法脱机浏览。
```

3、Refresh(刷新)

> 自动刷新并指向新页面。

```html
<meta http-equiv="Refresh" content="2；URL=http://www.baidu.com/">//2指的是2秒后跳转到URL指定的网址
```

4、Set-Cookie(cookie设定)

> 如果网页过期，那么存盘的cookie将被删除。

```html
<meta http-equiv="Set-Cookie" content="cookievalue=xxx;expires=Wednesday, 20-Jun-2007 22:33:00 GMT； path=/">//必须使用GMT的时间格式。
```

5、Window-target(显示窗口的设定)

> 强制页面在当前窗口以独立页面显示。

```html
<meta http-equiv="Window-target" content="_top"> //用来防止别人在框架里调用自己的页面
```

6、content-Type(显示字符集的设定)

> 设定页面使用的字符集。

```html
<meta http-equiv="content-Type" content="text/html; charset=gb2312">

```

7、Pics-label(网页等级评定)

```html
<meta http-equiv="Pics-label" contect=""> //在IE的internet选项中有一项内容设置，可以防止浏览一些受限制的网站，而网站的限制级别就是通过meta属性来设置的。

```

8、Page_Enter、Page_Exit

> 设定进出页面时的特殊效果

```html
<meta http-equiv="Page-Enter"    contect="revealTrans(duration=1.0,transtion=12)"> //进入页面时的效果 

<meta http-equiv="Page-Exit"    contect="revealTrans(duration=1.0,transtion=12)">//退出页面时的效果

Duration的值为网页动态过渡的时间，单位为秒。
Transition是过渡方式，它的值为0到23，分别对应24种过渡方式。如下表：
0 盒状收缩
1 盒状放射
2 圆形收缩
3 圆形放射
4 由下往上
5 由上往下
6 从左至右
7 从右至左
8 垂直百叶窗
9 水平百叶窗
10 水平格状百叶窗
11垂直格状百叶窗
12 随意溶解
13从左右两端向中间展开
14从中间向左右两端展开
15从上下两端向中间展开
16从中间向上下两端展开
17 从右上角向左下角展开
18 从右下角向左上角展开
19 从左上角向右下角展开
20 从左下角向右上角展开
21 水平线状展开
22 垂直线状展开
23 随机产生一种过渡方式
```

9、清除缓存（再访问这个网站要重新下载）

> 设定页面使用的字符集。

```html
<meta http-equiv="cache-control" content="no-cache">
- 指令（coontent 参数）
public
    响应可以被任何对象（客户端、代理服务器等）缓存
no-cache
    强制要求缓存把请求提交给原始服务器进行验证（协商缓存验证）
no-store
    不使用任何缓存
max-age
    缓存最大周期
must-revalidate
    一旦资源过期，在成功向原始服务器验证之前，不能缓存响应后的任何数据

- 缓存控制
控制是否开启缓存

字段：Pragma 和 Cache-Control

Pragma：no-cache 禁用缓存 （HTTP/1.0）
Cache-Control：缓存控制 （HTTP/1.1）

注意：符合缓存策略时，服务器不会发送新的资源，但不是说客户端和服务器就没有会话了，客户端还是会发请求到服务器的。

优先级从高到低是 Pragma -> Cache-Control -> Expires
```

10、设定网页的到期时间

```html
<meta http-equiv="expires" content="0">

```

11、关键字,给搜索引擎用的

```html
<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">

```

12、页面描述

```html
<meta http-equiv="description" content="This is my page">

```



#### **Cache Manifest** 缓存使用

> 已被 Service Worker 取代，在 w3c 标准中 去除了
>
> manifest是一个后缀名为minifest的文件，在文件中定义那些需要缓存的文件，支持manifest的浏览器，会将按照manifest文件的规则，像文件保存在本地，从而在没有网络链接的情况下，也能访问页面。缓存大小限制 ~=5M

```js

<html manifest="URL">
绝对 URL - 指向另一个网站（比如 href="http://www.example.com/demo.appcache"）
相对 URL - 指向网站内的一个文件（比如 href="demo.appcache"）


//eg: manifest.appcache [w3c建议后缀 .appcache]

# CACHE MANIFEST - 在此标题下列出的文件将在首次下载后进行缓存
CACHE MANIFEST
# 2012-02-21 v1.0.0
/theme.css
/logo.gif
/main.js

# NETWORK - 在此标题下列出的文件需要与服务器的连接，且不会被缓存
NETWORK:
login.php
# * 也可以使用星号“ * ”来指示所有其他资源/文件都需要因特网连接

#  在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）
FALLBACK:
/html/ /offline.html  # 第一个 URI 是资源，第二个是替补


```

// 通过javascript操作 跟新

```js
window.applicationCache.update();
```

第一行CACHE MANIFEST是固定的格式，且必须要写在第一行，也必须要有，NETWORK和FALLBACK为可选项。

FALLBACK中的资源必须和manifest文件同源。

引用manifest的html必须与manifest文件同源，在同一个域下。

当manifest文件发生改变时，资源请求本身也会触发更新

注释不仅仅起到不执行的作用，上述已经详细解释了，可以是版本号，时间戳或者md5码等等。

manifest文件中的cache部分不能使用通配符，必须手动指定，没有自动化工具。

##### // 问题

1、在开发过程中，通过ajax与WCF进行数据交互时，常常头一次或头几次数据加载成功，以后均加载失败。

因为启用的web离线缓存机制，所以每次ajax加载数据时是从本地缓存文件中读取的，用的是ajax的get模式，因为get模式缓存，所以不会重新向服务器请求数据，导致数据加载失败。
改成ajax post方式后，数据 never cache,所以每次刷新网站，均会向service请求数据。

报错： Application Cache Error event: Manifest fetch failed (404)

解决方法：
manifest 文件需要配置正确的 MIME-type，即 “text/cache-manifest”。
manifest 的 contentType = text/cache-manifest，扩展名建议为 .appcache
且必须在 web 服务器上进行配置，不同的服务器配置方法不一样。

2、页面离线，ajax更新。

首先，你可以修改下 manifest 文件来更新这个页面，但是作为文章内容页面离线以后，就会存储在本地了，如果你是一篇章的话，那么这个文章的内容页就被存下来了，你如果以相同的 url 去访问，不管你文章里面的数据更新没有，这个离线下来的页面都不会更新了 ( 除非你更新manifest 文件 ) 。所以，你所有的动态数据，都得用 ajax 方式去获取，就像客户端一样，离线的页面应该是一个没有数据的空壳，然后通过 ajax 去拉去数据填补这个空壳。然后要注意的是，ajax 的请求地址，要写到manifest 的 network 中。

3、离线页面的更新（长尾问题）

网站更新了，如何更新用户本地的离线页面呢？
与很多文章中说的一样，先上线你的文件，然后修改一下页面中引入的cache.manifest文件即可，比如修改下注释，修改后，如果再访问页面，就会先去校验manifest 时候有更新，如有更新，再次刷新页面的时候，页面就会更新了。
长尾问题（非常重要）：
就像前面说到的一样，如果你的 manifest 文件更新了，你访问页面，需要刷新一次，更新的页面才能 load加载进来，那么这样就有一个问题，如果你的后端数据，就是给 js ajax 接口的数据变化了，你对应的 js 也修改了。那么你修改 manifest 上线的时候，第一次开页面，页面就会出 bug 了。再刷一次页面，就好了。那么，这个第一次访问的 bug ，是我们不想看到的。
而且你不能知道用户什么时候第二次再来访问你的页面，所以你的页面一旦使用 manifest 离线，就像客户端一样，这样就出现了长尾问题。还好， manifest 有一些 js 接口，可以来判断， load 更新文件。

4、cache.status属性返回当前离线应用状态

 UNCACHED ( 数值 0) ：未启用离线应用
 IDLE ( 数值 1) ：已开启离线应用，但本地缓存的资源是最新的，并且未标记为废弃资源
 CHECKING ( 数值 2) ：当前更新缓存的状态为 “ 检查中 ”
 DOWNLOADING ( 数值 3) ：当前更新缓存的状态为 “ 下载资源中 ”
 UPDATEREADY ( 数值 4) ：当前更新缓存的状态为 “ 更新完毕 ”
 OBSOLETE ( 数值 5) ：已开启离线应用，但缓存资源都已标记为废弃


5、如果文件超出缓存5M的大小，会造成什么。
比如我A频道维护了自己的Application Cache，B频道也维护了自己的，这个时候A频道如果使用达到了一个峰值，会导致B频道所有的缓存失效。
所以，建议Application Cache存储公共资源，不要存储业务资源！

由更新机制来说，首次更新manifest时，因为页面加载已经开始甚至已经完成，缓存更新尚未完成，浏览器仍然会使用过期的资源；浏览器是当Application Cache有更新时，该次不会使用新资源，第二次才会使用。这个时候update事件中执行window.reload事件。

```js
window.applicationCache.addEventListener("updateready", function(){
    window.location.reload()
});
```

6、由上例可以知道，缓存的不只是显示定义的文件，比如上例中的applicationcache/时便会默认保存index.html为映射的数据，并且包含demo.appcache文件，很多时候会遇到一次文件更新线上老是不更新，这个时候随便在manifest配置文件中做一点修改即可更新。
做一下代码更改：

```html
<html  manifest="A.appcache">
=>
<html  manifest="B.appcache">
```

这个时候如果不做A.appcache的更新的话，缓存将不会更新，原因是index.html被缓存了，检测的仍然是原manifest清单
各个页面统一管理自己的manifest清单，意思是a页面配置了common.js，b页面也配置了common.js，意思是a页面更新后，
b页面的manifest不更改的话，b页面依旧读取的是老版本的文件，这个有一定道理却也有一定浪费，需要公共页面做处理。

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/minifest.png)

### Service Worker

> 代替 **Cache Manifest**

## input 

```html
type File

<p>
    <label for="soundFile">What does your voice sound like?:</label>
    <input type="file" id="soundFile" capture="user" accept="audio/*">
</p>
<p>
    <label for="videoFile">Upload a video:</label>
    <input type="file" id="videoFile" capture="environment" accept="video/*">
</p>
<p>
    <label for="imageFile">Upload a photo of yourself:</label>
    <input type="file" id="imageFile" capture="user" accept="image/*">
</p>

// 移动端适用，PC端只会弹出经典文件选择框
capture属性指定
	user	应使用面向用户的摄像头和/或麦克风。
    environment	应使用外向摄像头和/或麦克风
```

### input 有哪些 type 

-  button

没有默认行为的按钮，上面显示 value 属性的值，默认为空。

```html
<input type="button" name="button" />
```

- checkbox

复选框，可设为选中或未选中。

```html
<input type="checkbox" name="checkbox" />
```

- color

用于指定颜色的控件；在支持的浏览器中，激活时会打开取色器。

```html
<input type="color" name="color" />
```

- date

输入日期的控件（年、月、日，不包括时间）。在支持的浏览器激活时打开日期选择器或年月日的数字滚轮。

```html
<input type="date" name="date" />
```

- datetime-local

输入日期和时间的控件，不包括时区。在支持的浏览器激活时打开日期选择器或年月日的数字滚轮。

```html
<input type="datetime-local" name="datetime-local" />
```

- email

编辑邮箱地址的区域。类似  text 输入，但在支持的浏览器和带有动态键盘的设备上会有确认参数和相应的键盘。

```html
<input type="email" name="email" />
```

- file

让用户选择文件的控件。使用 accept 属性规定控件能选择的文件类型。

```html
<input type="file" accept="image/*, text/*" name="file" />
```

- hidden

不显示的控件，其值仍会提交到服务器。举个例子，右边就是一个隐形的控件。

```html

```

- image

带图像的  submit 按钮。显示的图像由 src 属性规定。如果 src 缺失，alt 属性就会显示。

```html
<input type="image" name="image" src="" alt="image input" />
```

- month

输入年和月的控件，没有时区。

```html
<input type="month" name="month" />
```

- number

用于输入数字的控件。如果支持的话，会显示滚动按钮并提供缺省验证（即只能输入数字）。拥有动态键盘的设备上会显示数字键盘。

```html
<input type="number" name="number" />
```

- password

单行的文本区域，其值会被遮盖。如果站点不安全，会警告用户。

```html
<input type="password" name="password" />
```

- radio

单选按钮，允许在多个拥有相同 name 值的选项中选中其中一个。

```html
<input type="radio" name="radio" />
```

- range

此控件用于输入不需要精确的数字。控件是一个范围组件，默认值为正中间的值。同时使用 htmlattrdefmin  和 htmlattrdefmax 来规定值的范围。

```html
<input type="range" name="range" min="0" max="25" />
```

- reset

此按钮将表单的所有内容重置为默认值。不推荐。

```html
<input type="reset" name="reset" />
```

- search

用于搜索字符串的单行文字区域。输入文本中的换行会被自动去除。在支持的浏览器中可能有一个删除按钮，用于清除整个区域。拥有动态键盘的设备上的回车图标会变成搜索图标。

```html
<input type="search" name="search" />
```

- submit

用于提交表单的按钮。

```html
<input type="submit" name="submit" />
```

- tel

用于输入电话号码的控件。拥有动态键盘的设备上会显示电话数字键盘。

```html
<input type="tel" name="tel" />
```

- text

默认值。单行的文本区域，输入中的换行会被自动去除。

```html
<input type="text" name="text" />
```

- time

用于输入时间的控件，不包括时区。

```html
<input type="time" name="time" />
```

- url

用于输入 URL 的控件。类似 text 输入，但有验证参数，在支持动态键盘的设备上有相应的键盘。

```html
<input type="url" name="url" />
```

- week

用于输入以年和周数组成的日期，不带时区。

```html
<input type="week" name="week" />
```

## 手机直接捕获摄像头数据

```html
<body>
  <label for="environment">Capture environment:</label>
  <br>
  <input
    type="file"
    id="environment"
    capture="environment"
    accept="video/*"
  >
  <br><br>
  <label for="user">Capture user:</label>
  <br>
  <input
    type="file"
    id="user"
    capture="user"
    accept="image/*"
  >
</body>
```

