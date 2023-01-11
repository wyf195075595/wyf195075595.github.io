---
title: CSS 3.0
date: 2022-10-26 18:00:00
tags: CSS
categories: htmlcss


---

**CSS世界已经有了CSS2和CSS3，那会有CSS4吗？**

答案是：不会有CSS4！

> 实际上，现在连CSS3这个词都已经名存实亡了，因为CSS3已经解体了。为什么说CSS3解体了呢？因为CSS按照大版本的方式进行迭代实在是太低效了。低效的原因其实很好理解，如果CSS这门语言必须按照大版本迭代发布的话，只要其中一个版本有一个CSS特性存在极大的争议，那这个版本的CSS的发布时间就会延后。例如，CSS2.1的规范在2002年开始制定，而一直到2011年才成为推荐规范，整个过程足足花费了10年的时间，就是因为一些次要的CSS特性拖了后腿。
>
> 为了加速没有争议的CSS特性的标准化，W3C的CSS工作组做出了一项被称为“Beijing doctrine”的决定，该决定将CSS划分为更小的组件，并把这些组件称为模块。这些模块互相独立，各自按照自己的速度走向标准化。例如，CSS选择器模块已经到了Level 4了，弹性布局模块还是Level 1。
>
> 将CSS划分为独立的模块是一个非常明智的决定，浏览器厂商可以根据对自己需求的判断决定究竟对哪些模块进行支持，哪怕这个模块还处于草案状态。例如CSS的env()函数的规范还是草案状态，但是Chrome和Safari浏览器的厂商却快速响应，对其进行了支持。对开发者而言，如此迅速地支持新特性犹如一场及时雨。
>
> 当然，这种模块化的设计也不是没有弊端，最大的问题就是设计冗余。例如，弹性布局和网格布局中的对齐属性其实可以统一，但它们却是分离的，这就会徒增学习成本；分栏布局、弹性布局和网格布局中的间隙其实可以统一，但它们也都是分离的，各有各的间隙属性。好在纠正及时，现在这三大布局全部开始改用gap属性表示间隙，减少了因为模块化带来的设计冗余。
>
> 模块化策略带来的好处远远大于弊端，正所谓瑕不掩瑜，与带来的好处相比，模块化设计的弊端几乎可以忽略不计。从这个角度看，CSS2.1之后的CSS世界就是模块化的新世界，所以我称之为CSS新世界。这个CSS新世界带来了新的CSS属性，并让很多CSS2中模糊的概念有了明确的定义，如尺寸体系、逻辑属性、CSS值类型划分等。CSS新世界中各模块虽然发展独立，但是相互间还是有很多共性和联系的。因此，CSS新世界不仅是一个模块化的世界，还是一个缤纷多彩，同时又自成一体的严谨的世界。

<!--more-->

## 互通互联的CSS数据类型

> 任何CSS属性值一定包含一个或多个数据类型。
>
> 在CSS2时代，CSS数据类型的概念并不怎么重要，因为常用的CSS属性翻来覆去就那几个，这些CSS属性支持的属性值我们反复使用就记住了。但是在CSS新世界中，由于各个CSS模块独立发展，没有统一的大版本进行约束，因此CSS的发展和变化非常快，这导致短时间内大量的CSS新属性如雨后春笋般涌现。
>
> 如果此时还和过去一样，只是凭借经验去学习CSS新特性，那么学习起来肯定是十分困难的，因为效率比较低下。此时CSS数据类型的价值就体现出来了，当CSS新属性出现的时候，我们无须记忆数量众多的属性值名称，只需要记住支持的数据类型即可，这样学习成本就大大降低了

举一个例子，background-image是使用频率非常高的一个CSS属性，这个CSS属性的语法结构是下面这样的：

```css
background-image: none | <image>
```

这个语法中出现的\<image>就是一种数据类型，它包括下面这些类型和函数：

● \<url>；

● \<gradient>；

● element()；

● image()；

● image-set()；

● cross-fade()

● paint()

也就是说，CSS的background-image属性不仅支持url()函数和渐变图像，还支持element()、image()、image-set()和cross-fade()等函数。

### 几个常见数据类型的简单介绍

> CSS数据类型非常多，保守估计，至少有50个，这里介绍几个常见且值得一提的数据类型。

CSS Shapes布局中有一个名为shape-outside的属性，我们不用关心这个CSS属性的含义，只看这个CSS属性的语法：

```css
shape-outside: none | <shape-box> || <basic-shape> | <image>
```

1. \<shape-box> 支持属性如下：

	- \<box>

		● content-box；

		● padding-box；

		● border-box。

	- margin-box

2. \<basic-shape> 支持属性如下：

	- inset()
	- circle()
	- ellipse()
	- polygon()
	- path()

3. \<image>

4. \<cololr>

	- <rgb()>；
	- <rgba()>；
	- <hsl()>；
	- <hsla()>；
	- \<hex-color>；
	- \<named-color>；
	- currentColor；
	- \<deprecated-system-color> 废弃的系统的颜色



[css值类型文档大全](https://www.zhangxinxu.com/wordpress/2019/11/css-value-type/)



### 学会看懂CSS属性值定义语法

> CSS属性值有专门的定义语法，用来表示CSS属性值的合法组成。例如，线性渐变的语法为：

```css
linear-gradient([ <angle> | to <side-or-corner> , ]? <color-stop-list>)
```

#### 学习CSS属性值定义语法的好处

> 在开始介绍具体的语法规则之前，我想先说说我们为什么要学会看懂CSS属性值定义语法（CSS value definition syntax）。就拿线性渐变的语法举例，根据我的观察，几乎无一例外，CSS开发者会写出类似下面这样的CSS线性渐变代码：

```css
background: linear-gradient(to bottom, deepskyblue, deeppink)
```

上面这句声明有问题吗？从功能上讲没问题，但是写法上却有瑕疵， “to bottom” 这几个字符是多余的，可直接写为

```css
background: linear-gradient(deepskyblue, deeppink)
```

> 如果你看得懂CSS的语法，那么在学习CSS的时候，只要稍微看一看线性渐变的语法，就能很轻松地知道“线性渐变的方向设置是可以省略的”这样一个细节知识，也就能写出更简洁的CSS代码。

#### CSS属性值定义语法详解

> CSS属性值定义语法是专门用来限定CSS属性合法取值的语法，这种语法包含以下3种基本组成元素：
>
> - 关键字
> - 数据类型
> - 符号

线性渐变语法：

```css
linear-gradient([<angle> | to <side-or-corner>, ]? <color-stop-list>)
```

上面语法中， to 为关键字， \<angle>,\<side-or-corner>,\<color-stop-list>是数据类型，[] | ? 为符号



1. 关键字

	关键字分为通用关键字和全局关键字：

	● auto、none、ease等关键字是通用关键字，或者可以称为普通关键字，这些关键字均只被部分CSS属性支持；

	● inherit、initial、unset和revert是全局关键字，属于被所有CSS属性支持的特殊关键字

2. 数据类型

	数据类型外面有一对尖括号（“<”和“>”）。有些数据类型是CSS规范中专门定义的，它们被称为基本类型，其他数据类型就被称为其他类型。

3. 符号

	符号是CSS语法中的重点和难点。CSS语法中的符号分为字面符号、组合符号和数量符号这3类

	- 字面符号指的是CSS属性值中原本就支持的合法符号，这些符号在CSS语法中会按照其原本的字面意义呈现。目前字面符号就两个，一个是逗号（,），另一个是斜杠（/）

		![image-20221021095011386](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221021095011386.png)

	- 组合符号用来表示数个基本元素之间的组合关系。目前共有5个组合符号，其中大多数组合符号的含义一目了然，除了“|”这个组合符号。因为“|”表示互斥，这在编程语言中比较少见，大家可以特别关注一下。

		![image-20221021095120238](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221021095120238.png)

	- 数量符号用来描述一个元素可以出现多少次，数量符号不能叠加出现，并且优先级高于组合符号。目前共有6个数量符号，大多数的数量符号的含义和在正则表达式中的含义是一样的

		![image-20221021095233527](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221021095233527.png)



> 接下来只要弄清楚\<angle>、\<side-or-corner>和\<color-stop-list>这几个数据类型的含义和语法，我们就可以理解线性渐变的语法了。



#### 了解CSS全局关键字

> inherit、initial、unset和revert都是CSS全局关键字属性值，也就是说所有CSS属性都可以使用这几个关键字作为属性值。

| 关键字属性值 | 实用性 | 兼容性 | 整体设计 |
| :----------: | :----: | :----: | :------: |
|   inherit    |   A    |   A+   |    A+    |
|   initial    |   B    |   B+   |    B     |
|    unset     |   B-   |   B-   |    B-    |
|    revert    |   B    |   C    |    B-    |

**用过都说好的继承关键字inherit**

> inherit这个关键字是继承的意思。IE浏览器从IE8（标准版）开始就已经支持该关键字了，而不是从IE9浏览器开始支持的。请记住，不是从IE9开始支持的，网络上的某些在线文档是错误的。

**可以一用的初始值关键字initial**

> initial是初始值关键字，可以把当前的CSS属性的计算值还原成**CSS语法中规定的初始值**。【**不是浏览器内置的样式**】

[示例](https://demo.cssworld.cn/new/2/3-1.php)



**很多人有这样一个误区：把 initial 关键字理解为浏览器设置的元素的初始值。实际上两者是不一样的。**

> 举个例子，实际开发的时候，\<ul>元素或 \<ol>元素默认的list-style-type样式会被CSS重置。但是可能会遇到这样的场景，即某些区域需要增加一些描述信息，因此需要重新使用list-style-type样式（小圆点或者数字），此时有些开发者就会使用initial关键字对该样式进行还原：

```css
ol {
    padding: initial;
    list-style-type: initial;
}
```

但是没有用！应为上边等同如下设置

```css
ol {
    padding: 0;
    list-style-type: disc;
}
```

而不是预想的

```css
ol {
    padding: 0 0 0 40px;
    list-style-type: decimal;
}
```

> 此时需要的全局关键字属性值其实是 revert，而不是initial。

> 兼容性
>
> 除IE浏览器之外，其他浏览器都很早就支持了initial，因此，至少在移动端项目（包括微信小程序）中大家可以百分之百放心使用这个关键字属性值。



**了解一下不固定值关键字unset**

> unset是不固定值关键字，其特性如下：如果当前使用的CSS属性是具有继承特性的，如color属性，则等同于使用inherit关键字；如果当前使用的CSS属性是没有继承特性的，如background-color，则等同于使用initial关键字。
>
> unset这个关键字只有配合all属性使用才有意义，因为对于某个具体的CSS属性，想要继承某个属性，那就使用inherit关键字；想要使用初始值，那就使用initial关键字，没有任何理由使用unset关键字。

如某元素很多默认属性不是我们需要的，但是我们又不想一个属性接一个属性地进行重置，怎么办呢？此时就可以先使用all:unset进行批量重置，再设置我们需要的CSS属性：

```css
dialog {
    all: unset;
}
```

> <dialog>元素的样式重置是很难得的unset使用场景，由于支持<dialog>元素的浏览器一定支持unset，因此也不用担心兼容性的问题。



**个人很喜欢的恢复关键字revert**

> revert 关键字可以让当前元素的样式还原成浏览器内置的样式。例如：

```css
ol {
    padding: revert;
    list-style-type: revert;
}
```

> 那么 \<ol>中的每一个 \<li>项都会有数字效果呈现，当然，前提是你没有对子元素 \<li>的list-style-type属性做过样式重置。这里有必要插一句，请记住：没有任何理由对 \<li>元素进行任何样式重置。因为所有浏览器的<li>元素默认都没有margin外间距，也没有padding内间距，list-style-type也是继承 \<ul>或 \<ol>元素，所以对 \<li>元素进行任何样式重置，既浪费CSS代码，也不利于列表序号的样式设置。此刻，你就可以看看手中的项目中的代码有没有对 \<li>元素做过样式重置，如果有，赶快删掉

[在支持 revert 样式的 浏览器中 设置恢复](https://demo.cssworld.cn/new/2/3-2.php)

- ##### HTML：

	```html
	<ol class="revert-ol">
	    <li>inherit关键字实用</li>
	    <li>initial关键字可用</li>
	    <li>unset关键字配合all使用</li>
	    <li>revert关键字有用</li>
	</ol>
	```

- ##### CSS：

	```css
	.revert-ol {
	    list-style: none;
	}
	@supports (padding: revert) {
	    .revert-ol {
	        padding: revert;
	        list-style-type: revert;
	    }
	}
	```



**指代所有CSS属性的all属性**

> all属性可以重置除unicode-bidi、direction以及CSS自定义属性以外的所有CSS属性。

```css
input {
    all: inherit;
} 
```

> 该段代码表示 \<input>元素中所有CSS属性都使用inherit关键字作为属性值

> all:inherit没有任何实用价值，all:initial也没有任何实用价值。有实用价值的是all:unset和all:revert。all:unset可以让任意一个元素样式表现和\<span>元素一样。all:revert可以让元素恢复成浏览器默认的样式，也是很有用的。



> 为什么unicode-bidi和direction这两个CSS属性不受all属性影响?
>
> 我们不妨反问一下，如果unicode-bidi和direction这两个CSS属性会受到all属性影响，那会出现什么问题呢？阿拉伯文的呈现形式是从右往左的，但是，direction属性的初始值却是ltr，即从左往右。如果all属性可以影响direction属性，那么执行all:initial的时候，这些阿拉伯文的网页文字全部都会变成从左往右呈现。大家可以想象一下我们的中文网页上的中文内容全部从右往左显示是什么样的，我敢保证，使用阿拉伯文的前端开发者绝对不会使用这个all属性的，all属性在阿拉伯文中从此名存实亡。
>
> 于是答案就出来了，之所以direction属性不受all影响，是因为当年direction属性设计失误，将其初始值设为了ltr，而不是auto。现在为了照顾从右往左阅读的场景，direction属性就被设计成不受all属性影响。unicode-bidi属性是direction属性的“跟屁虫”，而且它的功能还挺强大的，可以精确控制每一个文字的呈现方向，只是离开了direction属性就没用。既然这两个CSS属性形影不离，那就把unicode-bidi属性加入不会受all属性影响的属性队列吧。



#### css 新特性的渐进式增强处理技巧

> 有很多CSS新特性是对现有Web特性的体验升级，我们直接使用这些CSS新特性就好了，不要担心兼容性问题。因为在支持的浏览器中体验更好，在不支持的浏览器中也就是保持原来的样子而已。
>
> 做Web开发是没有必要让所有浏览器都显示得一模一样的，好的浏览器有更好的显示，糟糕的浏览器就只有普通的显示，这才是对用户更负责任的做法。



> 举个例子，IE10+浏览器支持CSS动画属性animation，我们要实现加载效果就可以使用一个很小的PNG图片，再借助旋转动画。这个方法的优点是资源占用少，动画效果细腻。于是，我们的需求来了，IE9及其以下版本浏览器还是使用传统的GIF动图作为背景，IE10+浏览器则使用PNG背景图外加animation属性实现加载效果。

这个需求的难点在于我们该如何区分IE9和IE10浏览器。大家千万不要再去找什么CSS Hack了，我们可以利用属性值的语法差异实现渐进增强效果。例如：

```css
.icon-loading {
    display: inline-block;
    width: 30px; height: 30px;
    background: url(../images/loading.gif);
    background: url(../images/loading.png), linear-gradient(transparent, transparent);
    animation: spin 1s linear infinite;
}
```

[background-blend-mode纹理背景渐进增强实现实例页面](https://demo.cssworld.cn/new/2/5-2.php)

**@supports规则下的渐进增强处理**

> @supports是CSS中的常见的@规则，可以用来检测当前浏览器是否支持某个CSS新特性，这是最规范、最正统的CSS渐进增强处理方法，尤其适合多个CSS属性需要同时处理的场景。
>
> 但是在实际开发的时候，@supports规则并没有在IE浏览器的兼容性问题上做出什么大的贡献。原因很简单，@supports规则的支持是从Edge12浏览器开始的，根本就没有IE浏览器什么事情。

**@supports规则常用的语法**

```css
.icon-loading {
    display: inline-block;
    width: 30px; height: 30px;
    background: url(./loading.gif);
}
/*Edge12+浏览器*/
@supports (animation: none) {
    .icon-loading {
        background: url(./loading.png);
        animation: spin 1s linear infinite;
    }
}
@keyframes spin {
    from { transform: rotate(360deg) };
    to { transform: rotate(0deg) };
}
```

> @supports规则还支持使用操作符进行判断，这些操作符是**not**、**and**和**or**，分别表示“否定”“并且”“或者”。
>
> 甚至连续判断3个以上的CSS声明也没问题还支持嵌套语法

```css
@supports (display: flex) and (display: grid) and (gap: 0) {}


@supports (display: flex) and (not (display: grid)) {}
```

[@supports嵌套语法测试实例页面](https://demo.cssworld.cn/new/2/5-3.php)

> @supports规则支持CSS自定义属性的检测和CSS选择器语法的检测。例如：

```css
@supports (--var: blue) {}

@supports selector(:default) {}
```

> 其中，CSS自定义属性的检测没有任何实用价值，本书不展开讲解；而CSS选择器语法的检测属于CSS Conditional Rules Module Level 4规范中的内容，目前浏览器尚未大规模支持，暂时没有实用价值，因此本书暂不讲解。



**浏览器还提供了CSS.supports()接口，让我们可以在JavaScript代码中检测当前浏览器是否支持某个CSS特性，语法如下：**

```js
CSS.supports(propertyName, value);
CSS.supports(supportCondition);
```

**@supports规则的花括号可以包含其他任意@规则，甚至是包含@supports规则自身**

```css
@supports (display: flex) and (not (display: grid)) {
    @supports (animation: none) {
        .icon-loading {
            background: url(./loading.png);
            animation: spin 1s linear infinite;
        }
    }
}
```

[@supports @规则嵌套测试实例页面](https://demo.cssworld.cn/new/2/5-4.php)



> 我觉得大家在日常工作中，应该大胆使用CSS新特性，同时再多花一点额外的时间对这些新特性做一些兼容性方面的工作。这绝对是一件非常划算的事情，无论是对用户还是对自身的成长都非常有帮助。



## 从增强已有的CSS属性开始



### 尺寸体系

> 尺寸体系，如果用一个金字塔来表示，那么在最上层的概念就是“Intrinsic Sizing”和“Extrinsic Sizing”。“Intrinsic Sizing”被称为“内在尺寸”，表示元素最终的尺寸表现是由内容决定的；“Extrinsic Sizing”被称为“外在尺寸”，表示元素最终的尺寸表现是由上下文决定的。

#### width:fit-content声明

> fit-content关键字是新的尺寸体系关键字中使用频率最高的关键字。你可以把fit-content关键字的尺寸表现想象成“紧身裤”，大腿的肉对应的就是元素里面的内容，如果是宽松的裤子，那肉眼所见的尺寸就比较大，但是如果是紧身裤，则呈现的尺寸就是大腿实际的尺寸。
>
> 实际上，fit-content关键字的样式表现就是CSS2.1规范中的“shrink-to-fit”，我称其为“包裹性”。这种尺寸表现和元素应用display:inline-block、position:absolute等CSS声明类似，尺寸收缩但不会超出包含块级元素的尺寸限制。

[文字居中和左对齐自动布局实例页面](https://demo.cssworld.cn/new/3/1-1.php)

> 如果是内联元素要收缩，可以使用display:inline-block声明；如果是块级元素要收缩，可以使用display:table声明。这两种方式效果一样，兼容性还更好，IE8+浏览器都提供了支持。这么一看，fit-content关键字岂不是没什么用？

**使用fit-content关键字有两大优点。**

- 保护了元素原始的display计算值，例如\<li>元素要是设置成了display:table，前面的项目符号就不会出现，::marker伪元素也会失效。
- 让元素的尺寸有了确定的值，这是fit-content关键字最重要也最可贵的优点。

**让元素的尺寸有了确定的值这点展开一下**

CSS中有不少布局需要有明确的元素的尺寸才能实现，非常典型的例子就是绝对定位元素使用margin:auto实现居中效果时需要设置具体的width或height的属性值，CSS代码示意如下：

```css
.cw-dialog {
    width: 300px; height: 200px;
    position: absolute;
    left: 0;top: 0; right: 0; bottom: 0;
    margin: auto;
    border: solid;
}
```

但是很多时候绝对定位元素的尺寸不是固定的，最终尺寸会根据内容自动变化，此时以上CSS就不适合了。有人想到了 translate 偏移 50%。这个方法不错，但是并不完美，而且这个地方占据了 transform 属性，这会导致绝对定位元素无法使用包含transform属性的动画效果。 此时如果使用 **fit-content** 关键字 。就不用担心包括transform属性的动画带来的冲突。 

```css
.cw-dialog {
    width: fit-content;
    height: fit-content;
    position: absolute;
    ledt: 0; top: 0; right: 0; bottom: 0;
    margin: 0 auto;
    border: solid;
    animation: tinUp .2s;
}
```

[效果展示](https://demo.cssworld.cn/new/3/1-2.php)

#### stretch、available和fill-available关键字究竟用哪个?

> 我们在页面中放置一个没有样式设置的\<div>元素，此时，该\<div>元素的宽度自动填满可用空间。
>
> stretch、available和fill-available这3个关键字虽然名称有所不同，但是作用都是一致的，那就是让元素的尺寸自动填满可用空间，就如同\<div>元素的默认尺寸表现。
>
> 下面问题来了，究竟该使用stretch、available和fill-available这3个关键字中的哪一个呢？先简单分析一下这3个关键字。

● stretch指“弹性拉伸”，是最新的规范中定义的关键字，替换之前的fill-available和available。

● available指“可用空间”，是Firefox浏览器使用的关键字，需要配合-moz-私有前缀使用。

● fill-available指“填充可用空间”，是webkit浏览器使用的关键字，需要配合-webkit-私有前缀使用。



> 实际开发的时候，我们需要用到stretch关键字的场景并不多。首先，block水平的元素、弹性布局和网格布局中的子项默认都自带弹性拉伸特性；其次，对于替换元素、表格元素、内联块级元素等这些具有“包裹性”的元素，建议使用“宽度分离原则”

```css
.container {
    margin: 15px;
    padding: 10px;
}

.container > img {
    width: 100%;
}
```

[stretch关键字与button按钮的margin自适应实例页面](https://demo.cssworld.cn/new/3/1-3.php)

#### min-content关键字

> min-content关键字实际上就是CSS2.1规范中提到的“preferred minimum width”或者“minimum content width”，即“首选最小宽度”或者“最小内容宽度”。
>
> 元素由content-box、padding-box、border-box和margin-box组成，元素最终占据的尺寸由这4个盒子占据的尺寸决定。其中padding-box、border-box和margin-box的尺寸表现规则不会因为元素的不同而有所不同，但是content-box不一样，它随着内容的不同，首选最小宽度也会不同。这个有必要好好讲一讲。

1. 替换元素

	按钮、视频和图片等元素属于替换元素，替换元素的首选最小宽度是当前元素内容自身的宽度

	```html
	<section>
	    <img src="./1.jpg" >
	</section>
	```

	上面这段HTML代码中的图片，如果原始尺寸是256px×192px，则<section>元素的首选最小宽度就是256px。

2. CJK文字

	CJK是Chinese/Japanese/Korean的缩写，指的是中文、日文、韩文这几种文字。这里以中文为代表加以说明。如果是一段没有标点的中文文字，则首选最小宽度是单个汉字的宽度。

	```html
	p {
		width: min-content;
		outline: 1px dotted;
	}
	<p>
	    感谢您的支持
	</p>
	```

3. 非CJK文字

	> 非CJK文字指的是除中文、日文、韩文之外的文字，如英文、数字和标点等字符。非CJK文字的首选最小宽度是由字符单元的宽度决定的，所有连续的英文字母、数字和标点都被认为是一个字符单元，直到遇到中断字符。

	哪些字符可以中断字符单元呢？

	1、Space空格（U+0020）无论是哪个浏览器，都能中断字符单元，并且忽略Space空格前后字符的类型

	2、短横线（U+002D），在webkit浏览器中可以中断字符单元

	3、在Firefox浏览器中，英文问号（U+003F）不能中断字符单元，而短横线（U+002D）可以，不过后面的字符必须是字母而不能是数字

	4、在IE浏览器和Edge浏览器中，英文问号（U+003F）不能中断字符单元，而短横线（U+002D）可以，但是要求短横线前后是由字母、数字或短横线组成的字符单元，且这个字符单元的长度要大于1

4. 最终的首选最小宽度

	[一个元素最终的首选最小宽度是所有内部子元素中最大的那个首选最小宽度值](https://demo.cssworld.cn/new/3/1-5.php)



#### max-content关键字

> max-content关键字表示最大内容宽度，max-content关键字的作用是让元素尽可能大，保证图文内容在一行显示，哪怕最终的宽度溢出外部容器元素

[示例](https://demo.cssworld.cn/new/3/1-6.php)





> 对CSS新的尺寸体系做一个总结。带content这个单词的3个关键字fit-content、min-content和max-content都是“内在尺寸”（intrinsic sizing），尺寸表现和内容相关；stretch关键字（也包括available关键字和fill-available关键字）是“外在尺寸”（extrinsic sizing），尺寸表现和上下文有关。这4个关键字一起撑起了CSS世界的尺寸体系。



### 深入了解CSS逻辑属性

> 整个CSS世界就是围绕“流”来构建的（详见《CSS世界》一书的第6页，1.3.2节）。在CSS2.1时代，CSS属性的定位都是基于方向的，而不是“流”。这样的设计其实是有问题的，基于方向进行定位虽然符合现实世界认知，但和CSS世界基于“流”的底层设计理念不符，这样就会产生不合理的问题。

两个水平按钮要给它们设置间距，margin-right: 10px;但是当 文档流方向发生变化时即 direction:rtl; 时，在右侧会出现空隙，此时应该是设置成margin-left: 10px;的效果。.

如果我们一开始设置的不是符合现实世界认知的margin-right属性，而是符合CSS世界“流”概念的逻辑属性margin-inline-end，代码如下：

```css
button {
    margin-inline-end: 10px;
}
```

[效果对比](https://demo.cssworld.cn/new/3/2-1.php)

**CSS逻辑属性有限的使用场景**

> CSS逻辑属性需要配合writing-mode属性、direction属性或者text-orientation属性使用才有意义。
>
> CSS中还有其他一些CSS属性值也可以改变DOM元素的呈现方向，例如flex-direction属性中的属性值row-reverse和column-reverse，但是请注意，这些属性值和CSS逻辑属性之间[没有任何关系](https://demo. cssworld.cn/new/3/2-2.php)
>
> [模拟微信聊天界面](https://demo.cssworld.cn/new/3/2-3.php)

[inline/block与start/end元素](https://demo.cssworld.cn/ new/3/2-4.php)

**width/height属性与inline-size/block-size逻辑属性**



### 在CSS边框上做文章

> 一个图形元素的装饰部件主要是边框和背景。在CSS2.1时代，边框只能是纯色的，效果太单调了。于是CSS规范制定者就开始琢磨，是不是可以在CSS边框上做文章，通过支持图片显示来增强边框的表现力呢？

#### 独一无二的border-image属性

> 所有与装饰有关的CSS属性都能从其他设计软件中找到对应的功能，如背景、描边、阴影，甚至滤镜和混合模式，但是唯独border-image属性是CSS这门语言独有的，就算其他软件有边框装饰，也不是border-image这种表现机制。这看起来是件好事情，你瞧，border-image多么与众不同！但实际上，border-image属性很少出现在项目代码中，其中重要的原因之一就是border-image属性过于特殊。

（1）对开发者而言，border-image属性怪异的渲染机制，导致学习成本较高，掌握border-image属性的人并不多。而且很多时候该属性对源图像的规格和比例也有要求，这导致使用成本也比较高。

（2）对设计师而言，border-image属性的视觉表现和现实认知是不一致的，而设计师的视觉设计多基于现实认知，因此，设计师无法为border-image属性量身定制图形表现。另外，当下的设计趋势是扁平化而非拟物化，边框装饰通常在项目中不会出现。

（3）border-image属性怪异的渲染机制导致元素的4个边角成了4个尴尬的地方，实现的边框效果往往不符合预期，最终导致开发者放弃使用border-image属性。

```css
border-image: url(./q.svg) 54;

border-image-source: url(./q.svg);
border-image-slice: 54;

```

> border-image-slice属性的正式语法如下，表示支持1～4个数值或1～4个百分比值，后面可以带一个关键字fill

```css
border-image-slice: <number-percentage>{1,4} && fill?
```

> border-image-slice属性的作用是对原始的图像进行划分，划分的方位和顺序同margin属性、padding属性一样，遵循上、右、下、左的顺序。例如border-image-slice:20表示在距离源图像上方20px、距离源图像右侧20px、距离源图像下方20px、距离源图像左侧20px的地方进行划分

![image-20221024155122282](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221024155122282.png)

> 此时4个边角区域只有很小的一部分被划分，而剩余的上、下、左、右区域会被拉伸，因此，作用在.example元素上的效果就会如图3-22所示，其中增加的几根辅助线可以方便大家理解。

![image-20221024155150672](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221024155150672.png)

[示例](https://demo.cssworld.cn/new/3/3-1.php)

> 理解了border-image-slice属性，border-image属性的学习就算完成了一半，剩下的就是学会控制九宫格的尺寸了，而控制九宫格尺寸的CSS属性就是border-image-width和border-image-outset。

[border-image-width](https://demo.cssworld.cn/new/3/3-2.php)

[border-image-outset](https://demo.cssworld.cn/new/3/3-3.php)

[border-image-repeat](https://demo.cssworld.cn/new/3/3-4.php)

> order-image-slice属性用于划分源图像，border-image-width用于控制九宫格第一区到第八区的尺寸，border-image-outset属性用于控制九宫格最中间第九区的尺寸。掌握这3个属性，就算完全理解border-image属性了。

border-image属性的正式语法如下：

```css
border-image: <'border-image-source'> || <'border-image-slice'> [/<border-image-width>| /<'border-image-width'>? /<'border-image-outset'>]? || <'border-image-repeat'>
```

[示例](https://demo.cssworld.cn/new/3/3-5.php)

[border-image实现效果示意](https://demo.cssworld.cn/new/3/3-6.php)

[红色条纹渐变边框](https://demo.cssworld.cn/new/3/3-7.php)

[圆角渐变边框](https://demo.cssworld.cn/new/3/3-8.php)



> CSS中共有 3 个属性可以实现对布局没有任何影响的轮廓扩展，分别是outline轮廓、box-shadow盒阴影和border-image边框图片

![image-20221024161943783](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221024161943783.png)

● 如果需要轮廓带有渐变效果，一定是使用border-image属性。

● 如果需要轮廓效果是纯色，且4个角为直角，则优先使用outline属性；如果outline属性不能使用（如无障碍访问需要），则使用box-shadow属性；如果box-shadow属性已经有了其他样式，则使用border-image属性。

● 如果需要轮廓有圆角效果，则一定是使用box-shadow属性。

● 如果需要轮廓和元素之间还有一段间隙，则优先使用outline属性；如果outline属性不能使用，则使用border-image属性。

● 如果需要轮廓只有一个方向，则不考虑outline属性。

● 如果需要兼容IE浏览器，则border-image属性不考虑。



### position 属性增强

> 全新的position属性值——sticky，单词“sticky”的中文意思是“黏性的”，position:sticky就是黏性定位。为了让接下来的描述更精准，我们不妨在这里先约定：黏性定位就是指元素应用了position:sticky声明；相对定位就是指元素应用了position:relative声明；绝对定位就是指元素应用了position:absolute声明；固定定位就是指元素应用了position:fixed声明。

> sticky属性值刚出来的时候，在圈子里是引发过一阵小热度的。但是，在2014年至2016年这长达3年的时间里，Chrome浏览器放弃了对它的支持，后来这个新特性就淡出了大众的视野。不知道出于什么原因，2017年之后，Chrome浏览器又重新开始支持黏性定位了。目前所有主流浏览器都已经支持黏性定位。

> 过去，黏性定位效果一定是通过JavaScript代码实现的。这个效果常用在导航元素上，具体表现为：当导航元素在屏幕内的时候，导航元素滚动跟随；当导航元素就要滚出屏幕的时候，导航元素固定定位。
>
> sticky属性值的设计初衷就是把原来JavaScript才能实现的黏性效果改由CSS实现。下面来看一个例子。

[效果预览](https://demo.cssworld.cn/new/3/4-1.php)

**黏性定位效果底层的渲染规则和固定定位没有任何关系，而是相对定位的延伸。先说说黏性定位和相对定位相似的地方。**

（1）元素发生偏移的时候，元素的原始位置是保留的。

（2）创建了新的绝对定位包含块，也就是黏性定位元素里面如果有绝对定位的子元素，那这个子元素设置left属性、top属性、right属性和bottom属性时的偏移计算是相对于当前黏性定位元素的。

（3）支持设置z-index属性值来改变元素的层叠顺序。

**再说说黏性定位和相对定位不一样的地方。**

（1）偏移计算元素不一样。相对定位偏移计算的容器是父元素，而黏性定位偏移计算的元素是层级最近的可滚动元素（overflow属性值不是visible的元素）。如果一个可滚动元素都没有，则相对浏览器视窗进行位置偏移。

（2）偏移定位计算规则不一样。黏性定位的计算规则比较复杂，涉及多个黏性定位专有的概念。

（3）重叠表现不一样。相对定位元素彼此独立，重叠的时候表现为堆叠；但是黏性定位元素在特定布局结构下，元素重叠的时候并不是表现为堆叠，而是会有A黏性定位元素推开B黏性定位元素的视觉表现。

**注意的三个点**

1. 可滚动元素对黏性定位的影响

	> 通常的Web页面都是窗体滚动的，而黏性定位偏移计算的元素是层级最近的那个滚动元素。因此，如果黏性定位元素的某个祖先元素的overflow属性值不是visible，那么窗体滚动的时候就不会有黏性定位效果，

	```html
	div {
	    overflow: hidden;
	}
	nav {
	    position: sticky;
	    top: 0;
	}
	
	<div>
	    <nav></nav>
	</div>
	```

	此时滚动页面，\<nav>元素是没有黏性效果的。注意，这不是bug，也不是sticky属性值没有渲染，而是因为此时\<nav>元素黏性定位的偏移计算是相对于父级\<div>元素计算的，黏性效果也只有在\<div>元素滚动的时候才能够体现。

	

	如果希望出现滚动，可以这样调整一下

	```css
	div {
		height: 400px;
	    overflow: auto;
	}
	div::after {
	    content: ''
	    display: block;
	    height: 800px;
	}
	```

	> 如果你的网页使用的是窗体滚动，又希望有黏性效果，那务必保证黏性定位元素的祖先元素中没有可滚动元素。

2. 深入理解黏性定位的计算规则

	> 如果黏性定位元素的父元素的高度和黏性定位元素的高度相同，则垂直滚动的时候，黏性定位效果是不会出现的。要讲清楚这个问题，就必须深入理解黏性定位的计算规则。
	>
	> 

	黏性定位中有一个“流盒”（flow box）的概念，指的是黏性定位元素最近的可滚动元素的尺寸盒子，如果没有可滚动元素，则表示浏览器视窗盒子。黏性定位中还有一个名为“黏性约束矩形”的概念，指的是黏性定位元素的包含块（通常是父元素）在文档流中呈现的矩形区域和流盒的4个边缘在应用黏性定位元素的left、top、right和bottom属性的偏移计算值后的新矩形的交集。由于滚动的时候流盒不变，而黏性定位元素的包含块跟着滚动，因此黏性约束矩形随着滚动的进行是实时变化的。假设我们的黏性定位元素只设置了top属性值，则黏性定位元素碰到黏性约束矩形的顶部时就开始向下移动，直到它完全被包含在黏性约束矩形中。[效果查看](https://demo.cssworld.cn/new/3/4-2.php)

	明白了黏性定位的计算规则，也就明白了为什么黏性定位元素的父元素和自身高度计算值一样的时候没有黏性效果。因为此时包含块高度和黏性定位元素的高度相同，这导致黏性约束矩形的最大高度和黏性定位元素的高度相同，黏性定位元素已经完全没有了实现黏性效果的空间。

	

3. 理解黏性定位的堆叠规则

	> 黏性定位元素的偏移由容器决定，如果多个黏性定位元素在同一容器中，则这几个黏性定位元素会产生元素重叠的情况；如果黏性定位元素分布在不同的容器中，同时这些容器在布局上是上下紧密相连的，则视觉上会表现为新的黏性定位元素挤开原来的黏性定位元素，形成依次占位的效果。

	[效果查看](https://demo.cssworld.cn/new/3/4-3.php)

	> 黏性定位元素在**同一个容器下会重叠**，而在**不同容器下则会依次推开**，这和上面等高父元素没有黏性效果的原因一样，都是黏性定位计算规则下的样式表现。

	

	> Safari浏览器中使用黏性定位需要添加-webkit-私有前缀。IE浏览器可以使用Polyfill进行支持，可以兼容到IE9+版本。同时设置top属性、bottom属性的时候，上下两个方位的黏性效果会同时生效。水平方向的left属性、right属性也是类似的，不过由于水平滚动场景不常见，因此，left属性、right属性并不常用。

	

	[层次滚动效果](https://demo.cssworld.cn/new/3/4-4.php)

	> 

	

### font-family属性和@font-face规则新特性

> 本节主要介绍与字体相关的一些新特性，包括font-family属性的功能加强，以及@font-face自定义字体。

全新的通用字体族包括以下几种。

● system-ui：系统UI字体。

● emoji：适用于emoji字符的字体家族。

● math：适用于数学表达式的字体家族。

● fangsong：中文字体中的仿宋字体家族。

#### system-ui

> 在过去，如果想要使用系统字体，只能使用font:menu、font:status-bar等CSS声明。但是，menu、status-bar、small-caption等font关键字属性值是包含字号的，不同操作系统中的字号会不一样，因此我们还需要通过设置font-size属性值重置字号大小，比较麻烦。system-ui字体族的出现很好地解决了使用系统字体的需求。

**为啥要使用系统字体？**

```css
body {
	font-familly: system-ui;
}
```

不同字体在不同系统下显示有差异，效果可能不如系统字体，还有可能发生网页设置的通用字体与系统字体冲突的问题



#### emoji

> 前主流的操作系统都已经内置emoji字体，如macOS、iOS、Android操作系统和Windows 10操作系统等。然而，虽然主流的操作系统内置了emoji字体，但是有些emoji字符并不会显示为彩色的图形，需要专门指定emoji字体

**emoji字体设置应该放在系统字体设置后面**

```css
.emoji {
    font-family: Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol, Noto Color Emoji;
}
```

● Apple Color Emoji用在Apple的产品中的，如iPhone（iOS）或者Mac Pro（macOS）等。

● Segoe UI Emoji是用在Windows操作系统中的emoji字体。

● Segoe UI Symbol是在Windows 7操作系统中添加的一种新字体，是一种Unicode编码字体，显示的是单色图案，非彩色图形。

● Noto Color Emoji是谷歌的emoji字体，用在Android和Linux操作系统中。

**以上4种字体涵盖了所有主流的操作系统。可以专门定义一个新的emoji字体来优化代码**

```css
@font-face {
    font-family: Emoji;
    src: local('Apple Color Emoji'),
        local('Segoe UI Emoji'),
        local('Segoe UI Symbol'),
        local('Noto Color Emoji');
}
.emoji {
    font-family: Emoji;
}
```

[Emoji字体设置后的作用实例页面](https://demo.cssworld.cn/new/3/5-1.php)

[Emoji字体设置放在最后的效果实例页面](https://demo.cssworld.cn/new/3/5-2.php)

[Emoji字体unicode-range范围限定效果实例页面](https://demo.cssworld.cn/new/3/5-3.php)



#### math通用字体族

> math通用字体族的作用是方便在Web中展现数学公式。数学公式包含非常多的层次关系，需要特殊的字体进行支持

有一种名为MathML的XML语言专门用来呈现富有层级关系的数学公式（对MathML的详细介绍见https://www.zhangxinxu.com/wordpress/?p=8108）



理论上在开发的时候，我们只要使用MathML语言进行数学公式书写就好了，无须关心背后的字体。但是，在实际操作中，Chrome浏览器并不支持MathML。为了兼容Chrome浏览器，我们需要对数学标签进行CSS重定义，此时就需要用到math通用字体族：

```css
math {
    font-family: Cambria Math, Latin Modern Math;
}
```

其中，Cambria Math是Windows操作系统中的数学字体，Latin Modern Math是macOS中的数学字体。



#### fangsong通用字体族

> 个字体族来自中文字体“仿宋”，仿宋是介于宋体（衬线字体）和楷体（手写字体）之间的一种字体。和宋体相比，仿宋笔画的水平线通常是倾斜的，端点修饰较少，笔画宽度变化较小。一般非常正式的公告才会用到这个字体，平常开发项目中很少用到



#### local()函数与系统字体的调用

> 从IE9浏览器开始，@font-face规则开始支持使用local()函数调用系统安装的字体。使用local()函数主要有两大好处。

（1）简化字体调用。例如我们要使用等宽字体，但是不同操作系统中的等宽字体不一样。为了兼容，我们需要一长串不同的字体名称作为font-family属性值，但是根本就记不住这么多字体，没关系，有了local()函数，使用这些字体的方法就一下子变得简单了

```css
@font-face {
    font-family: Mono;
    /*每个单词可不加引号*/、
    src: local('Menlo'),
        local('Monaco'),
        local('Consolas'),
        local('Liberation Mono'),
        ...;
}
code {
    font-family: Mono;
}
```

（2）在自定义字体场景下提高性能。例如我们希望在各个平台都能使用Roboto字体，则可以像下面这样重新定义下Roboto字体

```css
@font-face {
    font-family: Roboto;
    font-style: normal;
    font-weight: 400;
    src: local("Roboto"),
        local("Roboto-Regular"),
        url("./Roboto.woff2") format("woff2");
}
```

此时local()函数可以让已经安装了Roboto字体的用户无须发起额外的Roboto字体请求，优化了这部分用户的使用体验。



#### unicode-range属性

> 我们在使用@font-face规则自定义字体的时候，还可以通过使用unicode-range属性来决定自定义的字体作用在哪些字符上。例如设置emoji字体的作用范围

```css
@font-face {
    font-family: Emoji;
    src: local('Apple Color Emoji'),
        local('Segoe UI Emoji'),
        local('Segoe UI Symbol'),
        local('Noto Color Emoji');
    unicode-range: U+1F000-1F644,U+203C-3299;
}
```

在前端领域，使用Unicode编码显示字符在各种语言中都是可以的，不过前缀各有不同。

（1）在HTML中，字符输出可以使用&#x加上Unicode编码。

（2）在JavaScript文件中，为了避免中文乱码需要转义，应使用\u加上Unicode编码。

（3）在CSS文件中，如CSS伪元素的content属性，就直接使用\加上对应字符的Unicode编码值。

（4）unicode-range属性则是使用U+加上Unicode编码。

**unicode-range属性常用的Unicode编码值对中文用户而言，最常用的Unicode编码值有下面这些。**

● 基本二次汉字：[0x4e00,0x9fa5]（或十进制[19968,40869]）。

● 数字：[0x30,0x39]（或十进制[48, 57]）。

● 小写字母：[0x61,0x7a]（或十进制[97, 122]）。

● 大写字母：[0x41,0x5a]（或十进制[65, 90]）。



如果想获取某一个具体字符的Unicode编码值

```js
U = '℃'.codePointAt().toString(16) // 2103
```



#### **woff/woff2字体**

> 为了方便在网页中高效使用自定义字体，woff和woff2应运而生，它们是两个专门用在Web中的字体。其中，woff字体在2012年12月被World Wide Web Consortium（W3C）推荐使用，IE9+浏览器支持该字体。woff2字体最早在2013年7月的Chrome Canary版本上就可以使用了，发展到现在，几乎已经成为自定义图标字体使用的标准配置，目前浏览器对它的兼容性已经相当不错了。
>
> woff2字体没有必要再开启GZIP，因为这个字体文本本身就是压缩过的。
>
> 最后说一下woff字体的MIME type值。关于这一点有点小争议，拿woff2字体举例，Google使用的是font/woff2，而W3C则推荐使用application/font-woff2。我个人的建议是，在Web中使用的时候采用font/woff2，在服务器端进行MIME type配置的时候采用application/font- woff2。

#### font-display

> 假设我们定义一个名为MyFont的自定义字体，并且采用url()函数外链的方式引入..这时浏览器的字体加载行为表现为，应用MyFont字体的文本会先被隐藏，直到字体加载结束才会显示，但是这个隐藏最多持续3s，3s后字体仍未加载结束则会使用其他字体代替。这种加载体验利弊参半。

> **字体显示时间线**
>
> 字体显示时间线开始于浏览器尝试下载字体的那一刻，整个时间线分为3个时段，浏览器会在这3个时段让元素表现出不同的字体渲染行为。
>
> ● 字体阻塞时段：如果未加载字体，任何试图使用它的元素都必须以不可见的方式渲染后备字体；如果在此期间字体成功加载，则正常使用它。
>
> ● 字体交换时段：如果未加载字体，任何试图使用它的元素都必须以可见的方式渲染后备字体；如果在此期间字体成功加载，则正常使用它。
>
> ● 字体失败时段：如果未加载字体，则浏览器将其视为加载失败，并使用正常字体进行回退渲染。font-display的属性值就是围绕字体显示时间线展开的。

```css
font-display: [auto | block | swap | fallback | optional]
```

● auto：字体显示策略由浏览器决定，大多数浏览器的字体显示策略类似block。

● block：字体阻塞时段较短（推荐3s），字体交换时段无限。此值适合图标字体场景。

● swap：字体阻塞时段极短（不超过100ms），字体交换时段无限。此值适合用在小段文本，同时文本内容对页面非常重要的场景。

● fallback：字体阻塞时段极短（不超过100ms），字体交换时段较短（推荐3s）。此值适合用于大段文本，例如文章正文，同时对字体效果比较看重的场景，例如广告页面、个人网站等。

● optional：字体阻塞时段极短（不超过100ms），没有字体交换时段。此值的作用可以描述为，如果字体可以瞬间被加载（例如已经被缓存了），则浏览器使用该字体，否则使用回退字体。optional是日常Web产品开发更推荐的属性值，因为无论任何时候，网页内容在用户第一次访问时快速呈现是最重要的，不能让用户等待很长时间后再看到你认为的完美效果。



> 如果自定义字体的大小在30 KB以内，建议直接用Base64将其内联在页面中。不过只有woff2字体采取内联处理，woff字体依旧采用url()函数外链体验最佳，因为此时现代浏览器中的字体都是瞬间渲染，根本无须使用font-display属性进行字体加载优化



### 字符单元的中断与换行

字符单元默认的中断与换行规则如下。

● Space普通空格、Enter（回车）空格和Tab（制表符）空格这3种空格无论怎样组合都会合并为单个普通空格。

● 文字可以在CJK文本、普通空格和短横线连字符处换行，连续英文单词和数字不换行。



word-break

```css
word-break: normal | break-all | keep-all | break-word;
```

keep-all这个属性值可以让CJK文本不换行排版，同时又不影响非CJK文本的排版行为[效果预览](https://demo.cssworld.cn/new/3/6-1.php)

[word-break 属性效果](https://demo.cssworld.cn/new/3/6-2.php)



hyphens属性与连字符

> hyphens是专为英文场景设计的一个属性，这个属性可以让英文单词断开换行的时候带上连字符（也就是短横线），这样可以让读者知道上一行的尾部和下一行的头部连起来是一个完整的单词

```css
hyphens: none| manual|auto;
```

> 属性值auto可以让英文单词在行尾自动换行，同时带上短横线。需要注意的是，英文单词换行不需要设置word-break或者word-wrap属性，hyphens属性自带换行能力。如果你设置了word-break:break-all声明，反而不会有短横线效果。

> 连字符总共分两种。
>
> 一种是“硬连字符”（U+2010），称为可见的换行机会。这个字符就是我们键盘上的短横线“-”，是可见的。
>
> 另一种是“软连字符”（U+00AD），称为不可见的换行机会。这个字符很有意思，通常情况是隐藏的，但是，如果浏览器觉得有必要在软连字符所在位置打断单词，则连字符又会变为可见。

```html
<p>大家好，我叫zhang&shy;xin&shy;xu，感谢大家购买我的书。</p>
```

[hyphens:manual与软连字符优化排版效果实例页面](https://demo.cssworld.cn/new/3/6-3.php)

> 不过由于在**Chrome浏览器中不支持hyphens:none这个声明**，因此其没有实用价值。



#### \<wbr>与精确换行的控制

> HTML中有一个\<wbr>元素标签，可以实现连续英文和数字的精准换行，具体效果如下：如果宽度足够，不换行；如果宽度不足，则在\<wbr>元素所在的位置进行换行。也就是说，\<wbr>元素提供了一个换行机会。

[\<wbr>标签与排版效果优化实例页面](https://demo.cssworld.cn/new/3/6-4. php)

**\<wbr>实现换行原理**

> \<wbr>之所以能够创造新的换行机会，是因为其创建了一个带有换行特性的宽度为0px的空格。该空格的Unicode编码是U+200B，因此\<wbr>标签也可以替换为&#x200b;，例如下面HTML代码实现的效果和标签的效果是一样的

```html
<p>大家好，我叫zhang&#x200B;xin&#x200B;xu，感谢大家购买我的书。</p>
<p>大家好，我叫zhang<wbr>xin<wbr>xu，感谢大家购买我的书。</p>
```

**IE不兼容处理**

```css
wbr:after {
	content: '\00200B'	
}
```

**\<wbr>与\<br>换行的区别**

> \<wbr>是“Word Break Opportunity”的缩写，表示有机会就断开换行；而\<br>则是直接换行显示，无论宽度是否足够。



> ● 动态文本内容换行使用word-break:break-all和word-wrap:break-word组合代码，如果要彻底换行，还可以使用line-break:anywhere。
>
> ● 静态内容排版不建议使用word-break属性、word-wrap属性或者line-break属性，如果是英文单词，则使用&shy;软连字符优化排版；如果是非英文单词，则使用\<wbr>标签优化排版。

#### overflow-wrap:anywhere声明有什么用

> 这里主要讲一下属性值anywhere的作用。在展开讲解之前，先给大家讲解一个概念，关于“硬换行”和“软换行”。硬换行会在文本的换行点处插入实际换行符；而软换行的文本实际上仍在同一行，但看起来它被分成了多行，例如通过word-break:break-all让长英文单词换行就属于软换行。
>
> 属性值anywhere正常状态下的效果和属性值break-word类似，具体描述为：如果行中没有其他可接受的断点，则可以在任何点断开原本不可断开的字符串（如长单词或URL），并且在断点处不插入连字符。
>
> 属性值anywhere和属性值break-word的不同之处在于，overflow-wrap:anywhere在计算最小内容尺寸的时候会考虑软换行，而overflow-wrap:break-word则不会考虑软换行

overflow-wrap属性的正式语法如下：

```css
overflow-wrap: normal | break-word | anywhere;
```

[overflow-wrap:anywhere和break-word的区别实例页面](https://demo.cssworld.cn/new/3/6-5.php)

> 由于overflow-wrap:anywhere的兼容性不佳，因此它目前的实用性远不及line- break:anywhere，大家了解即可。



#### text-align属性相关的新特性

> text-align属性支持常用的属性值left、right、center、justify，也支持逻辑属性值start、end，除此之外，还新增了多个其他属性值。

```css
text-align: match-parent| justify-all| <string>;
```

> 不过这几个新增的属性值的兼容性都不太好，因此不适合在生产环境中使用，大家先简单了解一下即可。

match-parent

> match-parent视觉表现类似inherit，由于text-align属性本身就具有继承性，因此，match-parent不是用来改变视觉上的对齐效果的，而是用来改变“看不见”的对齐计算值。

justify-all属性

> 它和justify属性值的区别在于justify-all属性值可以让最后一行也表现为两端对齐。可惜目前还没有浏览器支持justify-all属性值

**CSS Text Module Level 4规范中，text-align属性还新增了对字符属性值的支持。**

```css
td {
    text-align: '.' center;
}
```

```html
<table>
    <col width="40">
    <tr><th>长途电话费</th></tr>
    <tr><td>￥1.30</td></tr>
    <tr><td>￥2.50</td></tr>
    <tr><td>￥10.80</td></tr>
    <tr><td>￥111.02</td></tr>
    <tr><td>￥85</td></tr>
    <tr><td>￥.30</td></tr>
    
</table>
```

此时，单元格的数值会按照字符“.”进行对齐



#### text-decoration属性全新升级

> text-decoration属性则是一个CSS缩写属性，完整的CSS属性包括text-decoration-line、text-decoration- style、text-decoration-color和text-decoration-thickness。

- text-decoration-line

	表示装饰线的类型。支持多个值一起使用

	1. none 没有装饰线
	2. underline 下划线
	3. overline 上划线
	4. line-through 贯穿线

- text-decoration-style

	表示装饰线的样式风格，

	1. solid	实线
	2. double 双实线
	3. dotted 点线
	4. dashed 虚线
	5. wavy 波浪线

- text-decoration-color：表示装饰线的颜色。

- text-decoration-thickness：表示装饰线的粗细

正式语法：

```css
text-decoration: <text-decoration-line> || <text-decoration-style> || <text-decoration-color> || <text-decoration-thinckness>
```

意思就是，4个子属性值位置随机、组合随机

[text-decoration 属性叠加](https://demo.cssworld.cn/new/3/8-1.php)

[宽度100%自适应的波浪线效果实现实例页面](https://demo.cssworld.cn/new/3/8-2.php)

**text-underline-position:under声明**

text-underline-position属性可以用来设置下划线的位置。

[text-underline-position属性实例页面](https://demo.cssworld.cn/new/3/8-3.php)

> text-underline-position属性除了支持under，还支持left、right和from-font这几个CSS规范定义的属性值。
>
> ● left和right这两个属性值平常根本用不到，只有在使用writing-mode属性让文字垂直排版，并且需要控制下划线左右位置的时候才会用到。
>
> ● from-font是CSS文本装饰规范Level 4新增的属性值，表示优先使用字体文件中设置的下划线位置，如果字体没有设置下划线对齐信息，就使用auto效果。大家目前可以无视from-font这个属性值，因为就算不考虑其目前糟糕的兼容性，这个属性值也是非常不实用的，限制太多了。
>
> 总而言之，如果使用了文字的下划线效果，你可能需要text-underline-position: under来优化下划线的显示位置。

**text-underline-offset属性**

> text-underline-position属性虽然可以调整下划线的位置，但是调整的位置都是固定的，不能满足多变的开发需求。此时，你更需要text-underline-offset属性。
>
> 

[text-underline-offset属性实例页面](https://demo.cssworld.cn/new/3/8-4.php)

> 百分比值表示偏移量相对于1em的大小。因此，text-underline-offset: 100%等同于text-underline-offset:1em。text-underline-offset支持负值，此时下划线就会向上偏移。text-underline-offset属性只对下划线类型的装饰线有效，对删除线和上划线都无效

**text-decoration-skip属性**

> text-decoration-skip属性可以用来控制装饰线和文字之间的重叠关系，这里的装饰线专指下划线。因为一些英文字符（如“g”“q”）的“小尾巴”会和下划线重叠，所以需要使用text -decoration-skip属性设置装饰线是跳过文字，还是和文字连在一起。虽然删除线（text-decoration:line-through）也会和文字重叠，但是它并不是text-decoration- skip属性的目标装饰线，因为删除线必须贯穿文字。



### color属性与颜色设置

148**个颜色关键字**

> [查看](https://demo.cssworld.cn/new/3/9-1.php)

（1）HTML中color属性算法和CSS中的color属性算法是不一样的。同样是一个无法识别的颜色关键字，在HTML中这个无法识别的颜色关键字会渲染成另外一个颜色，而在CSS中会直接忽略这个颜色关键字。具体案例参见《CSS世界》一书的9.1.1节。

（2）颜色关键字设置的颜色都是实色，不带透明度。目前没有CSS语法可以直接让颜色关键字带有透明度，唯一可行的方式是借助animation-delay负值实现。

（3）颜色关键字不区分大小写。例如下面语法也是可以正常解析的

（4）有一些颜色关键字是互相等同的

（5）所有颜色关键字中，只有两个颜色关键字是以“deep”开头的，分别是深天蓝色deepskyblue和深粉色deeppink，这是我最喜爱的两个颜色关键字。

（6）暗灰色darkgray的颜色要比灰色gray更浅，因此，并不是有“dark”前缀的颜色关键字控制的颜色就更深。

（7）在148个颜色关键字中，只有CSS Color Level 4新增的颜色关键字rebeccapurple不是规范的颜色名词，有人或许会奇怪为什么会有这样“不规范”的命名，实际上，这个命名的背后有一段你不知道的温情故事

#### **transparent关键字**

> 透明色

#### **currentColor关键字**

> currentColor关键字非常实用，且无可替代，它表示当前元素（或伪元素）所使用的color属性的计算值，currentColor关键字从IE9浏览器开始被支持。比较常用的全局设置是：

```css
body {
	fill: currentColor;
}
```

> 需要注意的是，CSS中很多属性的默认颜色就是color属性值，没有必要专门指定currentColor关键字，包括border-color、outline-color、caret-color、text-shadow、box-shadow等CSS属性

#### **RGB颜色和HSL颜色的新语法**

**HSL颜色**

> HSL颜色是由色调（Hue）、饱和度（Saturation）和亮度（Lightness）这3个元素组成的。HSL就是由这3个元素对应的英文单词的首字母组成的。色调值的大小是任意的，因为CSS世界中与色调相关的数值都是角度值，其单位是deg，所以无论设置什么值，最终都会在0～360deg这个范围中解析，例如−120deg=240deg、480deg=120deg。

**RGBA颜色**

> 从IE9浏览器开始，支持设置颜色的透明度了，RGBA和HSLA中的字母A指的就是Alpha透明通道，透明度取值范围任意，但是渲染范围是0～1，也就是小于0的透明度会当作0渲染，大于1的透明度会当作1渲染

**#RRGGBBAA颜色**

> 目前现代浏览器已经全面支持#RRGGBBAA颜色。其中R、G、B还是原来的十六进制表示的RGB色值，范围从00～FF，至于这里新出现的AA，则表示透明度，范围也是00～FF。
>
> #RRGGBBAA颜色虽然很实用，但是透明度的转换却没有RGB色值那么方便，因为透明度范围是0%～100%，而00～FF的十进制范围是0～255，所以需要转换

```js
// Alpha 范围 0~1
AA = (Alpha * 255).toString(16)
```

透明度50%就等于(0.5*255).toString(16)，结果是7f.8，约等于80。因此#FF000080就表示透明度约为50%的红色。

> 同样，#RRGGBBAA颜色也支持缩写，例如#f308等同于#ff330088



### 必学必会的background属性新特性



#### background-size属性

1. cover和contain

	cover和contain是两个关键字属性值，两者都不会改变背景图的原始比例，非常适合背景图像高宽不确定的场景。

	[效果示例](https://demo.cssworld.cn/new/3/10-1.php)

2. 理解auto关键字下的尺寸渲染规则

	> 在深入理解auto关键字的尺寸渲染规则之前，我们需要先了解一下常见图像的内在尺寸和内在比例。在CSS世界中，常见的图像有以下几种。

	● 位图。例如JPG或者PNG图片都属于位图，这些图像总是有自己的**内在尺寸（原始图像大小）**和**内在比例（原始图像比例）**。

	● 矢量图。例如SVG图像就属于矢量图，这些图像不一定具有内在尺寸。如果水平尺寸和垂直尺寸都设置了，那么它就具有内在的比例；如果没有设置尺寸，或者只设置了一个方向的尺寸，它可能有比例，也可能没有比例，要视SVG内部代码而定，如有些SVG元素内部只有\<defs>元素，此时矢量图就没有比例。

	● 渐变图像。就是使用CSS渐变语法绘制的图像，这些图像是没有内在尺寸和内在比例的。

	● 元素图像。例如使用element()函数把DOM元素作为背景图，此时的内在尺寸就是这个DOM元素的尺寸。

	**auto关键字的尺寸渲染规则**

	（1）如果图像水平和垂直方向同时具有内在尺寸，则按照图像原始大小进行渲染。例如一个PNG图片尺寸是800px×600px，那么背景图的尺寸就是800px×600px，这就是多倍图一定要设置background-size属性的原因，否则只能显示部分图像内容。

	（2）如果图像没有内在尺寸，也没有内在比例，则按照背景定位区域的大小进行渲染，等同于设置属性值为100%。所以，CSS渐变图像默认都是覆盖整个背景定位区域的，

	（3）如果图像没有内在尺寸，但具有内在比例，则渲染效果等同于设置属性值为contain [效果示例](https://demo.cssworld.cn/new/3/10-2.php)

	> preserveAspectRatio="none"可以去除SVG图像的内在比例，根据我的测试，除Firefox浏览器的渲染有问题外，IE、Edge和Chrome浏览器都可以达到预期效果。
	>
	> 

	（4）如果图像只有一个方向有内在尺寸，但又具有内在比例，则图像会拉伸到该内在尺寸的大小，同时宽高比符合内在比例

	（5）如果图像只有一个方向有内在尺寸而没有内在比例，则图像有内在尺寸的一侧会拉伸到该内在尺寸大小，没有设置内在尺寸的一侧会拉伸到背景定位区域大小。

3. 数值或百分比值

	background-size属性值无论是数值还是百分比值，都不能是负值，其中百分比值是相对于元素的背景定位区域计算的。背景定位区域是由background-origin属性决定的，默认值是padding box。[效果展示](https://demo.cssworld.cn/new/3/10-3.php)

#### background属性最成功的设计——多背景

语法很简单，就是使用逗号分隔多个独立的背景定位语法。

、

```css
.multiple-backgrounds {
    background: url(1.jpg) no-repeat top,url(1.jpg) no-repeat bottom;
}
```

**CSS渐变本质上也是一种图像，它也可以作为background-image的属性值。为什么CSS渐变的出现让多背景重获新生了呢？**

（1）CSS渐变可以实现纯色效果，只要渐变起止颜色一样即可。

（2）background-size属性也支持多背景，且可以任意控制尺寸。

[css渐变和多背景实现加减图标按钮效果](https://demo.cssworld.cn/new/3/10- 4.php)

[多背景实现灰白网格效果](https://demo.cssworld.cn/new/3/10-5.php)

更复杂、更“炫酷”的背景图案和纹理效果可以访问Css3 Patterns Gallery网站

如果想让语句合法，则需要使用CSS多背景语法，并且颜色值必须放在最后



#### background-clip

支持以下属性

```css
background-clip:border-box|padding-box|content-box|text;

```

> 其中border-box是默认值，表示背景图像或者背景颜色的显示区域是整个border-box，也就是边框的下方也会显示背景内容。

background-clip属性最实用的应用场景之一就是控制背景颜色的显示范围

举个例子，我们想要模拟一个复选框效果，设计师设计的复选框尺寸只有18px×18px，区域太小了，不容易点中，尤其是在移动端，此时我们就可以借助透明边框增大点击区域的范围，

- ##### HTML：

	```html
	默认：<label class="checkbox"></label> 
	选中：<label class="checkbox checked"></label>
	```

- ##### CSS：

	```css
	.checkbox {
	    display: inline-block;
	    width: 18px; height: 18px;
	    border: 3px solid transparent;
	    box-shadow: inset 0 1px, inset -1px 0, inset 0 -1px, inset 1px 0;
	    color: gray;
	    user-select: none;
	    vertical-align: middle;
	}
	.checkbox.checked {
	    color: deepskyblue;
	    background: currentColor;
	    background-clip: padding-box;
	}
	.checkbox.checked::before {
	    content: "";
	    display: block;
	    width: 10px; height: 3px;
	    margin: 5px auto 0;
	    border: solid #fff;
	    border-width: 0 0 2px 2px;
	    transform: rotate(-45deg);
	}
	```

[效果展示](https://demo.cssworld.cn/new/3/10-6.php)



#### background-clip:text声明与渐变文字效果

> background-clip:text原本是webkit内核浏览器的私有声明，只在Safari浏览器和Chrome浏览器中有效。后来，因为这个特性还挺实用，所以其被加入了最新的CSS边框和背景模块规范，目前Firefox浏览器和Edge15+浏览器也提供了支持。background-clip:text可以让背景图像按照字符形状进行剪裁，此时我们只要隐藏文字，就可以看到字符形状的背景效果了。这个特性可以用来实现文字纹理效果和更常见的文字渐变效果

[文字渐变效果](https://demo.cssworld.cn/ new/3/10-7.php)

> 另外，如果使用background-clip:text声明实现渐变文字效果，那么原本彩色的emoji字符的颜色会丢失。

#### **background-origin属性与背景定位原点控制**

background-origin属性支持下面几个属性值：

```css
background: border-box|padding-box|content-box;
```

#### background-repeat属性新增了space和round这两个关键字属性值

1. space 平铺

	让背景图像尽可能地重复，而不进行剪裁，每个重复单元的尺寸不会变化。其中第一张和最后一张图像固定在元素的两边，然后通过拉伸空白区域让剩余的图像均匀分布

2. round 拉伸

	背景图像会被拉伸，并保证不留间隙。随着定位区域空间的增加，如果（假设图像都是原始尺寸下的）剩余空间大于图像宽度的一半，则添加另外一张图像。在添加下一张图像时，当前的所有图像都会压缩以留出空间放下这个新添加的图像

[效果展示](https://demo.cssworld.cn/new/3/10-8.php)

#### 可以指定background-position的起始方位了

> 由于background-position的百分比定位的计算比较特殊（详见《CSS世界》一书的9.2.2节），因此，在过去，我们想要实现一个距离元素右下方特定距离的定位就不太容易，唯一的方法就是借助透明边框帮助定位。好在IE9浏览器已经开始支持指定background-position的起始位置了，于是我们想要让背景图像在距离右下方20px的位置就非常简单了：

```css
.example {
    width: 300px; height: 200px;
    border: solid deepskyblue;
    background: url(1.jpg) no-repeat right 20px bottom 20px;
}
```

[效果展示](https://demo.cssworld.cn/new/3/10-9.php)

也有这种写法

```css
.example {
    background: url(1.jpg) no-repeat calc(100% - 20px) calc(100% - 20px);
}
```

> 虽然这种写法也有效果，但是性能不够好，而且这种写法可能会让IE9浏览器崩溃

> **如果只有1个值**，无论是具体的数值、百分比值，还是关键字属性值，**则另外一个值一定是center**

> 总结一下，IE浏览器从IE9版本开始就已经支持background-position属性的3值和4值语法，因此不要再使用calc()函数实现相对于右下方的定位了。



### outline相关新属性outline-offset

outline-offset属性用于改变outline属性设置的轮廓的偏移位置。

以我的经验来看，outline-offset属性使用负值来缩小轮廓的频率要比使用正值来扩大轮廓的频率高很多。

[outline轮廓被覆盖out-offset优化示例](https://demo.cssworld.cn/new/3/11-1.php)

让合适的outline尺寸配合合适的outline-offset负值，我们可以通过颜色覆盖的方式实现渐变边框效果，或者绘制一个加号图案

```css
.example {
    width: 120px; height: 120px;
    background: linear-gradient(deepskyblue, deeppink);
    outline: 40px solid #fff;
    outline-offset: -92px;
}
```



### cursor属性新增的手形效果

> 

#### 放大手形zoom-in和缩小手形zoom-out简介

cursor:zoom-in的鼠标指针是[插图]形状，cursor:zoom-out的鼠标指针是[插图]形状，多用于在桌面端的网页中查看大图的交互场景中

[效果展示](https://demo.cssworld.cn/new/3/12-1.php)

#### 抓取手形grab和放手手形grabbing

cursor:grab的鼠标指针是一个五指张开的手的形状[插图]，cursor:grabbing的鼠标指针是一个五指收起的手的形状[插图]，它们多用在元素预览空间不足并需要拖动元素的交互场景中

[效果展示](https://demo.cssworld.cn/new/ 3/12-2.php)

> 

## 全新的布局方式

### 分栏布局

> 分栏布局也被称为多列布局、多栏布局，这种布局可以将内容布局到多个列框中，类似报纸上的排版。
>
> 分栏布局比较特殊，有别于传统布局，它将子元素在内的所有内容拆分为列，这与打印网页的时候将网页内容分成多个页面的方式类似。分栏布局主要针对图文排版布局，应用在横向排版场景中，文档流是倒N方向。有个别布局只能使用分栏布局实现，分栏布局虽然在日常开发中用得不多，但是遇到合适的场景时是一种非常有用的布局方式。

- ##### HTML：

	```html
	<ul> 
	    <li>重庆市</li>
	    <li>哈尔滨市</li>
	    <li>长春市</li>
	    <li>兰州市</li>
	    <li>北京市</li>
	    <li>杭州市</li>
	    <li>长沙市</li>
	    <li>沈阳市</li>
	    <li>成都市</li>
	    <li>合肥市</li>
	    <li>天津市</li>
	    <li>西安市</li>
	</ul>
	```

- ##### CSS：

	```css
	ul {
	    columns: 2;
	}
	```

[效果展示](https://demo.cssworld.cn/new/6/1-1.php)

> 相比其他布局方法，分栏布局最大的优点是不会改变元素原本的display计算值。例如，在默认状态下，<li>元素会出现项目符号，如圆点或数字序号。此时，如果对<li>元素使用弹性布局或网格布局，则项目符号就会消失，因为display:flex或display:grid会重置<li>元素内置的display:list-item声明

**分栏布局相关的CSS属性**

● columns；

● column-width；

● column-count；

● column-rule；

● column-rule-color；

● column-rule-style；

● column-rule-width；

● column-span；

● column-fill；

● column-gap。

> 虽然这10个CSS属性都有各自的作用，但是在实用程度上却有明显的差异。根据我的开发经验，超过80%的分栏布局只需要使用columns属性就足够，因此，大家的学习重心可以放在columns属性上，column-gap属性有时候也会用到，所以也可以关注下，至于剩下的属性，大家了解一下基本作用即可。

#### 重点关注columns属性

> columns属性是column-width和column-count属性的缩写，举几个使用columns属性的例子：

```css
/*栏目宽度*/
colums: 18em;

/*栏目数目*/
columns: auto;
columnsL 2;

/* 同时定义宽度和数目，顺序任意 */
columns: 2 auto;
columns: auto 2;
columns: auto 12em;
columns: auto auto;
```

**关于column-width**

> column-width表示每一栏/列的最佳宽度，注意，是“最佳宽度”，实际渲染宽度多半和指定的宽度是有出入的.。它不支持 % 语法

```css
.container {
    width: 300px;
    column-width: 200px;
}
```

这里容器宽度为300px，设定每一栏的宽度是200px，不足以分栏，此时容器里面的内容会无视column-width:200px声明，并按照容器的300px宽度排版。

```css
.container {
    width: 200px;
    column-width: 300px;
}
```

> 这里容器宽度为200px，设定的每一栏宽度是300px，比容器宽度还要宽，此时容器里面的内容会无视column-width:300px声明，并按照容器的200px宽度排版。

那么，什么情况下column-width设置的宽度值和实际渲染的宽度值一致呢？

这个问题的答案可能会出乎大家的意料：**几乎不存在分栏布局的栏目宽度就是column-width设置的宽度这样的场景。**

> 因为column-width和传统的width属性不同，column-width更像是一个期望尺寸，浏览器会根据这个期望尺寸确定分栏数目，一旦分栏数目确定了，column-width属性的使命也就完成了，接下来根据分栏数目对容器进行的分栏布局就和column-width属性没有任何关系了。

**关于column-count**

> column-count表示理想的分栏数目，又出现了很微妙的词——“理想的”，也就是意味着最终的分栏数目可能不受column-count属性值的控制。
>
> 在分栏布局中，最终分栏的数量要么由column-count属性决定，要么由column-width属性决定，这两个CSS属性都可能有更高的决定权，至于哪个CSS属性的决定权更高，是要看具体场景的。
>
> 决定权优先级的计算诀窍可以用一句话概括：**统一转换column-count值，哪个值小就使用哪一个。**

- ##### HTML：

	```html
	<div class="container-1">...</div>
	<div class="container-2">...</div>
	```

- ##### CSS：

	```css
	/*
		此容器设置2列
		容器宽/期望宽 = 360/100 ≈ 3 列
		取最小列2
	*/
	.container-1 {
	    width: 360px;
	    column-count: 2;
	    column-width: 100px;
	}
	/*
		此容器设置4列
		容器宽/期望宽 = 360/100 ≈ 3 列
		取最小列3
	*/
	.container-2 {
	    width: 360px;
	    column-count: 4;
	    column-width: 100px;
	}
	```

[效果展示](https://demo.cssworld.cn/new/6/1-2.php)

#### column-gap和gap属性的关系

> column-gap属性表示每一栏之间的空白间隙的大小，可以是长度值，也可以是百分比值

```css
/*关键字*/
coulmn-gap: normal;

/*长度值*/
coulmn-gap: 3px;
coulmn-gap: 3em;

/*百分比*/
coulmn-gap: 3%;
```

实际上，在分栏布局中，如果不考虑IE浏览器，我们可以直接使用gap属性设置分栏间隙大小，例如：

```css
.container {
    columns: 2;
    gap: 1rem;
}
```

至于原因，用一句话解释就是：**column-gap是gap属性的子属性。**

> 在网格布局规范制定之后的一段时间，CSS世界中的行与列之间的间隙使用了gap属性进行了统一，也就是分栏布局、弹性布局和网格布局的间隙都全部统一使用gap属性表示，而gap属性实际上是column-gap属性和row-gap属性的缩写。



#### 了解column-rule、column-span和column-fill属性

> 平常不太用得到，多出现在对分栏布局效果要求更高的场景中。

**了解column-rule属性**

> column-rule属性是column-rule-width、column-rule-style和column-rule- color这3个CSS属性的缩写。
>
> column-rule属性和border属性的语法和规则是一模一样的，只是column-rule是设置各个分栏的分隔线样式，border是设置元素的边框样式

- ##### CSS：

	```css
	.container {
	    width: 320px;
	    border: solid deepskyblue;
	    padding: 10px;
	    column-count: 2;
	    column-rule: dashed deepskyblue;
	}
	```

[效果展示](https://demo.cssworld.cn/new/6/1-3.php)

**了解column-span属性**

> column-span属性有点类似表格布局中的HTML属性colspan，表示某一个内容是否跨多栏显示。这个CSS属性是作用在分栏布局的子元素上的。

```css
column-span: none| all;
```

● none表示不横跨多栏，默认值。

● all表示横跨所有垂直列。

**【不支持数字**】

[all属性效果展示](https://demo.cssworld.cn/new/6/1-4.php)



**了解column-fill属性**

> column-fill的作用是当**内容分栏的时候平衡每一栏填充的内容**

```css
column-fill: auto | balance | balance-all;
```

● auto的作用是按顺序填充每一列，内容只占用它需要的空间。

● balance是默认值，作用是尽可能在列之间平衡内容。在分隔断开的上下文中，只有最后一个片段是平衡的。例如，有多个<p>元素，正好最后一个\<p>换行了，那这个\<p>元素的内容前后等分，保持平衡。这会造成最后一栏内容较少的问题。

● balance-all的作用是尽可能在列之间平衡内容。在分隔断开的上下文中，所有片段都是平衡的。该属性值目前没有任何浏览器支持，可以忽略。

[效果展示](https://demo.cssworld.cn/new/6/1-5.php)

（1）所有浏览器都能识别column-fill:auto，但是，需要容器有固定的高度才能准确渲染。如果容器没有设置具体的高度值，则仅在Firefox浏览器中有比较符合预期的渲染。因此，在实际开发的时候，column-fill:auto声明的使用一定要配合容器元素的height属性。

（2）所有浏览器都不能识别column-fill:balance-all



[分栏布局实现两端对齐布局]()

- ##### HTML：

	```html
	<h4>分栏布局实现两端对齐</h4>
	<div class="container">
	    <div class="list"></div>
	    <div class="list"></div>
	    <div class="list"></div>
	</div>
	```

- ##### CSS：

	```css
	.container {
	    width: 300px;
	    border: solid deepskyblue;
	    column-count: 3;
	    column-gap: 5%;
	}
	.list {
	    height: 100px;
	    background-color: deeppink;
	}
	```



**break-inside属性与元素断点位置的控制**

> break-inside属性可以定义页面打印、分栏布局或Regions布局（已废弃）发生中断时元素的表现形式。如果没有发生中断，则忽略该属性。

```css
break-inside: auto| avoid | avoid-page | avoid-column | avoid-region;
```

只需关注下面两个

● auto表示元素可以中断。

● avoid表示元素不能中断。

[效果展示](https://demo.cssworld.cn/new/6/1-7.php)



**box-decoration-break属性与元素断点装饰的控制**

> 在默认情况下，元素片段在跨行、跨列或跨页（如打印）时候的样式是分割渲染的，例如边框或者圆角都是分割开的



### 弹性布局

> 专门为现代Web应用设计的布局方式，代码简单，样式丰富，目前已经逐渐成为主流的布局方式，是所有前端开发者需要重点学习的布局方式。

弹性布局相关属性分为4个大的类目，分别是流向控制、对齐设置、顺序控制和弹性设置。

流向控制对应flex-flow属性及其相关属性。与对齐设置相关的CSS属性在弹性布局和网格布局中是通用的。顺序控制对应order属性，平常用得不多。弹性设置对应flex属性及其相关属性。

#### 设置display:flex声明发生了什么？

> 给任意元素设置display:flex声明或者display:inline-flex声明，弹性布局就会被创建。
>
> display:inline-flex声明可以让flex容器保持内联特性，也就是可以让图片和文字在一行显示，多用在精致的小控件布局中，至于其他特性表现则和display:flex声明一模一样。

```html
<div class="container">
    <content>1</content>
    裸露的文本元素
    <content>2</content>
    <content>3</content>
    
</div>
.container {
	display: flex;
}
```



1. flex子项块状化

	默认状态下，\<content>元素的display计算值是inline，而在上面这个例子中，\<content>元素的display计算值是block，其变成了块级元素。

	flex子项都是块级水平元素，因此，在flex子项元素中使用vertial-align属性一定是没有任何效果的。

	此时字符**裸露的文本元素**会变成**匿名块级元素**，和前后两个\<content>元素并排显示，如图6-15所示。但是，如果仅是空格字符（Enter空格、Space空格或Tab空格），则不会渲染，即使设置white- space:pre或者white-space:pre-wrap也是如此

2. flex子项浮动失效

	控制flex子项左右对齐是有专门的CSS属性的

3. flex子项支持z-index属性

	即使flex子项的position属性的计算值是static，flex子项也是支持z-index属性的。如果z-index属性值不是auto，则会创建新的层叠上下文

4. flex子项的margin值不会合并

	这一点和普通的块级元素不一样。

5. flex子项是格式化的尺寸

	就是说flex子项的尺寸在底层是有具体的计算值的，因此，我们可以使用margin:auto进行剩余空间的智能分配

6. 其他

	- flex子项如果被设置为绝对定位，则会脱离弹性布局
	- flex子项的尺寸默认表现为收缩，如果要设置建议的尺寸，可以给flex子项使用flex-basis属性，或者使用缩写的flex属性。
	- flex子项默认是水平排列的，这个特性表现是由flex-direction属性决定的
	- 而且就算flex子项的宽度之和超过flex容器，flex子项也不会换行，这个特性表现是由flex-wrap属性决定的

#### flex-direction属性与整体布局方向

> flex-direction属性用来控制flex子项整体布局方向，决定是从左往右排列还是从右往左排列，是从上往下排列还是从下往上排列

```css
flex-direction: row | row-reverse | column | column-reverse;
```

● row是默认值，表示flex子项显示为水平排列。

● row-reverse表示flex子项显示为水平排列

● column表示flex子项显示为垂直排列

● column-reverse表示flex子项显示为垂直排列，但方向和column属性值相反

[效果展示](https://demo.cssworld.cn/new/6/2-1.php)

#### flex-wrap属性与整体布局的换行表现

> flex-wrap属性用来控制flex子项是单行显示还是换行显示，以及在换行情况下，每一行内容是否在垂直方向的反方向显示。

```css
flex-wrap: nowarp | wrap | wrap-reverse;
```

● nowrap是默认值，表示flex子项是单行显示，且不换行

● wrap表示flex容器宽度不足的时候，flex子项会换行显示

● wrap-reverse表示宽度不足的时候，flex子项会换行显示，但是flex子项是从下往上开始排列的，也就是原本换行到下面一行的flex子项现在换行到上面一行

[效果展示](https://demo.cssworld.cn/new/6/2-2.php)

#### 熟练使用flex-flow属性

> flex-flow属性是flex-direction属性和flex-wrap属性的缩写，表示弹性布局的流动特性。

```css
flex-flow: <flex-direction> || <flex-wrap>;
```

使用时不区分方向，可以随意省略一个值

#### 对齐特性综述

在整个CSS世界中是有通用的含义的，具体如下：

● justify表示水平方向的样式设置；

● align表示垂直方向的样式设置；

● items表示全体元素的样式设置；

● content表示整体布局的样式设置；

● self表示元素自身的样式设置，其一定是应用在子元素上的。

因此，justify-content属性就表示整体布局的水平对齐设置，align-items就表示全体元素的垂直对齐样式设置。



#### justify-content属性与整体布局的水平对齐

常用语法

```css
justify-content: normal | flex-start | flex-end | center | space-between| space-evenly| space-around;
```

● normal是初始值，表示根据环境不同，可以采用不同的对齐表现。flex默认表现类似于start

● flex-start可以看成默认值，它是一个逻辑CSS属性值，与文档流方向相关，默认表现为整体布局左对齐。

● flex-end是逻辑CSS属性值，与文档流方向相关，默认表现为整体布局右对齐。**注意，如果flex容器设置了overflow滚动，同时应用justify-content:flex-end，滚动效果会失效。**

● center表现为整体布局居中对齐。

● space-between表示多余的空白间距只在元素中间区域分配

● space-around表示每个flex子项两侧都环绕互不干扰的等宽的空白间距，最终在视觉表现上边缘两侧的空白只有中间空白宽度的一半

● space-evenly表示每个flex子项两侧空白间距完全相等

[效果展示](https://demo.cssworld.cn/new/6/2-4.php)



#### 垂直对齐属性align-items与align-self

> align-items属性和align-self属性的一个区别是align-self属性是设置在具体的某一个flex子项上的，而align-items属性是设置在flex容器元素上的，控制所有flex子项的垂直对齐方式；另一个区别是align-self属性的初始值是auto，其余的属性值align-items属性和align-self属性的是一样的，含义也是一样的。

常用语法：

```css
align-items: stretch | flex-start | flex-end | center | baseline;

align-self: auto | stretch | flex-start | flex-end | center | baseline;
```

● stretch可以看成弹性布局中align-items属性的默认值，表示flex子项在垂直方向上拉伸。

● auto是align-self属性的默认值，表示flex子项的垂直对齐方式是由flex容器的align-items属性值决定的。

● flex-start是逻辑CSS属性值，与文档流方向相关，默认表现为flex子项顶部对齐

● flex-end是逻辑CSS属性值，与文档流方向相关，默认表现为flex子项底部对齐

● center表示flex子项都是垂直居中对齐

● baseline表示flex子项参与基线对齐。注意这里的措辞是“参与基线对齐”，并不是指flex子项和基线对齐，而是让所有flex子项的内外基线都在一条水平线上，换句话说，就是给每个flex子项里里外外写上多个字母“x”，这些字母“x”的下边缘保持对齐

[效果展示](https://demo.cssworld.cn/new/6/2-5.php)

> 补充一个小知识点，如果flex-direction属性的值是column或是column-reverse，则flex子项的垂直对齐不应使用align-items属性控制，而是应该使用justify-content属性控制

> align-self属性的各个属性值的表现和align-items属性的各个属性值一样，区别在于align-self属性设置的是具体某个flex子项的垂直对齐方式
>
> [效果展示](https://demo.cssworld.cn/new/6/2-6.php)



#### align-content属性与整体布局的垂直对齐

> lign-content属性和align-items属性的区别在于align-items属性设置的是每一个flex子项的垂直对齐方式，而align-content属性将所有flex子项作为一个整体进行垂直对齐设置。

```css
align-content: stretch | flex-start | flex-end | center | space-between | space-around | space-evenly;
```

● stretch可以看成弹性布局中align-content属性的默认值，表示每一行flex子项都等比例拉伸。例如，如果共两行flex子项，则每一行拉伸的高度是50%。

● flex-start是逻辑CSS属性值，与文档流方向相关，默认表现为顶部堆砌。

● flex-end是逻辑CSS属性值，与文档流方向相关，默认表现为底部堆放。

● center表现为整体垂直居中对齐。

● space-between表现为上下两行两端对齐，剩下的每一行元素等分剩余空间。

● space-around表现为每一行元素上下都享有独立不重叠的空白空间。

● space-evenly表现为每一行元素上下的空白空间的大小都是一致的。

[效果展示](https://demo.cssworld.cn/new/6/2-7.php)



#### order属性与单个子项的顺序控制

> 我们可以通过设置order属性来改变某一个flex子项的排序位置。

```css
order: <integer>; /*整数值，默认是 0*/
```

[效果展示](https://demo.cssworld.cn/new/6/2-8.php)



#### 深入理解flex属性



**隐藏的友善细节**

> flex属性是flex-grow、flex-shrink和flex-basis这3个CSS属性的缩写，这个很多人都知道，但是，我相信很多人并不知道flex属性悄悄做了很多其他CSS缩写属性都没有的优化。
>
> border, background 也是如此

border ：

● border:2px等同于border:2px none currentColor，也就是此时border-style的值是默认值none，border-color的计算值是当前的色值。

● border:#fff等同于border:medium none #fff，也就是此时border-width的值是默认值medium。

● border:solid等同于border:medium solid currentColor。

flex：

● flex:1等同于flex:1 1 0%，flex:1 2等同于flex:1 2 0%，即flex-basis使用的不是默认值auto，而是使用的0%。

● flex:100px等同于flex:1 1 100px，即flex-grow使用的不是默认值0，而是使用的1。

● flex:auto等同于设置flex: 1 1 auto，其作用就和它的名称一样，表示“自动”，即flex子项自动填满剩余空间或自动收缩。

● flex:none等同于设置flex: 0 0 auto，作用就和名称一样，表示“没有”，即flex子项没有弹性，适合固定尺寸元素（无须设置width属性）。



**flex属性的语法**

```css
flex: auto;
flex: none;
flex: <flex-grow> <flex-shrink> || <flex-basis>
```

1）如果flex的属性值只有1个值，则具体规则如下。

● 如果是数值，如flex: 1，则这个1为flex-grow属性的值，此时flex-shrink属性和flex-basis属性的值分别是1和0%。注意，这里的flex-basis属性的值是0%，而不是默认值auto。

● 如果是长度值，如flex:100px，则这个100px显然为flex-basis属性的值，因为3个缩写CSS属性中只有flex-basis的属性值支持长度值。此时flex-grow属性和flex- shrink属性的值都是1，注意，这里的flex-grow属性的值是1，而不是默认值0。

2）如果flex的属性值有2个值，则第一个值一定是flex-grow属性值（因为表示0个或1个的问号“?”在flex-shrink的后面），第二个值根据值的类型不同对应不同的CSS属性，具体规则如下。

● 如果第二个值是数值，例如flex: 1 2，则这个2是flex-shrink属性的值，此时flex-basis属性计算值是0%，并非默认值auto。

● 如果第二个值是长度值，例如flex: 1 100px，则这个100px为flex-basis属性值。

3）如果flex的属性值有3个值，则长度值为flex-basis属性值，其余2个数值分别为flex-grow和flex-shrink的属性值。



**理解flex-grow属性、flex-shrink属性和flex-basis属性**

> 为了让这3个属性更容易被理解，我们不妨把弹性布局中的尺寸分配看成分配家产。
>
> 故事是这样的，有一个姓范的人家生了5个孩子，分别叫作范张、范鑫、范旭、范帅和范哥。要是只有一个孩子，那这个孩子就继承100%的家产，但是现在有5个孩子，家长范某需要提早定好家产分配规则。而flex属性的作用就如同制定分配家产的规则。
>
> ● flex-basis属性用来分配基础数量的家产。
>
> ● flex-grow属性用来家产仍有富余的时候该如何分配。
>
> ● flex-shrink属性用来家产不足的时候该如何分配。

1. flex-grow属性指定了容器剩余空间多余时候的分配规则，默认值是0，表示多余空间不分配，语法如下：

	```css
	flex-grow: <number>; // 可以是小数默认是0
	```

	> 具体规则如下。
	>
	> ● 所有剩余空间总量是1。
	>
	> ● 如果只有1个flex子项设置了flex-grow属性值，则有两种分配情况。
	>
	> ● 如果flex-grow属性值小于1，则flex子项扩展的空间就是总剩余空间和这个比例的计算值。
	>
	> ● 如果flex-grow属性值大于1，则flex子项独享所有剩余空间。
	>
	> ● 如果有多个子项flex设置了flex-grow属性值，则有两种分配情况。
	>
	> ● 如果flex-grow属性值的总和小于1，则每个flex子项扩展的空间就是总剩余空间和当前flex子项设置的flex-grow比例的计算值。
	>
	> ● 如果flex-grow属性值的总和大于1，则所有剩余空间被利用，分配比例就是各个flex子项的flex-grow属性值的比例。
	>
	> 例如，所有flex子项都设置flex-grow:1，则表示剩余空间等分；如果设置的flex-grow比例是1:2:1，则中间的flex子项占据一半的剩余空间，剩下另外一半的剩余空间由前后两个flex子项等分。

2. flex-shrink属性指定了容器剩余空间不足时候的分配规则，默认值是1，表示空间不足要分配，语法如下：

	```css
	flex-shrink: <number> // 默认1
	```

	

	> 具体规则如下。
	>
	> ● 如果只有1个flex子项设置了flex-shrink属性值，则有两种分配情况。
	>
	> ● 如果flex-shrink属性值小于1，则收缩不完全，会有一部分内容溢出flex容器。
	>
	> ● 如果flex-shrink属性值大于等于1，则收缩完全，元素正好填满flex容器。
	>
	> ● 如果多个flex子项设置了flex-shrink属性，则有两种分配情况。
	>
	> ● 如果flex-shrink属性值的总和小于1，则收缩不完全，每个元素收缩尺寸和“完全收缩的尺寸”的比例就是该元素的flex-shrink属性的值。
	>
	> ● 如果flex-shrink属性值的总和大于1，则收缩完全，每个元素收缩尺寸的比例和flex-shrink属性值的比例一样。

3. flex-basis属性则是指定的分配基础尺寸，默认值是auto，语法如下：

	```css
	flex-basis: <length> | auto; // 默认值是 auto
	```

	

> 范某的家产分配遗嘱是在自己50岁时候制定的。由于范张、范鑫和范旭都已经成家立业，各自在外独立生活，因此，给这3个人分配了固定数目的家产，每人100万元；而范帅和范哥尚未成年，和范某还住在一起，所以，遗嘱就是剩下的家产由范帅和范哥两人平分，按照范某50岁时候的资产，范帅和范哥也分得人均100万元的家产。但是世事难料，没过几年范家家道中落，范某总资产已经不足300万元，此时，扣除答应范张、范鑫和范旭的300万元，已经没有多余家产了，范帅和范哥就没有家产可继承了，这就相当于容器剩余空间不足。因此，为了应对各种状况出现，在家产分配规则制定的时候，一定要明确好基础家产数量flex-basis，家产充足时候的分配规则flex-grow，以及家产不足时候的分配规则flex-shrink。
>
> 实现范张、范鑫和范旭每人有100万元固定家产，范帅和范哥有20万元保底家产。如果范某去世那天家产还有富余，则范帅和范哥按照3:2比例分配；如果没有剩余家产，则范张、范鑫和范旭按照2:1:1的比例分别给范帅和范哥匀20万元保底家产

- ##### HTML：

	```html
	<div class="container">
	    <item class="zhang">范张</item>
	    <item class="xin">范鑫</item>
	    <item class="xu">范旭</item>
	    <item class="shuai">范帅</item>
	    <item class="ge">范哥</item>
	</div>
	```

- ##### CSS：

	```css
	.container {
	    display: flex;
	    border: 2px dashed crimson;    
	}
	.container item {
	    border: 2px solid deepskyblue;    
	}
	.zhang {
	    flex: 0 2 100px;    
	}
	.xin {
	    flex: 0 1 100px;    
	}
	.xu {
	    flex: 0 1 100px;    
	}
	.shuai {
	    flex: 3 0 20px;    
	}
	.ge {
	    flex: 2 0 20px;    
	}
	```

[示例效果](https://demo.cssworld.cn/new/6/2-9.php)



#### 应该在什么时候使用flex:0/1/none/auto

> 常见的flex属性单值语法对应的计算值

![image-20221031150458362](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221031150458362.png)



1. flex:initial的基本表现

	应用flex:initial的元素在flex容器有剩余空间时其尺寸不会增长（flex-grow:0），在flex容器尺寸不足时尺寸会收缩变小（flex-shrink:1），同时当前应用flex:initial的元素的尺寸自适应于内容（flex-basis:auto）。

	**适用的场景**

	> 就是在那些希望元素尺寸收缩，同时元素内容较多又能自动换行的场景中可以不做任何flex属性设置

2. flex:0和flex:none的区别

	flex:0等同于设置flex: 0 1 0%

	flex:none等同于设置flex: 0 0 auto

	> **flex:0**元素尺寸不会弹性增大（flex-grow:0）,但是会弹性收缩(flex-shrink:1），考虑到此时flex-basis属性值是0%，表示基础尺寸是0，因此设置flex:0的元素尺寸表现为最小内容宽度，也就是文字会呈现“一柱擎天”的
	>
	> 
	>
	> **flex:none**的尺寸同样不会弹性增大（flex-grow:0），但是也不会弹性收缩（flex-shrink:0），我们可以理解为元素的尺寸没有弹性变化，考虑到此时flex-basis属性值是auto，即基础尺寸由内容决定，因此设置flex:none的元素最终尺寸通常表现为最大内容宽度。

	

3. flex:1和flex:auto的区别

	flex:1等同于设置flex: 1 1 0%

	flex:auto等同于设置flex: 1 1 auto

	> 元素尺寸可以弹性增大，也可以弹性减小，但是flex:1在容器尺寸不足时会优先最小化内容尺寸，flex:auto在容器尺寸不足时会优先最大化内容尺寸。
	>
	> 
	>
	> 当希望元素充分利用剩余空间，同时不会侵占其他元素应有的宽度的时候，适合使用flex:1，这样的场景在弹性布局中非常多
	>
	> [效果展示](https://demo.cssworld.cn/new/6/2-10.php)
	>
	> 
	>
	> 当希望元素充分利用剩余空间，但是元素各自的尺寸又需要按照各自内容进行分配的时候，就适合使用flex:auto。
	>
	> [宽度自动分配的导航栏](https://demo. cssworld.cn/new/6/2-11.php)



最后再总结一下。

● flex:initial表示默认的弹性布局状态，无须专门设置，适合小控件元素的分布布局（其中某一个flex子项的内容动态变化也没有关系）。

● flex:0适用场景较少，适合设置在替换元素的父元素上。

● flex:none适合设置在内容不能换行显示的小控件元素上，如按钮。

● flex:1适合等分布局。

● flex:auto适合基于内容动态适配的布局。



#### 详细了解flex-basis属性与尺寸计算规则

> 在弹性布局中，一个flex子项的最终尺寸是基础尺寸（或内容尺寸）、弹性增长或收缩、最大最小尺寸共同作用的结果。
>
> **最终尺寸计算的优先级是：**
>
> 最大最小尺寸限制 > 弹性增长或收缩 > 基础尺寸
>
> ● 基础尺寸由flex-basis属性或width属性，以及box-sizing盒模型共同决定；
>
> ● 内容尺寸指最大内容宽度，当没有设置基础尺寸时会顶替基础尺寸的角色；
>
> ● 弹性增长指的是flex-grow属性，弹性收缩指的是flex-shrink属性；
>
> ● 最大尺寸主要受max-width属性限制；最小尺寸则比较复杂，受最小内容宽度、width属性和min-width属性共同影响。



**flex-basis属性与盒模型**

> flex-basis属性的尺寸是作用在content-box上的，这一点和width属性是一样的
>
> flex子项里面的文本内容存在连续英文单词导致最小尺寸比较大，最终尺寸大于120px

[word-break:break-all对flex-basis尺寸渲染的影响](https://demo.cssworld.cn/new/6/2-12.php)



**理解flex-basis属性、width属性和基础尺寸之间的关系**

> 弹性布局中的尺寸表现几乎都是围绕基础尺寸展开的。其中flex-basis属性和width属性都可以用来设置flex子项的基础尺寸，对基础尺寸的影响关系如下。
>
> - 如果flex-basis属性和width属性同时设置了具体的数值，width属性值会被忽略，优先使用flex-basis的属性值作为基础尺寸
> - 如果flex-basis的属性值是初始值auto，则会使用width属性设置的长度值作为基础尺寸。
> - 如果flex-basis和width的属性值都是auto，则会使用flex子项的最大内容宽度作为基础尺寸，此时称为“内容尺寸”。

#### **深入理解最小尺寸**

> 在弹性布局中，虽然flex-basis属性和width属性的语法类似（例如百分比值的计算规则是一样的），性质也类似（例如都可以作为基础尺寸），但是如果涉及最小尺寸，两者的差异就体现出来了。
>
> flex-basis属性下的最小尺寸是由内容决定的，而width属性下的最小尺寸是由width属性的计算值决定的。这里出现的“最小尺寸”表示最终尺寸的最小值，这个“最小尺寸”是最小内容宽度、width属性和min-width属性共同作用的结果。

具体规则如下，如果flex-shrink属性不为0，则：

● 如果min-width属性值不是auto，则元素的最小尺寸就是min-width的属性值，此时width属性无法影响最小尺寸，哪怕width的属性值大于min-width的属性值；

● 比较width属性的计算值和最小内容宽度的大小，较小的值就是元素的最小尺寸；

● 如果width的属性值和min-width的属性值均为auto，则元素的最小尺寸就是最小内容宽度；

● 如果flex子项设置了overflow:hidden，且最小尺寸是由最小内容宽度决定的，则最小尺寸无效。

[flex-basic、width与最小内容宽度同时作用](https://demo.cssworld.cn/new/6/2-13.php)



> 最后总结下本节关于flex-basis属性的一些要点。
>
> ● flex-basis属性默认作用在content box上，IE11浏览器会忽略box-sizing属性。
>
> ● flex-basis属性优先级比width属性高，同时设置的时候，width属性无法影响基础尺寸，但是会影响最小尺寸（IE11除外）。
>
> ● 最小尺寸与flex-basis属性无关，而与最小内容宽度、width属性和min-width属性有关。
>
> ● flex-basis属性使用得当可以实现类似min-width属性或max-width属性的效果，min-width属性可以在不影响基础尺寸的前提下设置最小尺寸，从而解决弹性布局中打点无效的问题。
>
> ● flex-basis属性还支持很多关键字属性值，只不过目前兼容性不太好。

[宽度不确定时设置文字超出隐藏](https://demo.cssworld.cn/new/6/2-15.php)



**flex-basis属性还支持关键字属性值**

```css
flex-basis: content | fill | max-content | min-content | fit-content;
```



#### 弹性布局最后一行不对齐的处理

- 补齐最后一行的元素

- 理论上最好的方法是使用gap属性设置间隙

	```css
	.container {
	    display: flex;
	    flex-wrap: wrap;
	    gap: calc(4% / 3);
	}
	.list {
	    width: 24%; height: 100px;
	    background-color: skyblue;
	    margin-top: 15px;
	}
	```

	> 不过由于目前仅有Firefox浏览器和Chrome浏览器最新的几个版本支持gap属性，因此目前这个方法无法应用在生产环境。

- 最后一个元素设置动态margin值

	> 由于每一列的数目都是固定的，因此，我们可以计算出不同元素个数的列表应当设置多大的margin值才能保证完全左对齐。例如，假设每行可以存放4个元素，结果最后一行只有3个元素，如果最后一个元素的margin-right大小是“列表宽度+间隙大小”，那最后一行的3个元素也是可以完美左对齐的。

	```css
	/*最后一行有3个，或者7个元素*/
	.last:last-child:nth-child(4n -2) {}
	
	/*最后一行有2个或者6个元u怒诉*/
	.last:last-child:nth-child(4n -2) {
	    margin-right: calc(48% + 8% / 3);
	}
	```



如果flex**子项宽度不固定**

> 有时候，每一个flex子项的宽度都是不固定的，这个时候希望让最后一行左对齐如何实现呢？由于这种情况下间隙的大小不固定，对齐不严格，因此我们可以直接让最后一行左对齐，具体实现方法有两种。

1. 给最后一项设置margin-right:auto

2. 使用伪元素在列表的末尾创建一个flex子项，并设置flex:auto或设置flex:1

	```css
	.container::after {
	    content: '';
	    flex: auto;
	}
	```

**如果每一行列数不固定**

> 如果每一行的列数不固定，则上面的这些方法均不适用，需要使用其他方法来实现最后一行左对齐。
>
> 这个方法其实很简单，也很好理解，就是使用足够的空白标签进行填充占位，具体的占位数量是由最多列数的个数决定的。例如布局最多有7列，那我们可以使用7个空白标签进行填充占位；布局最多10列，那我们需要使用10个空白标签进行填充占位

```html
<div class="container">
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <i></i><i></i><i></i><i></i><i></i>
</div>
```

实现该方法的关键是要将占位的\<i>元素宽度和margin值设置得与.list列表元素一样，其他样式都不需要设置，相关CSS代码如下：

```css
.container {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    margin-right: -10px;
}
.list {
    width: 100px; height: 100px;
    background-color: skyblue;
    margin: 15px 10px 0 0;
}

.container > i {
    width: 100px;
    margin-right: 10px;
}
```

由于\<i>元素高度为0，因此，如此设置并不会影响垂直方向上的布局呈现，最终可以实现与图6-72所示一样的效果。



**如果列数不固定，HTML又不能调整**

> 有时候，由于客观原因，前端重构人员没有办法去调整HTML代码结构，同时布局的列表个数又不固定，这时候该如何实现最后一行左对齐效果呢？

那就使用网格布局



### 网格布局

> 给HTML元素设置display:grid或者display:inline-grid，网格布局就创建完成了！

```html
<div class="container">
    <item>1</item>
    <item>2</item>
    <item>3</item>
    <item>4</item>
    
</div>
div {
    display: grid;
}
```

> 此时该div就是“grid容器”，其子元素被称为“grid子项”。
>
> display:grid和display:inline-grid的区别是：inline-grid容器外部盒子保持内联特性，因此可以和图片文字在同一行显示；grid容器保持块状特性，宽度默认为100%，不和内联元素在一行显示。



#### **grid-template-columns和grid-template-rows属性简介**

> grid-template-columns和grid-template-rows属性主要用来指定网格的数量和尺寸等信息。

```css
.container {
    grid-template-columns: 80px auto 100px;
    grid-template-rows: 25% 100px auto 60px;
}
```

> ● grid-template-columns属性含3个值，表示网格分为3列，从左往右每列的尺寸分别是80px、auto（自动）和100px。
>
> ● grid-template-rows属性含4个值，表示网格分为了4行，从上往下每行的高度分别是25%、100px、auto（自动）和60px。

1. 网格线的命名

	> 两属性语法基本一致， 以 grid-template-columns 为例，

	```css
	grid-template-columns: <line-name> <track-size>;
	/* 中文示意 */
	grid-template-columns: <道路名称> <小区占地>;
	```

	● \<track-size>表示划分出来的小区的尺寸，可以是长度值、百分比值、fr单位（网格剩余空间比例单位）和尺寸关键字等多种类型的属性值。

	● \<line-name>表示划分的街道的名称，命名规则和CSS动画的命名规则一样。

	```css
	grid-template-columns: [道路名称-起始] 80px [道路名称2] auto [道路名称3] 100px [道路名称-结束];
	```

2. 聊聊 \<track-size>

	> grid-template-columns属性的默认值是none，可以使用grid-auto-columns属性设定网格尺寸。

grid-template-columns属性支持的其他值全都属于\<track-size>数据类型，共支持9种数据类型，分别如下：

● 长度值；

● 百分比值；

● 关键字属性值，包括min-content、max-content和auto；

●\<flex>数据类型，也就是以fr为单位的值；

● 函数值，包括repeat()、minmax()和fit-content()。

1. min-content

	网格布局中的同一行grid子项的高度和同一列grid子项的宽度都是一致的，因此min-content指的并不是某一个格子的最小内容尺寸，而是一排或者一列格子中所有最小内容尺寸中最大的那个最小内容尺寸值

2. max-content

	max-content关键字和min-content关键字类似，只是最终的尺寸是最大内容宽度中最大的那一个值。

3. auto

	auto关键字的计算规则有些复杂，W3C规范中是这样描述的：尺寸的上限是最大内容尺寸的最大值，但是不同于max-content关键字，max-content关键字的尺寸是固定的，这里的尺寸是会受到justify-content属性和align-content属性影响的。

	另外，当多列的宽度同时设置为auto的时候，这些列的宽度并不是等分的，而是在max-content尺寸的基础上增加同样大小的尺寸。



#### 了解网格布局专用单位fr

> fr是单词fraction的缩写，表示分数。
>
> 是这样的，网格布局往往有多列或者多行，其中有些列有固定的宽度，有些列的宽度就由页面自动分配，而fr就表示这些自动分配列的尺寸划分比例。
>
> fr单位值的计算规则如下。
>
> ● 如果所有fr值之和大于1，则按fr值的比例划分可自动分配尺寸。
>
> ● 如果所有fr值之和小于1，最终的尺寸是可自动分配尺寸和fr值的乘法计算值。

和auto关键字混合使用

> 如果部分列使用的是auto关键字，则fr值的计算规则就与设置auto这一列的内容密切相关。**fr值的可自动分配尺寸是容器尺寸减去设置auto关键字的列的fit-content尺寸**
>
> 

#### minmax()和fit-content()函数

1. minmax()函数

	minmax()函数支持两个参数值，例如：

	```css
	minmax(min, max)
	
	/*正式语法如下*/
	minmax([<length> | <percentage> | min-content | max-content | auto], [<length> | <percentage> | <flex> | min-content | max-content | auto])
	```

	**注意flex类型如fr为单位的值只能作为第二个值出现**

2. fit-content()函数

	fit-content()函数的作用用一句话解释就是：让尺寸适应于内容，但不超过设定的尺寸。底层计算公式如下：

	```css
	fit-content(limit) = max(minimum, min(limit, max-content))
	```

	minimum是尺寸下限，如果不考虑min-width/min-height属性，这个尺寸就是最小内容尺寸



#### repeat()函数

> repeat()函数只能作用在grid-template-columns和grid-template -rows这两个CSS属性上。

```css
repeat([<positive-integer>| auto-fill | auto-fit], <track-list>)
```

- \<positive-integer>，顾名思义，就是正整数的意思，表示尺寸重复的次数

- auto-fill和auto-fit相当于一个变量，表示一个不确定的重复次数，究竟重复多少次，是由grid容器和每一个grid子项的尺寸计算得到的。

	一一般配合其他函数一起使用

	```css
	.container {
	    grid-template-columns: repeat(auto-fill, minmax(100px, 1fr))
	}
	```

	这就实现了无论grid容器多宽，grid子项都会等比例充满grid容器（因为设置了1fr），同时保证宽度不小于100px，网格布局的[列数自动计算分配的智能弹性布局效果](https://demo.cssworld.cn/new/6/3-1.php)。

	> 当我们使用auto-fill关键字自动填充的时候，repeat()函数是不能和auto一起使用的。

- ．auto-fit关键字

	auto-fit和auto-fill关键字的作用是相似的，区别在于auto-fit关键字会把空白匿名网格进行折叠合并，而这个合并的0px大小的格子可以被认为具有单个格子轨道大小调整的功能，同时，空白匿名格子两侧的过道（grid-gap设置的间隙）也会合并。

	，auto-fit关键字把空白匿名网格连同左侧的间隙也一起合并了。这种合并设计有什么好处呢？好处就在于，如果配合fr值一起使用，可以保证[无论grid容器宽度多大，grid子项都可以填满grid容器](https://demo.cssworld.cn/new/6/3-2.php )



#### grid-template-areas属性

> grid-template-areas属性用来指定网格区域的划分，注意是areas，不是area，后面有个s。网格区域的划分性质和城市的行政区划分、小区的划分、农田的划分都很接近，划分网格区域可以更方便网格的管理与维护。

```css
.container {
    grid-template-areas: "
        <gride-area-name> | . | none | ..." 
        "..."
}
```

● \<grid-area-name>表示对应网格区域的名称，命名规则和animation-name属性值一样。

●.表示空的网格单元格。

●none表示没有定义网格区域。

[效果展示](https://demo.cssworld.cn/new/6/3-3.php)

> （1）如果我们给网格区域命名了，但是没有给网格线命名，则系统会自动根据网格区域名称生成网格线名称，规则是在区域名称后面加-start和-end。例如，某网格区域名称是“葡萄”，则左侧网格线名称就是“葡萄-start”，右侧网格线名称就是“葡萄-end”。
>
> （2）网格区域一定要形成规整的矩形区域，无论是L形，还是凹的或凸的形状都会认为是无效的属性值。

#### **缩写属性grid-template**

> grid-template属性是grid-template-rows、grid-template-columns和grid- template-areas属性的缩写

```css
.container {
	grid-template: none;
}
.container {
	grid-template: <grid-template-rows> / <grid-template-columns>;
}

.container {
	grid-template: [ <line-name>?<string> <track-size>? <line-names>? ]+ [/ <explicit-track-list>]?;
}
```

其中，前两种缩写相对简单，最后一种缩写略复杂，具体如下。

（1）属性值none表示将3个CSS属性都设置为初始值none。

（2）\<grid-template-rows> / \<grid-template-columns>表示行尺寸或列尺寸的设置

（3）第三种缩写是包含grid-template-areas属性的语法，其中\<string>指的就是grid-template-areas属性值，更准确地说是每一行网格的区域名称。

```css
.container {
    grid-template:
        "葡萄 葡萄 葡萄" 1fr
        "龙虾 养鱼 养鱼" 1fr
        "龙虾 养鱼 养鱼" 1fr
        "西瓜 西瓜 西瓜" 1fr
        / 1fr 1fr 1fr;
}
```

从语法可以看出，只有\<string>数据类型是必需的，其他数据类型都可以省略。例如\<track-size>（也就是尺寸）可以省略（会使用auto代替计算）





#### 了解grid-auto-columns和grid-auto-rows属性

> grid-auto-columns和grid-auto-rows属性的作用是指定任何自动生成的网格（也称为隐式网格）的尺寸大小。
>
> “**隐式网格**”是非正常网格，其在grid子项多于设置的单元格数量，或grid子项的位置出现在设定的网格范围之外时出现，而在规定容器内显示的网格称为“**显式网格**”
>
> grid-auto-columns属性和grid-auto-rows属性就是用来控制“隐式网格”的尺寸的

#### grid-auto-flow属性

> grid-auto-flow属性用来定义子项目元素的自动流动状态，grid-auto-flow属性在网格布局中的地位非常类似于弹性布局中的flex-direction属性。
>
> 默认值（row）

```css
grid-auto-flow: [row | column] || dense
```

```css
/*合法写法示例 */
grid-auto-flow: row;
grid-auto-flow: column;
grid-auto-flow: dense;
grid-auto-flow: row dense;
grid-auto-flow: column dense;
```

- row是默认值，表示没有指定位置的网格在水平（行）方向上自然排列。
- dense表示网格的自然排列启用“密集”打包算法，也就是说，如果稍后出现的网格比较小，则尝试看看其前面有没有合适的地方放置该网格，使网格尽可能排列紧凑。
- column表示没有指定位置的网格在垂直（列）方向上自然排列。



#### dense关键字

> dense关键字单从字面含义是不太好理解，dense的意思是“密集的”“稠密的”“浓密的”。空网格会被后面的网格顶上去

#### 缩写属性grid

> grid属性的缩写规则比较复杂。grid是这些CSS属性的缩写集合：grid-template- rows、grid-template-columns、grid-template-areas、grid-auto-rows、grid- auto-columns和grid-auto-flow。

```css
1、gird: none;

2、grid: <grid-template>

/*键字是一个只在grid缩写属性中出现的关键*/
/*3、auto-flow在后面*/
grid: <grid-template-rows>/ [auto-flow && dense?] <grid-auto-columns>?

/*4、auto-flow在前面*/
grid:  [auto-flow && dense?] <grid-auto-rows>？ / <grid-template-columns>?
```



> 梳理一下grid缩写的语法。
>
> ● grid:none很简单，没什么好说的。
>
> ● 如果没有隐式网格，且无须改变网格布局的自然流向，则使用grid-template属性。
>
> ● 最后两个语法是在出现隐式网格，或者需要改变网格布局的自然流向的时候使用，要么使用grid-template/auto-flow，要么使用auto-flow/grid-template，就这么简单。



#### 设置属性 column-gap 和 row-gap

> 以前是使用grid-column-gap属性和grid-row-gap属性来对网格布局中各个网格之间的间隙进行设置的
>
> 随着CSS Box Alignment Module Level 3的制定，无论是分栏布局、弹性布局还是网格布局，全部统一使用column-gap属性和row-gap属性来设置。



以column-gap属性举例，各大布局兼容性排序如下：

分栏布局 > 网格布局 > 弹性布局各个布局的浏览器兼容性如下。

● 分栏布局中，column-gap属性在IE10+浏览器中全兼容。

● 网格布局中，column-gap属性在Edge16+浏览器中都提供支持。

● 弹性布局中，column-gap属性目前仅有Chrome浏览器和Firefox浏览器提供不错的支持。

```css
.container {
    column-gap: <line-size>;
    row-gap: <line-size>;
}
/* 可以是长度值，也可以是百分比值 */
row-gap: 20px| 1em | 3vmin | 10%;
```



**缩写 gap**

```css
gap: <row-gap> <column-gap>;
```

```css
/*缩写前*/
.container {
    display: grid;
    grid: 1fr 2fr / 2fr 1fr;
    column-gap: 20px;
    row-gap: 10px;
}

.container {
    display: grid;
    grid: 1fr 2fr / 2fr 1fr;
    gap: 10px 20px;
}
```



#### 元素对齐属性 justify-items和align-items

> 在网格布局中，以-items结尾的对齐属性表示控制每一个元素在自己所在的网格中的对齐表现。justify-items属性用来定义元素在网格中的水平对齐表现，align-items属性则是用来定义元素在网格中的垂直对齐表现。

1. justify-items

	语法如下：

	```css
	.container {
	    justify-items: stretch | start | end | center;
	}
	```

	● stretch表现为元素水平尺寸拉伸，填满整个网格的水平空间。

	● start表现为元素的水平尺寸收缩为内容大小，同时沿着网格线左侧对齐显示

	● end表现为元素的水平尺寸收缩为内容大小，同时沿着网格线右侧对齐显示

	● center表现为元素的水平尺寸收缩为内容大小，同时在当前网格区域内部水平居中对齐显示

2. align-items

	```css
	.container {
	    align-items: normal | stretch | start | end | center | baseline;
	}
	```

	● normal是默认值，会根据使用场景的不同表现为stretch或者start。

	● stretch表现为元素的尺寸在垂直方向进行拉伸，以填满整个网格的垂直空间。

	● start表现为元素的垂直尺寸收缩为内容大小，同时沿着上网格线对齐显示

	● end表现为元素的垂直尺寸收缩为内容大小，同时沿着下网格线对齐显示

	● center表现为元素的垂直尺寸收缩为内容大小，同时在当前网格区域内部垂直居中对齐显示

	● baseline表现为每一行的各个grid子项沿着基线对齐

[元素对齐效果](https://demo.cssworld.cn/new/6/3-5.php)

[align-items默认值图片和按钮的样式表现](https://demo.cssworld.cn/new/6/3-6.php)

**缩写属性place-items**

> 使用place-items属性可以让align-items和justify-items属性写在单个声明中。语法如下：

```css
place-items: <align-items> <justify-items>?
```

> 垂直对齐写在前面，水平对齐写在后面，不过这种缩写顺序只适合CSS对齐属性，网格布局相关属性还是行在前、列在后

**整体对齐属性justify-content和align-content**

> justify-content属性和align-content属性分别指定了网格元素整体水平方向和垂直方向上的分布对齐方式。

```css
justify-content: normal | stretch | start | end | center | space-between | space-around | space-evenly;

align-content: normal | stretch | start | end | center | space-between | space-around | space-evenly;
```

● normal是默认值，效果和stretch一样。

● stretch可以看成justify-content属性和align-content属性的默认值，表示拉伸，表现为尺寸填满grid容器。拉伸效果需要在网格目标尺寸设为auto的时候才有效，如果固定了宽高，则无法拉伸。

● start是逻辑CSS属性值，与文档流方向相关。水平方向上默认表现为左对齐，垂直方向上默认表现为顶对齐

● end是逻辑CSS属性值，与文档流方向相关。水平方向上默认表现为右对齐，垂直方向上默认表现为底对齐

● center表现为水平居中对齐或垂直居中对齐

● space-between表现为grid子项两端对齐，中间剩余空间等分

● space-around表现为每个grid子项的上下或左右两侧都环绕互不干扰的相同尺寸的空白间距，在视觉上表现为grid子项边缘处的空白尺寸只有中间空白尺寸的一半

● space-evenly表现为每个grid子项上下或左右两侧的空白间距完全相等

[效果预览](https://demo.cssworld.cn/new/6/3-7.php)

**缩写属性place-content**

> 使用place-content属性可以让align-content属性和justify-content属性写在同一个CSS声明中

```css
place-content: <align-content> <justify-content>?
```

> place-content属性在弹性布局中也是有效的，不过需要注意一下兼容性，IE浏览器和Edge浏览器都不支持place-content缩写。



#### 区间范围设置属性grid-column-start/grid-column-end和grid-row-start/ grid-row-end

> 列范围设置属性grid-column-start/grid-column-end和行范围设置属性grid- row-start/grid-row-end是应用在grid子项上的，通过指定grid子项行和列的起止位置来表明当前grid子项所占据的范围

```css
.item {
    grid-column-start: <integer> | <name> | <integer> <name> | span <number> | span <name> | auto;
    grid-column-end: ... ;
    grid-row-start: ... ;
    grid-row-end: ... ;
    
}
```



（1）\<integer>指起止于第几条网格线，可以是负整数，但是不能是0，负整数表示从右侧开始计数网格线

（2）\<name>是自定义的网格线的名称。需要注意的是，这里的名称有一个自动补全-start后缀和-end后缀的特性

```css
.container {
    display: grid;
    grid-template-columns: [A-start] 100px [A-end B-start] auto [B-end] auto;
}

.item {
    grid-column-start: A;
    grid-column-end: B;
    background: red;
}
```

> 虽然grid-column-start的值是A，但是，浏览器在找不到名称为A的网格线的时候，会自动补全-start继续寻找；同样，grid-column-end:B会依次寻找名称为B和B-end的网格线。于是.item元素区间就是从[A-start]到[B-end]，跨度是2个显式网格

（3）\<integer> \<name>这个语法非常难理解，属于高阶应用，平常实践中的网格布局是用不到的。

> 这个语法表示当前名称为 \<name>的第 \<integer>个网格线，从定义上看，网格布局中需要有很多个名称一样的网格线才能匹配对应的网格线，但如果没有这么多同样名称的网格线会怎样呢？此时，浏览器会自动创建符合数量的隐式网格，这些隐式网格的网格线都是指定的这个名称。

（4）span \<number>表示当前网格会自动跨越指定的网格数量。

（5）span \<name>表示当前网格会自动扩展，直到选中指定的网格线名称。与span关键字相关的语法在后面会专门介绍。

（6）auto是默认值，表示自动，默认跨度是1个格子。



#### span关键字的样式表现

> span关键字和传统的 \<table>元素中单元格使用的HTML中的colspan属性和rowspan属性的作用是类似的，表示合并单元格（网格）。
>
> span \<number>中的 \<number>不能是负值，也不能是0，也不能是小数。

如果网格布局中有多个网格线的命名是B或者是B-start，例如：

```
.container {
	display: grid;
	grid-template-columns: [B] 80px [B] auto [B] 100px [D] auto auto;
}
```

则span B表示离grid-column-end位置最近的一个网格线B，而如果属性值是B而不是span B，则起始位置会是离grid-column-end位置最远的一个网格线B。

[span和不同数量网格线命名效果](https://demo.cssworld.cn/new/6/3-8.php)

**缩写属性grid-column和grid-row**

```css
grid-column: <grid-line> [/ <grid-line>]?

grid-row: <grid-line> [/ <grid-line>]?
```

> 其实就是使用斜杠把原始CSS属性的值原封不动地区分开，且不讲究顺序，当然，开发的时候还是建议把起始范围写在前面。

```css
/*缩写前*/
.item-b {
    grid-column-start: 2;
    grid-column-end: span 纵线3;
    grid-row-start: 第一行开始;
    grid-row-end: span 3;
}

/*缩写*/
.item-b {
    grid-column: 2 / span 纵线3;
    grid-row: 第一行开始 / span 3;
}
```



#### 缩写属性grid-area外加区域范围设置

> grid-area是一个缩写属性，它是grid-row-start、grid-column-start、grid-row-end和grid-column-end这4个CSS属性的缩写。

```css
grid-area: <area-name> | <row-start> / <column-start> / <row-end>/ <column-end>
```

● \<area-name>指区域名称，由grid-template-areas属性创建。

● \<row-start> / \<column-start> / \<row-end> /\<column-end>指占据网格区域的行列起止位置。

（1）如果grid-area属性有完整的4个值，则这4个值依次表示grid-row-start、grid-column-start、grid-row-end和grid-column-end这4个属性

```css
grid-area: 4 A/ span 4 / B / D
```

（2）如果grid-area属性有3个值，也就是把grid-column-end值省略了。

```css
grid-area: A / B / C;
```

（3）如果grid-area属性是2个值，说明grid-row-end值也被省略了

```css
grid-area: A / B;
/*等同于*/
grid-area: A /B /A /B;

gird-area: 1 / 2;
/*等同于*/
grid-area: 1 / 2/ auto/ auto;
```

（4）如果grid-area属性仅有1个值，说明grid-column-start值也被省略了

```css
gird-area: A;
/*等同于*/
gird-area: A/A/A/A;

grid-area: 2;
/*等同于*/
gird-area: 2/auto/auto/auto;

```

[grid-area与元素重叠](https://demo.cssworld.cn/new/6/3-9.php)



#### grid子项对齐属性justify-self和align-self

> justify-self属性用来设置单个网格元素的水平对齐方式，align-self属性则用来设置单个网格元素的垂直对齐方式。

```css
.item {
    justify-self: auto| noraml| stretch | start | end | center | baseline;
    align-self: ...;
}
```

**缩写 place-self**

```css
place-self: <align-self> <justify-self>;
```



#### 小结

```html
<div class="box">
    <div class="item"><span>1</span></div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <div class="item">5</div>
</div>
```

 

1、box 是容器

​      -display: grid /inline-grid 指定网格布局

​      -grid-template-columns 列宽 px,% 都可以

​      -grid-template-rows 行高 px,%,fr 都可以

​      -grid-row-gap  行间距

​      -grid-column-gap 列间距

​      -grid-gap 简写； eg: grid-gap: 行间距，列间距

​      -grid-template-areas 区域：将一个或多个单元格划分为一个区域

​        // 9个单元格，分为3个区域

​        grid-template-areas: 'a a a'

​                   'b b b'

​                   'c c c';

​        -区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为区域名-start，终止网格线自动命名为区域名-end。

​      -grid-auto-flow: row, column，row dense,column dense 自动布局算法按照通过逐行填充来排列元素，在必要时增加新行

​      -justify-items 属性，项目内容水平start | end | center | stretch

​      -align-items 属性，项目内容垂直 start | end | center | stretch

​      -place-items 属性， 简写 place-items: 水平 垂直;

​      -justify-content 属性，整个内容区域在容器里面的水平位置 start | end | center | stretch | space-around | space-between | space-evenly;

​      -align-content 属性，整个内容区域在容器里面的垂直位置 start | end | center | stretch | space-around | space-between | space-evenly;

​      -place-content 属性 简写 place-content: \<align-content> \<justify-content>;

​      -grid-auto-columns 属性，多余网格的列宽【当网格只有3X3但指定4行2列位置时时会生成多余的网格】

​      -grid-auto-rows 属性，多余网格的行高

​      -辅助函数

​        -repeat(重复数, 值) 设置行高，列宽使用 eg: repeat(3, 100px 150px 200px) 定义了9个项目列宽行高

​        -minmax()  产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。

​      -关键字

​        -auto-fill 在repeat中使用，示自动填充。项目充满容器

​        -fr ，1fr 代表1倍列宽，2fr代表2倍列宽

​        -auto 由浏览器自己决定长度



​    2、box>div 是项目，span不是

​    -（项目）的float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效

​      ▣项目位置

​        -grid-column-start属性：左边框所在的垂直网格线

​        -grid-column-end属性：右边框所在的垂直网格线

​        -grid-row-start属性：上边框所在的水平网格线

​        -grid-row-end属性：下边框所在的水平网格线

​        -grid-column 属性，简写：grid-column: \<start-line> /\ <end-line>;

​        -grid-row 属性， 简写：grid-row: \<start-line> / \<end-line>;

​        -grid-area 属性，指定项目放在哪一个区域。

​          .grid-area: a;// 指定项目放在a区域

​          .简写：grid-area: \<row-start> / \<column-start> / \<row-end> / \<column-end>; // grid-area: 1 / 1 / 3 / 3;

​        -justify-self 属性，置单元格内容的水平位置

​        -align-self 属性，设置单元格内容的垂直位置

​        -place-self 属性。place-self: \<align-self> \<justify-self>;

​      -关键字

​        -span 跨越了多少个网格

​    3、容器里面的水平区域称为"行"（row）垂直区域称为"列"（column）。

​    4、行和列的交叉区域，称为"单元格"（cell）

​      -正常情况下，n行和m列会产生n x m个单元格。比如，3行3列会产生9个单元格。

​    5、网格线"（grid line）

​      -正常情况下，n行有n + 1根水平网格线，m列有m + 1根垂直网格线，比如三行就有四根水平网格线

​      -指定网格线的名字 

​        grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4]

​        grid-template-rows: [r1 s1] 100px [r2] 100px [r3] auto [r4] // 可设置多个名字

​    

### CSS Shapes布局

> 使用CSS Shapes布局可以实现不规则的图文环绕效果，它需要和float属性配合使用。
>
> CSS Shapes布局的兼容性还是很不错的，移动端可用，企业内部使用的中后台项目也可用
>
> 与CSS Shapes布局相关的属性并不多，包括shape-outside、shape-margin和shape- image-threshold这3个CSS属性，学习成本比网格布局和弹性布局小很多。

#### shape-outside属性

shape-outside属性是shapes布局的核心，其支持的属性值分为如下4类。

- none表示默认值。

	表示普通的矩形环绕。

- \<shape-box>表示图形盒子。

	是指定文字环绕时依照哪个盒子模型的边缘来计算。

- \<basic-shape>表示基本图形函数。

	它和clip-path剪裁属性支持的基本形状函数一模一样。

- \<image>表示图像类。

	包括URL链接图像、渐变图像、cross-fade()函数图像、element ()函数图像等

[不同shape-outside盒子布局效果](https://demo.cssworld.cn/new/6/4-1.php)



**基本的形状函数**

> CSS世界中的“基本的形状函数”指的就是下面这4个形状函数，这几个形状函数在其他与形状相关的CSS属性中也是适用的，例如clip-path属性

● circle()表示圆。

```css
circle([<shape-radius>]? [at <position>]?)
/* 半径，圆心 */
eg:
shape-outside: circle(50px at 50px 50px)
```

● ellipse()表示椭圆。

> \<shape-radius>指的是水平半径和垂直半径，\<position>指的是椭圆的圆心位置

```css
ellipse([<shape-radius>{2}]? [at <position>]?)

eg:
shape-outside: ellipse(50px 75px at 50% 50%)


```

● inset()表示内矩形（包括圆角矩形）。

```css
inset(<shape-arg>{1,4} [round <border-radius>]?)

eg:
shape-outside: inset(10px 20px 30px 40px round 10px)
```

● polygon()表示多边形。

```css
polygon([<fill-rule>,]? [<shape-arg> <shape-arg>])

shape-outside: polygon(0 0, 100px 0, 0 50px, 10px 100px ,0 100px)
```

[效果展示](https://demo.cssworld.cn/new/6/4-2.php)

**图像类**

> 这里只讲URL图像和渐变图像，因为这两种类型最常用。

[文字围绕鹦鹉轮廓](https://demo.cssworld.cn/new/6/4-3.php)

```css
.shape {
    float: left;
    width: 150px; height: 200px;
    background-color: currentColor;
    shape-outside: url(../images/bird.png);
    -webkit-mask: url(../images/bird.png) no-repeat;
    mask: url(../images/bird.png) no-repeat;
    color: #cd0000;
}
```

**gradient渐变与环绕。**

> 这里的渐变可以是线性渐变、径向渐变、锥形渐变和对应的重复渐变。

[效果展示](https://demo.cssworld.cn/new/6/4-4.php)

```css
shape {
    float: left;
    width: 150px; height: 120px;
    --gradient: linear-gradient(to right bottom, #cd0000, transparent 50%, transparent 90%, #cd0000);
    shape-outside: var(--gradient);
    background: var(--gradient);
}
```



#### **shape-margin属性**

> shape-margin属性很好理解，其作用就是控制文字环绕图形时文字与元素边界的距离。这个属性很有用，因为在Shapes布局中，文字环绕有时候是无视margin属性的，想要让文字和元素边界保持一定距离，多半还得用shape-margin属性。

```css
/*长度单位*/
shape-margin: 10px;
shape-margin: 20mm;

/*百分比值*/
shape-margin: 20%;
```

只支持一个值

[效果演示](https://demo.cssworld.cn/new/6/4-5.php)



#### shape-image-threshold属性

> hreshold这个单词是“阈值”的意思，shape-image-threshold指图像环绕时候的半透明阈值，默认是0.0，也就是图像透明度为0的区域边界才能被文字环绕。同理，如果属性值是0.5，则表示透明度小于0.5的区域都可以被文字环绕。
>
> 就是说使用颜色渐变时透明度低的地方可以设置被文字覆盖

[效果展示](https://demo.cssworld.cn/new/6/4-6.php)



[布局案例](https://demo.cssworld.cn/new/6/4-7.php)



## 不同设备的适配与响应



### @media规则

> @media规则是用来匹配不同设备的，例如，响应式布局中常用的宽度查询与适配

```css
aside {
    width: 200px;
    float: left;
}
/*当前设备屏幕宽度小于 480px 的时候隐藏侧边栏*/
@media only screen and (max-width: 480px) {
    aside {
        display: none;
    }
}
```

#### 媒体查询修饰符

- only

	only修饰符很有意思，它本身并没有任何效果，将其去掉或加上，最终效果是没有任何变化的.

	> 为了让老旧的浏览器干脆不要识别一些新的查询语句，就设计了only修饰符，放在媒体类型的前面。这样only screen and (color)查询语句中的CSS代码就再也不会被老旧的浏览器解析了，因为这些老旧的浏览器认为only是非法的。

- not

	not否定的不是媒体类型，而是后面整个查询语句

#### 媒体类型

> 大家只需要关心screen、print和all这3个媒体类型就可以了，对于曾经支持的speech、tv等8个媒体类型，大家直接忽略即可，目前在规范中已经将它们舍弃了，浏览器也已经放弃了对它们的支持

想要在打印的时候使页面的头部和尾部

```css
@media print {
    header, footer {
        display: none;
    }
}
```

我们可以使用逗号同时指定多个媒体类型，例如：

```css
@media screen, print {...}
```

#### 媒体条件

> 媒体条件有3个，即not、and和or。

not表示否定某个媒体特性，例如not (color)表示非彩色设备。

and和or则是有效且常用的，前者表示条件同时满足，后者表示满足之一即可

```css
/* 如果设备更新频率慢，或者不支持鼠标行为 */
@media (update: slow) or (hover: none) {}

/*宽度在 320px~480px,同时分辨率是150dpi的设备*/
```



#### 媒体特性

> 媒体特性指的是对媒体特性的描述信息，包括浏览器、用户设备和使用环境等特性。

![img](https://raw.githubusercontent.com/wyf195075595/images/main/blog/epub_40870013_665)

> 媒体特性虽然多，但大多数媒体特性都不实用，只有width和height媒体特性，以及宽高比和屏幕分辨率等媒体特性比较实用，

#### 对深色模式和动画关闭的支持检测



1. prefers-color-scheme

	> prefers-color-scheme媒体特性可以用来检测当前网页是否处于深色模式（或称黑暗模式）中，其支持的参数值如下。
	>
	> ● no-preference表示系统没有告知用户使用的颜色方案。
	>
	> ● light表示系统倾向于使用浅色模式。
	>
	> ● dark表示系统倾向于使用深色模式。
	>
	> 
	>
	> 除该媒体特性的兼容性非常好之外，更重要的原因是这个媒体特性非常实用
	>
	> 以后的Web产品支持深浅两种主题是大概率事件，尤其是用户群广泛的产品，届时，必定会大规模使用prefers-color-scheme。

	```css
	/*深色模式*/
	@media (prefers-color-scheme: dark) {
	    body {background: #333; color: white}
	}
	
	/*浅色模式*/
	@media (prefers-color-scheme: light) {
	    body {background: #333; color: white}
	}
	```

	js中判断是否支持深色模式

	```js
	window.matchMedia("(prefers-color-scheme: dark)").matches;
	```

	prefers-color-scheme是CSS世界中被迅速支持的媒体特性之一，目前所有现代浏览器均支持此媒体特性，读者可以完全放心大胆地使用。

	[对网页快速改造深色模式的技巧](https://demo.cssworld.cn/new/7/1-1.php)

	```css
	@media (prefers-color-scheme: dark) {
	    body { 
	        filter: invert(1) hue-rotate(180deg);
	        background-color: #000;
	    }
	    img {
	        filter: invert(1) hue-rotate(180deg);
	    }
	    p::after {
	        content: "深色";
	    }
	}
	```

	> 不过，需要提醒一下大家，filter:invert(1)这种“偷懒”的技巧只适合用于不太重要的页面。根据我的实际开发经验，filter滤镜在Safari浏览器中会带来潜在的渲染问题。

2. prefers-reduced-motion

	> prefers-reduced-motion媒体特性的兼容性和prefers-color-scheme是一样的，毕竟属于体验增强特性，大家可以无顾虑地使用。
	>
	> prefers-reduced-motion用来检测操作系统是否设置了关闭不必要动画的操作，其支持的参数值如下。
	>
	> ● no-preference表示用户没有通知系统任何首选项。
	>
	> ● reduced表示用户已通知系统

	几乎所有操作系统都有对应的关闭不必要动画的首选项，具体设置方式如下。

	● Windows 10：设置→轻松使用→在Windows中显示动画。

	● Windows 7：控制面板→轻松访问→使计算机更易于查看→关闭所有不必要的动画（如果可能）。

	● macOS：系统偏好→辅助使用→显示器→渐弱动态效果。

	● iOS：设置→通用→辅助功能→渐弱动态效果。

	● Android 9+：设置→辅助功能→移除动画（或者高级视觉效果）。

	根据我查到的一些信息，关闭操作系统动画的用户并不少，尤其是对用户基数很大的产品而言。

	

	如果用户选择了关闭动画，那么我们要让Web应用中的动画同步关闭。例如，弹框出现就不需要有弹一下的动画效果，评论框也不需要从下方出现，直接让评论框显示出来就可以。CSS代码示意如下：

	```css
	@media (prefers-reduced-motion) {
	    .example-1 {
	        animation: none;
	    }
	    .example-2 {
	        transition: none;
	    }
	}
	
	/*甚至可以*/
	@media (prefers-reduced-motion) {
	    * {
	        animation: none;
	        transition: none;
	    }
	}pointer和any-pointer
	```



#### 对鼠标行为和触摸行为的支持检测

> :hover伪类在移动端，尤其在iOS的Safari浏览器中的交互会很奇怪，可以触发但是不容易触发，而且触发后的hover状态不太容易消除，体验并不好
>
> 用设备宽度解决，又会有手机横屏等问题。仅通过屏幕宽度判断是不是触屏设备是非常片面和不准确的
>
> 完美的CSS解决方法就是CSS世界新出现的支持检测鼠标行为和触摸行为的媒体特性。

1. any-hover

	> any-hover媒体特性可用于测试是否有任意可用的输入装置可以悬停（就是hover行为）在元素上。例如，鼠标这个输入装置就可以控制鼠标指针的位置，以及悬停在元素上。因此，下个不太严谨的结论，any-hover其实就是用来检测设备是否接入了鼠标的。

	● none表示没有输入装置可以实现悬停效果，或者没有可以实现指向的输入装置。

	● hover表示一个或多个输入装置可以触发元素的悬停交互效果。

	希望\<figcaption>元素在不支持悬停效果的设备上显示：

	```css
	figcaption {
	    display: none;
	}
	figure:hover figcaption {
	    display: block;
	}
	@media (any-hover: none) {
	    figcaption {
	        display: block;
	    }
	}
	```

	[效果展示](https://demo.cssworld.cn/new/7/1-2.php)

	> 该媒体特性在CSS新特性中算是兼容性不错的，考虑到any-hover媒体特性的作用是体验增强，因此在生产环境中大可放心使用，无须顾虑陈旧设备。

2. hover

	> hover媒体特性的语法和作用与any-hover是一样的，两者的主要区别在于，any-hover检测任意输入装置，而hover只检测主要的输入装置。

	● none表示主输入装置根本无法悬停或无法方便地悬停（例如，使用长点击来模拟悬停，而长点击这种交互并不方便），或者没有主输入装置。

	● hover表示主输入装置可以触发元素的悬停交互。

	```css
	figcaption {
	    display: none;
	}
	figure:hover figcaption {
	    display: block;
	}
	@media (hover: none) {
	    figcaption {
	        display: block;
	    }
	}
	```

	> hover媒体特性的兼容性更好，且从Edge12+就开始支持hover了，因此，如果是针对传统桌面端网页的体验优化，可以优先使用hover媒体特性。

3. pointer和any-pointer

	> pointer是与点击事件相关的。pointer和any-pointer媒体特性主要用于识别当前环境，判断是否可以非常方便地进行点击操作。

	any-pointer支持3个属性值，含义分别如下。

	● none表示没有可用的点击设备。

	● coarse表示至少有一个设备的点击不是很精确。例如，使用手指操作手机就属于点击不精确。

	● fine表示有点击很精准的设备。例如，用鼠标操作的计算机浏览器。

	[点击不精准情况下增大选框范围](https://demo.cssworld.cn/new/7/1-3.php)：

	```css
	@media (pointer: coarse) {
	    input[type="checkbox"] {
	        width: 30px;
	        height: 30px;
	    }
	}
	```

	

### 环境变量函数env()

> 环境变量函数env()规范的制定和兴起是由于iPhone X这类带有“刘海屏”和底部触摸条的移动设备的出现，如果按钮和底部触摸条在一起显示，就会出现交互冲突的问题，而env()函数可以让网页内容显示在设备的安全区域范围
>
> 通过使用env()函数，很多原本需要特殊权限才可以访问的信息就可以作为全局变量在整个页面文档中使用。只是，虽然env()函数的规划很长远，但是由于目前规范还只停留在第一阶段的草案阶段，内容很少，因此目前可以在实践中应用的就只有设置安全边距。
>
> env()函数的语法和var()函数的语法很相似，它们的区别在于，env()函数可以用在媒体查询语句中，甚至用在选择器中，但var()函数只能作为属性值或作为属性值的一部分。
>
> 或者说，凡是可以使用var()函数的地方一定可以使用env()函数，但是反过来却不成立。

```css
/* 直接使用4个安全内边距值 */
env(safe-area-inset-top);
env(safe-area-inset-right);
env(safe-area-inset-bottom);
env(safe-area-inset-left);

/* 使用4个安全内边距值，同时设置兜底尺寸值 */
env(safe-area-inset-top, 20px);
env(safe-area-inset-right, 1em);
env(safe-area-inset-bottom, 0.5vh);
env(safe-area-inset-left, 1.4rem);
```

![img](https://raw.githubusercontent.com/wyf195075595/images/main/blog/epub_40870013_673)

（1）和通常的CSS属性不同，env()函数中的属性是区分大小写的，因此，下面CSS代码中的padding-left值一定是50px：

```css
padding-left: env(SAVE-AREA-INSET-LEFT, 50px);
```

SAFE-AREA-INSET-LEFT是无法识别的属性，因此，会使用兜底的50px作为padding- left的属性值。

（2）要想使safe-area-inset-*属性表现出准确的间距，一定要确保viewport相关的\<meta>信息如下：

```css
<meta name="viewport" content="viewport-fit=cover">
```



### rem和vw单位与移动端适配最佳实践

让不同宽度设备下网页字体合适，动态设置字体大小

1. 使用媒体查询设置 html font-size(根字体)
2. 使用js根据屏幕尺寸设置对应的根字号大小

第一种做法对于非临界点尺寸的设备很不友好

第二种做法的优点在于任何宽度的手机都有对应的根字号设置，但是缺点很多,原罪布局用了Js，根字体会线性放大，后期改动适配策略，代价巨大

**了解视区相对单位**

视区相对单位指的是相对于浏览器视区尺寸（viewport）的单位，具体包括下面4个。

● vw——视区宽度百分值。

● vh——视区高度百分值。

● vmin——vw或vh，取小的那个值。

● vmax——vw或vh，取大的那个值。

[vh单位的经典应用]()

**calc()函数下的最佳实践**

> 有了vw单位，再配合calc()函数进行计算，无须使用任何JavaScript代码，我们就可以实现基于设备宽度的移动端布局适配方案

例如，希望375px～414px的宽度区间的根字号大小是16px～18px，就可以这么设置：

```css
html {
    font-size: 16px;
}
@media screen and (min-width: 375px) {
    html {
        /*
        	375px宽度使用16px基准尺寸，414px宽度时根字体大小正好18px
        */
        font-size: calc(16px + 2 * (100vw-375px) / 39);
    }
}

@media screen and (min-width: 414px) {
    html {
        font-size: 18px;
    }
}
```



clamp语法：

```css
html {
    font-size: 16px;
    font-size: clamp(16px, calc(16px + 2 * (100vw - 375px) / 39), 22px)
}
```

**rem单位不是万能的**

首先渲染尺寸并不总是整数,可能出现一些渲染问题，某些场景下可以把 rem改成 px 单位



### 使用touch-action属性控制设备的触摸行为

> touch-action属性是移动端中与手势触摸密切相关的CSS属性，它源自Windows Phone手机，属于微软系，后来被Chrome浏览器吸收借鉴，Firefox浏览器跟着支持，现在Safari浏览器也已经完全支持（iOS 13之前是部分支持），是一个在移动端可以畅行的CSS属性。

#### 常见应用

1. touch-action:manipulation取消300ms的点击延时

	> touch-action:manipulation表示浏览器只允许进行滚动和持续缩放操作，所以类似双击缩放这种非标准操作就不被允许。想当初，之所以click事件在移动端有300 ms延时，就是因为要避免点击行为和手机双击行为发生冲突。于是，当我们设置touch-action:manipulation时，取消了双击行为，300 ms延时也就不复存在了。

	```css
	html {
		touch-action: manipulation;
	}
	```

2. touch-action:none解决treated as passive错误

	如下js移动端模式下滑页面报错:

	```js
	document.addEventListener('touchmove', function(event) {
	    event.preventDefault();
	})
	```

	css解决方式：

	```css
	html {
	    touch-action: none;
	    overflow: hidden;
	}
	body {
	    touch-action: auto;
	    height: 100vh;
	    position: relative;
	    overflow: auto;
	}
	```

	[移动端查看效果](https://demo.cssworld.cn/new/7/4-1.php)

	不过，此方法改变了默认的滚动行为，比较适合原本就需要对默认滚动行为进行重置的单页，而对于传统的网页，建议还是使用JavaScript，通过传递passive:false参数设置来解决这个问题

	```js
	document.addEventListener('touchmove', function(even) {
	    event.preventDefault();
	}, {passive: false})
	```

	

#### 了解touch-action属性各个属性值的含义

ouch-action属性支持的属性值有：

● auto是默认值，表示手势操作完全由浏览器决定（如\<meta>元素的viewport属性通过设置user-scalable=no/yes来确定是否允许缩放）。

● manipulation表示浏览器只允许进行滚动和持续缩放操作，类似双击缩放这种非标准操作就不可以。此属性值可以用来解决点击后延时300ms的问题。iOS 9.3就已经支持该值。

● none表示不进行任何手势相关的行为，例如，你想用手指滚动网页就不行，双击放大或缩小页面也不可以，所有这些行为都要自定义。另外，从这个属性值开始，一直到最后一个属性值pinch-zoom，都是iOS 13才开始支持的。

● pan-x表示支持手指头水平移来移去的操作。

● pan-y表示支持手指头垂直移来移去的操作。

● pan-left表示支持手指头往左移动，移动开始后往右可以恢复的操作。

● pan-right表示支持手指头往右移动，移动开始后往左可以恢复的操作。

● pan-up表示支持手指头往上移动，移动开始后往下可以恢复的操作。

● pan-down表示支持手指头往下移动，移动开始后往上可以恢复的操作。

● pinch-zoom表示支持手指头缩放页面的操作。

```css
.example {
	touch-action: pan-left pan-up pan-zoom;
}
```

表示可以左移、上移和缩放。这些关键字属性值适合用在需要自定义手势行为的场景下，虽不常用，但很实用。



#### image-set()函数与多倍图设置

> image-set()函数的性质与element()函数、cross-fade()函数的性质是一样的，它们都属于\<image>数据类型，不过就实用性而言，image-set()函数明显高了不只一个级别

image-set()函数可以根据不同设备的屏幕密度或者分辨率来显示不同的背景图（background- image）或者遮罩图片（mask-image）等

```css
.example {
    background-image: image-set(url(1.jpg) 1x, url(2.jpg) 2x, url(1-print.jpg) 600dpi,)
}
```

如果屏幕是1倍屏，也就是设备像素比是1∶1的话，就使用1.jpg作为背景图；如果屏幕是2倍屏及以上，就使用1-2x.jpg作为背景图；如果设备的分辨率大于600dpi，就使用1-print.jpg作为背景图。

> 注意，图片地址需要写在url()函数里，url()函数中不用添加引号

HTML中有一个名为srcset的属性，这个属性与image-set()函数无论是名称还是语法都有相似之处，例如：

```html
<img src="1.jpg" srcset="1-2x.png 2x" >
```

[效果展示](https://demo.cssworld.cn/new/7/5-1.php)

```css
.image-set {
    width: 128px; height: 96px;
    background: url(./fallback.jpg);
    background: -webkit-image-set(
        url(w128.jpg) 1x, 
        url(w256.jpg) 2x, 
        url(w512.jpg) 3x);
    background: image-set(
        url(w128.jpg) 1x, 
        url(w256.jpg) 2x, 
        url(w512.jpg) 3x);
    background-size: cover;
}
```

[了解更多](https://www.zhangxinxu.com/wordpress/2014/10/responsive-images-srcset-size-w-descriptor/)

> 即便在桌面端浏览器中，image-set()函数也可以放心使用，不用担心会影响内容的呈现，因为它是一个渐进增强特性。实际开发时，在image-set()语句之前加一行background:url();语句兜底就可以了，这样就算浏览器不支持image-set()函数，界面样式依然表现良好。

虽然image-set()函数的设计初衷是好的，但是实用性很一般，因为在实际开发中往往会设置直接加载2倍图，1倍图是不加载的。这样做一是因为现在流量便宜，二是因为无须准备多张不同尺寸的图片，可以减少开发和维护成本。

只有下面两种情况才需要用到image-set()函数。

（1）不同屏幕密度下显示的是完全不同的图，而不是只有尺寸不一样的图。例如，在1倍屏下显示造型简单的图标，在多倍屏下显示细节丰富的图标。

（2）用户体验和流量收益足够明显的场景。例如类似WeChat这种用户基数很大的产品；或者给流量费用较高、信号较差的地区开发的产品。



## CSS的变量函数var()与自定义属性



### CSS变量的语法、特性和细节

> CSS变量的语法由两部分组成，一部分是CSS变量的声明，另一部分是CSS变量的使用。其中，CSS变量的声明由CSS自定义属性及其对应的值组成，而CSS变量的使用则通过变量函数var()调用CSS自定义属性实现。

```css
:root {
    --primary-color: deepskyblue;
}
button {
    background-color: val(--primary-color);
}
```

#### CSS自定义属性的命名

> CSS自定义属性时，支持数字命名的,中文等CJK文字也是可以随意使用的
>
> 虽然CSS自定义属性的命名限制较少，但是还是不支持包含$、[、]、^、(、)、%、"等特殊字符的命名，要使用这些特殊字符，需要使用反斜杠转义。

```css
:root {
    --\$: deepskyblue;
    color: var(--\$)
}
```

#### var()函数的语法和特性

> 其中，\<custom-property-name>指的就是自定义属性名；\<declaration-value>指的是声明值，可以理解为备选值或缺省值，当前面的自定义属性一定无效时，就会使用\declaration- value>定义的值。

```CSS
var( <custom-property-name> [, <declaration-value>]? )
```

```css
p {
    background-color: var(--any-what, deepskyblue)
}
```

**参数非法的有趣现象**

```css
body {
    --color: 20px;
    background-color: deeppink;
    background-color: var(--color, deepskyblue);
}
```

请问，此时 body 元素背景色是什么？

A. transarent	B.20px	C.deeppink	D.deepskyblue

> 这是var()函数非常有意思的一点：只要第一个参数值可能有效，哪怕这个参数值是一个乱七八糟的东西，这个var()函数依然会正常解析。下面重点来了：如果第一个参数值是不合法的，则var()函数解析为当前CSS属性的初始值或继承值（如果有继承性），也就是按照unset全局关键字的规则渲染。（注意，只是渲染规则类似，并不等同于直接设置unset关键字。）

上面的案例中的background-color属性值显然不能是20px，因为背景色的值是颜色值，不能是长度值，20px显然是不合法的，所以会使用background-color的初始值transparent代替, **所以正确答案是 A**

[利用初始值默认值设置点击效果](https://demo.cssworld.cn/new/8/1-1.php)

```css
button {
    /* 这里的空格很重要 */
    --open: ;
    color: #2a80eb;
    -webkit-text-fill-color: #fff;
    border-radius: 4px;
    padding: 9px 20px;
    border: 1px solid var(--open, rgba(0,0,0,.1));
    box-shadow: var(--open, inset 0 1px 2px rgba(0,0,0,.1));
    background: var(--open, linear-gradient(#0003, transparent)) currentColor;
    text-shadow: var(--open, -1px -1px #0003);
    transition: .15s;
}
button:active {
    /* 任意全局关键字都可以 */
    --open: inherit;
}
```

> 利用这种特性让CSS变量同时开启和关闭一个或多个不同的属性值，从而让CSS代码更加简洁
>
> 空格在语法上是可能有效的。但是空格对于box-shadow和background等CSS属性是无效的，因此，box-shadow和background等CSS属性均解析为初始值

#### var()函数的空格尾随特性

```css
hmtl { font-size: 14px; }

body {
    --size: 20 ;
    font-size: 16px;
    font-size: var(--size)px;
}
```

请问， 此时 body 元素的font-size 大小是多少？

A.medium	B.14px	C.16px	D.20px

> 此处font-size:var(--size)px等同于font-size:20px，注意，20后面有一个空格，这属于不合法的font-size属性值。由于语法上var(--size)px又是合法的，因此会重置font-size:16px，最终使用父元素设置的字号大小14px。
>
> **所以答案是B**

#### CSS自定义属性的作用域

> 全局作用域是 root ,其他作用域跟css 父子层级类似。div 中定义的变量在body 上使用无效果，反之则有效果。**本质上是继承特性， [Shadow DOM中的元素也能继承](https://demo.cssworld.cn/new/8/1-2.php)**



#### [CSS自定义属性值可以是任意值或表达式](https://weread.qq.com/web/reader/13c32c90726fa07d13c0072k9a132c802349a1158154a83)



#### CSS自定义属性值可以相互传递

```css
body {
    --green: #4caf50;
    --successColor: var(--green);
    --columns: 4;
    --margins: calc(24px / var(--columns));
}
```



#### CSS自定义属性不能自身赋值

```css
:root {
    --primary-color: deepskyblue;
}
.some-class {
    --primary-color: var(primary-color, #2a80eb);
    /*
     --primary-color 会被认为是非法的，color的颜色为当前上下文的颜色
    */
    color: var( --primary-color);
}
```



#### CSS自定义属性不支持用在媒体查询中

> 在 @media() 中无法使用，但是可以使用 evn()函数

### 在 页面中设置获取变量

html:

```html
<div id="box">
    <img src="1.jpg" style="border 10px solid var(--color);" />
</div>
```

js

```js
box.style.setProperty('--color', 'deepskyblue')
```

> 普通属性可以通过style.xxx = x 设置，但是自定义属性智能通过 setProperty属性设置。

```js
// 获取 --color CSS变量
var cssVarColor = getComputedStyle(box).getPropertyValue('--color')
```



### 使用content属性显示CSS自定义属性值的技巧

> 有时候需要让CSS变量中的自定义属性值能够同时作为字符内容在页面中呈现。我们很快就会想到使用::before和::after伪元素配合content属性来实现，但是，把CSS自定义属性值作为content属性值是没有任何效果的。

```css
/* 无效 */
.bar::before {
    content: var(--percent);
}
```

借助CSS计数器呈现CSS自定义属性值

```css
/* 有效 */
.bar::before {
    counter-reset: progress var(--percent);
    content: var(progress);
}
```

> 虽然content属性本身不支持CSS自定义属性值，但是counter-reset属性后面的计数器初始值是支持的，于是我们可以来一招“移花接木”，从而让CSS自定义属性值作为字符在页面中显示。

[css自定义属性实现上传进度条](https://demo.cssworld.cn/new/8/3-1.php)

> 如果希望兼容陈旧的设备，可以试试GitHub站点上一个名为css-vars-ponyfill的项目，其可以让CSS变量兼容到IE9+



### 使用CSS变量自定义全新的CSS语法

> 虽然目前CSS Color Level 4支持非常多的颜色写法，但是唯独不支持颜色关键字的半透明效果。所以我就会思考这样一个问题：有没有可能自创一个CSS函数语法，实现颜色关键字的半透明效果？

例如：

```css
color: keyword(red, 50%)
color: keyword(red, 0.5)
color: keyword(red / 50%)
color: keyword(red / 0.5)
```

> 直接这样写的话浏览器认为是不合法
>
> CSS自定义属性作为信使使整个语法合法化

```css
body {
    --keyword: keyword(red, 50%); // 合法
    color: var(--keyword);
}
```

> JavaScript获取使用了keyword()函数的元素，再将其转换成浏览器可识别的颜色函数
>
> （1）获取页面中所有包含keyword()函数的自定义属性。
>
> （2）遍历并观察所有DOM，如果设置了对应的自定义属性，则将keyword()函数语法转换成浏览器能够识别的rgba()函数语法。
>
> [效果展示](https://demo.cssworld.cn/new/8/4-1.php)

```js
/**
 * @description CSS 自定义的keyword()方法的支持和使用
 * @author zhangxinxu(.com) 2020-08-11
 * @docs https://www.zhangxinxu.com/wordpress/?p=9537
 * @license MIT 作者和出处保留
 */

(function () {
    if (!window.CSS) {
        return;
    }

    if (!NodeList.prototype.forEach) {
        NodeList.prototype.forEach = Array.prototype.forEach;
    }


    // 获取页面中所有的CSS自定义属性
    var isSameDomain = function (styleSheet) {
        if (!styleSheet.href) {
            return true;
        }

        return styleSheet.href.indexOf(window.location.origin) === 0;
    };

    var isStyleRule = function (rule) {
        return rule.type === 1;
    };

    var arrCSSCustomProps = (function () {
        return [].slice.call(document.styleSheets).filter(isSameDomain).reduce(function (finalArr, sheet) {
            return finalArr.concat([].slice.call(sheet.cssRules).filter(isStyleRule).reduce(function (propValArr, rule) {
                var props = [].slice.call(rule.style).map(function (propName) {
                    return [
                        propName.trim(),
                        rule.style.getPropertyValue(propName).trim()
                    ];
                }).filter(function ([propName]) {
                    return propName.indexOf('--') === 0;
                });

                return [].concat(propValArr, props);
            }, []));
        }, []);
    })();

    // 使用了keyword()语法的CSS自定义属性名
    var arrCssPropsValueIsKeyword = arrCSSCustomProps.filter(function (arrPropVal) {
        return /keyword\([\w\W]+\)/i.test(arrPropVal[1]);
    });

    // 设置自定义属性值的方法
    var funKeywordColor2Rgba = function (node) {
        if (node.nodeType != 1 || ['script', 'style', 'meta', 'title', 'head'].includes(node.nodeName.toLowerCase())) {
            return;
        }

        // 当前节点的所有样式对象
        var objStyle = window.getComputedStyle(node);

        // 所有设置了keyword()的自定义属性的遍历处理
        arrCssPropsValueIsKeyword.forEach(function (arr) {
            var cssProp = arr[0];

            // 判断当前元素是否设置了当前自定义属性
            var cssVarValueKeyword = objStyle.getPropertyValue(cssProp);

            if (!cssVarValueKeyword || !cssVarValueKeyword.trim() || !/keyword\([\w\W]+\)/i.test(cssVarValueKeyword)) {
                return;
            }

            cssVarValueKeyword = arr[1];

            // 解析与处理
            var keyColorAndOpacity = cssVarValueKeyword.replace(/\w+\(([\w\W]+)\)/, '$1');

            var arrKeyColorAndOpacity = keyColorAndOpacity.split(/\s+/);

            if (/,/.test(keyColorAndOpacity)) {
                arrKeyColorAndOpacity = keyColorAndOpacity.split(',');
            } else if (/\//.test(keyColorAndOpacity)) {
                arrKeyColorAndOpacity = keyColorAndOpacity.split(',');
            }

            if (arrKeyColorAndOpacity.length != 2) {
                return;
            }

            // 分出颜色和透明度
            var keyColor = arrKeyColorAndOpacity[0].trim();
            var opacity = (arrKeyColorAndOpacity[1] || '1').trim();

            // keyColor转rgb
            document.head.style.backgroundColor = keyColor;
            var rgbColor = window.getComputedStyle(document.head).backgroundColor;

            // 应用的颜色
            var applyColor = '';
            // 透明度替换
            if (/^rgba/.test(rgbColor)) {
                applyColor = rgbColor.replace('1)', opacity + ')');
            } else {
                applyColor = rgbColor.replace(')', ', ' + opacity + ')');
            }

            node.style.setProperty(cssProp, applyColor);
        });
    };


    var funAutoInitAndWatching = function () {
        // DOM Insert自动初始化
        if (window.MutationObserver) {
            var observerSelect = new MutationObserver(function (mutationsList) {
                mutationsList.forEach(function (mutation) {
                    var nodeAdded = mutation.addedNodes;
                    // 新增元素
                    nodeAdded.forEach(function (eleAdd) {
                        funKeywordColor2Rgba(eleAdd);
                    });
                });
            });

            observerSelect.observe(document.body, {
                childList: true,
                subtree: true
            });
        }

        // 如果没有开启自动初始化，则返回
        document.querySelectorAll('*').forEach(function (ele) {
            funKeywordColor2Rgba(ele);
        });
    };

    if (document.readyState != 'loading') {
        funAutoInitAndWatching();
    } else {
        window.addEventListener('DOMContentLoaded', funAutoInitAndWatching);
    }
})();
```



#### CSS变量模拟CSS新特性

> CSS中的有些非常棒的新特性还没有被浏览器所支持，attr()函数的新语法就是其中之一。如果浏览器支持attr()函数的新语法，那么我们就可以使用任意HTML自定义属性控制元素的样式
>
> [css变量Polyfill attr 语法](https://demo.cssworld.cn/new/8/4-2.php)

- ##### HTML：

	```html
	<button bgcolor="skyblue" radius="4">按钮</button>
	<button bgcolor="#00000040" radius="1rem">按钮</button>
	<button bgcolor="red" radius="50%">按钮</button>
	<button bgcolor="orange" radius="100% / 50%">按钮</button>
	```

- ##### CSS：

	```css
	button {
	    border: 0;
	    padding: .5em 1em;
	}
	button {
	    --attr-bg: attr(bgcolor color);
	    background-color: var(--attr-bg);
	    --attr-radius: attr(radius px, 4px);
	    border-radius: var(--attr-radius);
	}
	```

## 丰富的图形处理

### 超级实用的CSS遮罩

> CSS遮罩属性非常实用，它可以让一个元素按照某张图像的轮廓显示。有非常多的图形表现效果只能使用遮罩实现，因此CSS遮罩在CSS世界中有着独一无二的地位。

#### mask-image属性的详细介

> mask-image属性是CSS遮罩第一阶段就支持的CSS属性，兼容性非常好，作用是设置使用遮罩效果的图像。这里有一点需要说明，虽然浏览器很早就支持mask-image属性，但是只支持是部分传统的属性值，例如url()函数，很多新特性是第二阶段才出来的，可能会有浏览器不支持的情况，这一点会在接下来的内容中有所体现。

```css
mask-image: none | <image> | <mask-source>
```

● none是默认值，表示默认无遮罩图片。

● \<image>表示图像数据类型，包括CSS渐变图像、url()函数、image-set()函数、cross-fade()函数和element()函数等。

● \<mask-source>表示遮罩元素类型，主要指SVG遮罩元素。

1. [带有半透明的PNG图像的遮罩效果](https://demo.cssworld.cn/new/12/1-1.php)

	- ##### HTML：

		```html
		<img src="nature-8.jpg" class="mask-image" width="256" height="192">
		```

	- ##### CSS：

		```css
		.mask-image {
		    -webkit-mask: no-repeat center / contain;
		    mask: no-repeat center / contain;
		    -webkit-mask-image: url(../images/bird.png);
		    mask-image: url(../images/bird.png);
		}
		```

2. [SVG图形遮罩效果展示](https://demo.cssworld.cn/new/12/1-2.php)

	> 除了常见的GIF、PNG等位图图片，SVG矢量图片也可以作为mask-image的遮罩图像。

	- ##### HTML：

		```html
		<img src="8.jpg" class="mask-image" width="256" height="174">
		```

	- ##### CSS：

		```css
		.mask-image {
		    --svg: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Cpath d='M28.027 5.161l-17.017 17.017-7.007-7.007-3.003 3.003 10.010 10.010 20.020-20.020z'%3E%3C/path%3E%3C/svg%3E");
		
		    -webkit-mask-image: var(--svg);
		    mask-image: var(--svg);
		
		    -webkit-mask-repeat: no-repeat;
		    mask-repeat: no-repeat;
		}
		```

3. [用渐变图像实现遮罩效果](https://demo.cssworld.cn/new/12/1-3.php)

	> 在实际开发过程中，使用渐变图像实现遮罩效果是最常用的，也是最实用的，因为此时的渐变图像是代码生成的，相比url()函数外链的图像，其资源开销小，开发成本低，维护更加方便。

	- ##### HTML：

		```html
		<img src="1.jpg" class="mask-image" width="256" height="192">
		```

	- ##### CSS：

		```css
		.mask-image {
		    --gradient1: radial-gradient(600px 80px at top, transparent 150px, black 152px 1000px, transparent 0);
		    --gradient2: radial-gradient(600px 80px at bottom, transparent 150px, black 152px 1000px, transparent 0);
		    -webkit-mask-image: var(--gradient1), var(--gradient2);
		    mask-image: var(--gradient1), var(--gradient2);
		}
		```

4. [SVG图形中\<mask>元素作为遮罩图像](https://demo.cssworld.cn/new/12/1-4.php)

	> \<mask-source>数据类型并不常用，日常开发中还是使用PNG图像和SVG图像更方便。但这并不表示\<mask-source>数据类型一无是处，它有一个不可替代的巨大优势，就是让IE9在内的浏览器也能实现遮罩效果

5. [外链SVG文件的\<mask>元素作为遮罩元素](https://demo.cssworld.cn/new/12/1-6.php)

	> 无论是普通HTML元素，还是SVG元素，遮罩效果都只会在Firefox浏览器中出现，在IE浏览器、Edge浏览器和Chrome浏览器中均无效。

6. [image-set()、cross-fade()或element()作为遮罩图像](https://demo.cssworld.cn/new/12/1-7.php)

	> 所有\<image>数据类型的值都可以作为遮罩图像，自然也就包括image-set()、cross-fade()和element()这3个图像函数。

	- ##### HTML：

		```html
		<h4>image-set()</h4>
		<img src="1.jpg" class="mask-image image-set">
		
		<h4>cross-fade()</h4>
		<img src="1.jpg" class="mask-image cross-fade">
		
		<h4><span id="title">element()</span></h4>
		<img src="1.jpg" class="mask-image element">
		```

	- ##### CSS：

		```css
		.image-set {
		    -webkit-mask-image: -webkit-image-set(url(bird.png) 1x, url(triangle.svg) 2x);
		    mask-image: image-set(url(bird.png) 1x, url(triangle.svg) 2x);
		}
		.cross-fade {
		    -webkit-mask-image: -webkit-cross-fade(url(bird.png), url(triangle.svg), 50%);
		    mask-image: cross-fade(url(bird.png), url(triangle.svg), 50%);
		}
		.element {
		    -webkit-mask-image: -webkit-element(#title);
		    mask-image: -moz-element(#title);
		    mask-image: element(#title);
		}
		```

#### mask-mode属性

> mask-mode属性的默认值是match-source，作用是根据资源的类型自动采用合适的遮罩模式。例如，如果遮罩效果使用的是SVG中的\<mask>元素，则此时的mask-mode属性的计算值是**luminance，表示基于亮度判断是否要进行遮罩**。如果是其他场景，则计算值是**alpha，表示基于透明度判断是否要进行遮罩**
>
> mask-mode:luminance让JPG图片也能有遮罩效果示意

```css
mask-mode: match-source| luminance| alpha;
```

> 目前，仅Firefox浏览器支持mask-mode属性，Chrome浏览器并不提供支持，但是可以使用非标准的mask-source-type属性代替（没有私有前缀）

#### mask-repeat属性

> mask-repeat属性的默认值是repeat，作用类似于background-repeat属性。

```css
mask-repeat: repeat-x| repeat-y| repeat| no-repeat| space| round;
```

mask-image支持多遮罩图片，因此mask-repeat也支持多属性值

```css
mask-repeat: round repeat, space, repeat-x;
```

● space与background属性中的space的含义是类似的，表示遮罩图片尽可能地平铺，同时不进行任何剪裁。

● round表示遮罩图片尽可能靠在一起，没有任何间隙，同时不进行任何剪裁。这意味着图片可能会产生缩放效果。



#### mask-position属性

> mask-position和background-position支持的属性值和属性值的表现基本上都是一样的。例如，mask-position的默认计算值是0% 0%，也就是相对左上角定位。

```css
mask-position: top;
mask-position: right top;
mask-position: 30% 50%;
mask-position: top 20px left 20px;
mask-position: top, center;/* 多属性 */
```



#### mask-clip属性

> mask-clip属性用来设置遮罩效果显示的盒子区域。
>
> mask-clip的几个属性值中比较有实用价值的是border-box、padding-box和content-box这3个关键字的属性值，原因很简单——Chrome浏览器仅支持这3个关键字。

```css
mask-clip: border-box| padding-box| content-box| no-clip| fill-box| fill-box| stroke-box| view-box;
```

- no-clip

	mask-clip属性支持一个名为no-clip的属性值，我个人觉得这个值的效果还是很稀奇的，它的作用是不对元素的遮罩效果做区域上的限制，言外之意就是只要是元素身上“长出来”的东西，都可以应用遮罩效果。例如，轮廓（outline）、盒阴影（box-shadow）都是可以应用遮罩效果的。



#### 关于fill-box、stroke-box和view-box

> fill-box、stroke-box和view-box这3个关键字属性值要应用在SVG元素上才有效果。

● fill-box表示遮罩应用的区域是图形填充区域形成的边界盒子

● stroke-box表示的遮罩区域把描边占据的区域也包含在内。

● view-box表示使用最近的SVG视口作为参考盒子。如果SVG代码中的viewBox属性有设置，则遮罩区域盒子位于viewBox属性建立的坐标系的原点，尺寸由viewBox属性中的宽高值决定。

[效果展示](https://demo.cssworld.cn/new/12/1-8.php)

#### mask-origin属性

> mask-origin属性表示遮罩效果起始点，其与background-origin有很多类似之处。也支持多属性

```css
mask-origin: content-box| padding-box| border-box| fill-box| stroke-box| view-box;
```



#### mask-size属性

> mask-size属性的性质和background-size属性类似，支持的关键字属性值也类似，作用是控制遮罩图片尺寸.支持多属性

```css
mask-size: cover| contain;

mask-size: 50% auto;
mask-size: 3em 25%;
mask-size: auto 6px;
```

#### mask-type属性

> mask-type属性的功能和mask-mode属性类似，都是设置不同的遮罩模式，但还是有一个很大的区别，就是mask-type属性只能作用在SVG元素上

```css
mask-type: alpha| luminance;
```



#### mask-composite属性

> mask-composite属性表示同时使用多张图片进行遮罩时的合成方式

```css
mask-composite: add| subtract| intersect| exclude;
```

● add表示遮罩累加，是默认值。

● subtract表示遮罩相减，也就是遮罩图片重合的区域不显示。这就意味着，遮罩图片越多，遮罩区域越小。

● intersect表示遮罩相交，也就是遮罩图片重合的区域才显示遮罩。

● exclude表示遮罩排除，也就是遮罩图片重合的区域被当作透明的。

[效果展示](https://demo.cssworld.cn/new/12/1-9.php)

```html
<img src="1.jpg" class="mask-image"> 
.mask-image {
    mask: url(https://res.weread.qq.com/wrepub/web/40870013/bird.png) no-repeat center / contain, linear-gradient(black, black);
    webkit-mask-composite: xor;
    mask-composite: exclude; 
}
```



#### CSS遮罩的一些经典应用示例

[PNG/SVG背景图标变色最佳实践](https://demo.cssworld.cn/new/12/1-10.php)

- ##### HTML：

	```html
	<a href="javascript:" id="buttonDelete"><i class="icon-delete"></i> 删除</a>
	```

- ##### CSS：

	```css
	.icon-delete {
	    display: inline-block;
	    width: 20px; height: 20px;
	    background-color: currentColor;
	    --mask: url(/images/8/delete@2x.png) no-repeat center / 1.125em 1.125em;
	    -webkit-mask: var(--mask);
	    mask: var(--mask);
	
	    vertical-align: -4px;
	}
	```

> 虽说是最佳实践，但是上面的代码并不是完美的，因为应用遮罩的当前元素的outline效果会失去，这对于无障碍访问是有影响的，解决方法是将图标遮罩的执行放在伪元素中，也就是在.icon-delete::before{}语句中应用遮罩效果，这样，.icon-delete元素依然可以有outline轮廓效果。

[大尺寸PNG图片的尺寸优化](https://demo.cssworld.cn/new/12/1-11.php)

- ##### HTML：

	```html
	<img src="leaf.jpg" width="300">
	```

- ##### CSS：

	```css
	img {
	    --mask-url: url(leaf-mask.png);
	    -webkit-mask-image: var(--mask-url);
	    mask-image: var(--mask-url);
	    -webkit-mask-size: 300px;
	    mask-size: 300px;
	}
	```

> PNG图片的文件大小之所以大，就是因为其色彩过于丰富，如果把图片变成纯色，图片文件大小可以降低很多
>
> 单纯用png(259K),转成jpg(55.6),制作png纯色图片（4K）,由于遮罩图像的解析有跨域的限制，因此，大小不足5KB的纯色的PNG背景遮罩图像可以直接转换为Base64格式并内联在CSS文件中

#### -webkit-mask-box-image和mask-border属性

> -webkit-mask-box-image和mask-border属性实现的都是边框遮罩效果。它们之间的区别有以下几点。

● 语法细节不同。例如，-webkit-mask-box-image的属性值使用空格分隔，不会用到斜杠，而mask-border属性是多个CSS属性的缩写，会使用斜杠进行属性值的区分。

● -webkit-mask-box-image是非标准CSS属性，mask-border是标准CSS属性。

● -webkit-mask-box-image属性在webkit内核浏览器中兼容性极佳，因此，在移动端项目可以放心大胆使用；而mask-border属性在我书写这段内容的时候还没有被任何浏览器支持，暂无实用价值。

#### -webkit-mask-box-image属性

> background和mask有很多相似之处，一个是背景图像，另一个是背景遮罩。同样，这里的-webkit-mask-box-image属性和border-image属性也是这种关系，border-image属性表示边框图像，而-webkit-mask-box-image属性则表示边框遮罩。

```css
-webkit-mask-box-image: none;
-webkit-mask-box-image: <mask-box-image> [<top> <right> <bottom> <left> <x-repeat> <y-repeat>]
```

[尺寸任意渐变提示框效果](https://demo.cssworld.cn/new/12/1-12.php)



#### mask-border属性

> 虽然目前mask-border属性的兼容性不好，但是它毕竟是CSS规范属性，是未来之星。mask-border属性和border-image属性在语法上极为相似。首先，mask-border属性也是以下多个CSS属性的缩写：
>
> ● mask-border-mode；
>
> 支持alpha和luminance这两个关键字属性值
>
> ● mask-border-outset；
>
> 表示边框遮罩效果向外偏移的大小，支持长度值和数值，如果值是数值，则表示边框宽度border-width属性值的倍数
>
> ● mask-border-repeat；
>
> 表示遮罩图像的平铺方式，支持stretch、repeat、round、space等关键字属性值
>
> ● mask-border-slice；
>
> 表示对遮罩图像进行九宫格划分的方式，支持4个方位的划分
>
> ● mask-border-source；
>
> 支持任意的\<image>数据类型的图像，常见的有url()图像和渐变图像
>
> ● mask-border-width。
>
> 



### 实用的CSS剪裁属性clip-path

> clip-path属性可以用来对任意元素的可视区域进行剪裁。
>
> 相比CSS2.1中的clip属性，clip-path属性不需要将元素设置为绝对定位就能生效，并且剪裁的形状类型要远比clip属性丰富得多。clip属性只能进行矩形剪裁，而clip-path属性不仅可以进行矩形剪裁，圆形、多边形和不规则形状都是可以剪裁的。
>
> 同时clip-path属性是CSS新特性中兼容性非常好的几个CSS属性之一，iOS 7和Android 4.4这些非常古老的手机系统都支持clip-path属性，因此在移动端项目中可以放心使用clip-path属性。

```css
/* 关键字属性值 */
clip-path: none;

/* <clip-source> 值类型 资源剪裁 */ 
clip-path: url(resources.svg#someId);

/* <geometry-box> 值类型 盒子剪裁 */
clip-path: margin-box;
clip-path: border-box;
clip-path: padding-box;
clip-path: content-box;
clip-path: fill-box;
clip-path: stroke-box;
clip-path: view-box;

/* <basic-shape> 值类型 基本图形剪裁 */
clip-path: inset(100px 50px);
clip-path: circle(50px at 0 100px);
clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);
clip-path: path('M0.5,1 C0.5,1,0,0.7,0,0.3 A0.25,0.25,1,1,1,0.5,0.3 
A0.25,0.25,1,1,1,1,0.3 C1,0.7,0.5,1,0.5,1 Z');

```

（1）对于“资源剪裁”，Chrome浏览器仅支持使用内联SVG元素进行剪裁，不支持引用外链SVG元素进行剪裁

（2）对于“盒子剪裁”，所有值都不被Chrome浏览器支持。

#### **资源裁剪**

[SVG资源裁剪效果](https://demo.cssworld.cn/new/12/2-1.php)

[使用ClipPath Sprites技术实现的小图标效果](https://demo.cssworld.cn/new/12/2-2.php)

> 那就是内联的SVG元素不能使用display:none或者visibility:hidden进行隐藏，否则剪裁元素会被隐藏，并且此技术只适合填充模式的小图标，不适合描边小图标。

#### **盒子裁剪**

> 盒子剪裁只需要关心margin-box、border-box、padding-box和content-box这几个盒子类型即可。fill-box、stroke-box和view-box这3个盒子类型需要和SVG元素配合使用，在实际开发中用到的概率较小

[clip-path \<geometry-box>值对比实例页面](https://demo.cssworld.cn/new/12/2-3.php)

#### **基本图形剪裁**

> 基本图形剪裁是clip-path属性高频使用的一种剪裁方式，其可以实现剪裁效果的基本形状函数包括inset()、circle()、ellipse()、polygon()和path()。

- inset()函数

	语法与 clip 的 rect()函数类似，但是更强大.剪切便宜支持四个方向，还支持四个反向圆角 %值等

	```css
	inset(<length-percentage>{1,4} round <'border-radius'>);
	
	eg:
	/* 偏移大小15%，圆角大小10% 50% 10% 50%*/
	clip-path: inset(15% round 10% 50% 10% 50%);
	```

- circle()函数

	```css
	circle( [ <shape-radius> ]? [ at <position> ]? )
	
	eg:
	clip-path: circle(40% at right 10% bottom 10%);
	```

- ellipse()函数

	> 剪裁椭圆形状,与circle相比多了一个半径参数

	

	```css
	ellipse( [ <shape-radius>{2} ]? [ at <position> ]? )
	
	eg:
	img {
	    clip-path: ellipse(30% 50% at 75% 50%);
	}
	```

- polygon()函数

	> 使用频率最多，语法简单功能强大，可以有很多衍生的应用。

	```css
	polygon( <fill-rule>? , [ x, y ]# )
	```

	[实现渐变提示框](https://demo.cssworld.cn/new/12/2-4.php)

	实现双三角图标

	```css
	.double-triangle {
	    clip-path: polygon(5px 10px, 16px 3px, 16px 10px, 26px 10px, 26px 3px, 37px 10px, 
	26px 17px, 26px 10px, 16px 10px, 16px 17px)
	}
	```

	

- path()函数

	> 可以剪裁出任意图形效果，是一个功能非常强大的剪裁函数，其他所有基本形状函数都可以使用path()函数表示

	[实现图标变化效果](https://demo.cssworld.cn/new/12/2-5.php)

	- ##### HTML：

		```html
		<button class="icon-arrow"></button>
		```

	- ##### CSS：

		```css
		.icon-arrow {
		    width: 32px; height: 32px;
		    background: linear-gradient(45deg, deepskyblue, deeppink);
		    clip-path: path("M16.016 1.157l-15.015 15.015h9.009v16.016h12.012v-16.016h9.009z");
		    transition: .2s;
		}
		.icon-arrow:active {
		    clip-path: path("M16.016 31.187l15.015-15.015h-9.009v-16.016h-12.012v16.016h-9.009z");
		}
		```

	> （1）剪裁效果发生的时候，元素原始的位置是保留的，不会发生布局上的变化，这为clip-path属性在图形动态效果领域大显身手打下了技术基础。
	>
	> （2）被剪裁的区域不能响应点击行为，也不能响应:hover伪类和:active伪类。这一点和mask属性不同，元素应用mask属性遮罩效果后，透明的部分依然是可以点击的。
	>
	> （3）clip-path属性的几个基本图形函数都是支持动画效果的，但是需要关键坐标点的数量在动画前后保持一致。对于path()函数，还需要路径的指令保持一致才会有动画效果。

#### 了解nonzero和evenodd填充规则

> clip-path属性中的polygon()函数支持一个名为\<fill-rule>的数据类型，表示填充规则。
>
> 无论是SVG、Canvas还是CSS，只要涉及路径填充，都离不开填充规则，并且用来表示填充规则的参数名也都是完全一致的，即nonzero和evenodd。换句话说，弄懂了nonzero和evenodd这两个填充规则，不仅在CSS这门语言中受用，在SVG、Canvas和其他与路径填充相关的程序开发语言中都是受用的。

#### 差异

如果填充对象是一个三角形，则这两种填充规则没什么区别，如果填充对象是两个三角形，并且两者重叠，差异就出现了

![image-20221110120126561](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20221110120126561.png)

#### 一切都是交叉点的选择

> 填充规则的关键，就是确定复杂路径构成的图形的内部和外部。内部则填充，外部则透明。
>
> 顾名思义，“nonzero规则”就是“非零规则”，用通俗的话讲，就是计算某些值是不是0，如果不是0则表示内部，表现为填充；如果是0则表示外部，表现为不填充。“evenodd规则”就是“奇偶规则”，用通俗的话讲，就是计算某些值是不是奇数，如果是奇数则表示内部，表现为填充；如果是偶数则表示外部，表现为不填充。



#### clip-path属性的精彩应用示例

[clip-path实现全新过场交互效果](clip-path实现全新过场交互效果)

[渐变提示框效果](https://demo.cssworld.cn/new/12/2-7.php)



### -webkit-box-reflect属性与倒影效果的实现

> -webkit-box-reflect还是非标准的CSS属性，所以Firefox浏览器一直没有对-webkit-box-reflect属性进行支持，但是，Firefox浏览器有其他特性可以实现部分倒影效果。

除了方位可以指定，倒影的偏移大小和倒影的遮罩图像都是可以设置的，-webkit-box- reflect属性完整的语法如下：

```css
-webkit-box-reflect: [ above | below | right | left ]? 
<length>? <image>?
```

● 方位：可以是above、below、left和right这4个值中的任意一个，分别表示在上、下、左、右进行倒影。

● 偏移大小：表示倒影和原始元素的偏移距离，可以是数值，也可以是百分比值。如果是百分比值，则百分比大小是相对于元素自身尺寸计算的，与transform属性中translate()函数的百分比计算规则是一致的。

● 遮罩图像：可以实现对元素倒影的遮罩控制，支持url()函数图像、渐变图像等。

[效果展示](https://demo.cssworld.cn/new/12/3-1.php)

**小结：**

（1）倒影和outline、box-shadow属性一样，其不占据尺寸空间，同时倒影也无法响应点击事件。

（2）如果倒影的偏移值是使用百分比值设置的，那么这个百分比值对应的尺寸计算方位是根据倒影方向自动识别的。例如倒影方向是below或above，则偏移百分比值是根据原始元素的高度计算的；如果倒影方向是left或right，则偏移百分比值是根据元素的宽度来计算的。

（3）遮罩图像可以使用任意的CSS渐变语法，包括锥形渐变，不过这里的遮罩图像一次最多只能设置一张图像。

（4）使用遮罩图像的时候，倒影的偏移值是不能缺省的。如果没有偏移，请使用0占位

（5）倒影效果具有实时渲染特性，也就是说，如果我们对原始图像进行剪裁，倒影也会被剪裁。

（6）遮罩图像是没有跨域限制的，而mask-image属性使用的遮罩图像是有跨域限制的。

#### Firefox浏览器实现投影效果的解决方案

> 虽然Firefox浏览器不支持-webkit-box-reflect属性，却可以使用element()图像函数实现近似的投影效果。

[效果展示](https://demo.cssworld.cn/new/12/3-2.php)

这一效果的实现原理如下。

● 使用伪元素模拟倒影效果，不占据多余的HTML元素，也无法被机器识别，和真实倒影性质接近。

● 倒影元素设置为绝对定位，这样大多数场景下是不会影响布局的。设置pointer- events:none无视所有点击事情，模拟倒影元素的穿透性。

● 最终的一步就是倒影元素一定要使用element()函数作为背景图出现，这样才是一个和原始元素效果实时同步的倒影效果。

● 如果有遮罩效果，可以使用mask属性实现，Firefox浏览器对mask属性的支持度很高，大家放心使用。

> 上面案例中的倒影并不会随着原始图像同步变化，因为element()函数只能实时同步内容的变化。不过，要解决这个问题也很简单，在\<img>外面再套一层标签，把父元素作为倒影图像即可，这是因为此时\<img>元素属于倒影元素的子元素，变成了内容的一部分



### 　使用offset属性实现元素的不规则运动

> 要让一个元素按照不规则路径进行运动，在过去实现成本比较低、兼容性也比较好的方法是使用“SVG SMIL animation”，现代浏览器均提供支持。[《超级强大的SVG SMIL animation动画详解》](https://www.zhangxinxu.com/wordpress/2014/08/ so-powerful-svg-smil-animation/)进行了解



[路径运动实例](https://demo.cssworld.cn/new/12/4-1.php)

- ##### HTML：

	```html
	<img src="horse.png" class="horse-run">
	<svg width="280" height="150" viewBox="0 0 280 150">
	    <path d="M10,80 q100,120 120,20 q140,-50 160,0" stroke="#cd0000" stroke-width="2" fill="none" />
	</svg>
	```

- ##### CSS：

	```css
	.horse-run {
	    position: absolute;
	    offset-path: path("M10,80 q100,120 120,20 q140,-50 160,0");
	    animation: motion 3s linear infinite;
	}
	@keyframes motion {
	    100% { offset-distance: 100%;}
	}
	```

> 但是offset属性有一个缺点，那就是Safari浏览器并不提供对它的支持

offset`属性是多个CSS属性的缩写，相关属性包括：

- offset-anchor；

	> 用来确定偏移运动的锚点，也就是确定元素中沿着轨迹运动的点。支持百分比，固定长度。

	

- offset-distance；

	> 表示偏移的距离大小，也就是元素沿着路径移动的距离，支持百分比值和长度值
	>
	> 如果路径是封闭的，则无论offset-distance有多大的值，都可以看到位置的变化；如果路径是开放的，则负值的位置和0%的位置是一样的，大于100%的值的位置和100%的位置是一样的。

	如果移动路径是闭合路径，那么就会形成循环动画[三龙戏珠](https://demo.cssworld.cn/new/12/4-3.php)

- offset-path；

	> 指的是运动的路径，支持多种路径类型，但从语法而言，它和clip-path属性有众多相似之处。

	```css
	offset-path: none;
	offset-path: ray( [ <angle> && <size> && contain? ] );
	offset-path: <path()>;
	offset-path: <url>;
	offset-path: [ <basic-shape> || <geometry-box> ];
	```

	其中\<url>数据类型可以直接使用页面内联SVG元素中任意图形元素的路径，\<basic-shape>则包括inset、circle、ellipse、polygon等基本图形函数。

	咋一看很强大，但是只有path兼容性好，如ray()函数【射线状的偏移】，根本没有浏览器支持

- offset-position；

	> 定义路径的起始点
	>
	> ```css
	> offset-position: auto | <position>
	> ```
	>
	> - auto是初始值，表示偏移路径的起始点是元素正常的位置。
	> - \<position>用来指定偏移路径起始点的位置。
	>
	> 
	>
	> offset-position属性有3个特点：**语法简单、细节繁多、支持较差。**

	**细节：**

	- 如果元素的position属性的计算值是static，则offset-position属性是无效的。
	- 如果offset-path的属性值包含\<geometry-box>数据类型（margin-box、padding- box、border-box），或者使用了基本形状函数［circle()和ellipse()除外］，则offset-position属性也是无效的。
	- offset-position的属性值不是auto的时候会创建新的层叠上下文和包含块，类似于transform属性。
	- offset-position设置具体的定位值的表现和绝对定位是极度相似的，区别就在于绝对定位是脱离文档流的，位置的变化不会影响兄弟元素，但是offset-position属性产生的位移依然在当前布局中。
	- offset-position产生的位置偏移是相对于包含块区域计算的，而不是相对于自身，这一点和offset-anchor属性不同。

- offset-rotate。

	> 用来定义元素沿着offset-path路径运动时的方向和角度

	```css
	offset-rotate: [ auto | reverse ] || <angle>
	```

	- auto是初始值，表示元素沿着垂直于路径切线的方向运动。
	- \<angle>表示元素按照指定的角度运动，例如设置offset-rotate: 30deg后，则元素会一直保持相对于元素正常状态旋转30°的角度进行运动
	- auto \<angle>这种语法并不多见，却是一个非常巧妙的设计。auto \<angle>表示元素沿着垂直于路径切线的方向运动，但是元素自身需要增加一点旋转角度。auto \<angle>参数很实用。
	- reverse的含义和auto类似，区别在于方向是反的，等同于设置了auto 180deg。例如，设置offset-rotate: reverse后，元素会旋转180°，然后继续沿着路径切线角度运动



## 图片等多媒体的处理

### 　图片和视频元素的内在尺寸控制

> 为保持图片比例，不被拉伸变形。开发者往往利用具有内部尺寸的元素在没有指定高宽的情况下依然保持比例的特性，仅设置宽度值，或者仅设置高度值，让图片既有自适应的特性，又能保持比例。
>
> 但是首次加载图片，若图片没有加载出来，浏览器就会内在尺寸是0，此时图片占据高度0，图片加载成功后，高度突然变高，视觉上出现高度变化变现为内容跳动出现，图片会不断触发重绘，不仅性能不佳，用户体验也不好。

#### object-fit属性

> ```css
> object-fit: fill | contain | cover | none | scale-down
> ```
>
> contain和cover这两个属性值，因为两者几乎占据了全部的object-fit属性使用场景。

1. fill

	fill是默认值，表示“填充”，替换内容会拉伸，填满整个content-box的尺寸, 不保证保持原有的比例。

2. contain

	表示“包含”，替换内容保持原有尺寸比例，同时替换内容一定可以在content-box中完整显示，至少一个方向的尺寸和content-box保持一致。此关键字属性值可能会让content-box出现留白

3. cover

	表示“覆盖”，替换内容同样会保持原始的尺寸比例，同时替换内容会完全覆盖content-box区域，至少一个方向的尺寸和content-box保持一致。此关键字可能会让替换内容的部分区域不可见

4. none

	none表示替换内容的尺寸显示为原始的尺寸，无视外部的尺寸设置。如果图片尺寸较小，就会在四周产生大量留白；如果图片尺寸较大，则会有较大面积的图片区域被剪裁。在实际开发中，此关键字很少被使用。

5. scale-down

	样式表现就好像依次设置了none和contain关键字，然后选取呈现的尺寸较小的那个效果

> object-fit属性在现代浏览器中的兼容性非常好，Android 4.4.4就开始支持了，在移动端可以放心使用。



### object-position属性的作用规则

> 用于控制替换内容的位置。object- position的初始值是50% 50%，也就是说默认是居中效果。所以，无论object-fit的值是哪一个关键字属性值，图片都是水平、垂直居中的。
>
> 因此，要实现尺寸大小不固定图片的水平、垂直居中效果，可以试试先将\<img>元素的宽度设置为容器的宽度大小，然后设置object-fit:none。

[效果示例]()

> 在实际开发中，通常缩略图使用cover关键字，列表图使用contain关键字，全屏大图预览使用scale-down关键字

让替换元素在相对于content-box区域右下角20px 10px的地方定位：

```css
object-position: right 20px bottom 10px;
```



**Img Sprites技术**

> 过去使用background-position定位实现的CSS Sprites技术和配合animation属性实现的无损Gif模拟技巧，现在都可以使用object-position属性实现，我称之为“Img Sprites技术”。

[object-position属性img元素类gif效果](https://demo.cssworld.cn/new/10/1-3.php)

- ##### CSS：

	```css
	.love {
	    width: 100px; height: 100px;
	    object-fit: cover;
	    animation: heart-burst steps(28) .8s infinite both;
	}
	@keyframes heart-burst {
	  0% {
	    object-position: 0%;
	  }
	  100% {
	    object-position: 100%;
	  }
	}
	```

#### 使用image-orientation属性纠正图片的方向

> 当图片方向不正确时，纠正图片方向

```css
image-orientation: from-image | none;
```

- from-image为初始值，表示如果图片包含旋转信息，则自动旋转图片。
- 如果照片没有Exif信息，则表现为none关键字属性值。none表示无论照片是否包含Exif信息，都不进行旋转。

[image-orientation属性基本效果实](https://demo.cssworld.cn/new/10/2-1.php)



#### image-rendering属性与图像的渲染

> image-rendering属性用来设置图像的缩放算法，主要针对PNG和JPG这类位图。image-rendering属性可以设置在\<img>元素上，也可以设置在\<img>元素的祖先元素上。
>
> 在现代浏览器中，image-rendering属性还可以设置background图像和canvas画布图像的缩放算法。image-rendering属性只有在图像发生缩放的时候才会有效果。

```css
image-rendering: auto | crisp-edges | pixelated
```

- auto表示浏览器自动选择使用何种图像缩放算法，通常表现为平滑缩放。
- crisp-edges表示不使用平滑缩放算法，因此，缩放的图像会有较高的对比度和较锐利的边缘，也不会有模糊的感觉。常用的算法包括邻近算法和其他像素艺术算法，如2×SaI和hqx系列算法。
- pixelated表示当放大图像时，必须使用邻近算法，使图像看起来由大像素块组成；当缩小图像时，使用与auto关键字属性值相同的算法。

由于历史原因，各个浏览器用来控制图像算法的属性和属性值并不完全一致。

- IE浏览器使用非标准的声明-ms-interpolation-mode: nearest-neighbor。
- Firefox浏览器支持关键字属性值crisp-edges，但是并不支持pixelated。
- Chrome浏览器则支持关键字属性值pixelated，但并不支持crisp-edges关键字属性值，取而代之的关键字属性值是私有的-webkit-optimize-contrast。

[效果展示](https://demo.cssworld.cn/new/10/3-1.php)

> 在常规的场景中，图像采用平滑效果肯定是更好的，因此是用不到image-rendering属性的。但是，如果网页的设计风格是像素化风格或者锐化风格（例如一些像素风格的游戏介绍页面）



### 　不常用的图像类型函数

#### 实现图像半透明叠加的cross-fade()函数

> cross-fade()函数可以让两张图像半透明混合[效果展示](https://demo.cssworld.cn/new/10/4-1.php )
>
> ```css
> <image-combination> = cross-fade( <image>, <image>, <percentage> )
> ```
>
> 

```css
.cross-fade-image {
    width: 300px; height: 300px;
    background: no-repeat center / contain;
    background-image: url(1.jpg), url(2.jpg);
    background-image: cross-fade(url(1.jpg), url(2.jpg), 50%);   
}
```

**新语法，支持任意数量透明叠加，但是没有浏览器支持**

```css
cross-fade(url(red.png) 20%, url(yellow.png) 30%, url(blue.png) 50%);
```

本来可以用作水印添加，但是透明png图似乎效果更好，兼容性更好。于是它可以

[cross-fade()函数降低背景图像透明度](https://demo.cssworld.cn/new/10/4-2.php)



#### 神奇的element()函数

> CSS中的element()函数是一个非常神奇的函数，它可以让页面中任意DOM元素的渲染效果变成图像。

只有Firefox浏览器支持element()函数，chrome 不支持



## 　更绚丽的视觉表现



### 深入了解CSS滤镜属性

**filter**

> filter属性总共支持10个滤镜函数,所有filter属性支持的滤镜函数都支持animation动画效果

| 滤镜                                 |   释义   |
| ------------------------------------ | :------: |
| filter:blur(5px)                     |   模糊   |
| filter:brightness(2.4)               |   亮度   |
| filter:contrast(200%)                |  对比度  |
| filter:drop-shadow(4px 4px 8px blue) |   投影   |
| filter:grayscale(50%)                |   灰度   |
| filter:hue-rotate(90deg)             | 色调旋转 |
| filter:invert(75%)                   |   反相   |
| filter:opacity(25%)                  |  透明度  |
| filter:saturate(230%)                |  饱和度  |
| filter:sepia(60%)                    |   褐色   |

#### 模糊滤镜函数blur()

> 使用blur()函数可以让元素或者图像产生高斯模糊的效果

使用blur()函数后图像边缘泛白效果

解决：

```css
.box img {
    transform: scale(1.1);
    filter: blur(5px);
}
```

[径向模糊局部模糊效果](https://demo.cssworld.cn/new/11/1-1.php)

#### 亮度滤镜函数brightness()

> 可以用来调节元素的亮度.
>
> brightness()函数的参数值支持数值和百分比值，范围是0到无穷大。参数值0或0%表示纯黑色，参数值1或100%表示正常的亮度，0～1或0%～100%的亮度是线性变化的。随着参数值逐渐大于1或大于100%，元素的亮度也会逐渐提升。
>
> 值为空则使用参数1

[效果展示](https://demo.cssworld.cn/new/11/1-2.php#)



#### 对比度滤镜函数contrast()

> 可以用来调节元素的对比度.
>
> contrast()函数的**参数值支持数值和百分比值**，范围是0到无穷大。参数值0或0%表示毫无对比度，表现为纯灰色，色值是#808080，使用RGB色值表示为rgb(128,128,128)，也就是gray颜色关键字对应的色值。注意，这里说的是纯灰色，图像会直接变成一个灰色色块，而不是图像灰度。参数值1或100%表示正常的对比度。随着参数值逐渐大于1，元素的对比度也会逐渐提升。
>
> 值为空则使用参数1

#### 投影滤镜函数drop-shadow()

> 可以给元素设置符合真实世界阴影规则的投影效果
>
> 从语法上来看，drop-shadow()函数最多只支持3个数值，而box-shadow属性最多支持4个数值，其中第四个数值表示扩展。
>
> drop-shadow()函数没有内投影效果，而box-shadow属性可以使用inset关键字实现内阴影效果。
>
> drop-shadow()函数不支持投影叠加，box-shadow属性则允许无限累加投影

使用drop-shadow()函数实现的投影是**[符合真实世界表现的投影](https://demo.cssworld.cn/new/11/1-3.php)**，凡是透明镂空的地方，一定会留下相应的阴影轮廓。相对的，box-shadow属性实现的是盒阴影，只会在方方正正的盒子的四周留下阴影效果，无论是镂空还是凸出的图形都不会有阴影效果。

#### 灰度滤镜函数grayscale()

> 可以实现元素的去色效果，让所有彩色值变成灰度值。
>
> ```css
> img {
>     filter: grayscale(70%); /* 70% 等同于 0.7 */
> }
> ```
>
> 0或0%表示正常的图像表现。在0～1或0%～100%范围区间的灰度是线性变化的。

grayscale()函数比较经典的应用是在特殊的节日让网页变灰，这个效果只需一行CSS代码即可实现：

```css
body {
    filter: grayscale(1);
}
```



#### 色调旋转滤镜函数hue-rotate()

> 可以调整元素的色调，但饱和度和亮度保持不变
>
> ```css
> filter: hue-rotate(90deg);
> ```
>
> hue-rotate()函数的参数值支持角度值，例如90deg或0.5turn等，角度值的范围没有限制，每360度就是一个循环。

[实现7彩文字效果](https://demo.cssworld.cn/new/11/1-4.php)



#### 反相滤镜函数invert()

> invert()函数可以让元素的亮度和色调同时反转
>
> invert()函数的参数值支持数值和百分比值，范围是0到无穷大。参数值为1或100%的时候图像表现为完全反相。单纯从语法上来说，值可以大于1或者大于100%，但是效果不会再进一步变化。0或0%表示正常的图像表现。

```css
img {
	filter: invert(75%);/*0.75*/
}
```

invert()函数可以和hue-rotate()函数一起使用，实现反转元素亮度的效果，下面这段CSS代码在实现深色模式效果的时候很实用：

```css
filter: invert(1) hue-rotate(180deg);
```



#### 透明度滤镜函数opacity()

> opacity()函数可以改变元素的透明度，效果和opacity属性类似
>
> opacity()函数的参数值支持数值和百分比值，范围是0到无穷大。参数值为0或0%的时候图像表现为完全透明；参数值为1或100%或者更大的值时，图像均是正常表现。
>
> opacity()函数和opacity属性的区别在于，在部分浏览器中，使用opacity()函数可以启用硬件加速，性能会更好。不过由于opacity属性本身性能就非常好，因此没有任何必要使用opacity()函数。另外，同时使用opacity()函数和opacity属性的时候，元素的透明度效果会互相累加，也就是最终渲染图像的透明度会进一步降低。



#### 饱和度滤镜函数saturate()

> saturate()函数可以调整元素的饱和度
>
> saturate()函数的参数值支持数值和百分比值，范围是0到无穷大。参数值0或0%表示毫无饱和度，表现为灰度效果，等同于grayscale(1)；参数值1或100%表示正常的饱和度；随着参数值逐渐大于1，元素的饱和度也会逐渐提升。

```css
img {
    filter: saturate(230%);
}
```



#### 褐色滤镜函数sepia()

> sepia()函数可以让元素的视觉效果向褐色靠拢
>
> sepia()函数的参数值支持数值和百分比值，范围是0到无穷大。当参数值为1或100%，或者大于1或100%时，图像均表现为深褐色；当参数值为0或0%时，图像还是原始效果。

**sepia()函数在日常开发中使用不多，主要是用来实现老照片效果。**



### 更进一步的滤镜技术

#### [实现元素融合效果的技术](https://demo.cssworld.cn/new/11/1-5.php)

> 就是同时使用模糊滤镜函数和对比度滤镜函数

```css
.container {
    filter: blur(10px) contrast(5);
}
```

#### 引用SVG滤镜技术

**融合粘滞效果**

[filter SVG滤镜融合粘滞效果实](https://demo.cssworld.cn/new/11/1-6.php)

[filter SVG滤镜水波荡漾的效果实](https://demo.cssworld.cn/new/11/1-7.php)



#### 姐妹花滤镜属性backdrop-filter

> CSS背景滤镜属性backdrop-filter是一个人见人爱的属性，它可以非常轻松地实现毛玻璃效果等美观的滤镜特效。
>
> backdrop-filter属性的学习成本极低，因为它和filter属性的语法几乎是一模一样的。
>
> backdrop-filter属性和filter属性的区别在于backdrop-filter属性是让当前元素所在区域后面的内容应用滤镜效果，要想看到滤镜效果，需要当前元素本身是半透明或者完全透明的；而filter属性是让当前元素自身应用滤镜效果。

[backdrop-filter和filter属性效果对比示意实](https://demo.cssworld.cn/new/11/2-1.php)

#### backdrop-filter属性与毛玻璃效果

> 毛玻璃效果必须要设置元素透明度

[弹框毛玻璃效果实](https://demo.cssworld.cn/new/11/2-2.php)

[下拉框毛玻璃](https://demo.cssworld.cn/new/11/2-3.php)

> **对于backdrop-filter属性，请勿使用opacity()函数作为属性值。**



### 深入了解CSS混合模式

> CSS有下面3个混合模式相关属性：
>
> background-blend-mode 属性用于混合元素背景图案、渐变和颜色；
>
> mix-blend-mode 属性用于元素与元素之间的混合；
>
> isolation 属性用在祖先元素上，限制mix-blend-mode属性设置的混合模式的应用范围。
>
> 

| 混合模式类型 |   释义   |
| ------------ | :------: |
| normal       |   正常   |
| multiply     | 正片叠底 |
| screen       |   滤色   |
| overlay      |   叠加   |
| darken       |   变暗   |
| lighten      |   变亮   |
| color-dodge  | 颜色变淡 |
| color-burn   | 颜色加深 |
| hard-light   |   强光   |
| soft-light   |   柔光   |
| difference   |   差值   |
| exclusion    |   排除   |
| hue          |   色调   |
| saturation   |  饱和度  |
| color        |   颜色   |
| luminosity   |   亮度   |





#### mix-blend-mode:multiply

> 值multiply的混合效果是正片叠底，最终效果表现的色值的计算公式是：
>
> ```js
> C = AB/255
> ```
>
> 例如，已知颜色关键字deepskyblue的RGB色值是**rgb(0,192,255)**，颜色关键字deeppink的RGB色值是**rgb(255,20,147)**，则这两种颜色进行正片叠底混合后的色值是**rgb(0,15,147)**

[浅色背景素材混合效果实例](https://demo.cssworld.cn/new/11/3-2.php)



#### mix-blend-mode:screen

> 值screen的**混合效果是滤色**，最终效果的色值的计算公式是：
>
> ```js
> C = 255 - [(255-A)(255-B)]/255
> ```
>
> 

screen的效果和multiply正好相反，multiply的效果是混合后颜色变暗，而screen则是混合后颜色变亮。因为滤色混合模式将两个混合颜色的互补色值相乘，然后除以255。

滤色模式具有以下一些直观的特性：

- 任何颜色和黑色进行滤色混合后，还是呈现原来的颜色；
- 任何颜色和白色进行滤色混合后得到的仍是白色；
- 任何颜色和其他颜色进行滤色混合后，颜色会更浅，有点类似漂白的效果。

[滤色与前景特效](https://demo.cssworld.cn/new/11/3-3.php)

> 无论是正片叠底、滤色，还是接下来要介绍的混合模式，都不仅仅适用于图像，**还适用于视频元素**。例如，我们希望网页中有烟花播放的动态效果，无须使用WebGL这么复杂的技术，直接准备一个烟花播放的视频，由于视频默认的背景色都是黑色的，因此，我们只需要设置视频的混合模式是screen，烟花播放动画就出现了



#### mix-blend-mode:overlay

> overlay的**混合效果是叠加**，最终效果的色值的计算公式所示（表示底图的色值）。

```js
// 当 A <= 128时：
C = AB/128

// 当 A > 128时：
C = 255 - [(255 - A)(255 - B)]/128
```

叠加效果在Web中主要有两个应用场景，一个是在图像上显示文字水印，另一个是着色叠加

[叠加模式效果](https://demo.cssworld.cn/new/11/3-4.php)



#### mix-blend-mode:darken

> **混合效果是变暗**，表示将两种颜色的RGB通道值依次进行比较，哪个色值小就使用哪个色值。最终效果的色值的计算公式是：

```js
C = min(A,B)
```



#### mix-blend-mode:lighten

> 值lighten的**混合效果是变亮**，表示将两个颜色的RGB通道值依次进行比较，哪个色值大就使用哪个色值。最终效果的色值的计算公式是：

```js
C = max(A,B)
```



#### mix-blend-mode:color-dodge

> color-dodge的**混合效果是颜色变淡**，最终效果的色值的计算公式是：

```css
C = A + AB/(255 - B)
```

> ，适合处理高光下的人物照片，通过将照片和特定颜色混合，可以改变整个照片的色调（暖色调或是冷色调），而不会影响人物高光区域的细节。



#### mix-blend-mode:color-burn

> color-burn的**混合效果是颜色加深**，最终效果的色值的计算公式是：

```js
C = A - [(255 - A)(255 - B)]/B
```

> color-burn颜色加深混合模式可以用来保护底图的阴影，适合处理“幽深秘境”一类的照片，通过将照片和特定颜色混合，可以营造更加幽深的氛围。



#### mix-blend-mode:hard-light

> hard-light的**混合效果是强光**，就好像耀眼的聚光灯照射过来，表现为图像**亮的地方更亮，暗的地方更暗**。最终效果的色值的计算公式如下所示：

```js 
// B <= 128 时
C = AB/128

// 当B > 128 时
C = 255 - [(255 - A)(255 - B)]/128
```



#### mix-blend-mode:soft-light

> soft-light的混合效果是柔光，就好像发散的光源四处弥漫，它的表现效果和hard-light有类似之处，只是表现没有hard-light那么强烈。

```js
// 当B <= 128 时
C - AB/128 + (A/255)(A/255)(255 - 2 - B)

// 当B > 128 时
C = 255 - [(255 - A)(255 - B)]/128
```



[soft-light和hard-light混合模式效果对比](https://demo.cssworld.cn/new/11/3-6.php)



#### mix-blend-mode:difference

> difference的混合效果是差值，最终颜色的色值是用较浅颜色的色值减去较深颜色的色值的结果，计算公式是：

```js
C = |A - B|
```

[difference显示不同文字颜色实例](https://demo.cssworld.cn/new/11/3-7.php)



#### mix-blend-mode:exclusion

> 值exclusion的混合效果是排除，最终的混合效果和difference模式是类似的，区别在于exclusion的对比度要更低一些。最终效果的色值的计算公式是：

```js
C = A + B - AB/128
```

[difference和exclusion混合模式效果对比](https://demo.cssworld.cn/new/11/3-8.php)

**4种都属于颜色系混合模式。**

#### mix-blend-mode:hue

> 值hue表示色调混合，作用是将颜色混合，使用底层元素的亮度和饱和度，以及上层元素的色调
>
> 需要注意的是，所有颜色系混合模式（色调、饱和度、颜色和亮度）都不要使用黑色进行混合，因为这样会挖掉底层元素的颜色信息，导致最终混合后的颜色是灰色



#### mix-blend-mode:saturation

> 值saturation表示饱和度混合，混合后的颜色保留底图的亮度和色调，并使用顶图的饱和度。



#### mix-blend-mode:color

> 值color表示颜色混合，混合后的颜色保留底图的亮度，并使用顶图的色调和饱和度。

同样，颜色混合模式也可以通过使用CSS渐变让照片的色调变得丰富起来

[颜色系混合模式和色盘混合效果](https://demo.cssworld.cn/new/11/3-9.php)



#### mix-blend-mode:luminosity

> 值luminosity表示亮度混合，混合后的颜色保留底图的色调和饱和度，并使用顶图的亮度，效果和color模式正好是相反的



[mix-blend-mode各个混合模式值效果实例](https://demo.cssworld.cn/new/11/3-1.php)



### 滤镜和混合模式的化合反应

#### 使用白天素材模拟夜晚

```css
night {
    width: 256px; height: 256px;
    background: rgba(0,40,140,.6) url(./house-bed.jpg);
    background-size: 100%;
    background-blend-mode: darken;
    filter: brightness(80%) grayscale(20%) contrast(1.2);
}
```

#### 照片美化处理

```css
figure class="_1977">
  <img src="10.jpg">
</figure>
._1977 {
    position: relative;
    /* 应用滤镜 */
    filter: contrast(1.1) brightness(1.1) saturate(1.3);
}
._1977:after {
    content: '';
    height: 100%; width: 100%;
    position: absolute;
    left: 0; top: 0;
    pointer-events: none;
    background: rgba(243,106,188,.3);
    /* 应用混合模式 */
    mix-blend-mode: screen;
}
```

#### 照片风格处理

```css
<div class="sketch"></div>
.sketch {
    width: 256px; height: 171px;
    background: url(10.jpg) -2px -2px, url(10.jpg);
    background-size: 258px 173px;
    background-blend-mode: difference;
    filter: brightness(3) invert(1) grayscale(1);
}
```

[滤镜和混合模式一起使用实例页面](https://demo.cssworld.cn/new/11/3-10.php)



#### 混合模式属性background-blend-mode

> background-blend-mode属性可以在各个背景图像之间应用混合模式。background-blend-mode属性的使用频率要明显低于mix-blend- mode属性，原因有以下两个。
>
> - 真实世界的照片很少作为background-image背景图像呈现，因为不利于无障碍访问，而混合模式设计的初衷就是处理这类照片。
> - background-blend-mode属性的作用机制不像mix-blend-mode属性那么单纯，很多开发者并不能很好地驾驭它。例如使用混合模式让透明背景的小图标变成渐变图标，很多人会使用mix-blend-mode属性实现，但是能够使用background-blend-mode属性实现的人寥寥无几。

[background-blend-mode实现的纹理背景效果](background-blend-mode实现的纹理背景效果)



#### 　使用isolation: isolate声明隔离混合模式

> isolation: isolate声明的作用很单纯，就是用来隔离混合模式，限制混合模式的作用范围。

- auto是默认值，表示混合模式隔离与否根据具体情况而定。
- isolate表示对混合模式进行隔离。

> isolation:isolate声明之所以可以隔离混合模式，本质上是因为isolation: isolate创建了一个新的层叠上下文（stacking context）（有对层叠上下文不太了解的读者可以参阅《CSS世界》第214页第7章相关内容）。isolation:isolate本身并没有做什么特殊的事情，或者我可以这么大胆地说：“isolation:isolate除了创建层叠上下文，没有任何其他作用！”