---
title: 第十八章 canvas与动画
date: 2022-06-17 08:23:10
tags: 红宝书笔记
categories: js
---
## requestAnimationFrame

```js
早期js动画 都是通过定时器 来控制动画的执行。但是动画的时间间隔精度不足毫秒一直是个问题。

 IE8 及更早版本的计时器精度为 15.625 毫秒； 
 IE9 及更晚版本的计时器精度为 4 毫秒； 
 Firefox 和 Safari 的计时器精度为约 10 毫秒； 
 Chrome 的计时器精度为 4 毫秒。

requestAnimationFrame()方法接收一个参数，此参数是一个要在重绘屏幕前调用的函数。这个

函数就是修改 DOM 样式以反映下一次重绘有什么变化的地方。为了实现动画循环，可以把多个 requestAnimationFrame()调用串联起来，就像以前使用 setTimeout()时一样： 

function updateProgress() { 
	var div = document.getElementById("status"); 
	div.style.width = (parseInt(div.style.width, 10) + 5) + "%"; 
	if (div.style.left != "100%") { 
	requestAnimationFrame(updateProgress); 
	} 
} 
requestAnimationFrame(updateProgress); 

因为 requestAnimationFrame()只会调用一次传入的函数，所以每次更新用户界面时需要再手 动调用它一次。同样，也需要控制动画何时停止。结果就会得到非常平滑的动画。


cancelAnimationFrame

	与 setTimeout 类似， requestAnimationFrame 返回ID，可以通过 cancelAnimationFrame来取消
    
    let requestID = window.requestAnimationFrame(() => { 
        console.log('Repaint!'); 
    }); 
	window.cancelAnimationFrame(requestID);


通过requestAnimationFrame节流

function expensiveOperation() { console.log('Invoked at', Date.now()); } window.addEventListener('scroll', () => { 
    window.requestAnimationFrame(expensiveOperation); 
});

//因为重绘是非常繁琐的操作，所以上不上真正的节流

 
function expensiveOperation() { console.log('Invoked at', Date.now()); } 
window.addEventListener('scroll', () => { 
    if (!expensiveOperation.enabled) { 
        expensiveOperation.enabled = true; 
        window.requestAnimationFrame(expensiveOperation); 
        window.setTimeout(() => expensiveOperation.enabled = false, 50); } 
});


```
<!--more-->

## canvas

```js
//标签创建
<canvas id="drawing" width="200" height="200">A drawing of something.</canvas>
//js创建
let canvas = document.createElement('canvas');
    canvas.width = 200;
    canvas.height = 200;

let drawing = document.getElementById("drawing"); 

// 确保浏览器支持<canvas> 
if (drawing.getContext) { 
    let context = drawing.getContext("2d"); 
    // 其他代码 
}

toDataURL()方法导出<canvas>元素上的图像。这个方法接收一个参数：要生成图像的MIME类型

eg:
	let drawing = document.getElementById("drawing"); 
    // 确保浏览器支持<canvas> 
    if (drawing.getContext) { 
        // 取得图像的数据 URI 
        let imgURI = drawing.toDataURL("image/png"); 
        // 显示图片 
        let image = document.createElement("img"); 
        image.src = imgURI; document.body.appendChild(image); 
    }
【注意】 如果画布中的图像是其他域绘制过来的，toDataURL()方法就会抛出错误。

2D绘画上下文
	2D 绘图上下文提供了绘制 2D 图形的方法，包括矩形、弧形和路径。坐标的原点(0,0)在canvas左上角。width,height表示两个方向上最大值

填充和描边

let context = drawing.getContext("2d"); 
context.strokeStyle = "red"; //描边
context.fillStyle = "#0000ff";//填充
//相当于 设置 画笔 的描边与填充颜色(它们也可以是渐变色),除非再次修该否则所有描边填充操作都会使用这两种样式
    lineWidth:描边的宽度，可以是任意整数。
    lineCap:控制线条端点的形状 ['butt'(平头),'round'(出圆头),'square'(出方头)]
    lineJoin:控制线条交叉点形状。


绘制矩形
	有三个相关方法
    let context = drawing.getContext('2d');
    1、fillRect(x坐标,y坐标,宽,高)
		// 绘制半透明蓝色矩形 
        context.fillStyle = "rgba(0,0,255,0.5)"; 
        context.fillRect(30, 30, 50, 50);

    2、strokeRect(x坐标,y坐标,宽,高)
		//strokeRect()方法使用通过 strokeStyle 属性指定的颜色绘制矩形轮廓。下面是一个例子：
		// 绘制红色轮廓的矩形 
		context.strokeStyle = "#ff0000"; 
		context.strokeRect(10, 10, 50, 50);

    3、clearRect(x坐标,y坐标,宽,高)
		//擦除画布 矩形范围 起点(40,40) 宽高都为40的
		context.clearRect(40, 40, 10, 10);

绘制路径
	首先必须调用 beginPath()方法以表示要开始绘制新路径。然后调用下列方法来绘制路径。创建路径后，可使用 closePath() 绘制一条返回起点的线。绘制完成后，可以绘制描边，可以填充颜色，还可以调用 clip() 方法基于已有路径创建一个新裁剪区域。
     arc(x, y, radius, startAngle, endAngle, counterclockwise)：以坐标(x, y)为圆 心，以 radius 为半径绘制一条弧线，起始角度为 startAngle，结束角度为 endAngle（都是 弧度）。最后一个参数 counterclockwise 表示是否逆时针计算起始角度和结束角度（默认为 顺时针）。 
    
     arcTo(x1, y1, x2, y2, radius)：以给定半径 radius，经由(x1, y1)绘制一条从上一点 到(x2, y2)的弧线。 
    
     bezierCurveTo(c1x, c1y, c2x, c2y, x, y)：以(c1x, c1y)和(c2x, c2y)为控制点， 绘制一条从上一点到(x, y)的弧线（三次贝塞尔曲线）。 
    
     lineTo(x, y)：绘制一条从上一点到(x, y)的直线。  moveTo(x, y)：不绘制线条，只把绘制光标移动到(x, y)。 
    
     quadraticCurveTo(cx, cy, x, y)：以(cx, cy)为控制点，绘制一条从上一点到(x, y) 的弧线（二次贝塞尔曲线）。 
    
     rect(x, y, width, height)：以给定宽度和高度在坐标点(x, y)绘制一个矩形。这个方法 与 strokeRect()和 fillRect()的区别在于，它创建的是一条路径，而不是独立的图形。


    moveTo(x,y):将笔触移动到指定的坐标x以及y上。开始点也可以通过moveTo()函数改变。
    lineTo(x,y):绘制一条从当前位置到指定x以及y位置的直线。
    isPointInPath(x,y):用于确定指定的点是否在路径 上，可以在关闭路径前随时调用
    
    
绘制文本
	-fillText(x坐标,y坐标,可选最大像素宽度)
	-strokeText(x坐标,y坐标,可选最大像素宽度)
	都有下列3个属性：
         font：以 CSS 语法指定的字体样式、大小、字体族等，比如"10px Arial"。 
         textAlign：指定文本的对齐方式，可能的值包括"start"、"end"、"left"、"right"和 "center"。推荐使用"start"和"end"，不使用"left"和"right"，因为前者无论在从左到右 书写的语言还是从右到左书写的语言中含义都更明确。 
         textBaseLine ：指定文本的基线，可能的值包括 "top" 、 "hanging" 、 "middle" 、 "alphabetic"、"ideographic"和"bottom"。

		
        measureText():这个方法接收一个参数，即要绘制的文本，然后返回一个对象，包括文本将要占的宽度

 
变换
     rotate(angle)：围绕原点把图像旋转 angle 弧度。 
     scale(scaleX, scaleY)：通过在 x 轴乘以 scaleX、在 y 轴乘以 scaleY 来缩放图像。scaleX 和 scaleY 的默认值都是 1.0。 
     translate(x, y)：把原点移动到(x, y)。执行这个操作后，坐标(0, 0)就会变成(x, y)。 
     transform(m1_1, m1_2, m2_1, m2_2, dx, dy)：像下面这样通过矩阵乘法直接修改矩阵。 
    m1_1 m1_2 dx 
    m2_1 m2_2 dy 
    0	 0	 1 
     setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy)：把矩阵重置为默认值，再以传入的 参数调用 transform()。

	
    save():所有这一时刻的设置会被放到一个暂存栈中，多次调用保存多套设置
    strokeStyle():可以恢复上次save的配置

    
绘制图形
	//将现有图像绘制到画布 第四个参数开始为可选
    drawImage(要绘制 的图像,源图像 x 坐标,源图像 y 坐标,源图像宽度,源图像高度,目标区域 x 坐标,目标区域 y 坐标,目标区域宽度,目标区域高度)
	
	第一个参数处理 img 元素之外，还可以是 另一个 canvas 元素
	eg:
        let image = document.images[0]; 
        
        context.drawImage(image, 10, 10);
		
        context.drawImage(image, 50, 10, 20, 30);

        context.drawImage(image, 0, 10, 50, 50, 0, 100, 40, 60);

	结合其他一些方法，drawImage()方法可以方便地实现常见的图像操作。操作的结果可以使用 toDataURL()方法获取。不过有一种情况例外：如果绘制的图像来自其他域而非当前页面，则不能获取 其数据。此时，调用 toDataURL()将抛出错误。比如，如果来自 www.example.com 的页面上绘制的是 来自 www.wrox.com 的图像，则上下文就是“脏的”，获取数据时会抛出错误。


    
阴影

	2D 上下文可以根据以下属性的值自动为已有形状或路径生成阴影。 

     shadowColor：CSS 颜色值，表示要绘制的阴影颜色，默认为黑色。 
     shadowOffsetX：阴影相对于形状或路径的 x 坐标的偏移量，默认为 0。 
     shadowOffsetY：阴影相对于形状或路径的 y 坐标的偏移量，默认为 0。 
     shadowBlur：像素，表示阴影的模糊量。默认值为 0，表示不模糊。

	eg:
        let context = drawing.getContext("2d"); 
        // 设置阴影 
        context.shadowOffsetX = 5; 
        context.shadowOffsetY = 5; 
        context.shadowBlur = 4; 
        context.shadowColor = "rgba(0, 0, 0, 0.5)"; 
        // 绘制红色矩形 
        context.fillStyle = "#ff0000"; 
        context.fillRect(10, 10, 50, 50); 
        // 绘制蓝色矩形 
        context.fillStyle = "rgba(0,0,255,1)"; 
        context.fillRect(30, 30, 50, 50);


渐变
	线性渐变
	createLinearGradient(起点 x 坐标、 起点 y 坐标、终点 x 坐标和终点 y 坐标)
	//调用之后，该方法会以指定大小创建一个新的 CanvasGradient 对象并返回实例。
	
	addColorStop(色标位置, css颜色字符串)方法为渐变指定色标。
    
    eg:
        let gradient = context.createLinearGradient(30, 30, 70, 70); 
        gradient.addColorStop(0, "white"); 
        gradient.addColorStop(1, "black"); 
//这个 gradient 对象现在表示的就是在画布上从(30, 30)到(70, 70)绘制一个渐变。渐变的起点颜色 为白色，终点颜色为黑色。可以把这个对象赋给 fillStyle 或 strokeStyle 属性，从而以渐变填充 或描画绘制的图形：

		// 绘制红色矩形 
        context.fillStyle = "#ff0000"; 
        context.fillRect(10, 10, 50, 50); 
        // 绘制渐变矩形 
        context.fillStyle = gradient; 
        context.fillRect(30, 30, 70, 70);

		//红色矩形 渐变不是全部，因为渐变范围与矩形范围不一致

	径向渐变
    	createRadialGradient(起点圆心x,起点圆心y,半径,终点圆心x,终点圆心y,半径)方法来创建。
        一般起点和终点圆心都是同心圆，圆心坐标相同，控制半径就行
        eg:
            let gradient = context.createRadialGradient(55, 55, 10, 55, 55, 30); 
            gradient.addColorStop(0, "white"); 
            gradient.addColorStop(1, "black"); 
            // 绘制红色矩形 
            context.fillStyle = "#ff0000"; 
            context.fillRect(10, 10, 50, 50); 
            // 绘制渐变矩形 
            context.fillStyle = gradient; 
            context.fillRect(30, 30, 50, 50);


图案
	创建图案
    参数1：也可以是video元素或另一个canvas元素
    参数2：与background-repeat 属性是一样的，包括"repeat"、"repeat-x"、"repeat-y"和"no-repeat"。
	
	createPattern(img元素,重复图像的字符串)方法
    eg:
	
		let image = document.images[0], 
            pattern = context.createPattern(image, "repeat"); 
        // 绘制矩形 
        context.fillStyle = pattern; 
        context.fillRect(10, 10, 150, 150);

		//跟渐变一样，图案的起点实际上是画布的圆点（0，0）.将其填充设置为图案，表示在指定位置而不是开始绘制的位置显示图案



图像数据
	getImageData(开始位置x坐标,开始位置y坐标，宽，高):获取原始图像数据
    返回对象包含 width,height,data(包含图像的原始像素信息的数组。每个像素在data中由4个值表示（r,g,b,a）。每个值的范围都是[0,255])

	对原始图像数据访问可以灵活操作图像，例如创建简单的灰阶过滤器
    
    
合成
	2D上下文中绘制的所有内容都会应用两个属性：globalAlpha 和 globalComposition Operation，
        globalAlpha:指定内容透明度[0,1]
        globalCompositionOperation:表示新绘制的形状如何与上下文中已有的形状融合。值为如下字符串

         source-over：默认值，新图形绘制在原有图形上面。 
         source-in：新图形只绘制出与原有图形重叠的部分，画布上其余部分全部透明。 
         source-out：新图形只绘制出不与原有图形重叠的部分，画布上其余部分全部透明。 
         source-atop：新图形只绘制出与原有图形重叠的部分，原有图形不受影响。 
         destination-over：新图形绘制在原有图形下面，重叠部分只有原图形透明像素下的部分可见。 
         destination-in：新图形绘制在原有图形下面，画布上只剩下二者重叠的部分，其余部分完全 透明。 
         destination-out：新图形与原有图形重叠的部分完全透明，原图形其余部分不受影响。 
         destination-atop：新图形绘制在原有图形下面，原有图形与新图形不重叠的部分完全透明。 
         lighter：新图形与原有图形重叠部分的像素值相加，使该部分变亮。 
         copy：新图形将擦除并完全取代原有图形。 
         xor：新图形与原有图形重叠部分的像素执行“异或”计算。

        	
	eg:
		// 绘制红色矩形 
        context.fillStyle = "#ff0000"; 
        context.fillRect(10, 10, 50, 50); 
        // 设置合成方式 
        context.globalCompositeOperation = "destination-over"; 
        // 绘制蓝色矩形 
        context.fillStyle = "rgba(0,0,255,1)"; 
        context.fillRect(30, 30, 50, 50);
            虽然后绘制的蓝色矩形通常会出现在红色矩形上面，但将 globalCompositeOperation 属性的值 修改为"destination-over"意味着红色矩形会出现在蓝色矩形上面。



裁剪路径

	clip() 将当前正在构建的路径转换为当前的裁剪路径。
    
基本动画
	擦除画布定时画
    eg:太阳系的动画
    var sun = new Image();
    var moon = new Image();
    var earth = new Image();
    function init(){
      sun.src = 'https://mdn.mozillademos.org/files/1456/Canvas_sun.png';
      moon.src = 'https://mdn.mozillademos.org/files/1443/Canvas_moon.png';
      earth.src = 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png';
      window.requestAnimationFrame(draw);
    }

    function draw() {
      var ctx = document.getElementById('canvas').getContext('2d');

      ctx.globalCompositeOperation = 'destination-over';
      ctx.clearRect(0,0,300,300); // clear canvas

      ctx.fillStyle = 'rgba(0,0,0,0.4)';
      ctx.strokeStyle = 'rgba(0,153,255,0.4)';
      ctx.save();
      ctx.translate(150,150);

      // Earth
      var time = new Date();
      ctx.rotate( ((2*Math.PI)/60)*time.getSeconds() + ((2*Math.PI)/60000)*time.getMilliseconds() );
      ctx.translate(105,0);
      ctx.fillRect(0,-12,50,24); // Shadow
      ctx.drawImage(earth,-12,-12);

      // Moon
      ctx.save();
      ctx.rotate( ((2*Math.PI)/6)*time.getSeconds() + ((2*Math.PI)/6000)*time.getMilliseconds() );
      ctx.translate(0,28.5);
      ctx.drawImage(moon,-3.5,-3.5);
      ctx.restore();

      ctx.restore();

      ctx.beginPath();
      ctx.arc(150,150,105,0,Math.PI*2,false); // Earth orbit
      ctx.stroke();

      ctx.drawImage(sun,0,0,300,300);

      window.requestAnimationFrame(draw);
    }

    init();

高级动画
	
    var canvas = document.getElementById('canvas');
    var ctx = canvas.getContext('2d');

    var ball = {
      x: 100,
      y: 100,
      radius: 25,
      color: 'blue',
      draw: function() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
        ctx.closePath();
        ctx.fillStyle = this.color;
        ctx.fill();
      }
    };

    ball.draw();
	添加 速率，边界，加速度，长尾
    
    
像素操作
	ImageData 对象
    	对象中存储着canvas对象真实的像素数据，它包含以下几个只读属性：
        width
        	图片宽度，单位是像素
        height
        	图片高度，单位是像素
        data
        	Uint8ClampedArray类型的一维数组，包含着RGBA格式的整型数据，范围在0至255之间（包括255）。
        
     创建一个ImageData
     	var myImageData = ctx.createImageData(width, height);
     获得一个包含画布场景像素数据的ImageData对像
     	var myImageData = ctx.getImageData(left, top, width, height);

	实现一个颜色选择器
    var img = new Image();
    img.crossOrigin = 'anonymous';
    img.src = './assets/rhino.jpg';
    var canvas = document.getElementById('canvas');
    var ctx = canvas.getContext('2d');
    img.onload = function() {
      ctx.drawImage(img, 0, 0);
      img.style.display = 'none';
    };
    var hoveredColor = document.getElementById('hovered-color');
    var selectedColor = document.getElementById('selected-color');


    function pick(event, destination) {
      var x = event.layerX;
      var y = event.layerY;
      var pixel = ctx.getImageData(x, y, 1, 1);
      var data = pixel.data;

        const rgba = `rgba(${data[0]}, ${data[1]}, ${data[2]}, ${data[3] / 255})`;
        destination.style.background = rgba;
        destination.textContent = rgba;

        return rgba;
    }

    canvas.addEventListener('mousemove', function(event) {
        pick(event, hoveredColor);
    });
    canvas.addEventListener('click', function(event) {
        pick(event, selectedColor);
    });


性能优化
	离屏canvas
    	如果发现自己在每个动画帧上重复了一些相同的绘制操作，请考虑将其分流到屏幕外的画布上。 然后，您可以根据需要频繁地将屏幕外图像渲染到主画布上，而不必首先重复生成该图像的步骤。
        eg:
        myEntity.offscreenCanvas = document.createElement("canvas");
        myEntity.offscreenCanvas.width = myEntity.width;
        myEntity.offscreenCanvas.height = myEntity.height;
        myEntity.offscreenContext = myEntity.offscreenCanvas.getContext("2d");

        myEntity.render(myEntity.offscreenContext);

避免浮点数的坐标点，用整数取而代之
	当你画一个没有整数坐标点的对象时会发生子像素渲染。浏览器为了达到抗锯齿效果会做额外运算
    ctx.drawImage(myImage, 0.3, 0.5);

不要在用drawImage时缩放图像
    在离屏canvas中缓存图片的不同尺寸，而不要用drawImage()去缩放它们。

使用多层画布去画一个复杂的场景
	例如，假设您有一个游戏，其UI位于顶部，中间是游戏性动作，底部是静态背景。 在这种情况下，您可以将游戏分成三个<canvas>层。 UI将仅在用户输入时发生变化，游戏层随每个新框架发生变化，并且背景通常保持不变。
    
用CSS设置大的背景图
	避免每一帧绘制大图背景
    
用CSS transforms特性缩放画布
	CSS transforms 使用GPU，因此速度更快。 最好的情况是不直接缩放画布，或者具有较小的画布并按比例放大，而不是较大的画布并按比例缩小。
    eg:
    var scaleX = window.innerWidth / canvas.width;
    var scaleY = window.innerHeight / canvas.height;

    var scaleToFit = Math.min(scaleX, scaleY);
    var scaleToCover = Math.max(scaleX, scaleY);

    stage.style.transformOrigin = '0 0'; //scale from top left
    stage.style.transform = 'scale(' + scaleToFit + ')';

关闭透明度
	如果你的游戏使用画布而且不需要透明，当使用 HTMLCanvasElement.getContext() 创建一个绘图上下文时把 alpha 选项设置为 false 。这个选项可以帮助浏览器进行内部优化。

var ctx = canvas.getContext('2d', { alpha: false });


more

将画布的函数调用集合到一起（例如，画一条折线，而不要画多条分开的直线）
避免不必要的画布状态改变
渲染画布中的不同点，而非整个新状态
尽可能避免 shadowBlur特性
尽可能避免text rendering
尝试不同的方法来清除画布(clearRect() vs. fillRect() vs. 调整canvas大小)
 有动画，请使用window.requestAnimationFrame() 而非window.setInterval()
请谨慎使用大型物理库
```

