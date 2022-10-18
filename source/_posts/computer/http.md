---
title: 计算机网络科普
date: 2022-06-17 18:23:10
tags: http
categories: computerNetwork
---
### 资源

<!--more-->

```
所有类型的内容来源都是资源。

URI 
	统一资源标识符

资源标识符常见的形式
1、URL 统一资源定位符
	统一资源定位符（URL）是资源标识符最常见的形式。URL 描述了一台特定服务器 上某资源的特定位置。它们可以明确说明如何从一个精确、固定的位置获取资源。

2、URN 统一资源名
	RN 是作为特定内容的唯一名称使用 的，与目前的资源所在地无关。使用这些与位置无关的 URN，就可以将资源四处搬 移。通过 URN，还可以用同一个名字通过多种网络访问协议来访问资源。
	


事务

	一个 HTTP 事务由一条（从客户端发往服务器的）请求命令和一个（从服务器 发回客户端的）响应结果组成。这种通信是通过名为 HTTP 报文（HTTP message） 的格式化数据块进行的
	
	常见的HTTP方法
	
	HTTP方法 				描　　述 
	GET 			从服务器向客户端发送命名资源 
	PUT 			将来自客户端的数据存储到一个命名的服务器资源中去 
	DELETE 			从服务器中删除命名资源 
	POST 			将客户端数据发送到一个服务器网关应用程序 
	HEAD 			仅发送命名资源响应中的 HTTP 首部

	
	状态码
	
		每条 HTTP 响应报文返回时都会携带一个状态码。状态码是一个三位数字的代码， 告知客户端请求是否成功，或者是否需要采取其他动作。
	
	HTTP状态码			 描　　述 
	200 			OK。文档正确返回 
	302 			Redirect（重定向）。到其他地方去获取资源 
	404 			Not Found（没找到）。无法找到这个资源


Web的结构组件

• 代理 位于客户端和服务器之间的 HTTP 中间实体。 
• 缓存 HTTP 的仓库，使常用页面的副本可以保存在离客户端更近的地方。 
• 网关 连接其他应用程序的特殊 Web 服务器。 ：http/ftp网关
• 隧道 对 HTTP 通信报文进行盲转发的特殊代理。 
• Agent 代理 发起自动 HTTP 请求的半智能 Web 客户端。 ：web浏览器


URL语法
	
	<scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>


组　　件 			描　　述			 						默　认　值 
方案 			访问服务器以获取资源时要使用哪种协议 						无 
用户 			某些方案访问资源时需要的用户名 			  			 匿名 
密码 			用户名后面可能要包含的密码，中间由冒号（:）分隔 			<E-mail 地址 > 
主机 			资源宿主服务器的主机名或点分 IP 地址 					 无 
端口 			资源宿主服务器正在监听的端口号。很多方案都有默认端 口号（HTTP 的默认端口号为 80） 每个方案特有 

路径 			服务器上资源的本地名，由一个斜杠（/）将其与前面的 URL 组件分隔开来。路径组件的语法是与服务器和方案有 关的（本章稍后会讲到 URL 路径可以分为若干个段，每 段都可以有其特有的组件。） 	无 

参数 			某些方案会用这个组件来指定输入参数。参数为名 / 值对。 URL 中可以包含多个参数字段，它们相互之间以及与路径 的其余部分之间用分号（;）分隔 								  无 

查询 			某些方案会用这个组件传递参数以激活应用程序（比如数 据库、公告板、搜索引擎以及其他因特网网关）。查询组 件的内容没有通用格式。用字符“?”将其与 URL 的其余 部分分隔开来 		  无 

片段 			一小片或一部分资源的名字。引用对象时，不会将 frag 字 段传送给服务器；这个字段是在客户端内部使用的。通过 字符“#”将其与 URL 的其余部分分隔开来 							  无


绝对URL与相对URL
    <BASE href="" target="" > 可以在页面设置 基础URL 然后再其他资源引用时可以 使用相对URL
    
    
字符限制

	保留及受限的字符 
	字　　符 			保留/受限 
    % 			保留作为编码字符的转义标志 
    / 			保留作为路径组件中分隔路径段的定界符 
    . 			保留在路径组件中使用 
    .. 			保留在路径组件中使用 
    #			保留作为分段定界符使用 
    ?			保留作为查询字符串定界符使用 
    ; 			保留作为参数定界符使用 
    : 			保留作为方案、用户 / 口令，以及主机 / 端口组件的定界符使用 
    $, + 		保留 
    @ & = 				在某些方案的上下文中有特殊的含义，保留 
    { } | \ ^ ~ [ ] '	由于各种传输 Agent 代理，比如各种网关的不安全处理，使用受限 
    < > " 				不安全；这些字符在 URL 范围之外通常是有意义的，比如在文档中对 URL 自身 进行定界（比	
                        如 http://www.joes-hardware.com），所以应该对其进行编码 
    0x00-0x1F, 0x7F 			受限，这些十六进制范围内的字符都在 US-ASCII 字符集的不可打印区间内 
    >0x7F 					受限，十六进制值在此范围内的字符都不在 US-ASCII 字符集的 7 比特范围内


常见的方案格式

http	超文本传输协议方案 默认80端口
		基本格式： http://<host>:<port>/<path>?<query>#<frag> 
		示例：http://www.joes-hardware.com/index.html


https	与http方案是一对，唯一区别在于https使用了网景的SSL提供了端到端的加密，默认端口443
		基本格式： https://<host>:<port>/<path>?<query>#<frag> 
		示例： https://www.joes-hardware.com/secure.html

mailto	Mailto URL 指向的是E-mail地址。
		
		基本格式： mailto:<RFC-822-addr-spec> 
		示例： mailto:joe@joes-hardware.com

ftp		文本传输协议URL，可以用来从FTP服务器上下载或或向其上载文件，并获取ftp服务器上的目录结构内容的列表。
		基本格式： ftp://<user>:<password>@<host>:<port>/<path>;<params> 
		示例： ftp://anonymous:joe%40joes-hardware.com@prep.ai.mit.edu:21/pub/gnu/

rtsp,rtspu	RTSP URL 是可以通过实时流的传输协议 解析音/视频媒体资源的标识符。rtspu中的u表示它使用的UDP协议来获取资源的。
		基本格式： rtsp://<user>:<password>@<host>:<port>/<path> 
				 rtspu://<user>:<password>@<host>:<port>/<path> 
         示例： rtsp://www.joes-hardware.com:554/interview/cto_video

file	方案file表示一台指定主机上可直接访问文件，字段名都遵循通用格式。如果省略了主机名，就默认正在使用的URL的本地主机
		基本格式： file://<host>/<path> 
		示例： file://OFFICE-FS/policies/casual-fridays.doc

news	根据RFC 1036的定义，方案news 用来访问一些特定的文章或新闻组，特性：news URL自身包含的信息不足以对资源进行定位
		基本格式： news:<newsgroup> news:<news-article-id> 
		示例： news:rec.arts.startrek

telnet	方案telnet用于访问交互式业务。它表示的并不是对象自身，而是可以可以通过telnet协议访问的交互式应用程序（资源）
		基本格式： telnet://<user>:<password>@<host>:<port>/ 
		示例： telnet://slurp:webhound@joes-hardware.com:23/



HTTP报文
	报文三个部分：起始行，首部，主体
	
	这是请求报文的格式： 
	<method> <request-URL> <version> 
	<headers> 
	<entity-body> 
	
	这是响应报文的格式（注意，只有起始行的语法有所不同）： 
	
	<version> <status> <reason-phrase> 
	<headers> 
	<entity-body>


	method：方法
	request-URL：命名了所请求资源
	version：版本，报文所使用的http版本。格式：HTTP/<major>.<minor>
	status：状态码
		整体范围 	已定义范围 	分　　类 
		100 ～ 199  100 ～ 101  信息提示 
		200 ～ 299  200 ～ 206  成功 
		300 ～ 399  300 ～ 305  重定向 
		400 ～ 499  400 ～ 415  客户端错误 
		500 ～ 599  500 ～ 505  服务器错误

	reason-phrase：原因短语
	headers：首部。可以有0个或多个。
		首部实例 								描　　述 
	通用信息首部
		Connection 			允许客户端和服务器指定与请求 / 响应连接有关的选项 
		Date5 				提供日期和时间标志，说明报文是什么时间创建的 
		MIME-Version 		给出了发送端使用的 MIME 版本 
		Trailer 			如果报文采用了分块传输编码（chunked transfer encoding）方式，就可 以用这个首
							部列出位于报文拖挂（trailer）部分的首部集合 
		Transfer-Encoding 	告知接收端为了保证报文的可靠传输，对报文采用了什么编码方式 
		Update 				给出了发送端可能想要“升级”使用的新版本或协议 
		Via 				显示了报文经过的中间节点（代理、网关）
	通用缓存首部
		Cache-Control 		用于随报文传送缓存指示 
		Pragma 				另一种随报文传送指示的方式，但并不专用于缓存
	请求首部
		Client-IP8 			提供了运行客户端的机器的 IP 地址 
		From 				提供了客户端用户的 E-mail 地址 9 
		Host 				给出了接收请求的服务器的主机名和端口号 
		Referer 			提供了包含当前请求 URI 的文档的 URL 
		UA-Color 			提供了与客户端显示器的显示颜色有关的信息 
		UA-CPU 				给出了客户端 CPU 的类型或制造商 
		UA-Disp 			提供了与客户端显示器（屏幕）能力有关的信息 
		UA-OS 				给出了运行在客户端机器上的操作系统名称及版本 
		UA-Pixels 			提供了客户端显示器的像素信息 
		User-Agent 			将发起请求的应用程序名称告知服务器
	Accept首部
		Accept 				告诉服务器能够发送哪些媒体类型 
         Accept-Charset 	  告诉服务器能够发送哪些字符集 
		Accept-Encoding 	 告诉服务器能够发送哪些编码方式 
		Accept-Language 	 告诉服务器能够发送哪些语言 
		TE 					告诉服务器可以使用哪些扩展传输编码
	条件请求首部
		Expect 				允许客户端列出某请求所要求的服务器行为 
		If-Match 			如果实体标记与文档当前的实体标记相匹配，就获取这份文档 12 
		If-Modified-Since 	除非在某个指定的日期之后资源被修改过，否则就限制这个请求 
		If-None-Match 		如果提供的实体标记与当前文档的实体标记不相符，就获取文档 
		If-Range 			允许对文档的某个范围进行条件请求 
		If-Unmodified-Since 除非在某个指定日期之后资源没有被修改过，否则就限制这个请求 
		Range 				如果服务器支持范围请求，就请求资源的指定范围 13

	安全请求首部
		Authorization 包含了客户端提供给服务器，以便对其自身进行认证的数据 
		Cookie 客户端用它向服务器传送一个令牌——它并不是真正的安全首部，但确实 隐含了安全功能 14 
		Cookie2 用来说明请求端支持的 cookie 版本，
	代理请求首部
		Max-Forward 		在通往源端服务器的路径上，将请求转发给其他代理或网关的最大次 数——与 
							TRACE 方法一同使用 15 
		Proxy-Authorization  与 Authorization 首部相同，但这个首部是在与代理进行认证时使用的 
		Proxy-Connection     与 Connection 首部相同，但这个首部是在与代理建立连接时使用的
	响应的信息性首部
		Age 			（从最初创建开始）响应持续时间 
		Public 			服务器为其资源支持的请求方法列表 
		Retry-After 	如果资源不可用的话，在此日期或时间重试 
		Server 			服务器应用程序软件的名称和版本 
		Title1 			对 HTML 文档来说，就是 HTML 文档的源端给出的标题 
		Warning 		比原因短语中更详细一些的警告报文
	协商首部
		Accept-Ranges 	对此资源来说，服务器可接受的范围类型 
		Vary 			服务器查看的其他首部的列表，可能会使响应发生变化；也就是说，这是 一个首部列表，
						服务器会根据这些首部的内容挑选出最适合的资源版本发 送给客户端

	安全响应首部
		Proxy-Authenticate 来自代理的对客户端的质询列表 
		Set-Cookie 不是真正的安全首部，但隐含有安全功能；可以在客户端设置一个令牌，
        			以便服务器对客户端进行标识 19 Set-Cookie2 与 Set-Cookie 类似，
       				 RFC 2965 Cookie 定义；参见 11.6.7 节 
		WWW-Authenticate 来自服务器的对客户端的质询列表

	实体的信息性首部
		Allow 			列出了可以对此实体执行的请求方法 
		Location 		告知客户端实体实际上位于何处；用于将接收端定向到资源的（可能是新 的）位置（URL）上去

	内容首部
		Content-Base20 		解析主体中的相对 URL 时使用的基础 URL 
		Content-Encoding 	对主体执行的任意编码方式 
		Content-Language 	理解主体时最适宜使用的自然语言 
		Content-Length 		主体的长度或尺寸 
		Content-Location 	资源实际所处的位置 
		Content-MD5 		主体的 MD5 校验和 
		Content-Range 		在整个资源中此实体表示的字节范围 
		Content-Type 		这个主体的对象类型

	实体缓存首部

		ETag 				与此实体相关的实体标记 
		Expires			 	实体不再有效，要从原始的源端再次获取此实体的日期和时间 
		Last-Modified		 这个实体最后一次被修改的日期和时间


```

