---
title: 第十二章 BOM
date: 2022-06-17 08:23:10
tags: 红宝书笔记
categories: js
---

### window 对象

window对象是BOM的核心对象，表示浏览器的实例。ECMAScript 中的 Global 对象。
<!--more-->

```js

-GLOBAL作用域
	因为 window 对象被复用为 ECMAScript 的 Global 对象，所以通过 var 声明的所有全局变量和函 数都会变成 window 对象的属性和方法。

-窗口关系
    -top 对象始终指向最上层窗口，即浏览器窗口本身。
    -parent 始终指向当前窗口的父窗口。 如果当前窗口为最上层窗口，则 top = parent = window
	-self 对象，始终指向 window
-窗口位置与像素比
	-screenLeft,screenTop 用于表示窗口相对于屏幕左侧和顶部的位置，返回值的单位是CSS像素
    -moveTo、moveBy 移动窗口位置	浏览器可能默认禁用
    	moveTo(x,y) 接收绝对坐标
        moveBy(0,100) 就收相对当前位置 在 x,y轴上移动的位置
	-resizeTo、resizeBy 	调整窗口大小  浏览器可能默认禁用
    	resizeTo(w,h) 接收新的宽度与高度
        resizeBy(w,h) 接收宽度高度要缩放多少
	
-像素比
	CSS 像素是 Web 开发中使用的统一像素单位。这个单位的背后其实是一个角度：0.0213°。
    举个例子，手机屏幕的物理分辨率可能是 1920×1080，但因为其像素可能非常小，所以浏览器就需 要将其分辨率降为较低的逻辑分辨率，比如 640×320。这个物理像素与 CSS 像素之间的转换比率由 window.devicePixelRatio 属性提供。对于分辨率从 1920×1080 转换为 640×320 的设备，window. devicePixelRatio 的值就是 3。这样一来，12 像素（CSS 像素）的文字实际上就会用 36 像素的物理 像素来显示。 window.devicePixelRatio 实际上与每英寸像素数（DPI，dots per inch）是对应的。DPI 表示单 位像素密度，而 window.devicePixelRatio 表示物理像素与逻辑像素之间的缩放系数。


-窗口大小
	-outerWidth 返回浏览器窗口自身的大小
	-outerHeight 
	-innerWidth 返回浏览器中页面视口大小（不包括浏览器边框和工具栏）
	-innerHeight 

	document.documentElement.clientWidth 
	document.documentElement.clientHeight 返回页面视口的宽度和高度。

浏览器窗口自身的精确尺寸不好确定，但可以确定页面视口的大小，如下所示： 
    let pageWidth = window.innerWidth, pageHeight = window.innerHeight; 
    if (typeof pageWidth != "number") { 
        if (document.compatMode == "CSS1Compat"){ 
            pageWidth = document.documentElement.clientWidth; 
            pageHeight = document.documentElement.clientHeight; 
        } else { 
            pageWidth = document.body.clientWidth; 
            pageHeight = document.body.clientHeight; 
        } 
    }

-视口位置
	度量文档相对于视口滚动距离的属性	下面两种方法返回相等的值
    -pageXoffset/scrollX
	-pageYoffset/scrollY
	
	-scroll()、scrollTo、scrollBy() 方法。
    	-接收相对于视口距离的x和y坐标 ，这两个参数在前面两个方法中表示要滚动的坐标，在最后一个方法中表示滚动的距离。
        -以上方法还可接收 ScrollToOptions 字典
        	
            // 滚动到底部
            let documentHeight = document.documentElement.offsetHeight;
            let viewportHeight = window.innerHeight;
            // 滚动到最后一页， 在视口可见
            window.scrollTo(0, documentHeight - viewportHeight)
        
        	window.scrool({
                left:100,
                top:200,
                behavior:'auto'//smoth:平滑，auto：正常
            })

-导航与打开新窗口
	-window.open()方法可以用于导航到指定 URL，也可以用于打开新浏览器窗口。
		window.open(URL，目标窗口，特性字符串，浏览器历史记录是否代替当前加载页)
		第二个参数：如果有一个同名窗口则打开已存在的窗口（或frame窗格），否则新开一个此名窗口
        		还可取特殊值：_parent,_top,_blank (新页面打开),_self (本页面打开)

        与<a href="http://www.wrox.com" target="topFrame"/>相同 
        window.open("http://www.wrox.com/", "topFrame");


	特性字符串
    设置				值				说明
    
    fullscreen 		"yes"或"no" 	  表示新窗口是否最大化。仅限 IE 支持 
    height 			数值 			新窗口高度。这个值不能小于 100 
    left 			数值 			新窗口的 x 轴坐标。这个值不能是负值 
    location 		"yes"或"no"	  表示是否显示地址栏。不同浏览器的默认值也不一样。在设置										为"no"时，地址栏 可能隐藏或禁用（取决于浏览器）
	Menubar 		"yes"或"no" 	 表示是否显示菜单栏。默认为"no" 
    resizable 		"yes"或"no" 	  表示是否可以拖动改变新窗口大小。默认为"no" 
    scrollbars 		"yes"或"no"    表示是否可以在内容过长时滚动。默认为"no" 
    status 			"yes"或"no"    表示是否显示状态栏。不同浏览器的默认值也不一样 
    toolbar 		"yes"或"no"    表示是否显示工具栏。默认为"no" 
    top				 数值 		新窗口的 y 轴坐标。这个值不能是负值 
    width 			 数值 		 新窗口的宽度。这个值不能小于 100
	
eg:
    let wx = window.open('http://127.0.0.1:5503/html/test.html','haha'
                         ,"height=400,width=400,top=10,left=10,resizable=yes");
	
	//通过 window.open() 打开的页面，可以通过 window.opener 与原窗口通信，设置为 null 就不行了
    wx.close();//通过window.open创建的窗口可以，不经用户确认就关闭

	//判断浏览器是否禁用弹出窗口
	let blocked = false; 
	try { 
        let wroxWin = window.open("http://www.wrox.com", "_blank"); 
        if (wroxWin == null){
			blocked = true; 
        } 
    } catch (ex){ 
        blocked = true;
    } 
    if (blocked){ 
        alert("The popup was blocked!"); 
    }


-setTimeout,setInterval
	
	注意 所有超时执行的代码（函数）都会在全局作用域中的一个匿名函数中运行，因此函 数中的 this 值在非严格模式下始终指向 window，而在严格模式下是 undefined。如果 给 setTimeout()提供了一个箭头函数，那么 this 会保留为定义它时所在的词汇作用域。

	注意在使用 setTimeout()时，不一定要记录超时 ID，因为它会在条件满足时自动停止，否则会 自动设置另一个超时任务。这个模式是设置循环任务的推荐做法。setIntervale()在实践中很少会在 生产环境下使用，因为一个任务结束和下一个任务开始之间的时间间隔是无法保证的，有些循环定时任务可能会因此而被跳过。而像前面这个例子中一样使用 setTimeout()则能确保不会出现这种情况。一 般来说，最好不要使用 setInterval()。

-系统对话框 alert,confirm,prompt
	alert(提示信息) 警告框
    confirm(提示信息) 确认框
    	if (confirm("Are you sure?")) { 
            alert("I'm so glad you're sure!"); 
        } else { 
            alert("I'm sorry to hear you're not sure."); 
        }
	prompt(提示信息，默认值) 提示框

-location 对象
	
	属 性 					值 								说 明 
location.hash 			"#contents" 		URL 散列值（井号后跟零或多个字符），如果没有则 为空字符串 location.host 			"www.wrox.com:80" 	服务器名及端口号 location.hostname "www.wrox.com" 服务器名 
location.href 			"http://www.wrox.com:80/WileyCDA/ ?q=javascript#contents"
										当前加载页面的完整 URL。location 的 toString() 方法返回这个值 
location.pathname 		"/WileyCDA/" 		URL 中的路径和（或）文件名 location.port "80" 请求的端口。如											果 URL中没有端口，则返回空字符串 
	
location.protocol 		"http:" 			页面使用的协议。通常是"http:"或"https:" 
location.search 		"?q=javascript" 	URL 的查询字符串。这个字符串以问号开头 
location.username 		"foouser" 			域名前指定的用户名 
location.password 		"barpassword" 		域名前指定的密码 
location.origin 		"http://www.wrox.com" 	URL 的源地址。只读

	1）获取查询字符串
    	let getQueryStringArgs = function() { 
            // 取得没有开头问号的查询字符串 
            let qs = (location.search.length > 0 ? location.search.substring(1) : ""), 
            // 保存数据的对象 
            args = {}; 
            // 把每个参数添加到 args 对象 
            for (let item of qs.split("&").map(kv => kv.split("="))) {
                let name = decodeURIComponent(item[0]),
                    value = decodeURIComponent(item[1]); 
                if (name.length) { 
                    args[name] = value; 
                } 
            }
		    return args;
        }
	2）URLSearchParams 提供了一组标准API方法，通过它可以检查和修改查询字符串
    
    3）操作地址
    	assign(URL)  修改浏览器地址，刷新URL同时在浏览器历史记录中增加一条记录
        location.href = URL（最常见） | window.location = URL 会同样调用 assign()方法
        replace(URL) 导航到新页面，但不会怎加历史记录
        reload() 页面重载，默认会使用浏览器上次缓存，传参 reload(true) 强行从服务器重新加载
        		位于reload调用之后的代码 可能不会执行
                
        修改 location 对象的属性也会修改当前加载的页面。其中，hash、search、hostname、pathname 和 port 属性被设置为新值之后都会修改当前 URL，如下面的例子所示：
		// 假设当前 URL 为 http://www.wrox.com/WileyCDA/ 
        // 把 URL 修改为 http://www.wrox.com/WileyCDA/#section1 
        location.hash = "#section1"; 
        // 把 URL 修改为 http://www.wrox.com/WileyCDA/?q=javascript 
        location.search = "?q=javascript"; 
        // 把 URL 修改为 http://www.somewhere.com/WileyCDA/ 
        location.hostname = "www.somewhere.com"; 
        // 把 URL 修改为 http://www.somewhere.com/mydir/ 
        location.pathname = "mydir"; 
        // 把 URL 修改为 http://www.somewhere.com:8080/WileyCDA/ 
        location.port = 8080; 
        除了 hash 之外，只要修改 location 的一个属性，就会导致页面重新加载新 URL。

	【注意】修改 hash 的值会在浏览器历史中增加一条新记录。在早期的 IE 中，点击“后退” 和“前进”按钮不会更新 hash 属性，只有点击包含散列的 URL 才会更新 hash 的值。
	4、作为一个特例，片段标识符#top会让浏览器跳到文档顶部（假设没有元素有id="top"属性）
    location = '#top'

    
 -navigator对象
	
	属性/方法 					说 明 
    activeVrDisplays 	返回数组，包含 ispresenting 属性为 true 的 VRDisplay 实例 
    appCodeName 		即使在非 Mozilla 浏览器中也会返回"Mozilla" 
    appName 			浏览器全名 
    appVersion 			浏览器版本。通常与实际的浏览器版本不一致 
    battery 			返回暴露 Battery Status API 的 BatteryManager 对象 
    buildId 	浏览器的构建编号 connection 返回暴露 Network Information API 的 NetworkInformation 对象 	 cookieEnabled 		返回布尔值，表示是否启用了 cookie 
    credentials 		返回暴露 Credentials Management API 的 CredentialsContainer 对象 
    deviceMemory 		返回单位为 GB 的设备内存容量 
    doNotTrack 			返回用户的“不跟踪”（do-not-track）设置 
    geolocation 		返回暴露 Geolocation API 的 Geolocation 对象 
    getVRDisplays() 	返回数组，包含可用的每个 VRDisplay 实例 
    getUserMedia() 		返回与可用媒体设备硬件关联的流 
    hardwareConcurrency 返回设备的处理器核心数量 
    javaEnabled 		返回布尔值，表示浏览器是否启用了 Java 
    language 			返回浏览器的主语言 
    languages 			返回浏览器偏好的语言数组
    locks 				返回暴露 Web Locks API 的 LockManager 对象 
    mediaCapabilities 	返回暴露 Media Capabilities API 的 MediaCapabilities 对象 
    mediaDevices 		返回可用的媒体设备 g
    maxTouchPoints 		返回设备触摸屏支持的最大触点数 
    mimeTypes 			返回浏览器中注册的 MIME 类型数组 
    onLine 				返回布尔值，表示浏览器是否联网 
    oscpu 				返回浏览器运行设备的操作系统和（或）CPU 
    permissions 		返回暴露 Permissions API 的 Permissions 对象 
    platform 			返回浏览器运行的系统平台 
    plugins 			返回浏览器安装的插件数组。在 IE 中，这个数组包含页面中所有<embed>元素 
    product 			返回产品名称（通常是"Gecko"） 
    productSub 			返回产品的额外信息（通常是 Gecko 的版本） 
    registerProtocolHandler() 将一个网站注册为特定协议的处理程序 
    requestMediaKeySystemAccess() 返回一个期约，解决为 MediaKeySystemAccess 对象 
    sendBeacon() 		异步传输一些小数据 
    serviceWorker 		返回用来与 ServiceWorker 实例交互的 ServiceWorkerContainer 
    share() 			返回当前平台的原生共享机制 
    storage 			返回暴露 Storage API 的 StorageManager 对象 
    userAgent 			返回浏览器的用户代理字符串 
    vendor 				返回浏览器的厂商名称 
    vendorSub 			返回浏览器厂商的更多信息 
    vibrate() 			触发设备振动 
    webdriver 			返回浏览器当前是否被自动化程序控制

	1）检测浏览器是否安装某个插件
    	// 插件检测，IE10 及更低版本无效 
    let hasPlugin = function(name) { 
        name = name.toLowerCase();
		for (let plugin of window.navigator.plugins){ 
            if (plugin.name.toLowerCase().indexOf(name) > -1){ 
                return true; 
            } 
        } 
        return false; 
    } 
    // 检测 Flash 
    alert(hasPlugin("Flash")); 
    // 检测 QuickTime 
    alert(hasPlugin("QuickTime"));
	
	// 在旧版本 IE 中检测插件 
	function hasIEPlugin(name) { 
        try {
            new ActiveXObject(name);
            return true; 
        } catch (ex) {
            return false; 
        } 
    } 
    // 检测 
    Flash alert(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"));
    // 检测 QuickTime 
    alert(hasIEPlugin("QuickTime.QuickTime"));

	
	// 在所有浏览器中检测 Flash 
    function hasFlash() { 
        var result = hasPlugin("Flash"); 
        if (!result){ 
            result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash"); 
        } 
        return result; 
    }

	【注意】 plugins 有一个 refresh()方法，用于刷新 plugins 属性以反映新安装的插件。 这个方法接收一个布尔值参数，表示刷新时是否重新加载页面。如果传入 true，则所有 包含插件的页面都会重新加载。否则，只有 plugins 会更新，但页面不会重新加载。

	2）注册处理程序
    	registerProtocolHandler()方法，必须传入 3 个参数：要处理的协议（如"mailto"或 "ftp"）、处理该协议的 URL，以及应用名称。比如，要把一个 Web 应用程序注册为默认邮件客户端， 可以这样做：

		navigator.registerProtocolHandler("mailto", "http://www.somemailclient.com?cmd=%s", "Some Mail Client");

	
screen对象
    	很少使用，纯粹是客户端能力信息，每个浏览器暴露不同信息
        属 性 								说 明 
        availHeight 			屏幕像素高度减去系统组件高度（只读） 
        availLeft 				没有被系统组件占用的屏幕的最左侧像素（只读） 
        availTop 				没有被系统组件占用的屏幕的最顶端像素（只读） 
        availWidth 				屏幕像素宽度减去系统组件宽度（只读） 
        colorDepth 				表示屏幕颜色的位数；多数系统是 32（只读） 
        height 					屏幕像素高度 
        left 					当前屏幕左边的像素距离 
        pixelDepth 				屏幕的位深（只读） 
        top 					当前屏幕顶端的像素距离 
        width 					屏幕像素宽度 
        orientation 			返回 Screen Orientation API 中屏幕的朝向

history对象
    	history 对象表示当前窗口首次使用以来用户的导航历史记录。因为 history 是 window 的属性， 所以每个 window 都有自己的 history 对象。出于安全考虑，这个对象不会暴露用户访问过的 URL， 但可以通过它在不知道实际 URL 的情况下前进和后退。
	1）导航
    	-go(x)  x为1：前进一页，x为-1：后退一页。在旧浏览器中，x可以为字符串，跳到历史记录包含该字符串的记录中，可能是前进，后退或啥也不做
		-back()    后退一页
    	-forward()	前进一页
		-history.length  历史记录的条数
     【注意】 如果页面 URL 发生变化，则会在历史记录中生成一个新条目。对于 2009 年以来发布的主流浏览器，这包括改变 URL 的散列值（因此，把 location.hash 设置为一个新 值会在这些浏览器的历史记录中增加一条记录）。这个行为常被单页应用程序框架用来模 拟前进和后退，这样做是为了不会因导航而触发页面刷新。

	2）历史状态管理
        hashchange 在页面 URL 的散列值变化时触发
        history.pushState(state对象,状态标题,相对URL); 添加一个新的历史记录
        history.replaceState(state对象,状态标题,相对URL);会替换当前历史状态
			state对象限制 常在 500KB～1MB以内

        监听pushState事件
        window.addEventListener('popstate',(event)=>{
            let state = event.state;
            if(state){//第一个页面加载时的状态是 null
                processState(saate)
            }
        })

        histroy.state ;获取当前状态对象
        replaceState() 传入与 pushState() 一样的两个参数可以更新当前状态。

结构化克隆算法
	history.pushState()方法不使用JSON.stringify()来序列化状态数据，而是使用一种更可靠的序列化技术叫作“结构化克隆算法”。这个算法由HTML标准定义
    结构化克隆算法可以涵盖JSON.stringify()能够序列化的一切值，除此之外，它还支持很多其他JavaScript类型的序列化。比如Map、Set、Date、RegExp和定型数组。而且，它还能处理包含循环引用的数据结构和类
    。在克隆对象时，它不会复制原型对象、获取函数和设置函数，也不会复制不可枚举的属性。尽管结构化克隆算法可以克隆大多数内置JavaScript类型，但不能复制宿主环境定义的类型，例如文档的Element对象。
	这意味着传给history.pushState()的状态对象不必局限于能够被JSON.stringify()序列化的对象、数组和原始值。但要注意的是，如果传入自己定义的某个类的实例，则该实例被当作普通JavaScript对象被序列化，因此会丢掉其原型。
```

