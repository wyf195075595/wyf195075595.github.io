---
title: CSS 2.0
date: 2022-06-17 18:23:10
tags: CSS
categories: htmlcss

---

本章内容来自于《css的世界》-张鑫旭，类似于读书笔记吧。主要介绍css2.0的语法。本书主要用于进阶学习，不适用于初学者，书中也默认忽略一些基础的知识

### css 的世界

> 已操作系统划分世界，不同的操作系统就是平行世界。每种浏览器就是一个王国。
>
> 在CSS世界中，HTML是魔法石，选择器就是选择法器，CSS属性就是魔法师，CSS各种属性值就是魔法师的魔法技能，浏览器就是他们所在的“王国”，“王国”会不断更新法律法规（版本升级），决定是否允许使用新的魔法石（HTML5新标签新属性），是否允许新的魔法师入“国籍”（CSS3新属性），或者允许魔法师使用某些新技能（CSS新的属性值），以及是否舍弃某些魔法技能（如display:run-in）；操作系统就是他们所在的世界，不同的操作系统代表不同的平行世界，所以，CSS世界有这么几个比较大的平行世界，即Windows世界、OS X世界以及移动端的iOS世界和Android世界。不同世界的浏览器王国的命运不一样，例如，在OS X世界中，IE王国是不存在的，而Safari王国却异常强大，但在Windows世界中，Safari王国却异常落寞。以上这一切就构成了完整的CSS世界的“世界观”。



### 专业术语

1. 属性

2. 值

3. 关键字

4. 变量

5. 长度单位

	时间单位（如s、ms），还有角度单位（如deg、rad等）

	相对长度单位：如em,rem(相对字体),vh,vw,vmin,vmax(相对视区)

	绝对长度单位：px

6. 功能符

	值以函数的形式指定（就是被括号括起来的那种），主要用来表示颜色（rgba和hsla）、背景图片地址（url）、元素属性值、计算（calc）和过渡效果等，如rgba(0,0,0, .5)、url('css-world.png')、attr('href')和scale(-1)。

7. 属性值

8. 声明

	属性名加上属性值就是声明

9. 声明块

	就是选择器 {} 区域

10. 规则或规则集

	选择器后面的声明块就是一个规则集

	```css
	div {
	    ... 规则集
	}
	```

	

11. 选择器

12. 关系选择器

	常见的符号有空格、>、～，还有+等，这些都是非常常用的选择器。

13. @规则

	@规则指的是以@字符开始的一些规则，像@media、@font-face、@page或者@support，诸如此类

<!--more-->

### 未定义行为

> 不同浏览器对某一行为可能没制定规则，或规则不一致。结果会出现的问题。
>
> 狭隘的说法就是浏览器出现bug了

### 流（文档流）

> 默认的流规则： 自上而下，从左到右

在流中只有两种元素，内联，块级元素。块级元素具有换行属性,也就是一个水平流上只能单独显示一个元素，多个块级元素则换行显示。

理论上都能通过清除浮动去除影响

```css
.clear:after {
    content: '';
    display: table;// 也可以是 block,list-item(使用这个要去除项目符号，IE不支持)
    clear: both;
}
```

### 对  liet-item及 display 其他属性的理解

> 为啥 list-item 会出现 项目符号，将其理解为附加盒子。只有它是特殊的
>
> display: inline-block; 可以理解为 嵌套盒子，外边是内联盒子里边是块级盒子

### widh 默认值是 auto

包含了至少4种不同的表现

1. 充分利用空间

	div,p这些元素宽度默认是100%于父级容器

2. 收缩于包裹

	典型代表：浮动，绝对定位，inner-block元素或table元素。会收缩到合适宽度

3. 收缩到最小

	容易出现在 table-layout为 auto 的表格中

4. 超出容器限制

	除非明确width相关设置，否则上边3种情况尺寸都不会超过父级容器宽度。特殊情况长连续字母，数字或设置了 white-space: nowrap;

#### “鑫三无准则”，即“无宽度，无图片，无浮动”

> 内部尺寸：元素宽度由内部元素大小控制
>
> 外部尺寸：元素大小由外部容器大小绝定，就是上边 with auto 四种表现得第一种

外部尺寸：[宽度设置后会丧失流动性](http://demo.cssworld.cn/3/2-3.php)

内部尺寸：

1. [包裹性](http://demo.cssworld.cn/3/2-4.php) 实现文本少时居中显示，超出一行则居左显示
2. [首选最小宽度](http://demo.cssworld.cn/3/2-6.php) 实现一个凹凸文字效果
3. [最大宽度](http://demo.cssworld.cn/3/2-7.php) iScroll 根据元素尺寸和容器关系改变内部关系实现滚动

#### CSS流体布局下得宽度分离原则

> 就是CSS中的width属性不与影响宽度的padding/border（有时候包括margin）属性共存
>
> 但是css3 有 box-sizing属性可以改变盒模型就不需要这么麻烦了

不能这样写

```css
.box {width: 100px; border: 1px solid;}
或
.box {width: 100px; padding: 10px;}
```

这样写

```css
.father {
    width: 180px;
}
.son {
    margin: 0 20px;
    padding: 20px;
    border: 1px solid red;
}
```



### height: 100% 不生效

> 对于普通文档流中的元素，百分比高度值要想起作用，其父级必须有一个可以生效的高度值！

```html
// 页面设置全屏红色背景，不生效
<html>
    <head>
        .main {
        	width: 100%;
        	heiht: 100%;
        	background: red;
        }
    </head>
    <body>
        <div class="main">
            
        </div>
    </body>
</html>
// 除非 给 body,html 也同时设置了 height: 100%;
```

#### 为啥宽度设置100%可以，高度就不行？

规范中其实给出了答案。

​	如果包含块的高度没有显式指定（即高度由内容决定），并且该元素不是绝对定位，则计算值为auto。一句话总结就是：因为解释成了auto。要知道，auto和百分比计算，肯定是算不了的

​	宽度的解释却是：如果包含块的宽度取决于该元素的宽度，那么产生的布局在CSS 2.1中是未定义的。

> 还记不记得本书第2章最后的“未定义行为”吗？这里的宽度布局其实也是“未定义行为”，也就是规范没有明确表示该怎样，浏览器可以自己根据理解去发挥。好在根据我的测试，布局效果在各个浏览器下都是一致的。这里和高度的规范定义就区别明显了，高度明确了就是auto，高度百分比计算自然无果，width却没有这样的说法，因此，就按照包含块真实的计算值作为百分比计算的基数。

#### 如何让元素支持 hieght: 100% 效果

1. 设置显式得高度值

2. 使用绝对定位

	需要注意的是，绝对定位元素的百分比计算和非绝对定位元素的百分比计算是有区别的，区别在于绝对定位的宽高百分比计算是相对于padding box的，也就是说会把padding大小值计算在内，但是，非绝对定位元素则是相对于content box计算的



#### 为流体而生的min-width/max-width

> 改变其值不会触发过度动画。
>
> min-width/min-height的初始值是auto, max-width/max-height的初始值是none。
>
> max-width/max-height的初始值为何是none而不是auto呢？
>
> 这个问题的答案其实与下面小节的内容有关。我们不妨举个简单的例子解释一下，已知父元素宽度400像素，子元素设置宽度800像素，假如说max-width初始值是auto，那自然使用和width一样的解析渲染规则，此时max-width的计算值就应该是父元素的400像素，此时，你就会发现，子元素的800像素直接完蛋了，因为max-width会覆盖width。于是，我们的width永远不能设置为比auto计算值更大的宽度值了，这显然是有问题的，这就是为什么max-width初始值是none的原因。

权重 **!important** 在max-* 属性面前黯然失色

```css
<img src="1.jpg" style="width: 480px !important;">
img {
	max-width: 256px;
}
```

 结果是 max-width 覆盖了 width

#### min-width/max-width 冲突

```css
.container {
	min-width: 1400px;
    max-width: 1200px;
}
```

结果是 min-width 存活，没有后来居上

#### height 变化动画

一个下拉框如果下拉内容不确定，设置 height:auto;  那么从0 -> auto 的动画怎么计算展示

height使用的值是默认的auto，应该都知道的auto是个关键字值，并非数值，正如height:100%的100%无法和auto相计算一样，从0px到auto也是无法计算的，因此无法形成过渡或动画效果。

解决办法：

​	通过设置 max-height 

但是 max-height 的值设置的过大则会导致延迟效果，建议max-height使用足够安全的最小值，这样，收起时即使有延迟，也会因为时间很短，很难被用户察觉，并不会影响体验。



### 深入理解content

**内联盒子（inline box）**

  **“内联盒子”**不会让内容成块显示，而是排成一行，这里的**“内联盒子”**实际指的就是元素的**“外在盒子”**，用来决定元素是内联还是块级。该盒子又可以细分为**“内联盒子”**和**“匿名内联盒子”**两类：

![img](https:////upload-images.jianshu.io/upload_images/11794918-7f5b2f269ef80945.png?imageMogr2/auto-orient/strip|imageView2/2/w/603/format/webp)

内联盒子

**行框盒子（line box）**

![img](https:////upload-images.jianshu.io/upload_images/11794918-f1889ebe5b82b2da.png?imageMogr2/auto-orient/strip|imageView2/2/w/590/format/webp)

行框盒子

  每一行就是一个**“行框盒子”**（实线框标注），每个**“行框盒子”**又是由一个一个**“内联盒子”**组成的。每个行框盒子都前面有一个宽度为0的具有该元素的字体和行高属性的看不见的“幽灵空白节点”（strut）

**包含盒子（containing box）**

![img](https:////upload-images.jianshu.io/upload_images/11794918-3561f2dc90a8ead3.png?imageMogr2/auto-orient/strip|imageView2/2/w/577/format/webp)

包含盒子

<p>标签就是一个“包含盒子”（实线框标注），此盒子由一行一行的“行框盒子”组成。


  需要补充说明一点，在CSS 规范中，并没有**“包含盒子”**的说法，更准确的称呼应该是**“包含****块”**（containing block）。这里之所以把它称为盒子，一是为了与其他盒子名称统一，二是称为盒子更形象、更容易理解。

**替换元素**

> 什么是替换元素？
>
> 通过修改某个属性值呈现的内容就可以被替换的元素就称为“替换元素”。因此，<img>、<object>、<video>、<iframe>或者表单元素<textarea>和<input>都是典型的替换元素。

特性：

1. 内容的外观不受页面上的 CSS 的影响
2. 有自己的尺寸。
3. 在很多CSS属性上有自己的一套表现规则。

替换元素的 display 属性值

![image-20221010174705251](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221010174705251.png)

#### 替换元素的尺寸计算

将替换元素的尺寸从内而外分为3类：固有尺寸、HTML尺寸和CSS尺寸。

1. 固有尺寸指的是替换内容原本的尺寸（简单说就是原文件的尺寸）

	例如，图片、视频作为一个独立文件存在的时候，都是有着自己的宽度和高度的

2. HTML尺寸这个概念略微抽象 （简单来说就是标签自带的width,hieght等属性）

	我们不妨将其想象成水煮蛋里面的那一层白色的膜，里面是“固有尺寸”这个蛋黄蛋白，外面是“CSS尺寸”这个蛋壳。“HTML尺寸”只能通过HTML原生属性改变，这些HTML原生属性包括<img>的width和height属性、<input>的size属性、<textarea>的cols和rows属性等。

3. CSS尺寸

	特指可以通过CSS的width和height或者max-width/min-width和max-height/min-height设置的尺寸，对应盒尺寸中的content box。

权重： css尺寸>HTML尺寸>固有尺寸

#### <img>元素，图片的固定尺寸不受CSS宽高控制，那为何我们设定img的 width和height 属性会影响图片的尺寸呢？

​	因为图片中的**content**替换内容默认的适配方式是填充（fill），也就是外部设定的尺寸多大，我就填满、跟着一样大。换句话说，尺寸变化的本质并不是改变固有尺寸，而是采用了填充作为适配HTML尺寸和CSS尺寸的方式，且在CSS3之前，此适配方式是不能修改的。

object-fit 属性就是用来修改图片设配方式的

> 替换元素具有固定的宽高比例，所以如果只设置宽/高， 另一个属性会俺比例自动计算

> 如果任何尺寸都没有，则元素应该是300像素×150像素，这条规则<video>、<canvas>和<iframe>这些元素都符合，唯独图片例外。
>
> 结果不仅不是这个尺寸，而且各个浏览器下的尺寸还不一样。IE浏览器下是28×30，如图4-2所示。Chrome浏览器下是0×0，如图4-3所示。Firefox浏览器下显示的是0×22

> 关于图片的src 属性：除非没有 src 属性，否则不管是空值还是其他值，很多浏览器下还是会发送请求

> 对于Firefox浏览器，src默认的<img>不是替换元素，而是一个普通的内联元素，所以使用的就不是替换元素的尺寸规则，而是类似<span>的内联元素尺寸规则，宽高会无效
>
> 修复需设置
>
> ```css
> img { display: inline-block; }
> ```
>
> 

> chrome 浏览器中 img 要想表象的像替换元素一样要设置 alt="任意值"，否者就是一个普通的内联标签



#### src缺省时，img元素的alt信息展示

- ##### HTML：

	```html
	<img alt="美女沉思图" data-src="1.jpg">
	<p><button>设置src属性显示图片</button></p>
	```

- ##### CSS：

	```css
	img {
	    display: inline-block;
	    width: 256px; height: 192px;
	    /* 隐藏Firefox alt文字 */
	    color: transparent;
	    position: relative;
	    overflow: hidden;
	}
	img:not([src]) {
	    /* 隐藏Chrome alt文字以及银色边框 */
	    visibility: hidden;
	}
	img::before {
	    /* 淡蓝色占位背景 */
	    content: "";
	    position: absolute; left: 0;
	    width: 100%; height: 100%;
	    background-color: #f0f3f9;
	    visibility: visible;
	}
	img::after {
	    /* 黑色alt信息条 */
	    content: attr(alt);
	    position: absolute;
	    left: 0; bottom: 0;
	    width: 100%;
	    line-height: 30px;
	    background-color: rgba(0,0,0,.5);
	    color: white;
	    font-size: 14px;
	    transform: translateY(100%);
	    /* 来点过渡动画效果 */
	    transition: transform .2s;
	    visibility: visible;
	}
	img:hover::after {
	    transform: translateY(0);
	}
	```

- ##### JS：

	```js
	var eleButton = document.querySelector('button'),
	    eleImg = document.querySelector('img');
	
	if (eleButton && eleImg) {
	    var initValueButton = eleButton.innerHTML;
	    // 图片地址
	    var srcImage = eleImg.getAttribute('data-src');
	    // 移除该属性
	    eleImg.removeAttribute('data-src');
	    // 按钮点击事件
	    eleButton.addEventListener('click', function() {
	        if (this.innerHTML == initValueButton) {
	            this.innerHTML = '移除src属性';
	            // 图片显示
	            eleImg.setAttribute('src', srcImage);
	        } else {
	            this.innerHTML = initValueButton;
	            // src属性移除
	            eleImg.removeAttribute('src');
	        }
	    });
	}
	```

> 技巧：图片从普通元素变成替换元素，原本都还支持的：:before和：:after此时全部无效
>
> 兼容性问题
>
> 根据我的测试，目前Chrome和Firefox等浏览器支持，但IE浏览器不支持；其次，要想让Chrome或Firefox等浏览器生效，还有其他一些需要注意的技术点。
>
> （1）不能有src属性（证明观点的关键所在）；
>
> （2）不能使用content属性生成图片（针对Chrome）；
>
> （3）需要有alt属性并有值（针对Chrome）；
>
> （4）Firefox下：:before伪元素的content值会被无视，::after无此问题，应该与Firefox自己占用了：:before伪元素的content属性有关。

> Chrome浏览器的渲染表现帮助我们更好地理解了替换元素。什么表现呢？就是在Chrome浏览器下，所有的元素都支持content属性，而其他浏览器仅在：:before/::after伪元素中才有支持

实现设置默认图片

```css
img:not([src]) {
    content: url(1.jpg);
}
```

#### content与替换元素关系剖析

> content属性生成的内容都是替换元素？没错，就是替换元素！

1. content生成的文本是无法选中、无法复制的

2. content生成的文本无法被屏幕阅读设备读取，也无法被搜索引擎抓取

3. 不能左右：empty伪类 。content 中有内容也会被当成 空

4. content动态生成值无法获取

	计算动态值

	```css
	.total::after {
		content: counter(icecrean)
	}
	```

	试图获取计算值时却只能拿到 "counter(icecrean)"



> 辅助实现“两端对齐”以及“垂直居中/上边缘/下边缘对齐”效果

content换行符与打点loading效果实例页面

- ##### HTML：

	```html
	正在加载中<dot>...</dot>
	```

- ##### CSS：

	```css
	dot {
	    display: inline-block; 
	    height: 1em;
	    line-height: 1;
	    text-align: left;
	    vertical-align: -.25em;
	    overflow: hidden;
	}
	dot::before {
	    display: block;
	    content: '...\A..\A.';
	    white-space: pre-wrap;
	    animation: dot 3s infinite step-start both;
	}
	@keyframes dot {
	    33% { transform: translateY(-2em); }
	    66% { transform: translateY(-1em); }
	}
	```

（1）为什么使用<dot>这个元素？

​	<dot>是自定义的一个标签元素，除了简约、语义化明显外，更重要的是方便向下兼容，IE8等低版本浏览器不认识自定义的HTML标签，因此，会乖乖地显示里面默认的3个点，对我们的CSS代码完全忽略。

（2）为什么使用：:before，可不可以使用：:after？

​	伪元素使用：:before同时display设置为block，是为了在高版本浏览器下原来的3个点推到最下面，不会影响content的3行内容显示，如果使用：:after怕是效果就很难实现了。

（3）从content属性值来看，是3个点在第1行，而1个点反而在最后一行，为什么这么处理？

​	3个点在第一行的目的在于兼容IE9浏览器，因为IE9浏览器认识<dot>以及：:before，但是不支持CSS新世界的animation属性，所以，为了IE9也能正常显示静态的3个点，故而把3个点放在第一行。

（4）这里white-space值为何使用的是pre-wrap而不是pre？

​	这里的white-space:pre-wrap改成white-space:pre效果其实是一样的



#### 深入理解content计数器

> 所谓CSS计数器效果，指的是使用CSS代码实现随着元素数目增多，数值也跟着变大的效果

计数器有两个属性，一个方法

属性：

- counter-reset

	计数器-重置: 默认是0，可以设置初始值，可以是负整数，小数，不过，IE和Firefox对此都不识别，Chrome 小数向下取整

- counter-increment

	计数器递增：值为counter-reset的1个或多个关键字

方法：

- counter()/counters()

	-counter(name, style)

	name是计数器名， style 它支持lst-style-type支持的哪些值。作用：我们递增/减的不一定是数字，还可以是英文字母，罗马文等

	

	-counters(name, string, style)

	​	此方法用于生成子序号 如 1-1，1-1-1

	​	name: 计数器名， string:  表示子序号的连接字符串。

counter 计算实例：

```html
.one {
    counter-reset: one 10 two 20;
}
.one:before {
    content: counter(one) '~' counter(two);
    counter-increment: one;
}

<p class="one">《计数器》</p>
```

counters 计算实例：

```html
<style>
    ul {
        counter-reset: two 0;
    }
    li {
        list-style-type: none;
    }
    ul li::before {
        content: counters(two, '-') '：';
        counter-increment: two;
    }
</style>
<ul>
    <li>
        第一章
        <ul>
            <li>开始</li>
            <li>结束</li>
        </ul>
    </li>
    <li>第二章</li>
    <li>
        第三章
        <ul>
            <li>开始</li>
            <li>结束</li>
        </ul>
    </li>
    <li>
        第四章
        <ul>
            <li>开始</li>
            <li>结束</li>
        </ul>
    </li>
    <li>第五章</li>
</ul>

```



### padding 属性

设置了 box-sizing: border-box; 后元素的 content 区域会自动计算。也就是说padding就可以随意设置。但是如果padding 足够大的花 元素的尺寸也是会发生变化的。

例如：

```css
.box {
    width: 80px;
    padding: 20px 60px;
    box-sizing: border-box;
}

```

此时的width 会无效，最终宽度为120px ,而里面的内容则表现为 ”首选最小宽度“



> padding 对于内联元素的高度的影响。 对于内联元素padding 可以撑开元素的宽（影响布局尺寸）。但是高度却不行。虽然高度可以撑开（不影响布局尺寸）但是撑开的上下部分会与其他元素发生重叠

#### 设置实例分割线

```css
<a href="">登录</a><a href="">注册</a>

a + a:before {
    content: "";
    font-size: 0;
    padding: 10px 3px 1px;
    margin-left: 6px;
    border-left: 1px solid gray;
}
```

#### padding 的百分比值

与margin 不同 ，padding 不支持 负值，支持%但是，和高度等属性%计算规则有些差异。

padding百分比值无论是水平方向还是垂直方向均是相对于宽度（父级宽度）计算的！



#### 按钮 button 的padding

按钮padding与高度计算不同浏览器下千差万别

如何统一：

```html
<button id="btn"></button>
<label for="btn">按钮</label>

button {
    position: absolute;
	clip: react(0 0 0 0);
}
label {
	display: inline-block;
	line-height: 20px;
	padding: 10px;
}

```

#### padding  实现三道杠 ,双层圆点图形效果小图标

```css
.icon-menu {
    display: inline-block;
    width: 140px; height: 10px;
    padding: 35px 0;
    border-top: 10px solid;
    border-bottom: 10px solid;
    background-color: currentColor;
    background-clip: content-box;
}
.icon-dot {
    display: inline-block;
    width: 100px; height: 100px;
    padding: 10px;
    border: 10px solid;    
    border-radius: 50%;
    background-color: currentColor;
    background-clip: content-box;
}
```

#### padding 兼容性问题

> 如果容器可以滚动，在IE和Firefox浏览器下是会忽略padding-bottom值的，Chrome等浏览器则不会。
>
> 本质区别在于：Chrome浏览器是子元素超过content box尺寸触发滚动条显示，而IE和Firefox浏览器是超过padding box尺寸触发滚动条显示。

如 非Chrome浏览器中：

```html
<div style="height: 100px; padding: 50px 0;">
    <img src="0.jpg" height="300" />
</div>
```

底部没有50像素的padding-bottom间隙

兼容写法：

```html
<div style="height: 100px;">
    <img src="0.jpg" height="300" style="margin: 50px 0;" />
</div>
```



### margin 属性

只要元素的尺寸表现符合“充分利用可用空间”，无论是垂直方向还是水平方向，都可以通过margin改变尺寸。

CSS世界默认的流方向是水平方向，因此，对于普通流体元素，margin只能改变元素水平方向尺寸；但是，对于具有拉伸特性的绝对定位元素，则水平或垂直方向都可以，因为此时的尺寸表现符合“充分利用可用空间”

- 元素尺寸

	$().width()，$().height()方法 包括 padding和border。也就是元素的border-box尺寸。元素DOM API中 写作 offsetWidth和offsetHeight 也称为“元素偏移尺寸”

- 元素内部尺寸

	$().innerWidth()和$().innerHeight()方法，表示元素的内部区域尺寸，包括padding但不包括border，也就是元素的padding box的尺寸。在原生的DOM API中写作clientWidth和clientHeight，所以，有时候也称为“元素可视尺寸”

- 元素外部尺寸

	$().outerWidth(true)和$().outerHeight (true)方法，表示元素的外部尺寸，不仅包括padding和border，还包括margin，也就是元素的margin box的尺寸。没有相对应的原生的DOM API。



> margin对尺寸没有影响，只有元素是“充分利用可用空间”状态的时候，margin才可以改变元素的可视尺寸。
>
> 因为只要宽度设定，margin就无法改变元素尺寸，这和padding是不一样的。

#### [margin 实现 等高布局效果示意](http://demo.cssworld.cn/4/3-2.php)

这里我们设置了margin-bottom:-9999px意味着元素的外部尺寸在垂直方向上小了9999px。默认情况下，垂直方向块级元素上下距离是0，一旦margin-bottom:-9999px就意味着后面所有元素和上面元素的空间距离变成了-9999px，也就是后面元素都往上移动了9999px。此时，通过神来一笔padding-bottom:9999px增加元素高度，这正负一抵消，对布局层并无影响，但却带来了我们需要的东西—视觉层多了9999px高度的可使用的背景色。但是，9999px太大了，所以需要配合父级overflow:hidden把多出来的色块背景隐藏掉，于是实现了视觉上的等高布局效果。

优点：兼容性好，可兼容IE6

缺点：如果需要有子元素定位到容器之外，父级的overflow:hidden是一个棘手的限制；其次，当触发锚点定位或者使用DOM.scrollIntoview()方法的时候，可能就会出现奇怪的定位问题

其他方法实现：例如使用display:table-cell布局，左右两栏作为单元格处理，或者使用border边框来模拟

- ##### HTML：

	```html
	<div id="colLeft" class="column-left">
	    <h4>正方观点</h4>
	    <p>观点1</p>
	</div>
	<div id="colRight" class="column-right">
	    <h4>反方观点</h4>
	    <p>观点1</p>
	</div>
	
	<input type="button" id="leftMore" value="更多正方观点">
	<input type="button" id="rightMore" value="更多反方观点">
	```

- ##### CSS：

	```css
	.column-left,
	.column-right {
	    margin-bottom: -9999px;
	    padding-bottom: 9999px;
	}
	.column-left {
	    background-color: #34538b;
	}
	.column-right {
	    background-color: #cd0000;
	}
	```

- ##### JS：

	```js
	var $ = function (id) {
	    return document.getElementById(id);
	};
	// 分栏元素
	var colLeft = $('colLeft'), colRight = $('colRight');
	// 按钮元素
	var leftMore = $('leftMore'), rightMore = $('rightMore');
	
	// 序号
	var indexLeft = 1, indexRight = 0;
	
	if (colLeft && colRight && leftMore && rightMore) {
	    leftMore.onclick = function () {
	        indexLeft = indexLeft + 1;
	        colLeft.insertAdjacentHTML('beforeend', '<p>观点'+ indexLeft +'</p>');
	    };
	    rightMore.onclick = function () {
	        indexRight = indexRight + 1;
	        colRight.insertAdjacentHTML('beforeend', '<p>观点'+ indexRight +'</p>');
	    };
	}
	```



#### margin 的百分比值

> 和padding属性一样，margin的百分比值无论是水平方向还是垂直方向都是相对于宽度计算的



#### margin 合并

> 块级元素的上外边距（margin-top）与下外边距（margin-bottom）有时会合并为单个外边距，这样的现象称为“margin合并”

1. 块级元素，但不包括浮动和绝对定位元素

2. 只发生在垂直方向

	不考虑 writing-mode 的情况下才正确

**margin 合并的3种场景**

- 相邻兄弟元素margin合并

- [父级和第一个/最后一个子元素](http://demo.cssworld.cn/4/3-3.php)

	下面三种设置等效

	```html
	<div class="father">
	    <div class="son" style="margin-top: 80px"></div>
	</div>
	
	<div class="father" style="margin-top: 80px">
	    <div class="son"></div>
	</div>
	
	<div class="father" style="margin-top: 80px">
	    <div class="son" style="margin-top: 80px"></div>
	</div>
	```

	

**如何阻止 margin 合并？**

对于margin-top 可以进行如下操作（满足一个条件即可）

-  父元素设置为块状格式化上下文元素；

-  父元素设置border-top值；

-  父元素设置padding-top值；

-  父元素和第一个子元素之间添加内联元素进行分隔。

对于margin-bottom 合并，可以进行如下操作（满足一个条件即可）：

- 父元素设置为块状格式化上下文元素；

- 父元素设置border-bottom值；

- 父元素设置padding-bottom值；

- 父元素和最后一个子元素之间添加内联元素进行分隔；

- 父元素设置height、min-height或max-height。

在尝试设置 元素之间添加 空 div 阻止合并时还需如下设置

- 设置垂直方向的border；

- 设置垂直方向的padding；

- 里面添加内联元素（直接Space键空格是没用的）；
- 设置height或者min-height。



**margin 合并的规则**

> 计算规则总结为“正正取大值”“正负值相加”“负负最负值”

**margin 合并的意义**

让我们的页面结构容错性更强了，比方说最后一个元素移除或位置调换，均不会破坏原来的布局，也就是我们的CSS无须做任何调整。

**margin: auto;**

在父元素有富余宽度时，可设置 margin: 0 auto; 左右居中。要设置垂直居中可以改变流方向(writing-mode: vertical-lr)。要做到垂直居中则需要借助定位.如下

```css
.father {
	hieght.father {
    width: 300px; height: 150px;
    background-color: #f0f3f9;
    position:relative;
}
.son { 
    position: absolute; 
    top: 0; right: 0; bottom: 0; left: 0;
    width: 200px; height: 100px;
    background-color: #cd0000;
    margin: auto;
}

```

如果son 太大超过了father ，则margin会变化为0



**margin 无效的情况**

1. display计算值inline的非替换元素的垂直margin是无效的

	对于内联替换元素，垂直margin有效，并且没有margin合并的问题，所以图片永远不会发生margin合并。

2. 表格中的<tr>和<td>元素或者设置display计算值是table-cell或table-row的元素的margin都是无效的

3. margin合并的时候，更改margin值可能是没有效果的。除非超过当前最值

4. 绝对定位元素非定位方位的margin值“无效”。

	如：

	```css
	img { top: 10%; left: 30%; }
	```

	此时right和bottom值属于auto状态，也就是右侧和底部没有进行定位，此时，这两个方向设置margin值我们在页面上是看不到定位变化的

5. 定高容器的子元素的margin-bottom或者宽度定死的子元素的margin-right的定位“失效”。

	例如，一个普通元素，在默认流下，其定位方向是左侧以及上方，此时只有margin-left和margin-top可以影响元素的定位。但是，如果通过一些属性改变了定位方向，如float:right或者绝对定位元素的right右侧定位，则反过来margin-right可以影响元素的定位，margin-left只能影响兄弟元素。

6. 鞭长莫及导致的margin无效

	如下例子：

	```html
	<div class="box">
	    <img src="mml.jpg" />
	    <p>
	        内容
	    </p>
	</div>
	
	.box > img {
		float: left;
		width: 256px;
	}
	.box > p {
		overflow: hidden;
		margin-left: 200px;// 此时设置无效，准确地讲，此时的<p>的margin-left从负无穷到256px都是没有任何效果的
	}
	```

7. ）内联特性导致的margin无效 **(*)**

	如下：

	```html
	<div clss="box">
	    <img src="mml.jpg" />
	</div>
	.box > img {
		height: 96px;
		margin-top: -200px;
	}
	```

	一个容器里面有一个图片，然后这张图片设置margin-top负值，让图片上偏移。但是，随着我们的负值越来越负，结果达到某一个具体负值的时候，图片不再往上偏移了。比方说，本例margin-top设置的是-200px，如果此时把margin-top设置成-300px，图片会再往上偏移100px吗？不会！它会微丝不动，margin-top变得无效了。要解释这里为何会无效，需要对vertical-align和内联盒模型有深入的理解



### border 属性

**为啥 border-width 不支持百分比值？**

margin，padding都是相对于宽度计算。假设如果boder支持%，那么不同宽度屏幕的相同%值的边框大小就不一样，显然不合边框的语义， 同时设计之初也没有需要使用%值得场景

支持得关键字：

- thin: 相当于px
- medium: 相当于 3px (默认)
- think: 相当于4px

为啥 默认是medium，应为 border-style:double至少3px才有效果！



**border-style**

- solid 实线
- dashed 虚线
- dotted 虚点（IE中渲染为圆点，IE9一下不支持border-raduis,so...）
- doble 双线边框
- 其他 inset（内凹）、outset（外凸）、groove（沟槽）、ridge（山脊）风格老土过时，且兼容性惨不忍睹

计巧

- [border绘制带加号的上传按钮](https://demo.cssworld.cn/4/4-1.php)

- 通过透明border 设置左右间距

- [增加点击区域大小](http://demo.cssworld.cn/4/4-2.php)

- 绘制三角形 (通过不同的 border-style 可绘制更多形状)

	```css
	.div {
	    width: 0;
	    border: 10px solid;
	    border-color: $f30 transparent transparent transparent;
	}
	```

- [border实现等高布局](http://demo.cssworld.cn/4/4-4.php) 通过border-left/right只持支2-3栏



### 内联与流

#### 字母X与CSS世界的基线

> 在各种内联相关模型中，凡是涉及垂直方向的排版或者对齐的，都离不开最基本的基线（baseline）。例如，line-height行高的定义就是两基线的间距，vertical-align的默认值就是基线

**CSS中有一个概念叫作x-height，指的是字母x的高度。**

![image-20221012165319074](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221012165319074.png)

- ascender height：上下线高度。

- cap height：大写字母高度。

- median：中线。

- descender height：下行线高度。

**vertical-align:middle**

这里的middle是中间的意思。注意，跟上面的median（中线）不是一个意思。在CSS世界中，middle指的是基线往上1/2 x-height高度。我们可以近似理解为字母x交叉点那个位置。

**字母中的x与CSS中的ex**

> ex是CSS中的一个相对单位，指的是小写字母x的高度，没错，就是指x-height。
>
> ex的价值就在其副业上—不受字体和字号影响的内联元素的垂直居中对齐效果。

[基于ex单位的天然垂直居中对齐效果](https://demo.cssworld.cn/5/1-1.php)

- ##### CSS：箭头图标垂直对齐文字

	```css
	.icon-arrow {
	    display: inline-block;
	    width: 20px;
	    height: 1ex;
	    background: url(/images/5/arrow.png) no-repeat center;
	}
	```



#### **内联元素的高度之本—line-height**

一个空div 写上几个文字，请问div的高度是由什么决定的?

一般人都会觉得是文字font-size决定的，但本质上是由line-height属性全权决定的

[例子如下](http://demo.cssworld.cn/5/2-1.php)：

- ##### HTML：

	```html
	<div class="test1">我的高度是？</div>
	<div class="test2">我的高度是？</div>
	```

- ##### CSS：

	```css
	.test1,
	.test2 {
	    margin: 24px 0;
	    border: 1px solid #ccc;
	    background: #eee;
	}
	.test1 {
	    font-size: 16px;
	    line-height: 0;
	}
	.test2 { 
	    font-size: 0;
	    line-height: 16px;
	}
	```



**行距，半行距、内容区域**

一般业界的共识是：行距= 行高 - em-box（一个概念）

转换成CSS语言就是：行距= line-height - font-size

内容区域：Firefox/IE浏览器下文本选中带背景色的区域

[半行间距](http://demo.cssworld.cn/5/2-2.php。)

![image-20221012171416152](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221012171416152.png)

当字体是是宋体时，em-box 等高与 内容区域

假设line-height是1.5, font-size大小是14px，那么我们的半行距大小就是（套用上面的行距公式再除以2）:(14px ＊ 1.5-14px) / 2= 14px ＊ 0.25 = 3.5px。border以及line-height等传统CSS属性并没有小数像素的概念（从CSS3动画的细腻程度可以看出），因此，这里的3.5px需要取整处理，如果标注的是文字上边距，则向下取整；如果是文字下边距，则向上取整，因为绝大多数的字体在内容区域中都是偏下的。所以，假设设计师标注了文字字形上边缘到图片下边缘间距20px，则我们实际的margin-top值应该是17px，因为3.5px向下取整是3px。

[line-height通过控制行距实现文字排版](http://demo.cssworld.cn/5/2-3.php)

> 对于块级元素，line-height对其本身是没有任何作用的，我们平时改变line-height，块级元素的高度跟着变化实际上是通过改变块级元素里面内联级别元素占据的高度实现的。

**line-height可以让内联元素“垂直居中”**

```css
.title {
    height: 24px;
    line-height: 24px;
}
```

上面样式可以实现单行文字垂直居中，这似乎是众所周知的。但实际上这种说法不够严谨，准确来说是近似垂直居中

垂直居中的原理就是 **行距的上下等分机制**，如果行距的添加规则是在文字的上方或者下方，则行高是无法让文字垂直居中的。

说“近似”是因为文字字形的垂直中线位置普遍要比真正的“行框盒子”的垂直中线位置低

**[实现多行文字垂直居中](http://demo.cssworld.cn/5/2-4.php)**

这里的垂直居中其实也是近似

- ##### HTML：

	```html
	<div class="box">
	    <div class="content">基于行高实现的...</div>
	</div>
	```

- ##### CSS：

	```css
	.box {
	    width: 280px;
	    line-height: 120px;
	    background-color: #f0f3f9;
	    margin: auto;
	}
	.content {
	    // 创建一个独立的“行框盒子.既能重置外部的line-height为正常的大小，又能保持内联元素特性
	    display: inline-block;
	    line-height: 20px;
	    margin: 0 20px;
	    text-align: left;
	    // 产生一个非常关键的“行框盒子”,每个“行框盒子”都会附带的一个产物—“幽灵空白节点”，即一个宽度为0、表现如同普通字符的看不见的“节点”
	    vertical-align: middle;
	}
	```



**line-height 属性的值**

- normal

	计算值，只要字体相同，各个浏览器下的默认line-height解析值基本上都是一样的。

- 数值

	其最终的计算值是和当前font-size相乘后的值，数值(1.5) * fontSize

- 百分比值

	如line-height:150%，其最终的计算值是和当前font-size相乘后的值。

- 长度值

	也就是带单位的值，如line-height:21px或者line-height:1.5em等。最终的计算值也是和当前font-size相乘后的值

> [继承细节有所差别](http://demo.cssworld.cn/5/2-5.php)。如果使用数值作为line-height的属性值，那么所有的子元素继承的都是这个值；但是，如果使用百分比值或者长度值作为属性值，那么所有的子元素继承的是最终的计算值

**内联元素line-height的大值特性**

> 无论内联元素line-height如何设置，最终父级元素的高度都是由数值大的那个line-height决定的



#### **vertical-align**

> 它是line-height 的好朋友，为啥这么说呢？应为line-height 生效的地方，vertical-align 也一定起作用

很多人认为 单行文本只要设置行高是多少，其占据的高度就是多少。[看下面一个例子](http://demo.cssworld.cn/5/3-1.php)

- ##### HTML：

	```html
	<div class="box">
	    <span>文本</span>
	</div>
	```

- ##### CSS：

	```css
	.box {
	    display: inline-block;
	    line-height: 32px;
	}
	.box > span {
	    font-size: 24px;
	}
	```

	box 盒子高度是多少？

很多人认为 box 高度是32px, 因为没有设置高度，高度由line-height 决定。然而实际上确实要大那么几像素（受字体影响，增加高度不一样），比方说36px

为啥会这样呢？其实这里是vertical-align 下了黑手

其中一个关键点，那就是 fontSize 设置在span ,这就导致 box的fontSize 与 span 有差异。这时我们可以通过设置: 字体大小统一，或者设置对齐方式来 解决这个问题

#### [vertical-align 的属性值](http://demo.cssworld.cn/5/3-2.php)

- 数值百分比类，如20px、2em、20%等。•
- 上标下标类，如sub、super；•
- 文本类，如text-top、text-bottom；•
- 线类，如baseline（默认值）、top、middle、bottom；

经过一番测试我们可以发现负值全部都是往下偏移，正值全部都是往上偏移，而且数值大小全部都是相对于基线位置计算的，因此，从这一点来看，**vertical-align:baseline 等同于 vertical-align:0**。

> margin和padding是相对于宽度计算的，line-height是相对于font-size计算的，而这里的vertical-align属性的百分比值则是相对于line-height的计算值计算的。

**vertical-align 作用的前提**

有时候我们想设置 vertical-align 但是却不生效，为啥？

因为vertical-align起作用是有前提条件的，这个前提条件就是：**只能应用于内联元素以及display值为table-cell的元素。**换句话说，vertical-align属性只能作用在display计算值为inline、inline-block, inline-table或table-cell的元素上。因此，默认情况下，<span>、<strong>、<em>等内联元素，<img>、<button>、<input>等替换元素，非HTML规范的自定义标签元素，以及<td>单元格，都是支持vertical-align属性的，其他块级元素则不支持。

> 如果设置了元素 浮动，定位 会让元素块状化。然后导致vertical-align不起作用
>
> 

**另一种不生效情况如下：**

```css
.box {
    height: 128px;
}
.box > img {
    height: 96px;
    vertical-align: middle;
}
<div class="box">
	<img src="1.jpg" >
</div>
```

这种情况 图片顶着 box 上边缘，为啥不起作用？实际上是行框盒子前面的 **幽灵空包节点**高度太小，如果我们设置足够大的行高让 ”**幽灵节点**“ 高度足够，就会看到效果了

```css
.box {
    height: 128px;
    line-height: 128px; // 关键
}
.box > img {
    height: 96px;
    vertical-align: middle;
}
```



**为啥 diaplay: table-cell 可以无视行高？**

```css
.box {
    height: 128px;
    display: table-cell;
    vertical-align: middle;
}
.box > img {
    height: 96px;
}
<div class="box">
	<img src="1.jpg" >
</div>
```

对table-cell元素而言，vertical-align起作用的是table-cell元素自身

**[消除底部间隙](http://demo.cssworld.cn/5/3-5.php)**

```css
.box {
	width: 280px;
	outline: 1px solid #aaa;
    text-align: center;
}
.box > img {
    height: 96px;
}
```

```html
<div class="box" >
    <img src="1.jpg" >
</div>
```

结果 box 元素 高度肯能就无缘无故多了5 px.

间隙产生的三大元凶就是“幽灵空白节点”、line-height和vertical-align属性

1. 图片块状化。可以一口气干掉“幽灵空白节点”、line-height和vertical-align。
2. 容器line-height足够小。只要半行间距小到字母x的下边缘位置或者再往上，自然就没有了撑开底部间隙高度空间了。比方说，容器设置line-height:0。
3. 容器font-size足够小。此方法要想生效，需要容器的line-height属性值和当前font-size相关，如line-height:1.5或者line-height:150%之类；否则只会让下面的间隙变得更大，因为基线位置因字符x变小而往上升了。
4. 图片设置其他vertical-align属性值。间隙的产生原因之一就是基线对齐，所以我们设置vertical-align的值为top、middle、bottom中的任意一个都是可以的。

**内联特性导致的margin无效**

> 而在CSS世界中，非主动触发位移的内联元素是不可能跑到计算容器外面的，导致图片的位置被“幽灵空白节点”的vertical-align:baseline给限死了

```css
.box > img {
    height: 96px;
    margin-top: -200px;
}
```

```html
<div class="box">
    x<img src="../images/b2.png" alt="">
</div>
```

在幽灵节点处使用X代替，因为字符x下边缘和图片下边缘对齐，字符x非主动定位，不可能跑到容器外面，所以图片就被限死在此问题，margin-top失效。

**inline-block与baseline**

> vertical-align属性的默认值**baseline**在**文本之类的内联元素**那里就是**字符x的下边缘**，对于**替换元素**则是**替换元素的下边缘**。但是，如果是**inline-block元素**，则规则要复杂了：一个inline-block元素，如果里面**没有内联元素，或者overflow不是visible**,则该元素的基线就是其**margin底边缘**；否则其基线就是元素里面**最后一行内联元素的基线**。

```html
<span class="dib-baseline"></span>
<span class="dib-baseline">x-baselinex-baselinex-baselinex-baselinex-baseline</span>
```

```css
.dib-baseline {
    display: inline-block;
    width: 150px; height: 150px;
    border: 1px solid red;
    background-color: #ccc;
}
```

![image-20221013154058106](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221013154058106.png)

[text-align:jusitfy声明可以帮助我们实现兼容的列表两端对齐效果](http://demo.cssworld.cn/5/3-6.php)

[处理图标对齐](http://demo.cssworld.cn/5/3-7.php)

**[vertical-align middle 近似垂直居中的原因](http://demo.cssworld.cn/5/3-8.php))**

- 内联元素：元素的垂直中心点和行框盒子基线往上1/2 x-height处对齐（就是字符x的交叉点位置）。

- table-cell元素：单元格填充盒子相对于外面的表格行居中对齐。

> 基本上所有的字体中，字符x的位置都是偏下一点儿的，font-size越大偏移越明显，这才导致默认状态下的vertial-align:middle实现的都是“近似垂直居中”。
>
> 如果想要实现真正意义上的垂直居中对齐，只要想办法让字符x的中心位置就是容器的垂直中心位置即可，通常的做法是设置font-size:0。



**[vertical-align 的顶部，底部对齐](http://demo.cssworld.cn/5/3-9.php)**

- vertical-align:text-top：盒子的顶部和父级内容区域的顶部对齐。
- vertical-align:text-bottom：盒子的底部和父级内容区域的底部对齐。



**[基于vertical-align属性的水平垂直居中弹框](http://demo.cssworld.cn/5/3-10.php)**

- ##### HTML：

	```html
	<div class="container">
	    <div class="dialog">
	        <div class="content">内容占位</div>
	    </div>
	</div>
	```

- ##### CSS：

	```css
	.container {
	    position: fixed;
	    top: 0; right: 0; bottom: 0; left: 0;
	    /* for IE8 */
	    background: url(data:image/png;base64,iVB...g==);
	    /* for IE9+ */
	    background: rgba(0,0,0,.5), none;
	    text-align: center;
	    font-size: 0;
	    white-space: nowrap;
	    z-index: 99;
	    overflow: auto;
	}
	.container:after {
	    content: "";
	    display: inline-block;
	    height: 100%;
	    vertical-align: middle;
	}
	.dialog {
	    display: inline-block;
	    vertical-align: middle;
	    border-radius: 6px;
	    background-color: #fff;
	    text-align: left;
	    white-space: normal;
	}
	```

### 流的破坏与保护

#### float

> 设计初衷：浮动的本质就是为了实现文字环绕效果（即“父级高度塌陷”和“行框盒子区域限制”）
>
> float属性有个著名的特性表现，就是会让父元素的高度塌陷。

- 浮动锚点

	浮动锚点是float元素所在的“流”中的一个点，这个点本身并不浮动，就表现而言更像一个没有margin、border和padding的空的内联元素。

- 浮动参考

	指的是浮动元素对齐参考的实体

> 浮动锚点其作用就是产生“行框盒子”，因为“浮动锚点”表现如同一个空的内联元素，有内联元素自然就有“行框盒子”，于是，float元素对齐的参考实体“行框盒子”对于块状元素也同样适用了，只不过这个“行框盒子”由于没有任何内容，所以无尺寸，看不见也摸不着罢了。

例子：

```html
<h3>标题</h3>

右边新增一个a标签
<h3>标题<a href="#">更多</a></h3>

a 标签设置 float: right;

当标题文字出现换行时，更多 显示的位置？
IE6/7中浮动元素会在第二行显示，IE8以上的浏览器会显示在第一行

```

在CSS世界中，float元素的“浮动参考”是“行框盒子”，也就是float元素在当前“行框盒子”内定位

每一行内联元素都有一个“行框盒子”，这个例子中标题文字比较多，两行显示了，因此有上下两个“行框盒子”，而“更多”所在的<a>元素是在标题文字后面，位于第二行，因此，这里设置了float:right的<a>元素是相对于第二行的“行框盒子”对齐的。

那如果标题文字刚好够两行，那么 **更多**显示的位置是？

会单独展示在第三行？

**[利用float 破坏流特性的两栏自适应布局](http://demo.cssworld.cn/6/1-2.php)**

**clear 属性**

> 用来专门处理float 属性带来的塌陷问题
>
> 官方对clear属性的解释是：“元素盒子的边不能和前面的浮动元素相邻。”
>
> 就是设置了clear属性的元素自身如何如何，而不是让float元素如何如何

```css
clear: none | left| right | both;
```

我们一般常用 both 几乎不使用 right/left,应为 用它们时肯定可以用 both 代替

因为clear属性只有块级元素才有效，而 :after的伪类元素都是内联水平， 所以需要手动设置display

```css
.clear:after {
    content: '';
    display: table;// block/list-item
    clear: both;
}
```

> 由于clear:both的作用本质是让自己不和float元素在一行显示，并不是真正意义上的清除浮动，因此float元素一些不好的特性依然存在，于是，会有类似下面的现象。

由于clear:both的作用本质是让自己不和float元素在一行显示，并不是真正意义上的清除浮动，因此float元素一些不好的特性依然存在，于是，会有类似下面的现象。

- 如果clear:both元素前面的元素就是float元素，则margin-top负值即使设成-9999px，也不见任何效果。
- clear:both后面的元素依旧可能会发生文字环绕的现象



#### **css世界的结界**

> BFC全称为block formatting context，中文为“块级格式化上下文”。
>
> 表现原则：如果一个元素具有BFC，内部子元素再怎么翻江倒海、翻云覆雨，都不会影响外部的元素。

**触发BFC**

满足下面条件之一即可：

- <html>根元素；
- float的值不为none；
- overflow的值为auto、scroll或hidden；
- display的值为table-cell、table-caption和inline-block中的任何一个；
- position的值不为relative和static。

总结一下，我们对BFC声明家族大致过了一遍，能担任自适应布局重任的也就是以下几个。

- • overflow:auto/hidden，适用于IE7及以上版本浏览器；
- • display:inline-block，适用于IE6和IE7；
- • display:table-cell，适用于IE8及以上版本浏览器。

最后，我们可以提炼出两套IE7及以上版本浏览器适配的自适应解决方案。

（1）借助overflow属性，如下：

```css
.lbf-content {
    overflow: hidden;
}
```

（2）融合display:table-cell和display:inline-block，如下：[插图]

单元格有一个非常神奇的特性，就是宽度值设置得再大，实际宽度也不会超过表格容器的宽度.

```css
.lbf-content {
    display: table-cell; width: 9999px;
    /*如不需要兼容IE7 下面样式可省略*/
    display: inline-block; width: auto;
}
```

关于 display: table-cell; 元素内连续英文无法换行。

```css
.word-break {
    display: table;
    width: 100%;
    table-layout: fixed;
    word-break: break-all;
}
```



#### **最佳结界 overflow**

> 要想彻底清除浮动的影响，最适合的属性不是clear而是overflow。一般使用overflow:hidden，利用BFC的“结界”特性彻底解决浮动对外部或兄弟元素的影响

**overflow-x和overflow-y**

> 如果overflow-x和overflow-y属性中的一个值设置为visible而另外一个设置为scroll、auto或hidden，则visible的样式表现会如同auto。也就是说，除非overflow-x和overflow-y的属性值都是visible，否则visible会当成auto来解析。换句话说，永远不可能实现一个方向溢出剪裁或滚动，另一方向内容溢出显示的效果。

**overflow与滚动条**

HTML 中默认产生滚动的条的标签： html,textarea. 应为它们的overflow 属性默认值不是 visible，而是 auto

**关于浏览器滚动条**

- 针对PC端：无论是啥浏览器，默认滚动条均来自 html,而不是body .如需去除页面默认滚动条只需

```css
html {overflow: hidden;}
```

- 滚动条会占用容器的可用宽度或高度。（移动端不会）

实现表头固定，表格内容可滚动效果，一般使用双表格实现，但是当表格内容出现滚动条时，页面可能出现晃动，应为一开始没有滚动条后面出现滚动条，此时页面可用宽度发生变化，水平居中重新计算，导致页面发生晃动，体验非常不好。下边推荐一剂良药

```css
html {
    overflow-y: scroll;
}
:root {
    overflow-y: auto;
    overflow-x: hide;
}
:root body {
    position: absolute;
}
body {
    width: 100vw;
    overflow: hidden;
}
```

基本上药到病除，而且后遗症非常少，大家不妨试试！

对于Chrome浏览器：

• 整体部分，::-webkit-scrollbar；

• 两端按钮，::-webkit-scrollbar-button；

• 外层轨道，::-webkit-scrollbar-track；

• 内层轨道，::-webkit-scrollbar-track-piece；

• 滚动滑块，::-webkit-scrollbar-thumb；

• 边角，::-webkit-scrollbar-corner

但平时开发只用下面3个属性：

```css
::-webkit-scrollbar {
    /*血槽厚度*/
    width: 8px;
    height: 8px;
}
::-webkit-scrollbar-thumb {
    /*拖动条*/
    background-color: rgba(0,0,0,0.3)
        boorder-radius: 6px;
}
::-webkit-scrollbar-track {
    /*背景槽*/
    background-color: #ddd;
    border-radius: 6px;
}
```

**依赖overflow 的文字溢出**

单行超出隐藏

```css
.overfolw {
    text-overflow: ellipsis;
    overflow: hide;
    white-space: nowrap;
}
```

最多显示2行

```css
.ell-rows-2 {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
}
```



**overflow与锚点定位**

锚点定位： 通过 元素Id 属性，与a标签 href属性联动

```html
<a href="#1">发展历程</a>
...
<h2 id="1">
    ...
    发展历程
</h2>

<a href="javascript:">返回顶部</a>
```

锚点定位行为的发生，本质上是通过改变容器滚动高度或者宽度来实现的

[URL锚链锚点定位和overflow的选项卡切换效果实例页面](https://demo.cssworld.cn/6/4-2.php#four)

> 通过URL锚点定位有一个问题，如果页面有滚动条，切换时页面会发生滚动，而focus定位却不会（overflow:hiden;只是没有显示滚动条不影响锚点定位）

[focus锚点定位和overflow的选项卡切换效果实例页面](https://demo.cssworld.cn/6/4-3.php)

> 原理其实很简单，就是在每个列表里塞入一个肉眼看不见的<input>输入框，然后选项卡按钮变成<label>元素，并通过for属性与<input>输入框的id相关联，这样，点击选项按钮会触发输入框的focus行为，触发锚点定位，实现选项卡切换效果

#### **float的兄弟position:absolute**

> 都兼具“块状化”“包裹性”“破坏性”等特性，不少布局场合甚至可以相互替代
>
> absolute和float可以看作是“同父异母”的兄弟关系



**absolute的包含块**

> 就是元素用来计算和定位的一个框
>
> 有经验的人应该都知道，普通元素的百分比宽度是相对于父元素的content box宽度计算的，而绝对定位元素的宽度是相对于第一个position不为static的祖先元素计算的。

1. 根元素（大多数场景下html就是）被称为“初始包含快”，其尺寸等同于浏览器窗口大小

2. 如果元素position: fixed; 则包含块是初始包含快

3. 如果该元素的position是relative或者static，则“包含块”由其最近的块容器祖先盒的content box边界形成。

4. 如果元素position:absolute，则“包含块”由最近的position不为static的祖先元素建立

	如果该祖先是纯inline元素则规则略微复杂：

	- 假设给内联元素的前后各生成一个宽度为0的内联盒子（inline box），则这两个内联盒子的padding box外面的包围盒就是内联元素的“包含块”；
	- 如果该内联元素被跨行分割了，那么“包含块”是未定义的，也就是CSS2.1规范并没有明确定义，浏览器自行发挥。
	- 否则，“包含块”由该祖先的padding box边界形成。如果没有符合条件的祖先元素，则“包含块”是“初始包含



和常规元素相比，absolute绝对定位元素的“包含块”有以下3个明显差异：

（1）内联元素也可以作为“包含块”所在的元素；

（2）“包含块”所在的元素不是父块级元素，而是最近的position不为static的祖先元素或根元素；

（3）边界是padding box而不是content box。



> position: absolute;[定位元素的最大宽度会受到包含块宽度的影响](http://demo.cssworld.cn/6/5-1.php)。

> 元素padding值可能会影响 到 内部 absolute 定位元素的位置 

**对 absolute 的误区**

[一个绝对定位元素，没有任何left/top/right/bottom属性设置，并且其祖先元素全部都是非定位元素，其位置在哪里？](http://demo.cssworld.cn/6/5-4.php)



很多人都会认为是在浏览器窗口左上方，其实不是的，还是当前位置。

> 正是这种错误认知导致凡是使用absolute绝对定位的地方，一定父容器position:relative，同时left/top等属性定位，甚至必同时使用z-index属性设置层级。

> 我把这种没有设置left/top/right/bottom属性值的绝对定位称为“无依赖绝对定位”
>
> “无依赖绝对定位”本质上就是一个不占据任何空间的相对定位元素
>
> [无依赖绝对定位的强大之处](https://demo.cssworld.cn/6/5-5.php)
>
> [“无依赖绝对定位”与超越常规布局的排版实例页面](http://demo.cssworld.cn/6/5-6.php)
>
> [下拉列表定位](http://demo.cssworld.cn/6/5-7.php)

虽然“无依赖绝对定位”好处多多，但建议只用在静态交互效果上，比方说，导航二级菜单的显示与定位。如果是动态呈现的列表，建议还是使用JavaScript来计算和定位。



**无依赖绝对定位 对内联，块级元素表现**

```css
.follow {
    position: absolute;
}
```



```html
<h3>标题</h3> <span class="follow">span</span>

<h3>标题</h3> <div class="follow">div</div>
```



那么由于非绝对定位状态下span和div都在“标题”的下面，因此，这里最后的效果也同样是都在“标题”的下面。

**浮动与无依赖绝对定位 不一致的表现**

当“浮动”和“无依赖绝对定位”相遇的时候，就会发生一些不愉快的事情

```html
<div class+"nav"> 导航1 </div>
<img src="new.png" class="follow" />
<div class="nav" >导航2</div>
```

```css
.follow {
    posotion: absolute;
}
.nav {
    width: 100px;
    line-height: 40px;
    background-color: #333;
    color: white;
    text-align: center;
    float: left;
}
```

结果在IE和Chrome浏览器下，夹在中间的<img>在中间显示，但是Firefox浏览器却是在最后显示

对于上述场景，如果希望各个浏览器的表现都是一样的，<img>外层嵌套一层标签并浮动即可，注意，是外层标签浮动。由于浮动和绝对定位水火不容，本身设置浮动是没有任何效果的。

****



**absolute与text-align**

按道理讲，absolute 与 float 一样，都可以让元素块状化，应该不会受到内联元素对齐的text-align属性的影响。但 [text-align 可以改变absolute元素位置](http://demo.cssworld.cn/6/5-8.php)

- ##### HTML：

	```html
	<p><img src="1.jpg"></p>
	```

- ##### CSS：

	```css
	p {
	    width: 300px; height: 120px;
	    background-color: #eef0f6;
	    text-align: center;
	}
	img {
	    position: absolute;
	}
	```

> 这里之所以产生了位置变化，本质上是“幽灵空白节点”和“无依赖绝对定位”共同作用的结果。

img是内联水平， 所以p标签内 存在行框盒子，会存在幽灵节点。由于 img 设置了absolute 不占据位置，所以幽灵节点就居中了。根据无依赖绝对定位规则 图片就跟在幽灵节点后面。设置幽灵节点位置对齐间接影响了 img

[通过幽灵节点影响定位元素实现返回顶部](http://demo.cssworld.cn/6/5-10.php)

- ##### HTML：

	```html
	<div class="constr">
	    <div class="alignright">
	        <span class="follow">
	            <img src="circle.png">
	            <img src="backtop.png">
	        </span>
	    </div>
	</div>
	```

- ##### CSS：

	```css
	.constr {
	    width: 80%;
	    margin: auto;
	    background-color: #f0f3f9;
	}
	.alignright {
	    height: 0;
	    text-align: right;
	    overflow: hidden;
	}
	.alignright:before {
	    content: "\2002";
	}
	
	.follow {
	    position: fixed;
	    bottom: 100px;
	    z-index: 1;
	}
	.follow > img {
	    display: block;
	    margin: 10px;
	}
	```

#### **absolute与overflow**

上边示例代码中， 设置了hieght: 0; 又设置了overflow: hidden;那岂不是内容会被剪裁至不可见？

> 官方：绝对定位元素不总是被父级overflow属性剪裁，尤其当overflow在绝对定位元素及其包含块之间的时候。
>
> 换一种方法表述就是：如果overflow 不是定位元素，同时绝对定位元素和 overflow 容器之间也没有定位元素，则overflow无法对absolute元素进行剪裁。

下面HTML中图片不会被裁剪

```html
<div style="overflow: hidden;">
    <img src="1.jpg" style="position: absollute;" />
</div>
```

overflow 元素父级是定位元素也不会被裁决

```html
<div style="position: relative;">
    <div style="overflow: hidden;">
        <img src="1.jpg" style="position: absolute;"/>
    </div>
</div>
```

如果overflow属性所在的元素同时也是定位元素，里面的绝对定位元素会被剪裁：

```html
<div style="overflow: hidden; position: relative;">
    <img src="1.jpg" style="position: absollute;" />
</div>
```

如果overflow元素和绝对定位元素之间有定位元素，也会被剪裁：

```html
<div style="overflow: hidden;">
    <div style="position: relative;">
        <img src="1.jpg" style="position: absolute;"/>
    </div>
</div>
```

> 最后，非常有必要补充一点，那就是由于position:fixed固定定位元素的包含块是根元素，因此，除非是窗体滚动，否则上面讨论的所有overflow剪裁规则对固定定位都不适用。
>
> overflow元素自身transform的时候，Chrome和Opera浏览器下的overflow剪裁是无效的
>
> transform除了改变overflow属性原有规则，对层叠上下文以及position:fixed的渲染都有影响



#### **absolute与clip**

普通元素可以使用 overflow 进行裁剪，但是 fixed 元素 包含快是根元素，所以只能使用clip

clip属性要想起作用，元素必须是绝对定位或者固定定位，也就是position属性值必须是absolute或者fixed。

语法如下：

```css
clip: rect(top right bottom left)
```

实际语法应该是：

```css
clip: rect(top, right, bottom, left)
```

> 就顺序而言，top→right→bottom→left在CSS世界中是一脉相承的，和margin/border-width等属性的4个值的顺序一样，都是从top开始，顺时针旋转。
>
> 这里的4个值有一个明显不一样的地方，就是不能缩写，且和border-width类似，是不支持百分比值的。

场景：很多网站左上角都有包含自己网站名称的标识（logo），而这些标识一般都是图片，为了更好地SEO以及无障碍识别，我们一般会使用<h1>标签写上网站的名称，代码如下：

```html
<a href="/" class="logo">
	<h1>
        css 世界
    </h1>
</a>
```

如何隐藏<h1>标签中的“CSS世界”这几个文字，通常有以下一些技术选型。

- 下策是display:none或者visibility:hidden隐藏，因为屏幕阅读设备会忽略这里的文字。

- text-indent缩进是中策，但文字如果缩进过大，大到屏幕之外，屏幕阅读设备也是不会读取的。

- color:transparent是移动端上策，但却是桌面端中策，因为原生IE8浏览器并不支持它。color:transparent声明，很难用简单的方式阻止文本被框选。

- clip剪裁隐藏是上策，既满足视觉上的隐藏，屏幕阅读设备等辅助设备也支持得很好。

	```css
	.logo h1 {
	    /*无依赖绝对定位，nice*/
		position: absolute;
	    clip: rect(0 0 0 0);
	}
	```

	

这里使用的clip剪裁隐藏是我工作这么多年大浪淘沙筛选后的最佳实践，有对比才能显出好在何处

- display:none或者visibility:hidden隐藏有两个问题，一个是按钮无法被focus了，另外一个是IE8浏览器下提交行为丢失，原因应该与按钮focus特性丢失有关。

- 透明度0覆盖也是一个不错的实践。如果是移动端项目，建议这么做；但如果是桌面端项目，则完全没有必要。使用透明度0覆盖的问题是每一个场景都需要根据环境的不同重新定位，以保证点击区域的准确性，成本较高，但clip隐藏直接用一个类名加一下就好。

- 还有一种比较具有适用性的“可访问隐藏”是下面这种屏幕外隐藏：

	```css
	.abs-out {
	    position: absolute;
	    left: -999px;
	    top: -999px;
	}
	```

	

> 当一个控件元素被focus的时候，浏览器会自动改变滚动高度，让这个控件元素在屏幕内显示。假如说我们的<label>“提交”按钮在第二屏，则点击按钮的时候浏览器会自动跳到第一屏置顶，因为按钮隐藏在了屏幕外，于是发生了非常糟糕的体验问题。而clip就地剪裁，就不会有“页面跳动”的体验问题。于是，权衡成本和效果，clip隐藏成为了最佳选择，特别是对于桌面端项目。

> **clip隐藏仅仅是决定了哪部分是可见的，非可见部分无法响应点击事件等**
>
> **使用clip进行剪裁的元素其clientWidth和clientHeight包括样式计算的宽高都还是原来的大小**



#### **absolute的流体特性**

当absolute遇到left/top/right/bottom属性的时候，absolute元素才真正变成绝对定位元素。此时原本的相对特性丢失

说到流体特性，我们通常第一反应就是**<div>之类的普通块级元素**。实际上，绝对定位元素也具有类似的流体特性，当然，不是默认就有的，而是在**特定条件下才具有**，这个条件就是“**对立方向同时发生定位的时候**”。

例如：

```css
.box {
    position: absolute;
    left: 0;
    right: 0;
}
```

> 而绝对定位元素的这种流体特性比普通元素要更强大，**普通元素流体特性只有一个方向**，默认是水平方向，但是**绝对定位元素可以让垂直方向和水平方向同时保持流动性**。



#### **position:relative**

> 虽然说relative/absolute/fixed都能对absolute的“包裹性”以及“定位”产生限制，但只有relative可以让元素依然保持在正常的文档流中。
>
> relative的定位有两大特性：
>
> - 一是相对自身
>
> 	相对定位元素的left/top/right/bottom的百分比值是相对于包含块计算的，而不是自身.如果包含块的高度是auto，那么计算值是0，偏移无效
>
> 	各方向的值相对自身偏移
>
> - 二是无侵入
>
> 	当relative进行定位偏移的时候，一般情况下不会影响周围元素的布局。

当相对定位元素同时应用对立方向的定位值得时候，，也就是**top/bottom和left/right同时使用**的时候，其表现和绝对定位差异很大。**绝对定位是尺寸拉伸，保持流体特性**，但是**相对定位却是“你死我活”的表现**，也就是说，**只有一个方向的定位属性会起作用**。而孰强孰弱则是与文档流的顺序有关的，**默认的文档流是自上而下、从左往右**，因此**top/bottom同时使用的时候，bottom被干掉**；**left/right同时使用的时候，right毙命**。

[relative定位和margin定位对比实例页面](https://demo.cssworld.cn/6/6-1.php)



**relative的最小化影响原则**

（1）尽量不使用relative，如果想定位某些元素，看看能否使用“无依赖的绝对定位”；

（2）如果场景受限，一定要使用relative，则该relative务必最小化。

最小化影响 示例：

给img添加一层相对定位盒子，而不是给最外层盒子添加相对定位

```html
<div>
    <div style="position: relative;">
        <img src="1.jpg" style="position: absolute;top:0;left:0;"/>
    </div>
    <p>内容1</p>
    <p>内容2</p>
    <p>内容3</p>
    <p>内容4</p>
    ...
</div>
```



#### position:fixed固定定位

> position:fixed固定定位元素的“包含块”是根元素，我们可以将其近似看成<html>元素。换句话说，唯一可以限制固定定位元素的就是<html>根元素

实现将目标元素定位到某个模块的右上角：

```html
<div class="father">
    <div class="right">
        &nbsp;<div class="son"></div>
    </div>
</div>

.father {
	width: 300px; height: 200px;
	position: relative;
}
.right {
	height: 0;
	text-align: right;
	overflow: hidden;
}
.son {
	display: inline;
	width: 40px; height: 40px;
	position: fixed;
	margin-left: -40px;
}
```

**position:fixed的absolute模拟**

> 有时候我们希望元素既有不跟随滚动的固定定位效果，又能被定位元素限制和精准定位，那该怎么办呢？

把需要固定的元素，放置到出现滚动条元素的外面即可

```html
<html>
    <body>
        <div class="page">
            固定定位元素
        </div>
        <div class="fixed"></div>
    </body>
</html>

<style>
    hmtl, body {
        height: 100%;
        overflow: hidden;
    }
    .page: {
        height: 100%;
        overflow: auto;
    }
    .fixed {
        position: absolute;
    }
</style>
```



**position:fixed与背景锁定**

> 蒙层弹窗是网页中常见的交互，其中黑色半透明全屏覆盖的蒙层基本上都是使用position:fixed定位实现的。但是，如果细致一点儿就会发现蒙层无法覆盖浏览器右侧的滚动栏，并且鼠标滚动的时候后面的背景内容依然可以被滚动，并没有被锁定，体验略打折扣。如果希望背景锁定，该如何实现呢？

position:fixed蒙层之所以出现背景依然滚动，那是因为滚动元素是根元素，正好是position:fixed的“包含块”。所以，如果希望背景被锁定，可以借鉴“absolute模拟fixed定位”的思路，让页面滚动条由内部的普通元素产生即可。

如果是移动端项目，阻止touchmove事件的默认行为可以防止滚动；如果是桌面端项目，可以让根元素直接overflow:hidden

### CSS世界的层叠规则

> 默认情况下，网页内容是没有偏移角的垂直视觉呈现，当内容发生层叠的时候，一定会有一个前后的层叠顺序产生，有点儿类似于真实世界中“论资排辈”的感觉。



#### z-index只是CSS层叠规则中的一叶小舟

说到层叠，很多人第一 反应就是z-index 属性。z-index属性只有和定位元素（position不为static的元素）在一起的时候才有作用，可以是正数也可以是负数。理论上说，数值越大层级越高，但实际上其规则要复杂很多。

但随着CSS3新世界的到来，z-index已并非支队定位元素有效，flex盒子的子元素也可以设置。

网页中绝大多数元素是非定位元素，并影响层叠顺序属性远不止z-index， 因此大家千万不要认为z-index 属性就可以代表css世界的层叠规则



#### 理解CSS世界的层叠上下文和层叠水平（概念）

> 层叠上下文
>
> 英文称作stacking context，是HTML中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元素在z轴上就“高人一等”。

> 层叠水平
>
> 英文称作stacking level，决定了同一个层叠上下文中元素在z轴上的显示顺序。

需要注意的是，诸位**千万不要把层叠水平和CSS的z-index属性混为一谈**。尽管某些情况下z-index确实可以影响层叠水平，但是只限于定位元素以及flex盒子的孩子元素；而层叠水平所有的元素都存在。

#### 理解元素的层叠顺序（规则）

> 在CSS 2.1的年代，在CSS3新世界还没有到来的时候（注意这里的前提），层叠顺序规则如图7-2所示

![image-20221018082705146](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221018082705146.png)

1. 位于最下面的background/border特指层叠上下文元素的边框和背景色。每一个层叠顺序规则仅适用于当前层叠上下文元素的小世界。
2. inline水平盒子指的是包括inline/inline-block/inline-table元素的“层叠顺序”，它们都是同等级别的。
3. 单纯从层叠水平上看，实际上z-index:0和z-index:auto是可以看成是一样的。注意这里的措辞—“单纯从层叠水平上看”，实际上，两者在层叠上下文领域有着根本性的差异

**为啥 inline水平盒子层叠顺序比浮动元素还要高？**

background/border为装饰属性，浮动和块状元素一般用作布局，而内联元素都是内容。网页中最重要的是什么？当然是内容了

下面这两条是层叠领域的黄金准则。当元素发生层叠的时候，其覆盖关系遵循下面两条准则：

- 谁大谁上：当具有明显的层叠水平标识的时候，如生效的z-index属性值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。

- 后来居上：当元素的层叠水平一致、层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素。



#### 层叠上下文的特性

- 层叠上下文的层叠水平要比普通元素高（原因后面会说明）。
- 层叠上下文可以阻断元素的混合模式（参见http://www.zhangxinxu.com/wordpress/? p=5155的这篇文章的第二部分说明）。
-  层叠上下文可以嵌套，内部层叠上下文及其所有子元素均受制于外部的“层叠上下文”。
-  每个层叠上下文和兄弟元素独立，也就是说，当进行层叠变化或渲染的时候，只需要考虑后代元素。
- 每个层叠上下文是自成体系的，当元素发生层叠的时候，整个元素被认为是在父层叠上下文的层叠顺序中。

#### 层叠上下文的创建

（1）天生派：页面根元素天生具有层叠上下文，称为根层叠上下文。

（2）正统派：z-index值为数值的定位元素的传统“层叠上下文”。

（3）扩招派：其他CSS3属性。

#### 根层叠上下文

根层叠上下文指的是页面根元素，可以看成是<html>元素。因此，页面中所有的元素一定处于至少一个“层叠结界”中。



#### 定位元素与传统层叠上下文

对于position值为relative/absolute以及Firefox/IE浏览器（不包括Chrome浏览器）下含有position:fixed声明的定位元素，当其z-index值不是auto的时候，会创建层叠上下文。

##### HTML：

比较图片层级谁大谁先上，结果图片在上

```html
<div style="position:relative; z-index:auto;">
    <!-- 美女 -->
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:auto;">
    <!-- 美景 -->
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>
```

此时 父级z-index 层级为0，创建层叠上下文。比较父级层级，相同，后来居上，背景在上

```html
<div style="position:relative; z-index:0;">
    <!-- 美女 -->
    <img src="1.jpg" style="position:absolute; z-index:2;">  
</div>
<div style="position:relative; z-index:0;">
    <!-- 美景 -->
    <img src="2.jpg" style="position:relative; z-index:1;">  
</div>
```