---
title: CSS 2.1
date: 2022-10-20 18:00:00
tags: CSS
categories: htmlcss

---

本章内容来自于《css的世界》-张鑫旭，类似于读书笔记吧。主要介绍css2.1的语法。本书主要用于进阶学习，不适用于初学者，书中也默认忽略一些基础的知识.

CSS2.1中的CSS属性的设计初衷是展示图文

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
> 通过修改某个属性值呈现的内容就可以被替换的元素就称为“替换元素”。因此，**img**、**替换元素**、**video**、**iframe**或者表单元素**textarea**和**input**都是典型的替换元素。

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

	我们不妨将其想象成水煮蛋里面的那一层白色的膜，里面是“固有尺寸”这个蛋黄蛋白，外面是“CSS尺寸”这个蛋壳。“HTML尺寸”只能通过HTML原生属性改变，这些HTML原生属性包括**img**的width和height属性、**input**的size属性、**textarea**的cols和rows属性等。

3. CSS尺寸

	特指可以通过CSS的width和height或者max-width/min-width和max-height/min-height设置的尺寸，对应盒尺寸中的content box。

权重： css尺寸>HTML尺寸>固有尺寸

#### <img>元素，图片的固定尺寸不受CSS宽高控制，那为何我们设定img的 width和height 属性会影响图片的尺寸呢？

​	因为图片中的**content**替换内容默认的适配方式是填充（fill），也就是外部设定的尺寸多大，我就填满、跟着一样大。换句话说，尺寸变化的本质并不是改变固有尺寸，而是采用了填充作为适配HTML尺寸和CSS尺寸的方式，且在CSS3之前，此适配方式是不能修改的。

object-fit 属性就是用来修改图片设配方式的

> 替换元素具有固定的宽高比例，所以如果只设置宽/高， 另一个属性会俺比例自动计算

> 如果任何尺寸都没有，则元素应该是300像素×150像素，这条规则 **video**、**canvas**和**iframe**这些元素都符合，唯独图片例外。
>
> 结果不仅不是这个尺寸，而且各个浏览器下的尺寸还不一样。IE浏览器下是28×30，如图4-2所示。Chrome浏览器下是0×0，如图4-3所示。Firefox浏览器下显示的是0×22

> 关于图片的src 属性：除非没有 src 属性，否则不管是空值还是其他值，很多浏览器下还是会发送请求

> 对于Firefox浏览器，src默认的**img**不是替换元素，而是一个普通的内联元素，所以使用的就不是替换元素的尺寸规则，而是类似**span**的内联元素尺寸规则，宽高会无效
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

因为vertical-align起作用是有前提条件的，这个前提条件就是：**只能应用于内联元素以及display值为table-cell的元素。**换句话说，vertical-align属性只能作用在display计算值为inline、inline-block, inline-table或table-cell的元素上。因此，默认情况下，**span**、**strong**、**em**等内联元素，**img**、**button**、**input**等替换元素，非HTML规范的自定义标签元素，以及<td>单元格，都是支持vertical-align属性的，其他块级元素则不支持。

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

每一行内联元素都有一个“行框盒子”，这个例子中标题文字比较多，两行显示了，因此有上下两个“行框盒子”，而“更多”所在的**a**元素是在标题文字后面，位于第二行，因此，这里设置了float:right的**a**元素是相对于第二行的“行框盒子”对齐的。

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

结果在IE和Chrome浏览器下，夹在中间的**img**在中间显示，但是Firefox浏览器却是在最后显示

对于上述场景，如果希望各个浏览器的表现都是一样的，**img**外层嵌套一层标签并浮动即可，注意，是外层标签浮动。由于浮动和绝对定位水火不容，本身设置浮动是没有任何效果的。

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

场景：很多网站左上角都有包含自己网站名称的标识（logo），而这些标识一般都是图片，为了更好地SEO以及无障碍识别，我们一般会使用\<h1>标签写上网站的名称，代码如下：

```html
<a href="/" class="logo">
	<h1>
        css 世界
    </h1>
</a>
```

如何隐藏\<h1>标签中的“CSS世界”这几个文字，通常有以下一些技术选型。

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

	

> 当一个控件元素被focus的时候，浏览器会自动改变滚动高度，让这个控件元素在屏幕内显示。假如说我们的\<label>“提交”按钮在第二屏，则点击按钮的时候浏览器会自动跳到第一屏置顶，因为按钮隐藏在了屏幕外，于是发生了非常糟糕的体验问题。而clip就地剪裁，就不会有“页面跳动”的体验问题。于是，权衡成本和效果，clip隐藏成为了最佳选择，特别是对于桌面端项目。

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

> position:fixed固定定位元素的“包含块”是根元素，我们可以将其近似看成\<html>元素。换句话说，唯一可以限制固定定位元素的就是\<html>根元素

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

HTML：

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

> Chrome等WebKit内核浏览器下，position:fixed元素天然层叠上下文元素，无须z-index为数值。根据我的测试，目前IE和Firefox仍是老套路。

#### css3与新时代的层叠上下文

（1）元素为flex布局元素（父元素display:flex|inline-flex），同时z-index值不是auto。

（2）元素的opacity值不是1。

（3）元素的transform值不是none。

（4）元素mix-blend-mode值不是normal。

（5）元素的filter值不是none。

（6）元素的isolation值是isolate。

（7）元素的will-change属性值为上面2～6的任意一个（如will-change:opacitg、will-chang:transform等）。

（8）元素的-webkit-overflow-scrolling设为touch。



#### 层叠上下文与层叠顺序

普通元素具有了层叠上下文，其层叠顺序就会不见高，那它的层叠顺序究竟在哪个位置、哪个级别呢？

这里需要分两种情况讨论：

（1）如果层叠上下文元素不依赖z-index数值，则其层叠顺序是z-index:auto，可看成z:index:0级别；

（2）如果层叠上下文元素依赖z-index数值，则其层叠顺序由z-index值决定。

> 元素一旦成为定位元素，其z-index就会自动生效，此时其z-index就是默认的auto，也就是0级别，根据上面的层叠顺序表，就会覆盖inline或block或float元素。而不支持z-index的层叠上下文元素天然是z-index:auto级别，也就意味着，层叠上下文元素和定位元素是一个层叠顺序的，于是当它们发生层叠的时候，遵循的是“后来居上”准则。

[图片淡出，文字跑到图片后面](https://demo.cssworld.cn/7/5-1.php)

- ##### HTML：

	```html
	<div class="box">               
	    <span class="text">只有图片淡出，文案一直100%透明</span>
	    <img class="fade" src="1.jpg">
	</div>
	```

- ##### CSS：

	```css
	@keyframes fadeIn {
	  0% { opacity: 0; }
	  100% { opacity: 1; }
	}
	
	.box {
	  width: 256px; height: 192px;
	  position: relative;
	}
	.text {
	  line-height: 30px;
	  position: absolute; left: 0; right: 0; bottom: 0;
	  background-color: rgba(0,0,0,.5);
	  color: #fff;
	  text-align: center;
	  font-size: 14px;
	}
	.fade {
	  animation: fadeIn 2s 2s infinite; 
	}
	```

opacity的值不是1的时候，是具有层叠上下文的，层叠顺序是z-index:auto级别，跟没有z-index值的absolute绝对定位元素是平起平坐的。而本实例中的文字元素在图片元素的前面，于是，只要CSS3动画不是最终一瞬间的opacity:1，位于DOM流后面的图片就会遵循“后来居上”准则而覆盖文字。

知道原因，想要解决这个问题就很简单了：

（1）调整DOM流的先后顺序；

（2）提高文字的层叠顺序，例如，设置z-index:1。



#### z-index 的负值

> 一开始的时候，以为一个定位元素设置z-index负值，就会跑到页面的背后，隐藏掉，看不到了。结果实际上是有时候确实隐藏了，但有时候又隐藏不掉。为什么会这样？

z-index负值元素的层级：层叠上下文元素上面 <  z-index负值  < block元素的下面

[一个栗子](https://demo.cssworld.cn/7/6-1.php)

- ##### HTML：

	```html
	/* 此时图片被.box背景遮挡*/
	<h4>.box非层叠上下文元素</h4>
	<div class="box">
	    <img src="1.jpg">
	</div>
	
	/* 此时图片在.box背景上方*/
	<h4>.box是层叠上下文元素</h4>
	<div class="box context">
	    <img src="1.jpg">
	</div>
	```

- ##### CSS：

	```css
	.box {
	    background-color: blue;
	}
	.box > img { 
	    position: relative; 
	    z-index: -1; 
	    right: -50px;
	}
	
	.context {
	    transform: scale(1);
	}
	```

负值的应用：

1. IE8下的多背景模拟

	```css
	.box {
	    background-image: (1.jpg);
	    position: relative;
	    z-index: 0; /*创建层叠上下文*/
	}
	.box:before,
	.box:after {
	    content: '';
	    position: absolute;
	    z-index: -1;
	}
	.box:before {
	    background-image: (2.png);
	}
	.box:after {
	    background-image: (3.png);
	}
	```

	此时，就算．box元素里面是纯文字，伪元素图片照样在文字下面，如此广泛的适用场景使上面的处理几乎可以作为通用的多背景模拟实现准则来实现了

2. [定位在元素的后面](http://demo.cssworld.cn/7/6-2.php)

3. 可访问性隐藏

	z-index负值可以隐藏元素，只需要层叠上下文内的某一个父元素加个背景色就可以



#### z-index ”不犯二“准则

> 对于非浮层元素，避免设置z-index值，z-index值没有任何道理需要超过2。由于z-index不能超过2，因此，我称其为“不犯二”准则。

**为啥需要设置这个准则？**

（1）定位元素一旦设置了z-index值，就从普通定位元素变成了层叠上下文元素，相互间的层叠顺序就发生了根本的变化，很容易出现设置了巨大的z-index值也无法覆盖其他元素的问题。

（2）避免z-index“一山比一山高”的样式混乱问题。此问题多发生在多人协作以及后期维护的时候。例如，A小图标定位，习惯性写了个z-index:9; B一看，自己原来的实现被覆盖了，立马写了个z-index:99；结果比弹框组件层级还高，那还得了，立马弹框组件来一个z-index:999999；谁知后来，弹框中又要有出错提示效果……显然，最后项目的z-index层级管理就是一团糟。

> 很重要的一点，我这里的“不犯二”准则，并不包括那些在页面上飘来飘去的元素定位，弹框、出错提示、一些下拉效果等都不受这一准则限制。
>
> 对于这类JavaScript驱动的浮层组件，我会借助“层级计数器”来管理
>
> （1）总会遇到意想不到的高层级元素；
>
> （2）组件的覆盖规则具有动态性。

**层级计数器**

实际上就是一段JavaScript脚本，会遍历所有 **body** **处于显示状态的子元素**，并得到最大z-index值，和默认的z-index做比较。如果超出，则显示的组件的z-index自动加1，这样就不会出现有组件被其他组件覆盖的问题；如果不超出，就使用默认的z-index值，



### 强大的文本处理能力

#### font-size和vertical-align

> line-height的部分类别属性值是相对于font-size计算的，vertical-align百分比值属性值又是相对于line-height计算的，于是，看上去八辈子都搭不上边的vertical-align和font-size属性背后其实也有有着关联的。

如下：

```css
p {
    font-size: 16px;
    line-height: 1.5;
}
p > img {
    vertical-align: -25%;
}
```

此时 img 的vertical-align 的值是多少？

> 16 * 1.5 * -25% = -6px



#### 理解font-size与ex、em和rem的关系

> ex是字符x高度，显然和font-size关系密切，font-size值越大，自然ex对应的大小也就大
>
> 某元素 font-size 大小是多少，其1em 就是font Size 大小，em就是’中’等汉字的高度
>
> 根元素 font-size 大小就是 1rem 的大小



#### font-size的关键字属性值

1. 相对尺寸关键字

	• larger：大一点，是<big>元素的默认font-size属性值。

	• smaller：小一点，是<small>元素的默认font-size属性值。

2. 绝对尺寸关键字

	与当前元素font-size无关，仅受浏览器设置的字号影响。注意这里的措辞，是“浏览器设置”，而非“根元素”，两者是有区别的。

	• xx-large：好大好大，和<h1>元素计算值一样。

	• x-large：好大，和<h2>元素计算值一样。

	• large：大，和<h3>元素计算值近似（“近似”指计算值偏差在1像素以内，下同）。

	• medium：不上不下，是font-size的初始值，和<h4>元素计算值一样

	• small：小，和<h5>元素计算值近似。

	• x-small：好小，和<h6>元素计算值近似。• xx-small：好小好小，无对应的HTML元素。



#### font-size:0与文本的隐藏

> 桌面Chrome浏览器下有个12px的字号限制，就是文字的font-size计算值不能小于12px，我猜是因为中文，如宋体，要是小于12px，就会挤成成一团，略丑，Chrome看不下去，就直接禁用了。

如果font-size:0的字号表现就是0，那么文字会直接被隐藏掉，并且只能是font-size:0，哪怕设置成font-size:0.0000001px，都还是会被当作12px处理的。



#### 字体属性家族的大家长font-family

font-family支持两类属性值，

一类是“字体名”

```css
body {
    font-family: simsun; /*字体名称有空格就需要引号包起来*/
    /* 多字体 */
    font-family: 'PingFang SC','Microsoft Yahei';
}
```

一类是“字体族”

```css
font-family: serif;
```

- serif：衬线字体

	通俗讲就是笔画开始、结束的地方有额外装饰而且壁画粗细会有所不同的字体

- sans-serif：无衬线字体。

- monospace：等宽字体。

- cursive：手写字体。

- fantasy：奇幻字体。

- system-ui：系统UI字体。

字体族与字体名一起时，要将字体名放在前面，不然字体名可能不会加载，应为字体族满足文本渲染就不会在往后面解析了



#### 等宽字体的实践价值

> 所谓等宽字体，一般是针对英文字体而言的。据我所知，东亚字体应该都是等宽的，就是每个字符在同等font-size下占据的宽度是一样的。但是英文字体就不一定了，我随便写一个单词，就iMac吧，大家很明显地发现这个字符i要比M占据的宽度小。

首先等宽字体利于代码呈现。对于写代码的人来说，无论是什么语言，易读是第一位，使用等宽字体，我们阅读起来会更轻松舒服。因此，一般编辑器使用的字体或者Web上需要呈现源代码的字体都是等宽字体

[等宽字体与图形呈现案例一则](http://demo.cssworld.cn/8/2-1.php)

```css
    font-family: Consolas, Monaco, monospace;
```



#### ch单位与等宽字体布局

> ch和em、rem、ex一样，是CSS中和字符相关的相对单位。和ch相关的字符是0，没错，就是阿拉伯数字0。1ch表示一个0字符的宽度

但是我们网页内容的字符不可能都是0，所以这个单位乍看就显得很鸡肋。但是，如果和等宽字体在一起使用，它就可以发挥不一样的威力



#### 中文字体和英文名称

> 虽然一些常见中文字体，如宋体、微软雅黑等，直接使用中文名称作为CSS font-family的属性值也能生效，但我们一般都不使用中文名称，而是使用英文名称，主要是为了规避乱码的风险。还有一些中文字体直接使用中文名称作为CSS font-family的属性值是没有效果的，如思源黑体、兰亭黑体等，需要使用字体对应的英文名称才可以生效。



#### 补充说明

> 微软正黑体是一款全面支持ClearType技术的TrueType无衬线字体，用于繁体中文系统。相对应地，中国大陆地区用的是微软雅黑

> “思源黑体”和“思源宋体”是Adobe与Google合作推出的开源字体。其设计目标是可以广泛用于多种用途的计算机字体，比如用于手机、平板或者桌面的用户界面、网页浏览或者电子书阅读等，均包含7个字重

> 文泉驿微米黑”是Google Droid的开源衍生字体。Droid字体系是Google包含在著名的开源手机平台Android系统中的默认字体，其中的Droid Sans Fallback包含CJK标准汉字16000余个，是目前所知为数不多的开源中文字体之一（也是继文泉驿正黑之后第二个开源中文黑体）

#### 字体家族其他成员

**font-weight**

关键字：normal 和 bold

属性值：

• 100：文字很细，细如发丝。

• 200：文字很轻，轻如鸿毛。

• 300：文字较轻，轻如飞燕。

• 400：文字正常，等同normal。

• 500：文字不粗不细，不轻不重。

• 600：文字略粗，粗如小腿。

• 700：文字加粗，等同bold。

• 800：文字超粗，粗如大腿。

• 900：文字很重，重如泰山。

> 如果我们在设置属性值时发现文字没啥变化，那就是当前字体不行，可以换一个字体试试 （思源黑体）



#### 具有近似姐妹花属性值的font-style

```css
font-style

italic/oblique 这两个关键字都表示“斜体”
```

italic是使用当前字体的斜体字体，而oblique只是单纯地让文字倾斜。如果当前字体没有对应的斜体字体，则退而求其次，解析为oblique，也就是单纯形状倾斜。



#### font属性

font属性进行文本相关样式的缩写。可以缩写在font属性中的属性非常多，包括font-style、font-variant、font-weight、font-size、line-height、font-family等。完整语法为：

```css
[[font-style] || font-variant || font-weight]? font-size [/ line-hieght]? font-familly ]

? 表示0/1
|| 表示 或

font-size和font-family后面没有问号，也就是说是必需的
```

font-family 不能省略，而且很长，每次写都要带一大串就很不好，有啥技巧避免？

- 方法1

	可以随便找一个系统根本不存在的字体名占位，如字母a，或者特殊一点，用笑脸表情☺，然后再设置font-family:inherit来重置这个占位字体

	```css
	.font {
	    font: 30px/30px '☺';
	    font-family: inherit;
	}
	```

- 利用@font face规则将我们的字体列表重定义为一个字体，这是兼容性很好、效益很高的一种解决方法

**font 关键字**

• icon：包含图标内容所使用的字体，如所有文件夹名称、文件名称、磁盘名称，甚至浏览器窗口标题所使用的字体。

• caption：活动窗口标题栏使用的字体。

• status-bar：窗体状态栏使用的字体。

• small-caption：调色板标题所使用的字体。

• message-box：消息盒里面使用的字体。

• menu：菜单使用的字体，如文件夹菜单。

此外还有很多非标准关键字,略



#### **字体属性的应用价值**

目前，非常多网站的通用font-family直接就是：

```css
html {
	font-family: 'Microsoft YaHei';
}
```

这样一设置，就意味着所有操作系统下的所有浏览器都要使用“微软雅黑”字体。假如说用户的iMac或者macbook因为某些原因安装了“微软雅黑”字体，那岂不是这些系统原本更加漂亮的中文字体就不能使用了？

**设置让字体跟随系统**

```css
/* 三选一设置 */
html { font: menu; }
body { font-size: 16px; }

html { font: small-caption; }
body { font-size: 16px; }

html { font: status-bar; }
body { font-size: 16px; }

```



#### 真正了解@font face规则

> 很多人只要一提到@font face规则，心中就会不由自主“哦”地一声：“这个我知道，可以用来生成自定义字符小图标！”话是没错，问题在于很多人以为生成字符小图标就是@font face规则的全部，实际上这只是其功能之一，一旦真正了解@font face规则，你会发现，@font face规则可以做的事情其实非常多。

**@font face的本质是变量**

> 虽然说CSS3新世界中才出现真正意义上的变量var，但实际上，在CSS世界中已经出现了本质上就是变量的东西，@font face规则就是其中之一。@font face本质上就是一个定义字体或字体集的变量，这个变量不仅仅是简单地自定义字体，还包括字体重命名、默认字体样式设置等。

```css
@font-face {
    font-family: 'example';
    src: url(example.ttf);
    font-style: normal;
    font-weight: normal;
    unicode-range: U+0025-00FF;
}
```

1. **font-family**

	这里的 font-family 可以看成是一个字体变量，名称可以随意设置 如 “$”

2. **src**

	src表示引入的字体资源可以是系统字体，也可以是外链字体。如果是使用系统安装字体，则使用local()【IE9以上支持】功能符；如果是使用外链字体，则使用url()功能符

	示例：

	```css
	@font-face {
	    font-family: ICON;
	    src: url('icon.eot') format('eot');
	    src: url('icon.eot?#iefix') format('embeded-opentype'),
	    	 url ('icon.woff2') format('woff2'),
	    	 url ('icon.woff') format('woff'),
	    	 url ('icon.ttf') format('typetrue'),
	    	 url ('icon.svg#icon') format('svg');
	    font-weight: normal;
	    font-style: normal;
	}
	```

	**出现的格式**

	• svg格式是为了兼容iOS 4.1及其之前的版本，考虑到现如今iOS的版本数已经翻了一番，所以svg格式的兼容代码大可舍弃。

	• eot格式是IE私有的。注意，目前所有版本的IE浏览器都支持eot格式，并不是只有IE6～IE8支持。只是，IE6～IE8仅支持eot这一种字体格式。

	• woff是web open font format几个词的首字母简写，是专门为Web开发而设计的字体格式，显然是优先使用的字体格式，其字体尺寸更小，加载更快。Android 4.4开始全面支持。

	• woff2是比woff尺寸更小的字体，小得非常明显。因此，Web开发第一首选字体就是woff2，只是此字体目前仅Chrome和Firefox支持得比较好。

	• ttf格式作为系统安装字体比较多，Web开发也能用，就是尺寸大了点儿，优点在于老版本Android也支持。

	**综合上面的分析，我们可以得到如下的结论。**

	> （1）svg格式果断舍弃。
	>
	> （2）如果无须兼容IE8浏览器，eot格式果断舍弃。
	>
	> （3）如果无须兼容Android 4.3之前版本手机，ttf格式果断舍弃。

	之所以存在上面的写法，就是因为 **兼容性** 

	**#iefix 有什么用？**

	> 实际上没啥用，真正有用的其实是前面的问号。是这样的，IE9之前的版本解析有一个严重的问题，当src属性包含多个url()时，会把长长的字符当作一个地址解析而返回404错误。因此把eot格式放在第一位，然后在字体文件url地址后加上问号，这样IE9之前的版本会把问号之后的内容当作url的参数。、

	**为什么需要两个src?**

	> 如果是原生的IE7和IE8浏览器，第一个src实际上是多余的，为什么这么讲呢？之所以要放上来，很大一部分原因是为了测试工程师。因为现在测试工程师测试低版本的IE浏览器喜欢使用兼容模式，兼容模式的IE和原生同版本的IE的解析是有区别的，其中区别之一就是兼容模式的IE7和IE8不认识问号（?）解决方案，导致第二个src无法识别，不得已才多了第一行的src。

	**font-weight:normal和font-style:normal是不是多余的？**

	> 如果你没有同字体名的多字体设置，则它就是多余的，至少我在常规项目中删掉这两行CSS没有出现任何异常。

	**format()功能符有什么作用，可不可以省略？**

	> 我的回答是最好不要省略。format()功能符的作用是让浏览器提前知道字体的格式，以决定是否需要加载这个字体，而不是加载完了之后再自动判断。

	例子：

	```css
	@font-face {
	    font-family: ICON;
	    src: url('icon.eot') format('embedded-opentype'),
	        url('icon.ttf');
	}
	```

	> 这种写法只会加载ttf这一种格式字体，因为浏览器提前知道了文件格式是自己无法识别的

	于是乎最上面那种写法可以优化成：

	```css
	@font-face {
	    font-family: ICON;
	    src: url('icon.eot');
	    src: local('😊'),
	    	 url ('icon.woff2') format('woff2'),
	    	 url ('icon.woff') format('woff'),
	    	 url ('icon.ttf');
	}
	```

3. **font-style**

	> 在Chrome浏览器下，@font face规则设置font-style:italic可以让文字倾斜，但是这并不是其作用所在。

	@font face规则中的font-style和font-weight类似，都是用来设置对应字体样式或字重下该使用什么字体。因为有些字体可能会有专门的斜体字体，注意这个斜体字体并不是让文字的形状倾斜，而是专门设计的倾斜的字体，所以很多细节会跟物理上的请求不一样:

	```css
	@font-face {
	    font-family: 'I';
	    font-style: normal;
	    src: local('FZYaoti');
	}
	@font-face {
	    font-family: 'I';
	    font-style: italic;
	    src: local('FZShuTi');
	}
	```

	制定一个字体，名叫’I'，当文字样式正常的时候，字体表现使用“方正姚体”；当文字设置了font-style:italic的时候，字体表现为“方正舒体”。

4. **font-weight**

	> font-weight和font-style类似，只不过它定义了不同字重、使用不同字体

	```css
	@font-face {
	    font-family: 'QH';
	    font-weight: 400;
	    src: local('HYQihei 40S');
	}
	
	@font-face {
	    font-family: 'QH';
	    font-weight: 500;
	    src: local('HYQihei 50S');
	}
	@font-face {
	    font-family: 'QH';
	    font-weight: 600;
	    src: local('HYQihei 60S');
	}
	```

	是一个全新的字体，名为’QH'。当字重font-weight为400的时候，使用“汉仪旗黑40S”字重字体；为500的时候，使用“汉仪旗黑50S”字重字体；为600的时候，使用“汉仪旗黑60S”字重字体。

	[借助font-weight实现响应式图标实例页面](https://demo.cssworld.cn/8/5-1.php)

5. **unicode-range**

	unicode-range的作用是可以让特定的字符或者特定范围的字符使用指定的字体。例如，“微软雅黑”字体的引号左右间隙不均，方向不明，实在是看着不舒服，此时我们就专门指定这两个引号使用其他字体，CSS代码如下：

	```css
	@font-face {
	    font-family: quote;
	    src: local('SimSun');
	    unicode-range: U+201c, U+201d;
	}
	.font {
	    font-family: quote, 'Mircorosoft Yahei';
	}
	```

	

#### @font face与字体图标技术

> 从面向未来的角度讲，字体图标技术的使用会越来越边缘化，因为和SVG图标技术相比，其唯一的优势就是兼容一些老的IE浏览器。等再过几年，IE8等浏览器彻底被淘汰了，我们就没有任何使用字体图标技术的理由了。



**字体的本质是什么?**

> 所谓字体，本质上是字符集和图形的一种映射关系
>
> 一个字体文件就好比一个巨型商品房，里面有很多房间，每个房间都有一个唯一的门牌号，然后这些房间就专门用来挂名画。这里的“门牌号”就是“字符集”，“房间里的名画”就是我们的“字体图形”
>
> 举个例子，“家”这个汉字Unicode编码是5BB6，这个5BB6就是“门牌号”，在中文字体中，这个“门牌号”对应的房间里面的画作就长得是“家”这个肉眼所见的字符形状。也就是说，一个字符编码对应一个形状。

现在如果我们通过一定的手段，把挂5BB6这个“门牌号”房间里面的画作改成一个房屋的形状，那岂不是使用这个字体文件的时候，“家”就不是“家”，而是房子了呢？

字体图标技术就是使用类似的原理实现的，即把通常的字符映射成为另外的图标形状，于是，虽然我们眼睛看到的是个图标，但是实际上它本质上就是一个普通的字符。

```css
@font-face {
	font-family: ICON;
    src: url(icon.eot);
    src: url(icon.eot?#iefix) format('embedded-opentype'),
        url(icon.eot.woff2) format('woff2'),
        url(icon.eot.woff) format('woff');
        
}
.icon {
    font-family: ICON;
}
.icon-microphone:before {
    content: '\1f3a4';
}
```

> 这里，1f3a4就是一个唯一的“门牌号”，在通常的字体下，其字符展示的是一个斜的麦克风。但是，在ICON这个字体中，1f3a4的图形被映射成了一个正立的麦克风图形。

**知道了字体图标技术的原理**，我们就能很好地理解一些渲染现象了。

（1）因为原始字符和最终的图形表现相差很大，所以当我们的字体文件加载缓慢的时候，可以明显看到字符变图形的过程，这种加载体验是不太友好的，字体内联在CSS文件中可以有效避免这一问题，但往往字体文件体积都比较大，这样处理得不偿失。据我所知，除此之外并没有非常好的解决方法。

（2）原始的字符x-height和最终的图形x-height往往是不一样的，这会影响内联元素的垂直对齐，因此很容易出现页面高度闪动情况，这种加载体验也是不友好的。

（3）原始字符的ch宽度，也就是水平占据的宽度和最终的图形也是不一样的，因此很容易出现内联元素水平方向晃动的问题，这种加载体验也是不友好的，为此图标就需要设定具体宽度值

```css
.icon {
    display: inline-block;
    width: 20px;
    text-align: center;
    font-family: ICON;
}
```

**我可不可以把映射字符直接写在页面中，而不是放在：before伪元素中？**

> 从技术实现的角度来讲这是完全可以的，而且不支持伪元素的IE7等浏览器都支持这样做。但是在实际开发的时候，我并不建议这么做，有两点原因：一是不好维护，如果以后字符映射关系改变，而图标HTML是散布在各个页面中的，那么我们的改动就会很麻烦；二是从语义角度考虑，图标字符往往是不包含任何意义的，应该没有必要让搜索引擎知道，也无须让辅助设备读取，而伪元素恰好有这样的功能，如果内联在HTML中，则反而成了一种干扰。



#### 文本的控制



**text-indent与内联元素缩进**

> ，text-indent就是对文本进行缩进控制。但是这种缩进对内容要求比较高，如果段落掺杂英文、数字或者图片等内容，缩进反而可能会给人以参差不齐的感觉，加上现代Web形式更加多样，text-indent在实际项目中的应用已经脱离了它原本的设计初衷。

首先用得比较多的是text-indent负值隐藏文本内容

```html
<h1 class="logo"> CSS世界 </h1>

.logo {
	width: 120px;
	background: url(logo.png);
	text-indent: -120px;
}
```

有些人喜欢设置一个很大的 text-indent 负值：如 -9999em;

> 我是不建议这么做的。首先，这样做在某些设备下有潜在的性能风险，体现在滚屏的时候会发生卡顿；其次，对于一些智能设备的屏幕阅读软件，如VoiceOver，如果内容缩进在屏幕之外，它是不会读取的，这样就降低了页面的无障碍访问能力。另外，text-indent负值缩进在部分浏览器下会影响元素的outline区域，通常需要再设置overflow:hidden。



**如果单看最终的CSS样式效果下面代码是否等同？**

```css
.logo {
    width: 120px;
    text-indent: -120px;
}

.logo {
    width: 120px;
    text-indent: -100%;
}
```

答案是不等同，

text-indent的百分比值是相对于当前元素的“包含块”计算的，而不是当前元素。由于text-indent最终作用的是当前元素里的内联盒子，因此很容易让人误以为text-indent的百分比值是相对于当前元素宽度计算的。【补充：Chrome 72改变了百分比计算策略】

[示例](http://demo.cssworld.cn/8/6-1.php)

[实现百分比居中](https://demo.cssworld.cn/8/6-2.php)

**最后再说几个你可能不知道的小知识**。

> （1）text-indent仅对第一行内联盒子内容有效。
>
> （2）非替换元素以外的display计算值为inline的内联元素设置text-indent值无效，如果计算值是inline-block/inline-table则会生效。因此，如果父级块状元素设置了text-indent属性值，子inline-block/inline-table需要设置text-indent:0重置。
>
> （3）**input**标签按钮text-indent值无效。
>
> （4）**button**标签按钮text-indent值有效，但是存在兼容性差异，IE浏览器理解为单标签，百分比值按照容器计算，而Chrome和Firefox浏览器标签内还有其他Shadow DOM元素，因此百分比值是按照自身的尺寸计算的。
>
> （5）**input**和**textarea**输入框的text-indent在低版本IE浏览器下有兼容问题。



**letter-spacing与字符间距**

> letter-spacing可以用来控制字符之间的间距，这里说的“字符”包括英文字母、汉字以及空格等。

（1）继承性。

（2）默认值是normal而不是0。虽然说正常情况下，normal的计算值就是0，但两者还是有差别的，在有些场景下，letter-spacing会调整normal的计算值以实现更好的版面布局。

（3）支持负值，且值足够大的时候，会让字符形成重叠，甚至反向排列（非IE浏览器)

（4）和text-indent属性一样，无论值多大或多小，第一行一定会保留至少一个字符。letter-spacing还有一个非常有意思的特性就是，在默认的左对齐情况下，无论值如何设置，第一个字符的位置一定是纹丝不动的。

（5）支持小数值，即使0.1px也是支持的，但并不总能看到效果，这与屏幕的密度有关。对普通的桌面显示器，设备像素比是1，最小渲染单位是1px，因此，需要至少连续10个字符，才能看到0.1px产生的1px间距变化，如果是9个字符，则不会有效果，这很可能会让人误以为letter-spacing不支持非常小的数值，实际上是支持的。

（6）暂不支持百分比值。在实际开发的时候，letter-spacing除了控制文字内容排版外，还可以修复一些布局上的问题。例如，清除inline-block列表由于换行符或者空格产生的空白间隙，使我们的布局控制更精准

```css
.box {
    letter-spacing: -1em;
}
.list {
    letter-spacing: 0;
}
```

由于letter-spacing负值的字体重叠特性，我们还可以利用该属性实现一些文本动效

[字符动效分步示意](http://demo.cssworld.cn/8/6-4.php)



**word-spacing与单词间距**

word-spacing和letter-spacing名称类似，其特性也有很多共通之处：

（1）都具有继承性。

（2）默认值都是normal而不是0。通常情况下，两者表现并无差异。

（3）都支持负值，都可以让字符重叠，但是对于inline-block和inline-table元素却存在兼容性差异，Chrome浏览器下可以重叠，IE和Firefox浏览器下则再大的负值也不会重叠，因此不适合使用word-spacing来清除空白间隙。

（4）都支持小数值，如word-spacing:0.5px。

（5）在目前的CSS2.1规范中，并不支持百分比值，但新的草案中新增了对百分值的支持，是根据相对于字符的“步进宽度”（advance width）计算的。这属于新世界内容，本书不做介绍。

（6）间隔算法都会受到text-align:justify两端对齐的影响。

letter-spacing作用于所有字符，但word-spacing仅作用于空格字符。注意，是作用在“空格”上，而不是字面意义上的“单词”。例如，假设有以下CSS和HTML：

```html
.wp {
	word-spacing: 20px;
}
<p class="wp">
    我love前端！
</p>
```

love虽然是单词，但是没有空格，那么word-spacing 就无效

**如何设置CSS，让多按钮的时候中间自动有合适的间距呢？**

```css
.box {
    word-spacing: 20px;
}
```

在IE6时代，这确实是个非常好的方法，但如今下面的做法可能要更合适些：

```css
button + button {
	margin-left: 20px;
}
```



**了解word-break和word-wrap的区别**

> 准确来讲是 了解word-break:break-all和word-wrap:break-word的区别

word-break属性：

• normal：使用默认的换行规则。

• break-all：允许任意非CJK（Chinese/Japanese/Korean）文本间的单词断行。

• keep-all：不允许CJK文本中的单词换行，只能在半角空格或连字符处换行。非CJK文本的行为实际上和normal一致。【此属性兼容性不咋好】

word-wrap属性：

• normal：就是大家平常见得最多的正常的换行规则。

• break-word：一行单词中实在没有其他靠谱的换行点的时候换行。

> 它之前由于和word-break长得太像，难免会让人记不住或搞混，于是在CSS3规范里，这个属性的名称被修改了，叫作overflow-wrap 【兼容性可能不佳】

[那两者的区别是什么呢？](http://demo.cssworld.cn/8/6-5.php)

> word-break:break-all的作用是所有的都换行，毫不留情，一点儿空隙都不放过，而word-wrap:break-word则带有怜悯之心，如果这一行文字有可以换行的点，如空格或CJK（中文/日文/韩文）之类的，就不打英文单词或字符的主意了，在这些换行点换行，至于对不对齐、好不好看则不关心，因此，很容易出现一片一片空白区域的情况。



**white-space与换行和空格的控制**

> white-space属性声明了如何处理元素内的空白字符，这类空白字符包括Space（空格）键、Enter（回车）键、Tab（制表符）键产生的空白。因此，white-space可以决定图文内容是否在一行显示（回车空格是否生效），是否显示大段连续空白（空格是否生效）等。

属性值:

• normal：合并空白字符和换行符。

• pre：空白字符不合并，并且内容只在有换行符的地方换行。

• nowrap：该值和normal一样会合并空白字符，但不允许文本环绕。

• pre-wrap：空白字符不合并，并且内容只在有换行符的地方换行，同时允许文本环绕。

• pre-line：合并空白字符，但只在有换行符的地方换行，允许文本环绕。

![image-20221019154123966](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221019154123966.png)



**text-align与元素对齐 **

> 因为CSS是母语为英语的人发明的，所以在早期的时候，对中文或其他东亚语言并没有考虑得那么细致，从text-align:justify的表现上就可以窥见一斑。例如，IE浏览器（至少到IE11）到目前为止使用text-align:justify都无法让中文两端对齐，而Chrome、Firefox和Safari等浏览器都是可以的。
>
> 不过，好在IE有一个私有的CSS属性text-justify（目前也写入规范草案了）可以实现中文两端对齐的。

通过下面的CSS代码组合就可以实现全部浏览器都兼容的中文两端对齐

```css
.justify {
    text-align: justify;
    text-justify: inter-ideograph;
}
```

其中，属性值inter-ideograph的字面意思是“国际象形文字”，非官方非标准，可以放心使用，不用担心以后其他浏览器也支持之后出现新旧渲染不一致的问题。

[中文的两端对齐效果实现实例页面](http://demo.cssworld.cn/8/6-7.php)

CSS世界中有一个text-align-last属性，可以规定最后一行内联内容的排列方式，这是从IE浏览器过来的

```css
.justify {
    text-align-last: justify;
}
```

> 好一个及时雨！然而可惜，Safari浏览器，包括Safari 10，都不支持，以至于移动端和桌面端都不能使用，甚是遗憾。

[两端对齐案例](http://demo.cssworld.cn/8/6-8.php)



**如何解决text-decoration下划线和文本重叠的问题**

> 如果对细节要求较高，就会发现，下划线经常会和中文文字的下边缘粘连在一起，英文的话甚至直接穿过，越看越有心痛的感觉。

最佳实践就是使用 border 模拟下划线。另外，使用border-bottom模拟下划线的时候，border-color最好省略，这样就会使用文字的color颜色作为边框色，鼠标hover的时候，下划线会自动和文字一起变色

text-decoration还支持同时设置多个属性

```css
a {
    text-decoration: underline overline;
}
```



**一本万利的text-transform字符大小写**

> text-transform也是为英文字符设计的，要么全大写text-transform:uppercase，要么全小写text-transform:lowercase，似乎没什么值得挖掘的，但有一些场景使用它却会有一本万利的效果。

1. 身份证输入

	我国的身份证最后一位有可能是字母X，且各种场合都是指定必须大写。如果我们给输入身份证的 **input** 输入框设置

	```css
	input {
	    text-transform: uppercase;
	}
	```

	就算我们敲小写x,出现的也是大写

2. 验证码输入

	如果验证码不区分大小写，那么设置全转大写起步爽歪歪



#### **first-letter/:first-line伪元素**

> 很多年前，Chrome浏览器和IE9浏览器还未出现，那时候first-letter叫伪类选择器，写法是前面加一个冒号，如：first-letter。那时候的语义要更直白一些，选择第一个字符，然后设置一些样式。后来，伪类和伪元素被划分得更加明确和规范了，::after、::before、::backdrop、::first-letter、::first-line、::selection等是伪元素，:active、:focus、:checked等被称为伪类，这就导致：:first-letter的语义发生了一些变化—首字符作为元素的假想子元素。



::first-letter**伪元素生效的前提**

> 首先，元素的display计算值必须是block、inline-block、list-item、table-cell或者table-caption，其他所有display计算值都没有用，包括display:table和display:flex等。

**此外，不是所有的字符都能单独作为：:first-letter伪元素存在的。什么意思呢？我们看一个简单的例子，CSS和HTML代码如下：**

```html
p:first-letter { color: silver; }
<p>？？？？？？？</p>
```



按照我们的设想，第一个问号是银色，但实际上全部都是

> 为什么呢？这是因为常见的标点符号、各类括号和引号在：:first-letter伪元素眼中全部都是“辅助类”字符，有点儿买东西送赠品的感觉，但是赠品本身却不能购买，这里的问号“? ”就属于赠品

```html
p:first-letter { color: silver; }
<p>？？？？？？？辅助</p>
```

这次我们发现，"？？？？？？？辅" 变成了银色。这就是**赠品**的表现

> 有人可能会有疑问：那到底哪些字符属于“赠品”，哪些属于“商品”呢？我特意做了测试，总结下来就是，“赠品字符”包括·@#%&＊()（）[]【】{}::"""; ; '''》《, , .。? ? ! ! …＊、/\。
>
> 正常情况下可以直接作为伪元素的字符就是数字、英文字母、中文、$、一些运算符，以及非常容易被忽视的空格等。这里的“空格”有必要再加粗强调一下，因为它的确是很容易被忽视的一个字符。

> 最后说明一点，字符前面不能有图片或者inline-block/inline-table之类的元素存在

如下：会导致不生效

```html
p:first-letter {color: silver;}
<p><i style="display:inline-block;"></i>银色</p>
```

> **::before伪元素也参与：:first-letter伪元素**

```html
p:before {
	content: '新闻';
}
p:first-letter {
	color: silver;
}
<p>这是新闻的标题......</p>
```

结果“新”变成了银色,包括IE8在内的浏览器都是这样的表现。



**::first-letter伪元素可以生效的CSS属性**

• 所有字体相关属性：font、font-style、font-variant、font-weight、font-size、line-height和font-family。

• 所有背景相关属性：background-color、background-image、background-position、background-repeat、background-size和background-attachment。

• 所有margin相关属性：margin、margin-top、margin-right、margin-bottom和margin-left。

• 所有padding相关属性：padding、padding-top、padding-right、padding-bottom和padding-left。

• 所有border相关属性：缩写的border、border-style、border-color、border-width和普通书写的属性。

• color属性。

• text-decoration、text-transform、letter-spacing、word-spacing（合适情境下）、line-height、float和vertical-align（只有当float为none的时候）等属性。

> 因此，如果妄图使用visibility:hidden或者display:none隐藏：:first-letter伪元素，还是省省吧。



**::first-letter伪元素的一些有意思的特点**

1. 支持部分display属性值标签嵌套。::first-letter伪元素获取可以跨标签，也就是不仅能选择匿名内联盒子，还能透过层层标签进行选择，但是也有一些限制，并不是所有标签嵌套都是有用的。

	> display值如果是inline、block、table、table-row、table-caption、table-cell、list-item都是可以的，但是不能是inline-block和inline-table，否则：:first-letter伪元素会直接无效；而display:flex则改变了规则，直接选择了下一行的字符内容。

	[眼见为实](http://demo.cssworld.cn/8/7-1.php)

2. 颜色等权重总是多了一层。

	这是非常容易犯的一个错误，也是CSS世界十大不易理解问题之一。例如，下面这个问题是某同行发邮件问我的，我简单编辑了一下：

	```html
	p:first-letter {
	    color: red;
	}
	p > span {
		color: blue!important;
	}
	
	<p><span>第一个</span>字符看看会不会变红？</p>
	```

	请问“第”这个字符的颜色是什么？

> 基本上，超过95%的前端人员会认为是blue，因为大家都是从CSS选择器权重的角度去考虑的。这个答案本身没问题，但是却忽略了很重要的一点，::first-letter伪元素其实是作为子元素存在的，或者说应当看出是子元素，于是就很好理解了。对于类似color这样的继承属性，子元素的CSS设置一定比父元素的级别要高，哪怕父级使用了重量级的！important，因为子元素会先继承，然后再应用自身设置。因此，上面CSS和HTML代码的最终结果是，第一个字符“第”字的颜色是red，红色！这就是：:first-letter伪元素的另外一个重要特性—颜色等权重总是多了一层。



**::first-letter实际应用举例**

- 电商产品经常会有价格，价格前面一般都有一个¥符号，这个符号字体往往会比较特殊，字号也比较大，同时和文字的数值有几像素的距离

	```html
	.price:first-letter {
		maargin-right: 5px;
		font-size: xx-large;
		...
	}
	<p>
	    ￥399
	</p>
	```

	

	

**故事相对较少的：first-line伪元素**

> 它们长得类似，很多特性也类似。但是相比之下，:first-line的故事要少一些，没有“赠品字符”之类的梗存在。

• :first-line和：first-letter伪元素一样，IE9及以上版本浏览器支持双冒号：:first-line{}写法，IE8浏览器只认识单冒号写法。

• :first-line和：first-letter伪元素一样，只能作用在块级元素上，也就是display为block、inline-block、list-item、table-cell或者table-caption的元素设置：first-line才有效，table、flex之类都是无效的。

• :first-line和：first-letter伪元素一样，仅支持部分CSS属性，例如：

所有字体相关属性；

- color属性；

- 所有背景相关属性；

- text-decoration、text-transfor、letter-spacing、word-spacing、line-height和vertical-align等属性。



• :first-line和：first-letter伪元素一样，color等继承属性的权重总是多了一层，毕竟称为“伪元素”，就好像里面还有个子元素。如果：first-line和：first-letter同时设置颜色，:first-letter级别比：first-line高，即使：first-line写在后面，甚至加！important（如果浏览器支持）也是如此。

• :first-line和：first-letter伪元素一样，也支持标签嵌套，但是具体细则和：first-letter出入较大，例如，它不支持table相关属性等。

[眼见为实](http://demo.cssworld.cn/8/7-2.php)

> 在Chrome 中inline-block 元素会阻断 first-line



### 元素装饰与美化



#### 少得可怜的颜色关键字

> CSS1的时候只支持16个基本颜色关键字
>
> CSS2的时候，显然应该要新增一些颜色关键字。或许很多人满怀希望，总以为会有什么惊喜，结果果然很惊喜：居然只加入了一个颜色—橙色orange。
>
> 到了CSS3的时候，以为又会是“雷声大，雨点小”，结果这次却出人意料，一下子增加了100多个颜色关键字
>
> 到了CSS4的时候，以为又会有什么惊人之举，结果又仅增加了一个颜色关键字—rebeccapurple
>
> [更多](http://www.zhangxinxu.com/wordpress/? p=4859)

> currentColor变量是个好东西，可以使用当前color计算值，即所谓颜色值。
>
> IE9+浏览器才支持它。

####  CSS世界的background很单调

> CSS世界中的background大部分有意思的内容都是在CSS3新世界中才出现的，如Multiple backgrounds（多背景）、background-size（背景尺寸）、background-origin（背景初始定位盒子）、background-clip（背景剪切盒子）等。

background相关属性的集合，包括：

• background-image: none

• background-position: 0% 0%

• background-repeat: repeat

• background-attachment: scroll

• background-color: transparent

如果是IE9+浏览器，则还包括：

• background-size: auto auto

• background-origin: padding-box

• background-clip: border-box



**隐藏元素的background-image到底加不加载?**

> 根据我的测试，一个元素如果display计算值为none，在IE浏览器下（IE8～IE11，更高版本不确定）依然会发送图片请求，Firefox浏览器不会，至于Chrome和Safari浏览器则似乎更加智能一点：如果隐藏元素同时又设置了background-image，则图片依然会去加载；如果是父元素的display计算值为none，则背景图不会请求，此时浏览器或许放心地认为这个背景图暂时是不会使用的。

> 如果想用background-image实现鼠标光标经过变换图片的效果（例如，灰色的关闭图片鼠标光标经过变成深色），则务必将这两张图片合并在一张图片上，除了减少请求外，这样做更重要的好处是交互体验更好了。如果图片不合在一起，当鼠标光标经过的时候，就会去请求另外一张图片的地址，如果这个图片没有被缓存，则请求发出去到图片显示是有一段时间的，很容易出现鼠标光标经过关闭图片，结果图片消失的情况，实际上图片不是消失了，而是还在请求的路上。



**与众不同的background-position百分比计算方式**

![image-20221020103129977](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221020103129977.png)

IE8浏览器最多只支持同时出现2个值，从IE9开始支持同时出现3个值或4个值，**作用是指定定位的偏移计算从哪个方位算起**

```css
background-position: right 40px bottom 20px;
```

表示距离右边缘40像素，距离底边缘20像素

**position值也支持百分比值**

position值的百分比值有着特殊的计算公式：

```js
position = (容器的宽 - 图片的宽) * percentX

position = (容器的高 - 图片的高) * percentY
```

因此，当background-position:100% 100%时候，实际定位值就是容器尺寸和图片尺寸的差，于是就有了右下角所示的定位效果。

**当我们设置百分比负值时**

```css
background-position: -50% -50%;
```

结果他的效果类似于

```css
background-position: 40px 10px;
```

> 深受传统百分比定位迷惑的我们可能一时会想不通，明明是个负值百分比定位，怎么会是一个正值效果呢？
>
> 我们套用 **position** 百分比值计算公式，就豁然开朗了！此案例中容器尺寸是160×160，图片尺寸是256×192，图片尺寸大于容器尺寸，所以：
>
> •（容器的宽度-图片的宽度）×（-50%）的结果是个正值；
>
> •（容器的高度-图片的高度）×（-50%）的结果也是个正值。
>
> 因此，最终的表现并不是图片定位在容器外，而是定位在容器内。



** background-repeat与渲染性能**

> background-repeat支持repeat、repeat-x、repeat-y这几个值，语义清晰，兼容性好，没什么有趣的故事。background-repeat以前用得很多，但如今设计趋势是喜欢扁平和纯色，其使用频率下降明显，反倒是在实现一些复杂纹理或者配合“蝉原则”实现随机背景这些比较新潮的地方见到的比较多。

关于 background-repeat 性能，

```css
.overlay {
	background: url('alpha.png');
	background: rgba(0,0,0, .75);
}
```

> 有些人为了追求极致，把 alpha.png 做成了1X1像素大小，，但是遮罩背景出现的时候会有明显的卡顿，体验非常不好。究其原因，就是平铺图片尺寸太小，平铺次数太多，渲染太吃力，其实我们大可把背景图保存成100像素×100像素大小，这样一来，图片尺寸并没有大多少，但是渲染性能却有明显提升。

**外强中干的background-attachment:fixed**

> 在CSS世界中，background-attachment支持scroll和fixed两个属性值，其中scroll是默认值，就是平常使用背景图的效果表现；fixed表示背景相对于当前文档视区定位，也就是页面再怎么滚动背景图片位置依旧纹丝不动，稳若泰山。
>
> 听上去，background-attachmen:fixed应该和position:fixed声明一样，是个很厉害的角色才对。但实际上其外强中干，在某些场合它确实很厉害，但是局限太大，没法实用。

- 要实现一个图片局部动态模糊效果

	```css
	.box {
		width: 256px; hieght: 1192px;
	    background-image: url(1.jpg);
	    background-attachment: fixed;
	    position: relative;
	    overflow: hidden;
	}
	.box > .drag {
	    width: 100px; height: 100px;
	    background: inherit;
	    filter: blur(5px);
	    cursor: grap;
	    position: absolute;
	}
	```

	> drag元素的background继承于父元素．box，同样的背景图，同样的background-attachment: fixed锁定，根本就不需要JavaScript动态改变background-position位置，因为元素都是相对于文档视区定位的。这看似美好，却有一个很大的局限性，那就是页面的高度不能超过一屏，因为页面一旦可以滚动，效果就完全被毁掉，背景图立马就显示不全了，因为元素被滚走了，但是背景图还是固定的。
	>
	> 而绝大多数网页都是可滚动的，这就让background-attachment:fixed只能局限于窗体背景图的使用上。当然，也有不太好的支持方法，就是background-attachment:fixed交互做出一个独立的小页面，然后主页面使用一个小的iframe嵌套。
	>
	> IE9及以上版本浏览器新增了一个background- attachment属性值local，难道它就是用来解决上面的“不合乎预期”的现象的？不好意思，你想多了，它们不是一路的。local的作用是，如果．box元素可以滚动，则．box元素的背景图也可以被滚走，默认的scroll值是固定的，和fixed无半点儿关系。

**background-color背景色永远是最低的**

> 什么意思呢？就是background无论是单背景图还是多背景图，背景色一定是在最底下的位置。
>
> 知道这一特性有什么作用呢？为了及时准确反馈用户的点击行为，我们会在链接或按钮元素上增加：active样式，通常的思路是：active时变换一下背景色，但是这样有一个很大的问题，即每个按钮的背景色都是不一样的，那岂不是要写很多个：active样式？有没有什么整站通用的简单代码呢？当然有。我们可以试试使用背景图片代替背景色

```CSS
a[href]:active, button:active {
    background-image: linear-gradient(to top, rgba(0,0,0, .05), rgba(0,0,0, .05))
}
```

因为背景色一定是在最底下的位置，所以这里的background-image一定是覆盖在按钮等元素背景色之上的，不会影响按钮原来的背景色。

如果是桌面端Web项目，需要兼容IE8和IE9浏览器，我的建议是使用一个同等效果的PNG图片代替，具体代码如下：

```css
a[href]:active, button:active {
    /*IE8,IE9*/
    background-image: url(xxx);
    /*IE10+*/
    background-image: linear-gradient(to top, rgba(0,0,0, .05), rgba(0,0,0, .05))
}
```



**利用多背景的属性hack小技巧**

> 虽然IE8浏览器并不支持多背景，但是并不表示IE8浏览器和多背景效果无缘。因为IE8浏览器支持：before和：after两个伪元素，所以配合z-index负值，我们可以实现最多3个图片的多背景效果，对绝大部分的需求来说足够了。



**IE9浏览器不支持背**

> IE9浏览器不支持背景渐变，不过，也是有手段可以做兼容的，那就是使用IE私有的渐变滤镜。例如，一个红蓝渐变，可以使用下面的代码：

```css
filter: progid: DXImageTransform.Micorosoft.gradient(startcolorstr=red, encolorstr=blue, gradientType=1);
```

> 这行滤镜代码主要有3个参数，依次是startcolorstr、endcolorstr和gradient Type。其中gradientType=1代表横向渐变，gradientType=0代表纵向淅渐变，startcolorstr代表渐变起始的色彩。除了使用颜色关键字，还可以使用十六进制颜色表示法，如startcolorstr=#FF0000; endcolorstr代表渐变结尾的色彩，也支持十六进制颜色表示法，如endcolorstr=#0000FF。

> 想要渐变半透明怎么办？可以使用IE浏览器的8字符的十六进制颜色表示法，其格式为#AARRGGBB, AA、RR、GG、BB均为十六进制正整数，取值范围为00～FF。RR指定红色值，GG指定绿色值，BB指定蓝色值，AA指定透明度。00表示完全透明，FF表示完全不透明。超出取值范围的值将被恢复为默认值。例如，渐变起始红色可以写成startcolorstr=#FFFF0000。

有些人并不清楚如何将0～1的CSS3标准透明度值转换成十六进制。事实上，可以这样处理：打开浏览器控制台，假设需要转换的透明度是opacity，则可以输入下面的代码再回车：

```js
Math.round(256*opacity).toString(16);
```

综上所述，要想实现一个100%红色到50%透明度蓝色垂直渐变，可以使用如下代码组合：

```css
.gradient {
    filter: progid:DXImageTransform.Micorosoft.gradient(startsolorstr=#FFFF0000, endcolorstr=#7F0000FF, gradientType=0);
    background: linear-gradient(to bottom, red, rgba(0,0,255, .5))
}

:root .bgcolor {
    filter: none;
}
```

在IE9浏览器下，rgba半透明和filter渐变会同时起作用，因此使用：root选择器重置了一下。



### 元素的显示与隐藏

> 使用CSS让元素不可见的方法很多，剪裁、定位到屏幕外、明度变化等都是可以的。虽然它们都是肉眼不可见，但背后却在多个维度上都有差别。

**总结的一些比较好的隐藏实践。**

- 如果希望元素不可见，同时不占据空间，辅助设备无法访问，同时不渲染，可以使用 **script** 标签隐藏。

	```html
	<script type="text/html">
		<img src="1.jpg" />
	</script>
	```

	此时图片是不会有请求的，如果希望在 **script** 标签中再放置其他不渲染的模板内容，可以试试 **textarea** 元素

	```html
	<script type="text/html">
		<img src="1.jpg" />
		<textarea style="display:none;">
			<img src="2.jpg" />
		</textarea>
	</script>
	```

	图片2.jpg也是不会有请求的。另外， **script**  标签隐藏内容获取使用script.innerHTML, **textarea** 使用textarea.value。

- 如果希望元素不可见，同时不占据空间，辅助设备无法访问，但资源有加载，DOM可访问，则可以直接使用display:none隐藏

- 如果希望元素不可见，同时不占据空间，辅助设备无法访问，但显隐的时候可以有transition淡入淡出效果，则可以使用

	```css
	.hidden {
	    position: absolute;
	    visibility: hidden;
	}
	```

- 如果希望元素不可见，不能点击，辅助设备无法访问，但占据空间保留，则可以使用visibility:hidden隐藏

- 如果希望元素不可见，不能点击，不占据空间，但键盘可访问，则可以使用clip剪裁隐藏

	```css
	.clip {
	    position: absolute;
	    clip: rect(0 0 0 0);
	}
	.out {
	    position: relative;
	    left: -999em;
	}
	```

- 如果希望元素不可见，不能点击，但占据空间，且键盘可访问，则可以试试relative隐藏。例如，如果条件允许，也就是和层叠上下文之间存在设置了背景色的父元素，则也可以使用更友好的z-index负值隐藏。例如：

	```css
	.lover {
	    position: relative;
	    z-index: -1;
	}
	```

- 如果希望元素不可见，但可以点击，而且不占据空间，则可以使用透明度。

	```css
	.opacity {
	    position: absolute;
	    opacity: 0;
	    filter: Alpha(opcity=0);
	}
	```

- 如果单纯希望元素看不见，但位置保留，依然可以点可以选，则直接让透明度为0。

	```css
	.opacity {
	    opacity: 0;
	    filter: Alpha(opcity=0);
	}
	```

	

> 实际开发场景千变万化，上面罗列的实践不足以覆盖全部情形。例如，在标签受限的情况下希望隐藏某文字，可能使用text-indent缩进是最友好的方法。如果希望显示的时候可以加一个transition动画，就可能要使用max-height进行隐藏了

```css
.hidden {
    max-height: 0;
    overflow: hidden;
}
```



> 在Firefox浏览器下，display:none的元素的background-image图片是不加载的，包括父元素display:none也是如此；如果是Chrome和Safari浏览器，则要分情况，若父元素display:none，图片不加载，若本身背景图所在元素隐藏，则图片依旧会去加载；对IE浏览器而言，无论怎样都会请求图片资源。

```html
.bg1 {
    background: url(1.png);
}
.bg2 {
    background: url(2.png);
}

<div hidden class="bgl"></div>
<div hidden >
   <div class="bg2"></div> 
</div>
```

> 我们发现只加载了1.png，因此，在实际开发的时候，如头图轮播切换效果，那些默认需要隐藏的图片作为背景图藏在隐藏元素的子元素上，微小的改动就可以明显提升页面的加载体验，可以说是非常实用的小技巧。

还有一些属性也是天然display:none的。例如，hidden类型的 **input 输入框

```css
<input type="hidden" name="id" value="1">
```

HTML5中新增了hidden这个布尔属性，可以让元素天生display:none隐藏。例如：

```html
<div hidden>
    看不见我
</div>
```

IE11以及其他现代浏览器都支持它，因此，如果要兼容桌面端，需要如下CSS设置：

```css
[hidden] {
	display: none;	
}
```



#### visibility与元素的显隐

> 有一些人简单地认为display:none和visibility:hidden两个隐藏的区别就在于：display:none隐藏后的元素不占据任何空间，而visibility:hidden隐藏的元素空间依旧保留。实际上并没有这么简单，visibility是一个非常有故事的属性。

1. visibility的继承性

	首先，它最有意思的一个特点就是继承性。父元素设置visibility:hidden，子元素也会看不见，究其原因是继承性，子元素继承了visibility:hidden，但是，如果子元素设置了visibility:visible，则子元素又会显示出来。这个和display隐藏有着质的区别。

	[眼见为实](http://demo.cssworld.cn/10/2-1.php)

2. visibility与CSS计数器

	visibility:hidden不会影响计数器的计数，这和display:none完全不一样

	> visibility:hidden虽然让其中一个列表不可见了，但是其计数效果依然在运行。相比之下，设置display:none的列表就完全没有参与计数运算。

3. visibility与transition

	下面的CSS是会让．box元素hover时显示．target子元素，但不会有过渡效果：

	```css
	.box > .target {
	    display: none;
	    position: absolute;
	    opacity: 0;
	    transition: opacity .25s;
	}
	
	.box:hover > .target {
	    display: block;
	    opacity: 1;
	}
	
	```

	但是，下面的CSS语句却可以让．target子元素有淡出的过渡效果：

	```css
	.box > .target {
	    position: absolute;
	    opacity: 0;
	    transition: opacity .25s;
	    visibililty: hidden;
	}
	
	.box:hover > .target {
	    visibililty: visible;
	    opacity: 1;
	}
	
	```

	这是为什么呢？因为CSS3 transition支持的CSS属性中有visibility，但是并没有display。

	hover 列表操作出现下拉，鼠标移入第二行，但因为经过第一行导致第一行出现下拉导致第二行触发不了，这种现象。

	[visibility与内容hover的延时显示实例页面](http://demo.cssworld.cn/10/2-3.php)



### 用户界面样式



#### 和border形似的outline属性

> outline表示元素的轮廓，语法和border属性非常类似，分宽度、类型和颜色，支持的关键字和属性值与border属性一模一样

Highlight是系统高亮色，这里用来模拟IE和Firefox浏览器的outline效果相当合适

```css
:focus + label.btn {
	outline: 1px dotted Hghlight;
    outline: 5px auto -webkit-focus-ring-color;
}
```



**真正的不占据空间的outline及其应用**

> 内联元素的上下padding值似乎不占据任何空间，但是一旦祖先元素overflow计算值不是visible，同时padding值足够大，滚动条就会出现，暴露出“不占据空间”其实是一个假象。但是outline属性确实不占据任何空间，轮廓宽度设置得再宽广，也不会影响任何元素的任何布局，并且outline轮廓是可穿透的。考虑到outline是一个从IE8开始就被支持的CSS属性，这就注定了outline要脱离其设计初衷，在其他方面大显神通，例如，用于实现一些看似棘手的布局效果。

**头像裁剪时，只有选择范围清晰，其他区域黑色透明背景**

```css
.crop {
    overflow: hidden;
}
.crop > .crop-area {
    width: 80px; height: 80px;
    outline: 256px solid rgba(0,0,0,.5);
    background: url(about:blank);
    background: linear-gradient(to top, transparent, transparent);
    filter: alpha(opacity=50);
    cursor: move;
}
:root .crop-area {
    filter: none;
}
```

用一句话概括就是使用一个大大的 outline 来实现周围半透明黑色遮罩。因为 outline 宽度设置再大，也不会对布局产生任何影响，至于超出的区域，通过容器overflow:hidden 隐藏就可以了。没错，原理就是这么简单。



**自动填满屏幕剩余空间的应用技巧**

页面内容不足一屏中间高度是由内容撑开的。所以导致底部留白的现象

如何让底部背景色正好填满剩余屏幕区域呢？目前我知道的最好的办法就是巧用outline属性。假设底部HTML代码是这样的：

```html
<div class="footer">
    <p>
        Designed &amp; Powered by zhangxinxu
    </p>
</div>
```

```css
.footer {
    height: 50px;
}
.footer > p {
    position: absolute;
    left: 0; right: 0;
    text-align: center;
    padding: 15px 0;
    background-color: #a0b3d6;
    outline: 999px solid #a0b3d6;
    clip: rect(0 9999px 9999px 0);
}
```



#### 光标属性cursor

> cursor属性值几乎可以认为是当下支持的关键字属性值最多的CSS属性，没有之一。

面就是按照功能特性对其进行的分类以及具体解释描述。

1. 常规

	- cursor:auto:cursor默认值。

		> auto表示光标形状根据内容类别浏览器自动进行处理。例如，输入框里面光标表现为cursor:text，带href属性的链接表现为cursor:pointer，而原生的 **button** 表现为cursor:default等。

	

	

	- cursor:default：系统默认光标形状

		> 于是，久而久之，大家就约定俗成，所有链接和按钮都使用手形。以至于发展到现在，使用原生的<button>按钮甚至下拉框的时候，都要设置一个cursor:pointer。这种奇怪的发展史真是比小说还精彩

	- cursor:none：这个声明非常有意思，可以让光标隐藏不见。

		> 看视频的时候，尤其全屏看视频的时候。此时鼠标一直在界面上晃着，是很碍眼、很难受的。一般可以这么处理：如果鼠标静止3秒不动，就设置页面或视频元素cursor:none隐藏光标，如果有mousemove行为，再显示即可！

		

2. 链接和状态

	- cursor:pointer：光标表现为一只伸出食指的手，类似这样

	- cursor:help：帮助，通常是光标头上带了个问号

	- cursor:progress：表示进行中的意思。从语义上讲，其适合loading处理

		> 有一个场景却非常适合使用cursor:progress，那就是页面加载的时候。如今进行Web开发，没有JavaScript几乎寸步难行，而JavaScript加载完毕是需要一定时间的，网络不好的时候，这个加载时间延迟可能会非常明显，于是用户就会遇到明明界面已经呈现了，但是点击“展开更多”按钮却没有任何反应，原因就是JavaScript还没有完全加载完毕。此时就非常适合cursor:progress出马了，我们默认在<body>标签上设置：

		```css
		body {
		    cursor: progreess;
		}
		```

		js加载完毕可再设置为 auto

	-  cursor:wait：我们先看看光标形状，可能是[插图]这样的转圈圈，或者是沙漏或者是表，总之和电脑死机时候的光标是一样的。

	- cursor:context-menu:cursor:context-menu兼容性比较复杂，Mac OS X和Linux系统下的Chrome和Firefox浏览器是支持的，但是Windows系统下的Chrome和Firefox浏览器却不支持。

3. 选择

	- cursor:text：潜台词是文字可被选中，形状类似。默认文本字符或者可输入的单复选框的光标就表现成这样，因为文字可以被选中；反过来，如果文字是不能被选中的，光标就不应该是cursor:text。
	- cursor:vertical-text：潜台词是文字可以垂直选中，形状类似 |——|
	- cursor:crosshair：十字光标
	- cursor:cell:cursor:cell中的cell和display:table-cell中的cell其实可以看成是同一个东西，也就是单元格

4. 拖拽

	- cursor:move：光标变成cursor:move，往往就意味着当前元素是可以移动的
	- cursor:copy：光标变成cursor:copy，往往就意味着当前元素是可以被复制的
	- cursor:alias：光标变成cursor:copy，往往就意味着当前元素是可以创建别名或者快捷方式的
	- cursor:no-drop：光标变成cursor:copy，往往就意味着当前元素放开到当前位置是不允许的
	- cursor:not-allowed：光标变成cursor:not-allowed，往往就意味着当前行为是禁止的，形状类似 🚫

5. 滚动

	- cursor:all-scroll：表示上下左右都可以滚动，但有一个很糟糕的问题：Windows系统下光标表现和cursor:move一样。再考虑到本身作用场景局限，我觉得可以忽略此声明。

6. 拉伸

	- cursor:col-resize：光标形状类似 <-||-> 它适用于移动垂直线条
	- cursor:row-resize：光标形状类似 上面的旋转90° 。它适用于移动水平线条
	- 单向拉伸：总共8个方位8个不同的关键字属性值
	- 双向拉伸：总共4个对立方位组合

7. 缩放

	- cursor:zoom-in：光标形似放大镜
	- cursor:zoom-out：光标形似缩小镜

8. 抓取

	- cursor:grab：光标是一个五指张开的手
	- cursor:grabbing：光标是一个五指收起的手



**自定义光标**

> 从IE6开始，我们就可以自定义网页中的光标样式，因此，对于cursor属性，兼容性都不是问题。例如，IE8不支持上面提到的cursor:none，就是通过自定义手段实现兼容的

```css
.cur-none {
    cursor: url(transparent.cur);
}
```

对于Chrome等浏览器，可以直接使用PNG图片作为光标，但是IE浏览器不行。IE仅支持专门的．cur格式的光标文件.



### 流向的改变

> 至少在我接触的这么多项目里，没有见到有谁使用过CSS的direction属性。为什么呢？是因为direction长得丑吗？虽然说direction确实其貌不扬，但是CSS世界不会有这样的歧视。那是因为兼容性吗？更不是了，direction早在IE6时代就已经被支持了
>
> 那究竟是什么原因呢？
>
> 我认为多半是因为宣传不够。要是所有前端人能够人手一本这本书，自然就不会有这样的问题了，因为本书热衷于挖掘CSS属性的潜力，可以让那些默默无闻但有能力的CSS属性熠熠生辉、焕发青春。direction就是一个典型，该属性简单且好记，属性值少，兼容性好，关键时候省心省力。

#### **direction简介**

基本上只要关心这两个属性

```css
direction: ltr;// 左到右
direction: rtl// 右到左
```

direction属性默认有这么一个特性，即可以改变替换元素或者inline-block/inline-table元素的水平呈现顺序

[举个栗子](https://demo.cssworld.cn/12/1-1.php)

通常，我们让单行文字溢出用点显示，这个点通常都是在右边的，省略的都是最后的文字，配合direction属性，我们可以让这个点打在开头，让前面的文字省略

```html
.ell {
	width: 240px;
    white-space: nowrap;
    text-overflow: ellipsis;
    text-ovverflow: hidden;
}

<P class="ell" dir="ltr">
    开头，中间，结束
</P>
<P class="ell" dir="rtl">
    开头，中间，结束
</P>

```

[示例](https://demo.cssworld.cn/12/1-2.php)

direction属性还可以轻松改变表格中列的呈现顺序,将表头位置反向



directionL:rtl还可以让text-justify两端对齐元素，最后一行落单的元素右对齐显示

[示例](https://demo.cssworld.cn/12/1-3.php)



**direction的黄金搭档unicode-bidi**

> direction属性似乎只能改变图片或者按钮的呈现顺序，但对纯字符内容（尤其中文）好像并没有什么效果，但实际上，我们也是可以指定中文每个字符都反向呈现的，方法就是借助direction的搭档属性unicode-bidi。

> unicode-bidi
>
> 中文和英文夹杂，或者阿拉伯文和英文夹杂，此时就会出现文本阅读方向不一样的情况，阿拉伯文是从右往左读，英文是从左往右，而这种混合方向同时出现的现象就称为“双向性”，因此unicode-bidi作用就是明确字符出现“双向性”时应当有的表现。

```css
unicode-bidi: normal
unicode-bidi: embed
unicode-bidi: bidi-override
```

- normal：正常排列。假设设置了direction:rtl，则图片、按钮以及问号、加号之类的字符会从右往左显示，但是中文、英文字符还是从左往右显示。
- embed:embed属性值要想起作用，只能作用在内联元素上。在通常情况下，embed属性值的表现和normal是一样的，导致很多人不明白embed到底和normal有什么区别。其实它们的区别很简单，embed属性值的字符排序是独立内嵌的，不受外部影响  [举个栗子](https://demo.cssworld.cn/12/1-4.php)
- bidi-override：顾名思义，bidi-override就是“重写双向排序规则”，通常样式表现为所有的字符都按照统一的direction顺序排列，例如，若设置direction:rtl，则所有字符都会从右往左反向排列，效果强烈。



> 实际上我们无须设置unicode-bidi:bidi-override以及direction属性，直接在元素前后分别插入U+202E字符和U+202C字符（可缺省）也可以实现字符反向排列效果
>
> ```html
> <p>
>     &#x202E;123456789
> </p>
> ```
>
> 



#### 改变CSS世界纵横规则的writing-mode

> writing-mode之所以给人“生僻”的感觉，是有原因的。实际上writing-mode这个CSS属性很早就诞生了，IE5.5浏览器就已经支持它了。那就奇怪了！writing-mode既然这么厉害，出现的时间早、资格老，为什么一直沉寂了差不多20年呢？那是因为在很长一段时间里，Firefox、Chrome这些现代浏览器都不支持writing-mode, writing-mode基本上就是IE浏览器的私有产物。很多人对IE一直没什么好感，对吧？由此及彼，自然对writing-mode也不待见。

> 和float属性有些类似，writing-mode原本是为控制内联元素的显示而设计的（即所谓的文本布局）。因为在亚洲，尤其像中国这样的东亚国家，存在文字的排版不是水平而是垂直的情况，如中国的古诗文.和float属性有些类似，writing-mode原本是为控制内联元素的显示而设计的（即所谓的文本布局）。因为在亚洲，尤其像中国这样的东亚国家，存在文字的排版不是水平而是垂直的情况，如中国的古诗文

```css
/*关键字值*/
writing-mode: horizontal-tb;// 水平方向
writing-mode: horizontal-rl;// 垂直方向
writing-mode: horizontal-lr;// 垂直方向
```

![image-20221020173039576](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221020173039576.png)



补充说明如下。

• 相同的writing-mode属性值并不会累加。例如，如果父子元素均设置了writing-mode:tb-rl，只会渲染一次，子元素并不会两次“旋转”。

• 在IE浏览器下，如果一个自身具有布局的元素（不是纯文本之类元素）writing-mode属性值和父元素不同，那么当子元素的布局流变化的时候，其父元素坐标系统的可用空间会被充分利用。这段文字太过术语化，我解释一下就是：在IE浏览器下，当布局元素从水平变成垂直的时候（举个例子），你就想象为元素在垂直方向是100%自适应父元素高度的。因此，IE浏览器下（不包括Edge 13及以上版本），元素vertical流的时候你会发现高度高得吓人，布局和其他现代浏览器不一样，正是这个原因。

• 虽然Chrome和Opera认识tb-rl等老的IE属性值，但也仅仅是认识而已，并没有任何实际效果！



**writing-mode不经意改变了哪些规则**

> writing-mode将页面默认的水平流改成了垂直流，颠覆了我们以往的很多认知，基于原本水平方向才适用的规则全部都可以在垂直方向适用！

[水平方向margin合并](http://demo.cssworld.cn/12/2-1.php)

[普通块元素可以使用margin:auto实现垂直居中](http://demo.cssworld.cn/12/2-2.php)

[writing-mode下块元素垂直居中](http://demo.cssworld.cn/12/2-3.php)

[使用text-indent实现文字下沉效果](http://demo.cssworld.cn/12/2-5.php)

[我们要实现小图标的旋转效果是很麻烦的，因](http://demo.cssworld.cn/12/2-6.php)

> writing-mode和direction的关系writing-mode、direction和unicode-bidi是CSS世界中三大可以改变文本布局流向的属性，其中direction和unicode-bidi属于近亲，经常一起使用，也是仅有的两个不受CSS3的all属性影响的CSS属性，基本上就是和内联元素一起使用。它貌似是为阿拉伯文字设计的。
>
> 乍一看，writing-mode似乎包含了direction和unicode-bidi的某些功能和行为，例如，vertical-rl的rl和direction的rtl值有相似之处，都是从右往左。然而，实际上两者是没有交集的。因为vertical-rl此时的文档流为垂直方向，rl表示水平方向，此时再设置direction:rtl，实际上值rtl改变的是垂直方向的内联元素的文本方向，一横一纵，没有交集。而且writing-mode可以对块状元素产生影响，直接改变了CSS世界的纵横规则，要比direction强大得多。它貌似是为东亚文字设计的。
>
> 然而，CSS的奇妙之处就在于：某些特性当初可能就是为某些图文排版设计的，但是我们可以利用它带来的特性发挥自己的创造力，实现其他很多意想不到的效果。因此，上面出现的“三剑客”都是非常好的资源。