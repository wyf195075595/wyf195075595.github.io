---
title: 第十四章 DOM
date: 2022-06-17 08:23:10
tags: 红宝书笔记
categories: js
---

### Node类型
	DOM Level 1 描述了名为 Node 的接口，这个接口是所有 DOM 节点类型都必须实现的。Node 接口 在 JavaScript中被实现为 Node 类型，在除 IE之外的所有浏览器中都可以直接访问这个类型。在 JavaScript 中，所有节点类型都继承 Node 类型，因此所有类型都共享相同的基本属性和方法。
    
<!--more-->
```js


节点层级
	document节点表示每个文档节点的根节点，根节点的唯一子节点是html元素（文档元素），其他元素都存在这个元素内
Node节点共12种类型：

	1、Document类型  
         nodeType 等于 9； 
         nodeName 值为"#document"； 
         nodeValue 值为 null； 
         parentNode 值为 null； 
         ownerDocument 值为 null；
        文档信息
             document.documentElement：html元素
             document.body: body元素
             document.head: head元素
             document.URL: 完整URL
             document.domain: 域名	可以解决因子域不同的跨域问题，只能设置一次，再次修改会报错
             document.referrer: 来源
             document.forms 包含文档中所有<form>元素。 
             document.images 包含文档中所有<img>元素。 
             document.links 包含文档中所有带 href 属性的<a>元素。

		兼容性检测：
        	document.implementation是一个对象
			{
                createDocument,
                createDocumentType,
                createHTMLDocument,
                hasFeature
             }
			document.implementation.hasFeature(特性名称，DOM版本)废弃了 草
			
	2、Element类型
         nodeType 等于 1； 
         nodeName 值为元素的标签名； 
         nodeValue 值为 null； 
         parentNode 值为 Document 或 Element 对象；

    	操作属性
        	-getAttribute 一般不用
        	-setAttribute
        	-removeAttribute
        
	3、Text
         nodeType 等于 3； 
         nodeName 值为"#text"； 
         nodeValue 值为节点中包含的文本； 
         parentNode 值为 Element 对象； 
         不支持子节点。
         appendData(text)，向节点末尾添加文本 text； 
         deleteData(offset, count)，从位置 offset 开始删除 count 个字符； 
         insertData(offset, text)，在位置 offset 插入 text； 
         replaceData(offset, count, text)，用 text 替换从位置 offset 到 offset + count 的 文本； 
         splitText(offset)，在位置 offset 将当前文本节点拆分为两个文本节点； 
         substringData(offset, count)，提取从位置 offset 到 offset + count 的文本。 除了这些方法，还可以通过 length 属性获取文本节点中包含的字符数量。这个值等于 nodeValue. length 和 data.length。

		创建文本节点
        document.createTextNode() 接收插入的文本
        拆分文本节点
    	ele.firstChild.splitText(5); //传入截断位置    
    
        
	4、Comment 类型
         nodeType 等于 8； 
         nodeName 值为"#comment"； 
         nodeValue 值为注释的内容； 
         parentNode 值为 Document 或 Element 对象； 
         不支持子节点。
		eg:
			<div id="myDiv"><!-- A comment --></div> 
		这里的注释是<div>元素的子节点，这意味着可以像下面这样访问它： 
        let div = document.getElementById("myDiv"); 
        let comment = div.firstChild; 
        alert(comment.data); // "A comment"

		创建注释节点方法
        document.createComment('A comment');
	5、CDATASEction
         nodeType 等于 4； 
         nodeName 值为"#cdata-section"； 
         nodeValue 值为 CDATA 区块的内容； 
         parentNode 值为 Document 或 Element 对象； 
         不支持子节点。
	    CDATA模块只在XML文档中有有效
        
        
     6、DocumentType
         nodeType 等于 10； 
         nodeName 值为文档类型的名称； 
         nodeValue 值为 null； 
         parentNode 值为 Document 对象； 
         不支持子节点。
		document.doctype; //h5中 <!DOCTYPE html> 
		document.doctype.name; //h5中  html 

     7、DocumentFragment
         nodeType 等于 11； 
         nodeName 值为"#document-fragment"； 
         nodeValue 值为 null； 
         parentNode 值为 null； 
         子节点可以是 Element、ProcessingInstruction、Comment、Text、CDATASection 或 EntityReference。
		创建文档片段
        document.createDocumentFragment();
		文档片段继承了所有文档类型具备的可以执行 DOM操作方法，添加至文档片段的存在与文档树的元素会从文档树中移除，文档片段被添加到其他元素时 其中元素会从文档片段 移除
        
     8、Attr
         nodeType 等于 2； 
         nodeName 值为属性名； 
         nodeValue 值为属性值； 
         parentNode 值为 null； 
         在 HTML 中不支持子节点； 
         在 XML 中子节点可以是 Text 或 EntityReference。
		属性是节点去不被认为是DOM树的一部分。Attr节点很少直接被引用，通常开发者使用setAttribute,getAttribute,removeAttribute 。 方法操作属性
     	
     
     
	类型判断 
         -ele.nodeType 返回（1-12）
         -ele.nodeName 元素标签名称
         -ele.nodeValue 始终为null
    节点查询
    	-previousSibling 前一个节点
		-nextSibling	后一个节点
		-firstChild 	childNodes中第一个子节点
		-lastChild 		childNodes中最后一个子节点
		-ownerDocument   指向代表整个文档的文档节点的指针
        -parentNode   此节点的父节点（并非所有节点都有父节点）

	节点的操做
    	-appendChild(newNode)	添加新的节点（传入文档已有节点，这个节点会转换到新的位置）
        -insertBefore(newNode, someNode) 添加至 someNode 前一个位置
    	-replaceChild(newNode, someNode) 被替换的节点会从文档树中移除	
        -removeChild(someNode)		接收要移除的节点
    	-cloneNode()   接收一个Boolean值，false只复制此节点 ，true复制此节点即其children
    	-normalize()
			由于解析器实现的差异或 DOM 操作等原因，可能会出现并不包含文本的文本节点，或者文本节点
之间互为同胞关系。在节点上调用 normalize()方法会检测这个节点的所有后代，从中搜索上述两种 情形。如果发现空文本节点，则将其删除；如果两个同胞节点是相邻的，则将其合并为一个文本节点。


```

### DOM编程

```js
操作DOM

1、创建元素
	document.createElement()方法创建一个新元素
    
2、添加文本或其他元素
	-append()和prepend()接收任意多个参数，这些参数可以是Node对象或字符串。
    	append()把参数添加到孩子列表的末尾。
    	prepend()把参数添加到孩子列表的开头。
    -如果需要在包含元素的孩子列表中间插入 Element 或 Text节点.需要获取一个同辈节点的引用调用下面方法
    	before()在该同辈前面插入新内容
    	after()在该同辈后面插入新内容

	【注意】要注意的是，元素只能被插入到文档中的一个地方。如果某个元素已经在文档中了，你又把它插入到了其他地方，那它会转移到新位置，而不会复制一个新的过去

	-创建元素副本
		cloneNode()方法
3、删除，替换节点
    replaceWith()替换它。
    remove()不接收参

	DOM API也定义了插入和删除内容的老一代方法。比如，appendChild()、insertBefore()、replaceChild()和removeChild()，都比这里介绍的方法难用，因此不应该再使用它们了。







动态脚本
//加载js文件
function loadScript(url) { 
    let script = document.createElement("script"); 
    script.src = url; 
    document.body.appendChild(script); 
}
//加载js代码 ， 相当于将源代码传给eval()方法
function loadScriptString(code){ 
    var script = document.createElement("script"); 
    script.type = "text/javascript"; try { 
        script.appendChild(document.createTextNode(code)); 
    } catch (ex){ 
        script.text = code; 
    } 
    document.body.appendChild(script); 
}

动态样式
//加载样式文件
function loadStyles(url){ 
    let link = document.createElement("link"); 
    link.rel = "stylesheet"; 
    link.type = "text/css"; 
    link.href = url; 
    let head = document.getElementsByTagName("head")[0]; 
    head.appendChild(link); 
}
//加载样式代码
function loadStyleString(css){ 
    let style = document.createElement("style"); 
    style.type = "text/css"; try{ 
        style.appendChild(document.createTextNode(css)); 
    } catch (ex){ 
        style.styleSheet.cssText = css; 
    } 
    let head = document.getElementsByTagName("head")[0]; 
    head.appendChild(style); 
}

【注意】 对于 IE，要小心使用 styleSheet.cssText。如果重用同一个<style>元素并设 置该属性超过一次，则可能导致浏览器崩溃。同样，将 cssText 设置为空字符串也可能 导致浏览器崩溃。

操作表格
    <table>元素添加了以下属性和方法： 
     caption，指向<caption>元素的指针（如果存在）； 
     tBodies，包含<tbody>元素的 HTMLCollection； 
     tFoot，指向<tfoot>元素（如果存在）； 
     tHead，指向<thead>元素（如果存在）； 
     rows，包含表示所有行的 HTMLCollection； 
     createTHead()，创建<thead>元素，放到表格中，返回引用； 
     createTFoot()，创建<tfoot>元素，放到表格中，返回引用； 
     createCaption()，创建<caption>元素，放到表格中，返回引用； 
     deleteTHead()，删除<thead>元素； 
     deleteTFoot()，删除<tfoot>元素； 
     deleteCaption()，删除<caption>元素； 
     deleteRow(pos)，删除给定位置的行； 
     insertRow(pos)，在行集合中给定位置插入一行。 <tbody>元素添加了以下属性和方法： 
     rows，包含<tbody>元素中所有行的 HTMLCollection；
     deleteRow(pos)，删除给定位置的行； 
     insertRow(pos)，在行集合中给定位置插入一行，返回该行的引用。 <tr>元素添加了以下属性和方法： 
     cells，包含<tr>元素所有表元的 HTMLCollection； 
     deleteCell(pos)，删除给定位置的表元； 
     insertCell(pos)，在表元集合给定位置插入一个表元，返回该表元的引用。

NodeList
	理解 NodeList 对象和相关的 NamedNodeMap、HTMLCollection，是理解 DOM 编程的关键。这 3 个集合类型都是“实时的”，意味着文档结构的变化会实时地在它们身上反映出来，因此它们的值始终代表最新状态

	//无限循环
    for (let div of document.getElementsByTagName("div")){ 
        let newDiv = document.createElement("div"); 
        document.body.appendChild(newDiv); 
    }



MutationObserver接口 
	//实例化一个 MutationObserver
	let observer = new MutationObserver((mutationRecord) => console.log('body changed'));
	let mutationObserverInit = { attributes: true };

	//传入 要监视的元素，MutationObserverInit.只要元素不被垃圾回收，会一直监视，
	observer.observe(document.body, mutationObserverInit);//可以多次监听
	document.body.background = '#333';
	observer.disconnect();
	//disconnect()之后，不仅会停止此后变化事件的回调，也会抛弃已经加入任务队列要异步执行的回调
	document.body.background = '#fff';//没有日志
	
	console.log(observer.takeRecords());//返回记录队列，并清空MutationObserver的队列记录
	
	


	//反映具体变化
	MutationRecord{
        [
            {
            	addedNodes: NodeList [],
                attributeName: "background",//被修改的属性名
                attributeNamespace: null,//
                nextSibling: null,//对于childrenList类型变化，返回节点的后一个同胞
                oldValue: null,
                previousSibling: null,//对于childrenList类型变化，返回节点的前一个同胞
                removedNodes: NodeList [],//对于childrenList类型变化，删除的节点的NodeList
                target: body.alternate-logo.non-google-page.win,//修改影响的目标节点
                type: "attributes"//变化的类型"attributes"、"characterData"或"childList"

            }
        ],
        length: 1
    }
	-MutationObserverInit 与观察范围

	属 性 										说 明 
    subtree 		布尔值，表示除了目标节点，是否观察目标节点的子树（后代） 如果是 false，则只观察	
    				目标节点的变化；如果是 true，则观察目标节点及其整个子树 默认为 false 

	attributes 		 布尔值，表示是否观察目标节点的属性变化 默认为 false 

	attributeFilter  字符串数组，表示要观察哪些属性的变化 把这个值设置为 true 也会将 attributes 的
    				值转换为 true 默认为观察所有属性 
                    
    attributeOldValue 		布尔值，表示 MutationRecord 是否记录变化之前的属性值 把这个值设置为
     						true 也会将 attributes 的值转换为 true 默认为 false

	characterData 			布尔值，表示修改字符数据是否触发变化事件 默认为 false 	

	characterDataOldValue 	布尔值，表示 MutationRecord 是否记录变化之前的字符数据 把这个值设置为 						true 也会将 characterData 的值转换为 true 默认为 false 

	childList 				布尔值，表示修改目标节点的子节点是否触发变化事件 默认为 false


```

### DOM扩展

```js
Selectors API

1、querySelector()  接收css选择符参数 返回第一个匹配元素

2、querySelectorAll()  接收css选择符参数 返回全部匹配元素

3、matches()	接收css 选择符参数，如果匹配则返回true，否则false。用于检测上面两个方法是否有返回值,只会检查元素是否与选择符匹配。如果匹配，返回true；否则，返回false
	eg:如果ｅ是一个HTML 标题元素 返回 true
	function isHeading(e) {
        return e.matches('h1,h2,h3,h4,h5,h6')
    }
4、closest
	closest()从当前元素开始，沿DOM树向上匹配。
    eg: 在处理一个单击事件时，你可能想知道该事件是否发生在一个超链接上。事件对象会告诉你事件目标，但该目标也许是超链接的文本而非<a>标签本身。为此，可以让事件处理程序像这样查找最近的超链接：
	// 查找有href属性的最近的外围<a>标签
	let hyperlink = event.target.closest('a[href]');

元素遍历
	IE9 之前的版本不会把元素间的空格当成空白节点，而其他浏览器则会。这样就导致了 childNodes 和 firstChild 等属性上的差异。

 childElementCount，返回子元素数量（不包含文本节点和注释）； 
 firstElementChild，指向第一个 Element 类型的子元素（Element 版 firstChild）； 
 lastElementChild，指向最后一个 Element 类型的子元素（Element 版 lastChild）； 
 previousElementSibling ，指向前一个 Element 类型的同胞元素（ Element 版 previousSibling）； 
 nextElementSibling，指向后一个 Element 类型的同胞元素（Element 版 nextSibling）。

HTML5

css类扩展
1、getElementsByClassName()  这个方法只会返回以调用它的对象为根元素的子树中所有匹配的元素。

2、classList
	要操作类名，可以通过 className 属性实现添加、删除和替换。但 className 是一个字符串，修改后需要重新赋值 classList 才会生效
     add(value)，向类名列表中添加指定的字符串值 value。如果这个值已经存在，则什么也不做。 
     contains(value)，返回布尔值，表示给定的 value 是否存在。 
     remove(value)，从类名列表中删除指定的字符串值 value。 
     toggle(value)，如果类名列表中已经存在指定的 value，则删除；如果不存在，则添加。

3、焦点管理
	-document.activeElement	始终包含当前拥 有焦点的 DOM 元素。
    -document.hasFocus()	该方法返回布尔值，表示文档是否拥有焦点：用于确定用户是否在操作页面

    
HTMLDocument 扩展

1. readyState 属性
	document.readyState  用于判断文档是否加载完成
         loading，表示文档正在加载； 
         complete，表示文档加载完成。

2. compatMode 属性 :用于检测页面是 标准模式 还是 混杂模式
	标准模式下 document.compatMode 的值是"CSS1Compat"，
    而在混杂模式下， document.compatMode 的值是"BackCompat"：
	
    if (document.compatMode == "CSS1Compat"){ 
        console.log("Standards mode"); 
    } else { 
        console.log("Quirks mode"); 
    }
3. head 属性
	增加了 document.head 作为对 document.body 的补充

字符集属性

	characterSeet：用于指定字符集。默认 "UTF-16"

	document.characterSet = "UTF-8";

自定义数据属性
	data-xxx
	eg:  <div data-id="1" ></div>
		通过元素的 dataset.id 访问
     【注意】 data-my-name、data-My-Name 要通过 myName 来访问）。


插入标记
1、innerHTML
	在所有现代浏览器中，通过 innerHTML 插入的<script>标签是不会执行的。 IE8前 指定 defer就可以执行
	多数浏览器支持插入 style 标签元素
2、outerHTML 属性
	调用它的元素会被传入的 HTML 字符串经解释之后生成的 DOM 子树取代。
    
3、insertAdjacentHTML()与 insertAdjacentText()
	-insertAdjacentHTML() 方法将指定的文本解析为 Element 元素，并将结果节点插入到DOM树中的指定位置。它不会重新解析它正在使用的元素，因此它不会破坏元素内的现有元素。这避免了额外的序列化步骤，使其比直接使用innerHTML操作更快。
    -insertAdjacentText() 方法将一个给定的文本节点插入在相对于被调用的元素给定的位置。

     "beforebegin"，插入当前元素前面，作为前一个同胞节点； 
     "afterbegin"，插入当前元素内部，作为新的子节点或放在第一个子节点前面； 
     "beforeend"，插入当前元素内部，作为新的子节点或放在最后一个子节点后面； 
     "afterend"，插入当前元素后面，作为下一个同胞节点。

	eg:
		var d1 = document.getElementById('one')；
         d1.insertAdjacentHTML('afterend', '<div id="two">two</div>')；
        
		//　此时新的结构： <div id="one">one</div><div id="two">two</div>
    	
         document.head.insertAdjacentHTML(
         'beforeend',
         '<style>body{transform:rotate(180deg)}</style>'
         )
4、动画监听
	-过渡动画
    	首次触发过渡时 transitionrun
    	视觉发生变化时 transitionstart
    	动画完成时 transitionend
    -帧动画
		动画开始时触发 animationstart
    	动画重复时触发（不包括最后一次） animationiteration
    	动完成时触发 animationend
        
scrollIntoView()
	DOM 规范中没有涉及的一个问题是如何滚动页面中的某个区域。为填充这方面的缺失，不同浏览 器实现了不同的控制滚动的方式。在所有这些专有方法中，HTML5 选择了标准化 scrollIntoView()。

     alignToTop 是一个布尔值。 
    - true：窗口滚动后元素的顶部与视口顶部对齐。 
    - false：窗口滚动后元素的底部与视口底部对齐。 
     scrollIntoViewOptions 是一个选项对象。 
    - behavior：定义过渡动画，可取的值为"smooth"和"auto"，默认为"auto"。 
    - block：定义垂直方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默 认为 "start"。 
    - inline：定义水平方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默 认为 "nearest"。 
     不传参数等同于 alignToTop 为 true。


    
专有扩展 
    尚未被标准化，由部分浏览器专有和采用
        
     1、children属性
     	IE9 之前的版本与其他浏览器在处理空白文本节点上的差异导致了 children 属性的出现。 children 属性是一个 HTMLCollection，只包含元素的 Element 类型的子节点。
        
     2、contains()
		如果目标节点是被搜索节点的后代，contains()方法返回true，在要搜素的祖先元素上调用
        
        此外 compareDocumentPosition()方法也可以确定节点间的关系。返回掩码
        
       掩 码 								节点关系 
        0x1 						断开（传入的节点不在文档中） 
        0x2 						领先（传入的节点在 DOM 树中位于参考节点之前） 
        0x4 						随后（传入的节点在 DOM 树中位于参考节点之后） 
        0x8 						包含（传入的节点是参考节点的祖先） 
        0x10 						被包含（传入的节点是参考节点的后代）

		let result = document.documentElement.compareDocumentPosition(document.body);
		console.log(!!(result & 0x10));
		以上代码执行后 result 的值为 20（或 0x14，其中 0x4 表示“随后”，加上 0x10“被包含”）。

插入标记
    1、innerText 属性 
    	移除元素所有后代并插入一个包含该值的文本节点。 它会对HTML语法字符（ > < "" & ）进行转译
    2、outerText 属性
		与innerText 类似，读一样 ，写不同，作用范围包含调用它的节点		
	
滚动
	scrollIntoViewIfNeeded（）
    // 如果不可见，则将元素可见 
    document.images[0].scrollIntoViewIfNeeded();


```

DOM2 / DOM3

```js
XML命名空间

样式
	css 中的以 - 分割的属性  在js中 是 驼峰命名法。
	float 是js保留字 所以不能作为属性名。 js中用 cssFloat 代替
	如：background-image  ->   backgroundImage
	
	在标准模式下，所有尺寸都必须包含单位。在混杂模式下，可以把 style.width 设置为"20"，相当于"20px"。如果是在标准模式下，把 style.width 设置为"20"会被 忽略，因为没有单位。实践中，最好一直加上单位。

	1、DOM样式属性和方法 只作用于行内样式，不做用于类样式
         cssText，包含 style 属性中的 CSS 代码。 最实用 
         length，应用给元素的 CSS 属性数量。
         getPropertyValue(propertyName)，返回属性 propertyName 的字符串值。 
         item(index)，返回索引为 index 的 CSS 属性名。 
         removeProperty(propertyName)，从样式中删除 CSS 属性 propertyName。 
         setProperty(propertyName, value, priority)，设置 CSS 属性 propertyName 的值为 value，priority 是"important"或空字符串。
         getPropertyPriority(propertyName)，如果 CSS 属性 propertyName 使用了!important 则返回"important"，否则返回空字符串。
         parentRule，表示 CSS 信息的 CSSRule 对象。
        
	2、计算属性 （只读，返回值 随浏览器格式可能不同）
		document.defaultView.getComputedStyle(ele, null) 第二参数 null 表示不查询伪类元素
		
       -计算样式的属性是绝对值，百分比和点等相对单位都被转换成了绝对值。
       -值为颜色的属性将以“rgb()”或“rgb()”格式返回
       -简写属性不会被计算，只有它们代表的基础属性会被计算。例如，不能查询margin属性，而要查询marginLeft、marginTop等
	   -
        
操作样式表
	CSSStyleSheet 类型表示 CSS 样式表，包括使用<link>元素和通过<style>元素定义的样式表。
	
	属性
	 disabled，布尔值，表示样式表是否被禁用了（这个属性是可读写的，因此将它设置为 true 会禁用样式表）。 
	 href，如果是使用<link>包含的样式表，则返回样式表的 URL，否则返回 null。 
	 media，样式表支持的媒体类型集合，这个集合有一个 length 属性和一个 item()方法，跟所 有 DOM 集合一样。同样跟所有 DOM 集合一样，也可以使用中括号访问集合中特定的项。如果 样式表可用于所有媒体，则返回空列表。 
	 ownerNode，指向拥有当前样式表的节点，在 HTML 中要么是<link>元素要么是<style>元素 （在 XML 中可以是处理指令）。如果当前样式表是通过@import 被包含在另一个样式表中，则这 个属性值为 null。
     parentStyleSheet，如果当前样式表是通过@import 被包含在另一个样式表中，则这个属性 指向导入它的样式表。 
     title，ownerNode 的 title 属性。 
     type，字符串，表示样式表的类型。对 CSS 样式表来说，就是"text/css"。

	-document.styleSheets  表示文档中可用的的样式表集合，length属性保存着文档中样式表数量
	
	方法
	 cssRules，当前样式表包含的样式规则的集合。 
	 ownerRule，如果样式表是使用@import 导入的，则指向导入规则；否则为 null。 
	 deleteRule(index)，在指定位置删除 cssRules 中的规则。 
	 insertRule(rule, index)，在指定位置向 cssRules 中插入规则。
	
    eg1: // 这个函数实现’light‘ 和 'dark' 主题的切换
	// 给 <link rel="stylesheet" id="light-theme / dark-theme">
	function toggleTheme() {
        let lightTheme = document.querySelector('#light-theme');
        let darkTheme = document.querySelector('#dark-theme');
        if(darkTheme.disabled) { // 当前是浅色主题，切换到深色主题
            lightTheme.disabled = true;
            darkTheme.disabled = false;
        } else { // 当前是深色主题，切换到浅色主题
            lightTheme.disabled = false;
            darkTheme.disabled = true;
        }
    }

	css规则
	 cssText，返回整条规则的文本。这里的文本可能与样式表中实际的文本不一样，因为浏览器 内部处理样式表的方式也不一样。Safari 始终会把所有字母都转换为小写。 
	 parentRule，如果这条规则被其他规则（如@media）包含，则指向包含规则，否则就是 null。 
	 parentStyleSheet，包含当前规则的样式表。 
	 selectorText，返回规则的选择符文本。这里的文本可能与样式表中实际的文本不一样，因为 浏览器内部处理样式表的方式也不一样。这个属性在 Firefox、Safari、Chrome 和 IE 中是只读的， 在 Opera 中是可以修改的。 
	 style，返回 CSSStyleDeclaration 对象，可以设置和获取当前规则中的样式。 
	 type，数值常量，表示规则类型。对于样式规则，它始终为 1。 
	
		在这些属性中，使用最多的是 cssText、selectorText 和 style。cssText 属性与 style.cssText 类似，不过并不完全一样。前者包含选择符文本和环绕样式声明的大括号，而后者则只包含样式声明（类 似于元素上的 style.cssText）。此外，cssText 是只读的，而 style.cssText 可以被重写。


	eg:
	let sheet = document.styleSheets[0]; 
	let rules = sheet.cssRules || sheet.rules; // 取得规则集合 
	let rule = rules[0]; // 取得第一条规则 
	rule.style.backgroundColor = "red"
	sheet.insertRule("body { background-color: silver }", 0); // 新增样式
	sheet.deleteRule(0); // 删除样式


元素尺寸
	1、偏移尺寸
		 offsetHeight，元素在垂直方向上占用的像素尺寸，包括它的高度、水平滚动条高度（如果可 见）和上、下边框的高度。 
		 offsetLeft，元素左边框外侧距离包含元素左边框内侧的像素数。 
		 offsetTop，元素上边框外侧距离包含元素上边框内侧的像素数。 
		 offsetWidth，元素在水平方向上占用的像素尺寸，包括它的宽度、垂直滚动条宽度（如果可 见）和左、右边框的宽度。

```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/offset.png)

```js
//获取元素 顶部偏移量
function getElementTop(element) { 
    let actualTop = element.offsetTop; 
    let current = element.offsetParent; 
    while (current !== null) { 
        actualTop += current.offsetTop; 
        current = current.offsetParent; 
    } 
    return actualTop; 
}
//获取元素 左部偏移量
function getElementLeft(element) { 
    let actualLeft = element.offsetLeft; 
    let current = element.offsetParent; 
    while (current !== null) { 
        actualLeft += current.offsetLeft; 
        current = current.offsetParent; 
    } 
    return actualLeft; 
}


2、客户端尺寸
	元素的客户端尺寸（client dimensions）包含元素内容及其内边距所占用的空间。客户端尺寸只有两 个相关属性：clientWidth 和 clientHeight。其中，clientWidth 是内容区宽度加左、右内边距宽 度，clientHeight 是内容区高度加上、下内边距高度。
```

<img src="https://raw.githubusercontent.com/wyf195075595/images/main/blog/client.png"  />

```js
3、滚动尺寸
	最后一组尺寸是滚动尺寸（scroll dimensions），提供了元素内容滚动距离的信息。有些元素，比如 <html>无须任何代码就可以自动滚动，而其他元素则需要使用 CSS 的 overflow 属性令其滚动。滚动 尺寸相关的属性有如下 4 个。 
	 scrollHeight，没有滚动条出现时，元素内容的总高度。 
	 scrollLeft，内容区左侧隐藏的像素数，设置这个属性可以改变元素的滚动位置。 
	 scrollTop，内容区顶部隐藏的像素数，设置这个属性可以改变元素的滚动位置。 
	 scrollWidth，没有滚动条出现时，元素内容的总宽度。


```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/scroll.png)

```js
	scrollWidth 和 scrollHeight 与 clientWidth 和 clientHeight 之间的关系在不需要滚动的 文档上是分不清的。如果文档尺寸超过视口尺寸，则在所有主流浏览器中这两对属性都不相等， scrollWidth 和 scollHeight 等于文档内容的宽度，而 clientWidth 和 clientHeight 等于视口 的大小。 
	scrollLeft 和 scrollTop 属性可以用于确定当前元素滚动的位置，或者用于设置它们的滚动位

4、确定元素尺寸
	getBoundingClientRect()方法，放回一个DOMRect对象，包含6个属性
    

```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/getBoundingReact.png)

```js
遍历
	NodeIterator 和 TreeWalker——从某个起点开始执行对 DOM 结构的深度优先遍历。

	如下：以document为根节点的深度优先遍历
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/sdblyx.png)

```js
1、NodeIterator
    document.createNodeIterator()方法创建实例 ， 接收4个参数
     root，作为遍历根节点的节点。 
     whatToShow，数值代码，表示应该访问哪些节点。 
     filter，NodeFilter 对象或函数，表示是否接收或跳过特定节点。 
     entityReferenceExpansion，布尔值，表示是否扩展实体引用。这个参数在 HTML 文档中没 有效果，因为实体引用永远不扩展。


	-whatToShow 参数是一个位掩码，通过应用一个或多个过滤器来指定访问哪些节点。这个参数对应 的常量是在 NodeFilter 类型中定义的。 
     NodeFilter.SHOW_ALL，所有节点。 
     NodeFilter.SHOW_ELEMENT，元素节点。 
     NodeFilter.SHOW_ATTRIBUTE，属性节点。由于 DOM 的结构，因此实际上用不上。 
     NodeFilter.SHOW_TEXT，文本节点。 
     NodeFilter.SHOW_CDATA_SECTION，CData 区块节点。不是在 HTML 页面中使用的。 
     NodeFilter.SHOW_ENTITY_REFERENCE，实体引用节点。不是在 HTML 页面中使用的。 
     NodeFilter.SHOW_ENTITY，实体节点。不是在 HTML 页面中使用的。 
     NodeFilter.SHOW_PROCESSING_INSTRUCTION，处理指令节点。不是在 HTML 页面中使用的。 
     NodeFilter.SHOW_COMMENT，注释节点。 
     NodeFilter.SHOW_DOCUMENT，文档节点。 
     NodeFilter.SHOW_DOCUMENT_TYPE，文档类型节点。 
     NodeFilter.SHOW_DOCUMENT_FRAGMENT，文档片段节点。不是在 HTML 页面中使用的。 
     NodeFilter.SHOW_NOTATION，记号节点。不是在 HTML 页面中使用的。

    这些值除了 NodeFilter.SHOW_ALL 之外，都可以组合使用。比如，可以像下面这样使用按位或 操作组合多个选项： 
    let whatToShow = NodeFilter.SHOW_ELEMENT | NodeFilter.SHOW_TEXT;
	
	-filter参数用于过滤节点，如果节点应该访问就 返回 NodeFilter.SHOW_ACCEPT,NodeFilter.FILTER_SKIP。

		作为节点过滤器的函数。NodeFilter 对象只有一个方法 acceptNode()，
        
	let filter = { acceptNode(node) {
        return node.tagName.toLowerCase() == "p" 
            ? NodeFilter.FILTER_ACCEPT 
        	:NodeFilter.FILTER_SKIP;
   		}
     };

	
	let iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);

	NodeIterator 的两个主要方法是 nextNode()和 previousNode()。
    -nextNode()方法在DOM中以深度遍历优先方式前进一步,根节点调用时返回 null
    -previousNode()方法 遍历中后退一步，最后一个节点时返回 null


2、TreeWalker  （通常取代 NodeIterator）
	TreeWalker 是 NodeIterator 的高级版。除了包含同样的 nextNode()、previousNode()方法， TreeWalker 还添加了如下在 DOM 结构中向不同方向遍历的方法。
    
     parentNode()，遍历到当前节点的父节点。 
     firstChild()，遍历到当前节点的第一个子节点。 
     lastChild()，遍历到当前节点的最后一个子节点。 
     nextSibling()，遍历到当前节点的下一个同胞节点。 
     previousSibling()，遍历到当前节点的上一个同胞节点。

    let div = document.getElementById("div1"); 
    let walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, null, false);

    walker.firstChild(); 
    walker.nextSibling(); 
    let node = walker.firstChild(); 
    while (node !== null) { 
        console.log(node.tagName); 
        node = walker.nextSibling(); 
    }

	walker.currentNode = document.body; //无论那种遍历方法 都可以这样 修改起点


	不同的是，节点过滤器（filter）除了可以返回 NodeFilter.FILTER_ACCEPT 和 NodeFilter. FILTER_SKIP，还可以返回 NodeFilter.FILTER_REJECT。
	在使用 NodeIterator 时，NodeFilter. FILTER_SKIP 和 NodeFilter.FILTER_REJECT 是一样的。
    但在使用 TreeWalker 时，NodeFilter. FILTER_SKIP 表示跳过节点，访问子树中的下一个节点，而 NodeFilter.FILTER_REJECT 则表示跳 过该节点以及该节点的整个子树。
    例如，如果把前面示例中的过滤器函数改为返回 NodeFilter. FILTER_REJECT（而不是 NodeFilter.FILTER_SKIP），则会导致遍历立即返回，不会访问任何节点。 这是因为第一个返回的元素是<div>，其中标签名不是"li"，因此过滤函数返回 NodeFilter.FILTER_ REJECT，表示要跳过整个子树。因为<div>本身就是遍历的根节点，所以遍历会就此结束。


范围

Range 接口表示一个包含节点与文本节点的一部分的文档片段。

	可以用 Document 对象的 Document.createRange 方法创建 Range，也可以用 Selection 对象的 getRangeAt 方法获取 Range。另外，还可以通过 Document 对象的构造函数 Range() 来得到 Range。
    
创建范围（只能在创建它的文档关联，不能在其他文档中使用）
let range = document.createRange();
属性
 startContainer，范围起点所在的节点（选区中第一个子节点的父节点）。 
 startOffset，范围起点在 startContainer 中的偏移量。如果 startContainer 是文本节 点、注释节点或 CData 区块节点，则 startOffset 指范围起点之前跳过的字符数；否则，表示 范围中第一个节点的索引。 
 endContainer，范围终点所在的节点（选区中最后一个子节点的父节点）。 
 endOffset，范围起点在 startContainer 中的偏移量（与 startOffset 中偏移量的含义相同）。 
 commonAncestorContainer，文档中以startContainer和endContainer为后代的最深的节点。 这些属性会在范围被放到文档中特定位置时获得相应的值。

方法
 setStartBefore(refNode)，把范围的起点设置到 refNode 之前，从而让 refNode 成为选 区的第一个子节点。startContainer 属性被设置为 refNode.parentNode，而 startOffset 属性被设置为 refNode 在其父节点 childNodes 集合中的索引。 
 setStartAfter(refNode)，把范围的起点设置到 refNode 之后，从而将 refNode 排除在选 区之外，让其下一个同胞节点成为选区的第一个子节点。startContainer 属性被设置为 refNode.parentNode，startOffset 属性被设置为 refNode 在其父节点 childNodes 集合 中的索引加 1。 
 setEndBefore(refNode)，把范围的终点设置到 refNode 之前，从而将 refNode 排除在选区 之外、让其上一个同胞节点成为选区的最后一个子节点。endContainer 属性被设置为 refNode. parentNode，endOffset 属性被设置为 refNode 在其父节点 childNodes 集合中的索引。 
 setEndAfter(refNode)，把范围的终点设置到 refNode 之后，从而让 refNode 成为选区的 最后一个子节点。endContainer 属性被设置为 refNode.parentNode，endOffset 属性被 设置为 refNode 在其父节点 childNodes 集合中的索引加 1。 调用这些方法时，所有属性都会自动重新赋值。不过，为了实现复杂的选区，也可以直接修改这些 属性的值。

//复制范围
let newRange = range.cloneRange();

//清理
range.detach(); // 从文档中剥离范围 
range = null; // 解除引用


与range类似的对象
Selection   document.getSelection().toString() 获取选中文字
TextRange  仅IE完全支持
```

