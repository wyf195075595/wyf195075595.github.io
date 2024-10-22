---
title: SVG
date: 2022-06-17 08:23:10
tags: js
categories: js
---

```js
<?xml version="1.0" standalone="no"?>
<svg width="200" height="250" version="1.1" xmlns="http://www.w3.org/2000/svg">

  <rect x="10" y="10" width="30" height="30" stroke="black" fill="transparent" stroke-width="5"/>
  <rect x="60" y="10" rx="10" ry="10" width="30" height="30" stroke="black" fill="transparent" stroke-width="5"/>

  <circle cx="25" cy="75" r="20" stroke="red" fill="transparent" stroke-width="5"/>
  <ellipse cx="75" cy="75" rx="20" ry="5" stroke="red" fill="transparent" stroke-width="5"/>

  <line x1="10" x2="50" y1="110" y2="150" stroke="orange" fill="transparent" stroke-width="5"/>
  <polyline points="60 110 65 120 70 115 75 130 80 125 85 140 90 135 95 150 100 145"
      stroke="orange" fill="transparent" stroke-width="5"/>

  <polygon points="50 160 55 180 70 180 60 190 65 205 50 195 35 205 40 190 30 180 45 180"
      stroke="green" fill="transparent" stroke-width="5"/>

  <path d="M20,230 Q40,205 50,230 T90,230" fill="none" stroke="blue" stroke-width="5"/>
</svg>
```

### 矩形

<!--more-->

> `rect`元素会在屏幕上绘制一个矩形 。其实只要6个基本属性就可以控制它在屏幕上的位置和形状。

```html
<rect x="60" y="10" rx="10" ry="10" width="30" height="30"/>
```

x	矩形左上角的x位置

y	矩形左上角的y位置

width	矩形的宽度

height	矩形的高度

rx	圆角的x方位的半径

ry	圆角的y方位的半径



### 圆形

> `circle`元素会在屏幕上绘制一个圆形。它只有3个属性用来设置圆形。

```html
<circle cx="25" cy="75" r="20"/>
```

r	圆的半径

cx	圆心的x位置

cy	圆心的y位置



### 椭圆

> [Ellipse](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/ellipse) 是`circle`元素更通用的形式，你可以分别缩放圆的x半径和y半径（通常数学家称之为长轴半径和短轴半径）。

```html
<ellipse cx="75" cy="75" rx="20" ry="5"/>

```

rx	椭圆的x半径

ry	椭圆的y半径

cx	椭圆中心的x位置

cy	椭圆中心的y位置



### 线条

> [Line](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/line) 绘制直线。它取两个点的位置作为属性，指定这条线的起点和终点位置。

```html
<line x1="10" x2="50" y1="110" y2="150" stroke="black" stroke-width="5"/>
```

x1	起点的x位置

y1	起点的y位置

x2	终点的x位置

y2	终点的y位置



### 折线

> [Polyline](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/polyline)是一组连接在一起的直线。因为它可以有很多的点，折线的的所有点位置都放在一个points属性中：

```html
<polyline points="60, 110 65, 120 70, 115 75, 130 80, 125 85, 140 90, 135 95, 150 100, 145"/>

```

points

点集数列。每个数字用空白符、逗号、终止命令或者换行符分隔开。每个点必须包含2个数字，一个是x坐标，一个是y坐标。所以点列表 (0,0), (1,1) 和(2,2)可以写成这样：“0 0, 1 1, 2 2”。路径绘制完后闭合图形，所以最终的直线将从位置(2,2)连接到位置(0,0)。

### [多边形](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Basic_Shapes#多边形)

[`polygon`](https://developer.mozilla.org/zh-CN/docs/SVG/Element/polygon)和折线很像，它们都是由连接一组点集的直线构成。不同的是，`polygon`的路径在最后一个点处自动回到第一个点。需要注意的是，矩形也是一种多边形，如果需要更多灵活性的话，你也可以用多边形创建一个矩形。

xmlCopy to Clipboard

```html
<polygon points="50, 160 55, 180 70, 180 60, 190 65, 205 50, 195 35, 205 40, 190 30, 180 45, 180"/>
```

- [points](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Basic_Shapes#points_2)

	点集数列。每个数字用空白符、逗号、终止命令或者换行符分隔开。每个点必须包含 2 个数字，一个是 x 坐标，一个是 y 坐标。所以点列表 (0,0), (1,1) 和 (2,2) 可以写成这样：“0 0, 1 1, 2 2”。路径绘制完后闭合图形，所以最终的直线将从位置 (2,2) 连接到位置 (0,0)。

### 路径

> `path`可能是SVG中最常见的形状。你可以用path元素绘制矩形（直角矩形或者圆角矩形）、圆形、椭圆、折线形、多边形，以及一些其他的形状，例如贝塞尔曲线、2次曲线等曲线。

```html
<path d="M20,230 Q40,205 50,230 T90,230" fill="none" stroke="blue" stroke-width="5"/>
```

一个点集数列以及其它关于如何绘制路径的信息。

每一个命令都有两种表示方式，一种是用**大写字母**，表示采用绝对定位。另一种是用**小写字母**，表示采用相对定位

#### 直线命令

M x y		m dx dy		(M命令是移动画笔位置，但是不画线）

L x y 		 l  dx dy         (最常用的是“Line to” 命令，`L`)

H x 		   h dx				(绘制水平线)
V y 		   v dy				(绘制垂直线)

Z				z					(用于闭合路径，不区分大小写)

#### 曲线命令

贝塞尔曲线

1、三次贝塞尔曲线需要定义一个点和两个控制点，

用C命令创建三次贝塞尔曲线，需要设置三组坐标参数：

```js
C x1 y1, x2 y2, x y (or c dx1 dy1, dx2 dy2, dx dy)

<path d="M130 110 C 120 140, 180 140, 170 110" stroke="black" fill="transparent"/>

```

2、三次贝塞尔曲线命令S

​	你可以将若干个贝塞尔曲线连起来，从而创建出一条很长的平滑曲线。通常情况下，一个点某一侧的控制点是它另一侧的控制点的对称（以保持斜率不变）。这样，你可以使用一个简写的贝塞尔曲线命令S

![](D:\Typora\workSpace\resource\bsr3.png)

```js
 S x2 y2, x y (or s dx2 dy2, dx dy)
 
<path d="M10 80 C 40 10, 65 10, 95 80 S 150 150, 180 80" stroke="black" fill="transparent"/>
```

3、二次贝塞尔曲线Q命令

​	它比三次贝塞尔曲线简单，只需要一个控制点，用来确定起点和终点的曲线斜率。因此它需要两组参数，控制点和终点坐标。

```js
Q x1 y1, x y (or q dx1 dy1, dx dy)

<path d="M10 80 Q 95 10 180 80" stroke="black" fill="transparent"/>
```

4、二次贝塞尔曲线T命令

​	可以通过更简短的参数，延长二次贝塞尔曲线。T命令前面必须是一个Q命令，或者是另一个T命令，才能达到这种效果。如果T单独使用，那么控制点就会被认为和终点是同一个点，所以画出来的将是一条直线。

![](D:\Typora\workSpace\resource\bsr2.png)

```js
T x y (or t dx dy)

<path d="M10 80 Q 52.5 10, 95 80 T 180 80" stroke="black" fill="transparent"/>
```



​	虽然三次贝塞尔曲线拥有更大的自由度，但是两种曲线能达到的效果总是差不多的。具体使用哪种曲线，通常取决于需求，以及对曲线对称性的依赖程度。

#### 弧形命令

弧形命令A

​	

```js
 A rx ry x-axis-rotation large-arc-flag sweep-flag x y
 a rx ry x-axis-rotation large-arc-flag sweep-flag dx dy
 
  <path d="M80 80 A 45 45, 0, 0, 0, 125 125 L 125 80 Z" fill="green"/>
  <path d="M230 80 A 45 45, 0, 1, 0, 275 125 L 275 80 Z" fill="red"/>
  <path d="M80 230 A 45 45, 0, 0, 1, 125 275 L 125 230 Z" fill="purple"/>
  <path d="M230 230 A 45 45, 0, 1, 1, 275 275 L 275 230 Z" fill="blue"/>
 
 前两个参数分别是x轴半径和y轴半径
 三个参数表示弧形的旋转情况: x-axis-rotation（x轴旋转角度）
 large-arc-flag（角度大小） 和sweep-flag（弧线方向），large-arc-flag决定弧线是大于还是小于180度，0表示小角度弧，1表示大角度弧。sweep-flag表示弧线的方向，0表示从起点到终点沿逆时针画弧，1表示从起点到终点沿顺时针画弧。
最后两个参数是指定弧形的终点
```

​		用路径来绘制完整的圆或者椭圆是比较困难的，因为圆上的任意点都可以是起点同时也是终点，无数种方案可以选择，真正的路径无法定义。通过绘制连续的路径段落，也可以达到近似的效果，但使用真正的circle或者ellipse元素会更容易一些。

### 填充与边框

#### fill 与 stroke

`fill`属性设置对象内部的颜色，`stroke`属性设置绘制对象的线条的颜色。

```js
 <rect x="10" y="10" width="100" height="100" 
    stroke="blue" 
    fill="purple"
    fill-opacity="0.5" //控制填充色的不透明度
    stroke-opacity="0.8"//控制描边的不透明度
    stroke-width="20"   //描边的宽度（路径的每一侧都有均匀分布的描边）
    stroke-linecap="butt"//控制边框终点的形状
    stroke-linejoin="miter"//两条描边线段之间，用什么方式连接
    stroke-dasharray="5,5"//将虚线类型应用在描边上
/>
stroke-linecap属性的值有三种可能值：

    butt用直边结束线段，它是常规做法，线段边界90度垂直于描边的方向、贯穿它的终点。
    square的效果差不多，但是会稍微超出实际路径的范围，超出的大小由stroke-width控制。
    round表示边框的终点是圆角，圆角的半径也是由stroke-width控制的。

stroke-linejoin 两条描边线段之间，用什么方式连接

	miter是默认值，表示用方形画笔在连接处形成尖角。
    round表示用圆角连接，实现平滑效果。
    bevel，连接处会形成一个斜接。
        
stroke-dasharray
	
	数字必须用逗号分割（空格会被忽略）。每一组数字，
    第一个用来表示填色区域的长度
    第二个用来表示非填色区域的长度。

fill-rule:	用于定义如何给图形重叠的区域上色；
stroke-miterlimit:	定义什么情况下绘制或不绘制边框连接的miter效果；
stroke-dashoffset:	定义虚线开始的位置。

    注意，FireFox 3+支持rgba值，并且能够提供同样的效果，但是为了在其他浏览器中保持兼容，最好将它和填充/描边的不透明度分开使用。如果同时指定了rgba值和填充/描边不透明度，它们将都被调用。
```

### 使用css

> 除了定义对象的属性外，你也可以通过CSS来样式化`填充`和`描边`。语法和在html里使用CSS一样，只不过你要把`background-color`、`border`改成`fill`和`stroke`。注意，不是所有的属性都能用CSS来设置。上色和填充的部分一般是可以用CSS来设置的，比如`fill`，`stroke`，`stroke-dasharray`等，但是不包括下面会提到的渐变和图案等功能。另外，`width`、`height`，以及路径的命令等等，都不能用css设置。判断它们能不能用CSS设置还是比较容易的

```js
 <rect x="10" height="180" y="10" width="180" style="stroke: black; fill: red;"/>
 
     或者利用<style>设置一段样式段落。就像在html里这样的<style>一般放在<head>里，在svg里<style>则放在<defs>标签里。<defs>表示定义

<?xml version="1.0" standalone="no"?>
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg" version="1.1">
  <defs>
    <style type="text/css"><![CDATA[
       #MyRect {
         stroke: black;
         fill: red;
       }
    ]]></style>
  </defs>
	
	<?xml-stylesheet type="text/css" href="style.css"?>//引用外部样式表

  <rect x="10" height="180" y="10" width="180" id="MyRect"/>
</svg>
```

#### 渐变

> 并非只能简单填充颜色和描边，更令人兴奋的是，你还可以创建和并在填充和描边上应用渐变色。

1、线性渐变

```js


线性渐变沿着直线改变颜色，要插入一个线性渐变，你需要在SVG文件的`defs元素`内部，创建一个节点。

<svg width="120" height="240" version="1.1" xmlns="http://www.w3.org/2000/svg">
  <defs>
      <linearGradient id="Gradient1">
        <stop class="stop1" offset="0%"/>
        <stop class="stop2" offset="50%"/>
        <stop class="stop3" offset="100%"/>
      </linearGradient>
      <linearGradient id="Gradient2" x1="0" x2="0" y1="0" y2="1">
        <stop offset="0%" stop-color="red"/>
        <stop offset="50%" stop-color="black" stop-opacity="0"/>
        <stop offset="100%" stop-color="blue"/>
      </linearGradient>

      <style type="text/css">
          <![CDATA[
            #rect1 { fill: url(#Gradient1); }
            .stop1 { stop-color: red; }
            .stop2 { stop-color: black; stop-opacity: 0; }
            .stop3 { stop-color: blue; }
          ]]>
      </style>
  </defs>

  <rect id="rect1" x="10" y="10" rx="15" ry="15" width="100" height="100"/>
  <rect x="10" y="120" rx="15" ry="15" width="100" height="100" fill="url(#Gradient2)"/>

</svg>


注意: 你也可以在渐变上使用xlink:href属性。如果使用了该属性时，一个渐变的属性和颜色中值（stop）可以被另一个渐变包含引用。在下例中，你就不需要再Grandient2中重新创建全部的颜色中值（stop）。
 <linearGradient id="Gradient1">
   <stop id="stop1" offset="0%"/>
   <stop id="stop2" offset="50%"/>
   <stop id="stop3" offset="100%"/>
 </linearGradient>
 <linearGradient id="Gradient2" x1="0" x2="0" y1="0" y2="1"
    xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#Gradient1"/>
尽管通常你可能在文档的顶部就定义了Gradient1，但我在结点上直接包含了xlink的命名空间，关于这点的更多信息我们会在讨论图片的时候详解。
```

2、径向渐变

> 径向渐变与线性渐变相似，只是它是从一个点开始发散绘制渐变。创建径向渐变需要在文档的`defs`中添加一个[``](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/radialGradient)元素

```js
<?xml version="1.0" standalone="no"?>
<svg width="120" height="240" version="1.1" xmlns="http://www.w3.org/2000/svg">
  <defs>
      <radialGradient id="RadialGradient1">
        <stop offset="0%" stop-color="red"/>
        <stop offset="100%" stop-color="blue"/>
      </radialGradient>
      <radialGradient id="RadialGradient2" cx="0.25" cy="0.25" r="0.25">
        <stop offset="0%" stop-color="red"/>
        <stop offset="100%" stop-color="blue"/>
      </radialGradient>
  </defs>

  <rect x="10" y="10" rx="15" ry="15" width="100" height="100" fill="url(#RadialGradient1)"/>
  <rect x="10" y="120" rx="15" ry="15" width="100" height="100" fill="url(#RadialGradient2)"/>

</svg>

一个中心点，由cx和cy属性及半径r来定义，通过设置这些点我们可以移动渐变范围并改变它的大小
第二个点被称为焦点，由fx和fy属性定义。第一个点描述了渐变边缘位置，焦点则描述了渐变的中心


    spreadMethod属性，该属性控制了当渐变到达终点的行为，但是此时该对象尚未被填充颜色。这个属性可以有三个值：pad、reflect或repeat。Pad就是目前我们见到的效果，即当渐变到达终点时，最终的偏移颜色被用于填充对象剩下的空间。reflect会让渐变一直持续下去，不过它的效果是与渐变本身是相反的，以100%偏移位置的颜色开始，逐渐偏移到0%位置的颜色，然后再回到100%偏移位置的颜色。repeat也会让渐变继续，但是它不会像reflect那样反向渐变，而是跳回到最初的颜色然后继续渐变。

    两种渐变都有一个叫做 gradientUnits（渐变单元）的属性，它描述了用来描述渐变的大小和方向的单元系统。该属性有两个值：userSpaceOnUse 、objectBoundingBox。默认值为objectBoundingBox，我们目前看到的效果都是在这种系统下的，它大体上定义了对象的渐变大小范围，所以你只要指定从0到1的坐标值，渐变就会自动的缩放到对象相同大小。userSpaceOnUse使用绝对单元，所以你必须知道对象的位置，并将渐变放在同样地位置上。上例中的radialGradient需要被重写成：

 <radialGradient id="Gradient" cx="60" cy="60" r="50" fx="35" fy="35" gradientUnits="userSpaceOnUse">
```

#### 图案

跟渐变一样，需要放在SVG文档的<defs>内部。



```js
<?xml version="1.0" standalone="no"?>
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg" version="1.1">
  <defs>
    <linearGradient id="Gradient1">
      <stop offset="5%" stop-color="white"/>
      <stop offset="95%" stop-color="blue"/>
    </linearGradient>
    <linearGradient id="Gradient2" x1="0" x2="0" y1="0" y2="1">
      <stop offset="5%" stop-color="red"/>
      <stop offset="95%" stop-color="orange"/>
    </linearGradient>
<pattern id="Pattern" x="0" y="0" width=".25" height=".25">
  <rect x="0" y="0" width="50" height="50" fill="skyblue"/>
  <rect x="0" y="0" width="25" height="25" fill="url(#Gradient2)"/>
  <circle cx="25" cy="25" r="20" fill="url(#Gradient1)" fill-opacity="0.5"/>
</pattern>
  </defs>

  <rect fill="url(#Pattern)" stroke="black" x="0" y="0" width="200" height="200"/>
</svg>
```



#### Texts

```js
<text x="10" y="10">Hello World!</text>

属性x和属性y性决定了文本在视口中显示的位置。
属性text-anchor，可以有这些值：start、middle、end或inherit，允许决定从这一点开始的文本流的方向

下列每个属性可以被设置为一个SVG属性或者成为一个CSS声明：
	font-family、font-style、font-weight、font-variant、font-stretch、font-size、font-size-adjust、kerning、letter-spacing、word-spacing和text-decoration。


<text>
  <tspan font-weight="bold" fill="red">This is bold and red</tspan>
</text>
1、text
    x
        为容器设置一个新绝对x坐标。它覆盖了默认的当前的文本位置。这个属性可以包含一个数列，它们将一个一个地应用到tspan元素内的每一个字符上。

    dx
        从当前位置，用一个水平偏移开始绘制文本。这里，你可以提供一个值数列，可以应用到连续的字体，因此每次累积一个偏移。

    此外还有属性y和属性dy作垂直转换。

    rotate
        把所有的字符旋转一个角度。如果是一个数列，则使每个字符旋转分别旋转到那个值，剩下的字符根据最后一个值旋转。

    textLength
        这是一个很模糊的属性，给出字符串的计算长度。它意味着如果它自己的度量文字和长度不满足这个提供的值，则允许渲染引擎精细调整字型的位置。

        
        
2、tref
        tref元素允许引用已经定义的文本，高效地把它复制到当前位置。你可以使用xlink:href属性，把它指向一个元素，取得其文本内容。你可以独立于源样式化它、修改它的外观。
        
     <text id="example">This is an example text.</text>

    <text>
        <tref xlink:href="#example" />
    </text>



3、textPath
	该元素利用它的xlink:href属性取得一个任意路径，把字符对齐到路径，于是字体会环绕路径、顺着路径走：
    
    <path id="my_path" d="M 20,20 C 40,40 80,40 100,20" fill="transparent" />
    <text>
      <textPath xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#my_path">
        This text follows a curve.
      </textPath>
    </text>
```

#### 基础变形

```js
g元素：
	用于将多个元素打组
	
1、平移
	<rect x="0" y="0" width="10" height="10" transform="translate(30,40)" />
	该示例将呈现一个矩形，移到点(30,40)，而不是出现在点(0,0)。如果没有指定第二个值，它默认被赋值0。
   
2、旋转
	<rect x="20" y="20" width="20" height="20" transform="rotate(45)" />
        
3、斜切
	利用一个矩形制作一个斜菱形。可用skewX()变形和skewY()变形。每个需要一角度以确定元素斜切到多远。

4、缩放
	scale()变形改变了元素的尺寸。它需要两个数字，作为比率计算如何缩放。0.5表示收缩到50%。如果第二个数字被忽略了，它默认等于第一个值。

5、矩阵matrix()
	matrix(a, b, c, d, e, f)变形设置结果矩阵，实现复杂变形
    
可应用于 g 元素上，下面两种效果一样
<g transform="scale(2)">
  <rect width="50" height="50" />
</g>

<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <svg width="100" height="100" viewBox="0 0 50 50">
    <rect width="50" height="50" />
  </svg>
</svg>
    
```

#### 剪切和遮罩

```js
1、剪切
    Clipping用来移除在别处定义的元素的部分内容。在这里，任何半透明效果都是不行的。它只能要么显示要么不显示。
	eg:将圆切成半圆   
    在(100,100)创建一个圆形，半径是100。属性clip-path引用了一个带单个rect元素的<clipPath>元素。它内部的这个矩形将把画布的上半部分涂黑。注意，clipPath元素经常放在一个defs元素内。
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
      <defs>
        <clipPath id="cut-off-bottom">
          <rect x="0" y="0" width="200" height="100" />
        </clipPath>
      </defs>

      <circle cx="100" cy="100" r="100" clip-path="url(#cut-off-bottom)" />
    </svg>

2、遮罩
	Masking允许使用透明度和灰度值遮罩计算得的软边缘。
    eg:
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
      <defs>
        <linearGradient id="Gradient">
          <stop offset="0" stop-color="white" stop-opacity="0" />
          <stop offset="1" stop-color="white" stop-opacity="1" />
        </linearGradient>
        <mask id="Mask">
          <rect x="0" y="0" width="200" height="200" fill="url(#Gradient)"  />
        </mask>
      </defs>

      <rect x="0" y="0" width="200" height="200" fill="green" />
      <rect x="0" y="0" width="200" height="200" fill="red" mask="url(#Mask)" />
    </svg>

opacity定义透明度
	<rect x="0" y="0" width="100" height="100" opacity=".5" />
        
【注意】所有的SVG元素的初始display值都是inline。
```

#### 嵌入光栅图像

> 很像在HTML中的`img`元素，SVG有一个`image`元素，用于同样的目的。你可以利用它嵌入任意光栅（以及矢量）图像。它的规格要求应用至少支持PNG、JPG和SVG格式文件。

```js
//SVG的<image>元素允许在一个SVG对象内部呈现光栅图像。
<svg version="1.1"
     xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
     width="200" height="200">
  <image x="90" y="-65" width="128" height="146" transform="rotate(45)"
     xlink:href="https://developer.mozilla.org/static/img/favicon144.png"/>
</svg>
```

#### 滤镜效果

```js
滤镜通过 <filter> 元素进行定义，并且置于 <defs> 区块中。

<svg width="250" viewBox="0 0 200 85"
     xmlns="http://www.w3.org/2000/svg" version="1.1">
  <defs>
    <!-- Filter declaration -->
    <filter id="MyFilter" filterUnits="userSpaceOnUse"
            x="0" y="0"
            width="200" height="120">

      <!-- offsetBlur -->
      <feGaussianBlur in="SourceAlpha" stdDeviation="4" result="blur"/>
      <feOffset in="blur" dx="4" dy="4" result="offsetBlur"/>

      <!-- litPaint -->
      <feSpecularLighting in="blur" surfaceScale="5" specularConstant=".75"
                          specularExponent="20" lighting-color="#bbbbbb"
                          result="specOut">
        <fePointLight x="-5000" y="-10000" z="20000"/>
      </feSpecularLighting>
      <feComposite in="specOut" in2="SourceAlpha" operator="in" result="specOut"/>
      <feComposite in="SourceGraphic" in2="specOut" operator="arithmetic"
                   k1="0" k2="1" k3="1" k4="0" result="litPaint"/>

      <!-- merge offsetBlur + litPaint -->
      <feMerge>
        <feMergeNode in="offsetBlur"/>
        <feMergeNode in="litPaint"/>
      </feMerge>
    </filter>
  </defs>

  <!-- Graphic elements -->
  <g filter="url(#MyFilter)">
      <path fill="none" stroke="#D90000" stroke-width="10"
            d="M50,66 c-50,0 -50,-60 0,-60 h100 c50,0 50,60 0,60z" />
      <path fill="#D90000"
            d="M60,56 c-30,0 -30,-40 0,-40 h80 c30,0 30,40 0,40z" />
      <g fill="#FFFFFF" stroke="black" font-size="45" font-family="Verdana" >
        <text x="52" y="52">SVG</text>
      </g>
  </g>
</svg>
```

#### SVG字体（**SVG字体当前只在Safari和Android浏览器中受支持。**）

### SVG 转成流

```js
<div class="svg-wrap">
    <svg t="1651739359824" class="icon" viewBox="0 0 1025 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
        p-id="5520" xmlns:xlink="http://www.w3.org/1999/xlink" width="64.0625" height="64">
        <defs>
            <style type="text/css"></style>
        </defs>
        <path d="M971.2 646.2H55V377.8h916.3v268.4z m-892.2-24h868.3V401.8H79v220.4z" p-id="5521" fill="#F19600">
        </path>
        <path d="M0 500h62.9v24H0zM962.7 500h62.9v24h-62.9z" p-id="5522" fill="#F19600"></path>
    </svg>
</div>

var svgXml = $('.svg-wrap').html();
// SVG 转成流
var image = new Image();
image.src = 'data:image/svg+xml;base64,' + window.btoa(unescape(encodeURIComponent(svgXml))); //给图片对象写入base64编码的svg流

var canvas = document.createElement('canvas'); //准备空画布
canvas.width = $('.svg-wrap svg').width();
canvas.height = $('.svg-wrap svg').height();

// SVG转图片下载
var context = canvas.getContext('2d'); //取得画布的2d绘图上下文
context.drawImage(image, 0, 0);
var a = document.createElement('a');
a.href = canvas.toDataURL('image/png'); //将画布内的信息导出为png图片数据
a.download = "MapByMathArtSys"; //设定下载名称
a.click(); //点击触发下载
```



### 实用工具库[Snap.svg](http://snapsvg.io/)

> Snap.svg可以帮助开发者更轻松地使用SVG，就如同使用JQuery操作DOM一样。
>
> 荐直接阅读张鑫旭老师的《Snap.svg中文文档》

● Snap类用于聚合其他各个子类别的方法和属性。

● Element类是在Snap中操作的基本单元，它将普通的SVG元素包装为element实例，其原型方法包括属性设置、动画设置、变形、交互事件绑定、DOM元素选择器及一些工具函数等。

● Paper类聚合了实现SVG原生特性的方法，包括基本形状及自定义路径的绘制、蒙版及滤镜、文字绘制及图形分组管理等。

● Set类聚合了元素分组管理的方法。

● Matrix类聚合了构建变形矩阵的方法。

● mina聚合了动画的缓动函数设定方法。

```js
// 它的构造函数可以接收元素的尺寸数据生成一个空白的SVG，也可以传入单个或一组现有的SVG元素的ID，甚至支持直接传入CSS query selector（查询选择器）
// like s = Snap(800, 600);
var s = Snap("#svg");
// Lets create big circle in the middle:
var bigCircle = s.circle(150, 150, 100);
// By default its black, lets change its attributes
bigCircle.attr({
    fill: "#bada55",
    stroke: "#000",
    strokeWidth: 5
});
// Now lets create another small circle:
var smallCircle = s.circle(100, 150, 70);
// Lets put this small circle and another one into a group:
var discs = s.group(smallCircle, s.circle(200, 150, 70));
// Now we can change attributes for the whole group
discs.attr({
    fill: "#fff"
});
// Now more interesting stuff
// Lets assign this group as a mask for our big circle
bigCircle.attr({
    mask: discs
});
// Despite our small circle now is a part of a group
// and a part of a mask we could still access it:
smallCircle.animate({r: 50}, 1000);
// We don’t have reference for second small circle,
// but we could easily grab it with CSS selectors:
discs.select("circle:nth-child(2)").animate({r: 50}, 1000);
// Now lets create pattern
var p = s.path("M10-5-10,15M15,0,0,15M0-5-20,15").attr({
        fill: "none",
        stroke: "#bada55",
        strokeWidth: 5
    });
// To create pattern,
// just specify dimensions in pattern method:
p = p.pattern(0, 0, 10, 10);
// Then use it as a fill on big circle
bigCircle.attr({
    fill: p
});
// We could also grab pattern from SVG
// already embedded into our page
discs.attr({
    fill: Snap("#pattern")
});
// Lets change fill of circles to gradient
// This string means relative radial gradient
// from white to black
discs.attr({fill: "r()#fff-#000"});
// Note that you have two gradients for each circle
// If we want them to share one gradient,
// we need to use absolute gradient with capital R
discs.attr({fill: "R(150, 150, 100)#fff-#000"});
 

```

Snap.svg虽然易用，但它的源代码大小超过200KB，即使压缩后也有81KB，这样说你或许没有什么感觉，当你知道了完整的Vue2.0框架压缩后也不过才90KB后就能明白笔者想要表达的重点了，技术的选型一定是由需求场景决定的。若为了实现一个小效果而大动干戈地引入一个巨型工具包则是不明智的做法

