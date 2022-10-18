---
title: Nodejs 编程基础
date: 2022-06-17 18:23:10
tags: node
categories: node
---

### 编程基础

<!--more-->

```js
1、控制台输出
	-console.log()	控制台可见
	-console.error() 控制台不可见
2、命令参数与环境变量
	-程序的输入首先是从命令行参数获取，其次是从环境变量中获取的。
	Node也遵循了这些Unix惯例。Node程序可以从字符串数组process.argv中读取其命令行参数。这个数组的第一个元素始终是Node可执行文件的路径。第二个参数是Node执行的JavaScript代码文件的路径。数组中剩下的所有元素都是你在调用Node时，通过命令行传给它的空格分隔的参数。

	eg:假设把这个极短的Node程序保存到文件argv.js中
		argv.js
			console.log(process.argv)
		
		//控制台输入命令 ：node --trace-uncaught argv.js --arg2 filename
        
         打印结果：
            	[
                  'D:\\node\\node.exe',// 可执行文件 完全限定的文件系统路径
                  'E:\\code\\jsTest\\nodejs\\argv.js',// 被执行文件 完全限定的文件系统路径
                  '--arg2',
                  'filename'
                ]

	【注意】：
        ·process.argv 的第一和第二个元素是Node可执行文件和被执行JavaScript文件的完全限定的文件系统路径
        有意提供给Node可执行文件且由它解释的命令行参数会被Node可执行文件使用，不会出现在process.argv中
		（前面例子中的命令行参数--trace-uncaught实际上没什么用，这里只是用来演示它不会出现在输出中）
		 出现在JavaScript文件名之后的任何参数（如--arg1和filename）都会出现在process.argv中。

	-node把这些变量保存在process.env对象中使用。
		这个对象的属性名是环境变量的属性名，而属性值（始终是字符串）是对应变量的值
        
//   查询电脑环境变量：node -p -e "process.env"   ||  node --eval "process.env" --print
{
  ALLUSERSPROFILE: 'C:\\ProgramData',
  APPDATA: 'C:\\Users\\19507\\AppData\\Roaming',
  CLASSPATH: '%JAVA_HOME%\\lib;%JAVA_HOME%\\lib\\tools.jar;',
  CommonProgramFiles: 'C:\\Program Files\\Common Files',
  'CommonProgramFiles(x86)': 'C:\\Program Files (x86)\\Common Files',
  CommonProgramW6432: 'C:\\Program Files\\Common Files',
  COMPUTERNAME: 'BAIXIAOCHUN',
  ComSpec: 'C:\\WINDOWS\\system32\\cmd.exe',
  DriverData: 'C:\\Windows\\System32\\Drivers\\DriverData',
  HOMEDRIVE: 'C:',
  HOMEPATH: '\\Users\\19507',
  'IntelliJ IDEA': 'D:\\IntelliJ IDEA 2021.1.2\\bin;',
  'JAVA＿HOME': 'C:\\Program Files\\Java\\jdk1.8.0_144',
  LOCALAPPDATA: 'C:\\Users\\19507\\AppData\\Local',
  LOGONSERVER: '\\\\BAIXIAOCHUN',
  M2_HOME: 'D:\\maven\\apache-maven-3.6.3',
  NUMBER_OF_PROCESSORS: '16',
  OneDrive: 'C:\\Users\\19507\\OneDrive',
  OneDriveConsumer: 'C:\\Users\\19507\\OneDrive',
  OS: 'Windows_NT',
  Path: 'C:\\ProgramData\\Oracle\\Java\\javapath;C:\\Program Files\\Common Files\\Oracle\\Java\\javapath;C:\\Windows\\system32;C:\\Windows;C:\\Windows\\System32\\Wbem;C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\;C:\\Windows\\System32\\OpenSSH\\;C:\\Program Files (x86)\\NVIDIA Corporation\\PhysX\\Common;C:\\Program Files\\NVIDIA Corporation\\NVIDIA NvDLISR;D:\\node\\;D:\\svn\\bin;D:\\Git\\cmd;C:\\Program Files\\MongoDB\\Server\\4.0\\bin;C:\\WINDOWS\\system32;C:\\WINDOWS;C:\\WINDOWS\\System32\\Wbem;C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\;C:\\WINDOWS\\System32\\OpenSSH\\;%JAVA_HOME%\\bin;%JAVA_HOME%\\jre\\bin;C:\\Users\\19507\\AppData\\Local\\Microsoft\\WindowsApps;;D:\\vsCode\\Microsoft VS Code\\bin;C:\\Users\\19507\\AppData\\Roaming\\npm;D:\\IntelliJ IDEA 2021.1.2\\bin;',
  PATHEXT: '.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC',
  PROCESSOR_ARCHITECTURE: 'AMD64',
  PROCESSOR_IDENTIFIER: 'AMD64 Family 23 Model 96 Stepping 1, AuthenticAMD',
  PROCESSOR_LEVEL: '23',
  PROCESSOR_REVISION: '6001',
  ProgramData: 'C:\\ProgramData',
  ProgramFiles: 'C:\\Program Files',
  'ProgramFiles(x86)': 'C:\\Program Files (x86)',
  ProgramW6432: 'C:\\Program Files',
  PROMPT: '$P$G',
  PSModulePath: 'C:\\Program Files\\WindowsPowerShell\\Modules;C:\\WINDOWS\\system32\\WindowsPowerShell\\v1.0\\Modules',
  PUBLIC: 'C:\\Users\\Public',
  SESSIONNAME: 'Console',
  SystemDrive: 'C:',
  SystemRoot: 'C:\\WINDOWS',
  TEMP: 'C:\\Users\\19507\\AppData\\Local\\Temp',
  TMP: 'C:\\Users\\19507\\AppData\\Local\\Temp',
  USERDOMAIN: 'BAIXIAOCHUN',
  USERDOMAIN_ROAMINGPROFILE: 'BAIXIAOCHUN',
  USERNAME: '19507',
  USERPROFILE: 'C:\\Users\\19507',
  windir: 'C:\\WINDOWS'
}

3、程序生命期
	Node程序通常是异步的，并且基于回调和事件处理程序。Node程序在运行完初始文件、调用完所有回调、不再有未决事件之前不会退出。基于Node的服务器程序监听到来的网络连接，理论上会永远运行，因为它始终要等待下一个事件。
    程序通过调用process.exit()可以强制自己退出。用户通常需要在终端窗口中按Ctrl-C来终止运行中的Node程序。程序通过使用process.on("SIGINT", ()=>{})注册信号处理函数可以忽略Ctrl+C。
	如果程序中的代码抛出异常，也没有catch子句捕获该异常，程序会打印栈追踪信息并退出。由于Node天生异步，发生在回调或事件处理程序中的异常必须局部处理，否则根本得不到处理。
    为防止代码崩溃，可以注册一个全局处理程序
    process.setUncaughExceptionCaptureCallback(e=> {
        console.error("Uncaught exception:", e)
    })
	类似的，如果你的程序创建一个期约被拒绝，而且没有.catah()调用处理，也会这样。
    可以注册一个全局处理程序，捕获并处理期约被处理的错误
    process.on("unhandledRejection", (reason, promise)=> {
        // reason 是会传给 .catch() 函数的拒绝理由
        // promise 是被拒绝的期约对象
    })
4、Node模块
	Node的模块系统使用require()函数向模块中导入值，使用exports对象或module.exports属性从模块中导出值。
也就是说 node环境下 支持 es6(v13及以上)和 CommonJS模块
	通过指定后缀名来告诉node加载的是什么模块
    -.cjs 
    Node始终会将它作为一个CommonJSS模块来对待，会提供require()函数，而如果其中使用了import或export声明，则会抛出SyntaxError。
    -.mjs 
    	那么Node始终会将它作为一个ES6模块来加载，假设其中使用了import和export，并且不提供require()函数
    -.js
    	对于没有明确给出.mjs或cjs扩展名的文件，Node会在同级目录及所有包含目录中查找一个名为package.json的文件。一旦找到最近的package.json文件，Node会检查其中JSON对象的顶级type属性。如果这个type属性的值是module，Node将该文件按ES6模块来加载。如果这个属性的值是commonjs，那么Node就按CommonJS模块来加载该文件。注意，运行Node程序并不需要有package.json文件。如果没有找到这个文件（或找到该文件但它没有type属性），Node默认会使用CommonJS模块。这个package.json的招术只有你想在Node中使用ES6模块，但又不希望使用.mjs扩展名时才是必需的。

5、node包管理器
	安装node时会获得一个名为npm的程序。这个程序就是Node的包管理器。npm通过位于程序根目录下的package.json文件跟踪依赖（以及与程序相关的其他信息）。这个package.json文件是由npm创建的，如果你想在项目中使用ES6模块，需要在其中添加"type":"module"。
    
6、Node默认异步
	Node采用了Web使用的单线程JavaScript编程模型，使得创建网络服务器变得极其简单。Node是针对I/O密集型程序（如网络服务器）进行设计和优化的。特别地，Node的设计让实现高并发（同时处理大量请求的）服务器非常容易。
	util.promisify()包装函数能够轻易创建其基于回调API的期约版。下面演示了怎么把readConfigFile()函数重写为返回一个期约对象：
    const util = require('util');
    const fs = require('fs');// 需要文件系统模块
	const pfs = {// fs函数基于期约的变体
        readFile:　util.promisify(fs.readFile)
    }
    function readConfigFile(path) {
        return pfs.readFile(path, "utf-8").then(text => JSON.parse(text))
    }


	asycn/await 版本
	async function readConfigFile(path) {
        let text = await pfs.readFile(path, "utf-8");
        return JSON.parse(text);
    }

7、缓冲区
	Node中有一个比较常用的数据类型就是Buffer，常用于从文件或网络读取数据。Buffer类（或称缓冲区）非常类似字符串，只不过它是字节序列而非字符序列。
    Node是在核心JavaScript支持定型数组（参见11.2节）之前诞生的，因此没有表示无符号字节的Uint8Array。Node的Buffer类就是为了满足这个需求而设计的。在JavaScript语言支持Uint8Array之后，Node的Buffer类就成为Uint8Array的子类。
    Buffer与其超类Uint8Array的区别在于，它是设计用来操作JavaScript字符串的。
    以下是几个代码示例，展示了如何使用Buffer，以及如何与字符串相互转换：
    
    let b = Buffer.from([0x41,0x42,0x43]);// 
	b.toString();// 'ABC' 默认 'utf8'
	b.toString('hex');// 414243
	
    let computer = Buffer.from('IBM3111', 'ascii');
	for(let i = 0; i < computer.length; i++) {
        computer[i]--;
    }
    computer.toString('ascii');// 'HAL2000'
    computer.subarray(0,3).map(x=>x+1).toString();// 'IBM'

	// 使用Buffer.alloc()创建一个'空'缓冲区
	let zeros = Buffer.alloc(1024);// 124 个 0
	let ones = Buffer.alloc(128, 1);// 128 个 1
	let dead = Buffer.alloc(1024, 'DEADBEEF','hex');// 重复字节魔石

	// 缓冲区有方法可以从或者向缓冲区中在任意指定偏移位置读取或写入多字节值
	dead.readUInt32BE(0);
	dead.readUInt32BE(0);
	dead.readBigUInt32BE(0);
	dead.readBigUInt64BE(0);
	dead.readUInt32LE(0);

	如果你的Node程序会操作二进制数据，那么一定会经常使用Buffer类。而如果你只是操作与读取或写入文件或网络相关的文本，那么可能只会将Buffer作为数据的中间表示。很多Node API都可以接收或返回字符串或Buffer对象的输入或输出。一般来说，如果在使用这些API时，你传递的是字符串，或者期待返回字符串，那么都需要指定要使用的文本编码的名字。而此时，通常根本不会使用Buffer对象。
    
8、　事件与EventEmitter

	对其中很多API而言，这种异步性的表现形式为两个参数、错误在先的回调，当请求的操作完成时会被调用。但一些更复杂的API则是基于事件的。在API是围绕对象而非函数设计的，或者回调需要多次被调用，或者需要多种类型的回调时，通常会是这种情况。
    比如，以net.Server类为例，一个这种类型的对象就是一个服务器套接口，可以接收来自客户端的连接。它在首次开始监听连接时会发送一个“监听”事件，在每次客户端连接时都会发送一个“连接”事件，而在被关闭不再监听时会发送一个“关闭”事件。
在Node中，发送事件的对象都是EventEmitter或其子类的实例：
	const EventEmitter = require('events');// 模块名与类名不一致
	const net = require('net');
	let server = new net.Server();// 创建一个 Server 对象
	server instanceof EventEmitter;// true Server 是 EventEmitter

	server.on('connection', socket => {
        socket.end('Hellow World', 'utf8');
    })
	// .once() 注册的事件监听器在被调用一次之后就会被自动清除
	// 解绑事件
	server.off(...)
    // 也可以使用 removeListener(), addListener()
               
    调用顺序是注册的顺序。如果有多个处理程序，它们会在一个线程上被顺序调用。
    注意，Node没有并行调用。更重要的，事件处理程序会被同步调用，而非异步调用。这意味着emit()方法不会把事件处理程序排队到将来某个时刻再调用。emit()会调用所有注册的处理程序（一个接一个），并且会在最后一个事件处理程序返回之后返回。
    Node也定义了setImmediate()，用于调度某个函数在所有待调用的回调和事件被处理之后立即执行。
    
    EventEmitter类也定义了一个emit()方法，可以导致注册的事件处理程序被调用。调用emit()时必须在第一个参数传入事件类型的名字。而传给emit()的所有后续参数都会成为注册的事件处理程序的参数。
    
9、流
	在实现处理数据的算法时，最简单的办法通常都是先把所有数据读到内存中，进行处理，然后再把数据写到某个地方。例如，可以像下面这样写一个复制文件的Node函数：
    const fs = require('fs');
	// 一个异步但非流式（因而低效）的函数
	function copyFile(sourceFilename, destinationFilename, callback) {
        fs.readFile(sourceFilename, (err, buffer)=> {
            if(err) {
                callback(err)   
            } else {
                fs.writeFile(destinationationFileName, buffer, callback)
            }
        }) 
    }
    缺点: 就是它不能在读完旧文件之前就写新文件。
    
    
Node支持4种基本的流
    
-可读流（readable）
	可读流是数据源。比如，fs.createReadStream()就返回一个指定文件的流，可以通过它读取文件内容。process.stdin也是一个可读流，可以从标准输入返回数据。

-可写流（writable）
	可写流是数据的接收地或目的地。比如，fs.createWriteStream()返回的值是可写流，允许分块写入数据，并将所有数据输出到指定文件。

-双工流（duplex）
	双工流把可读流和可写流组合为一个对象。比如，net.connect()和其他Node网络API返回的Socket对象就是双工流。如果你写入套接口，你的数据就会通过网络发送到该套接口连接的计算机上。如果你读取套接口，则可以访问由其他计算机写入的数据。

-转换流（transform）
	转换流也是可读和可写的，但与双工流有一个重要区别：写入转换流的数据在同一个流会变成可读的（通常是某种转换后的形式）。比如，zlib.createGzip()函数返回一个转换流，可以（使用gzip算法）对写入其中的数据进行压缩。类似地，crypto.createCipheriv()函数也返回一个转换流，可以对写入其中的数据进行加密或解密。
  
    
流的应用

    -管道
		把从流中读取的数据写入另一个流。
        *pipe():把 可写流 简单地传给 可读流的pipe()方法即可
        const fs = require('fs');
		function pipeFileToSocket(filename, socket) {
            fs.createReadStream(filename).pipe(socket)
        }

		下面这个实用函数通过管道把一个流导向另一个流，并在完成或发生错误时调用一个回调：
        
        function pipe(readable, writable, callback) {
            // 首先，设置错误处理
            function handleError(err) {
                readable.close();
                writable.close();
                callback(err);
            }
            
            // 接下来定义管道并处理正常终止的情况
            readable
            	.on('error', handleError)
            	.on(writable)
            	.on('error', handleError)
            	.on('finish', callback)
        }
	
        转换流特别适合与管道一起使用，可以创建多个流的传输管道。下面这个示例函数实现了文件压缩：
        const fs = require('fs');
        const zlib = require('zlib');
        function gzip(filename, callback) {
            // 创建流
            let source = fs.createReadStream(filename);
            let destination = fs.createWriteStream(filename + '.gz');
            let gzipper = zlib.createGzip();
            
            // 设置管道
            source
            	.on('error', callback) //读取错误时间调用回调
            	.pipe(gzipper)
            	.pipe(destination)
            	.on('error', callback) // 写入出错时调用回调
            	.on('finish', callback);// 写入完成时调用回调
        }

        使用pipe()方法从可读流向可写流复制数据很容易。不过在实践中，经常要对流经程序的数据做某些处理。为此，一种方式是实现自己的Transform流来完成相应处理，这种方式可以让你避免手工读取和写入流。例如，下面这个函数类似于Unix的grep命令，该函数从输入流读取文本行，但只写入与指定正则表达式匹配的行：
        
        const stream = require('stream');
        class GrepStream extends stream.Transform {
            constructor(pattern) {
                super({decodeString: false});// 不把字符串转换回缓冲区
                this.pattern = pattern;// 要匹配的正则表达式
                this.incompleteLine = '';// 最后一个数据快的剩余数据
            }
            
            // 在一个字符串准备好可以转换时会调用这个方法
            // 它应该把转换后的数据传给指定的回调函数
            // 我们期待输入是字符串，因此这个流应该只被连接到
            // 调用了 setEncodeing()方法的可读流
            _transform(chunk, encoding, callback) {
                if(typeof chunk !== 'string') {
                    callback(new Error("Expected a stirng but got a buffer"));
                    return
                }
                // 把 chunk 添加到之前不完整的行
                // 并将所有内容按换行符分割为数组
                let lines = (this.incompleteLine + chunk).split('\n');
                
                // 数组的最后一个元素时新的不完整行
                this.incompleteLine = lines.pop();
                
                // 查找所有匹配的行
                let output = lines						// 所有完整行的开始
                		.filter(l => this.pattern.test(l))// 筛选匹配的行
                		.join('\n');					// 最后将它们拼接起来
                // 如果有匹配，在最后加上一个换行符
                if(output) {
                    output += '\n'
                }
                // 始终调用回调，即使没有输出
                callback(null, output)
            }
            
            // 这个方法会在流关闭前被调用 
            // 利用这个机会可以把最后的数据写出来
            _flush(callback) {
                // 如果还有不完整的行而且匹配
                // 则把它传给回调函数
                if(this.pattern.test(this.incompleteLine)) {
                    callback(null, this.incompleteLine + '\n');
                }
            }
        }

        // 现在使用这个类可以写一个类似 grep 的程序
        let pattern = new RegExp(process.argv[2]);// 从命令行取得正则表达式
        process.stdin						    // 以标准输入作为起点 
			.setEncoding('utf8')				// 将内容作为Unicode字符串读取
			.pipe(new GrepStream(pattern))		 // 通过管道把它传给我们的GrepStream
			.pipe(process.stdout)				// 再用管道把结果传给标准输出
			.on('error', ()=> process.exit())	 // 如果标准输出关闭，则退出
10、异步迭代
	在Node 12及之后，可读流是异步迭代器，这意味着在一个async函数中可以使用for/await循环从流中读取字符串或Buffer块，而代码结构就像同步代码一样
	重写上述例子
    
    // 从来源流读取文本行，将匹配
    // 指定模式的行写入目标流
    async function grep(source, destination, pattern, encoding="utf8") {
        // 设置来源流以读取字符串，非缓冲区
        source.setEncoding(encoding);
        // 在目标流上设置错误处理程序，以防止标准输出
        // 意外关闭(比如，通过管道输出到 head 等)
        destination.on('error', err=> process.exit());
        
        // 我们读取的块不大可能以换行符结尾，因此每个模块可能包含不完整的行。在这里记录
        let incompleteLine = '';
        
        // 使用 for/await 循环异步从输入流读取快
        for await (let chunk of source) {
            // 把上一块的末尾加上这个块，然后分割成行
            let lines = (incompleteLine + chunk).split('\n');
            // 最后一行不完整
            incompleteLine = lines.pop();
            // 现在遍历每一行，把目标的行写入目标
            for(let line of lines) {
                if(pattern.test(line)) {
                    destination.write(line + '\n', encoding);
                }
            }
        }
        // 最后，检查末尾的文本
        if(pattern.test(incompleteLine)) {
            destination.write(incompleteLine + '\n', encoding);
        }
    }

	let pattern = new RegExp(process.argv[2]);// 从命令行取得正则表达式
	grep(process.stdin, process.stdout, pattern)// 调用这个异步grep()函数
		.catch(err=> {
        console.log(err);
        process.exit();
    })

11、写入流及背压处理
	write(字符串/缓冲区, '字符串编码', callback)；
    write方法有个重要的返回值，在调用一个流的write()方法时，它始终会接收并缓冲传入的数据块。如果内部缓冲区未满，它会返回true。如果内部缓冲区已满或太满，它会返回false。

	write()方法返回false值是一种背压（backpressure）的表现。背压是一种消息，表示你向流中写入数据的速度超过了它的处理能力。对这种背压的正确反应是停止调用write()，直到流发出“drain”（耗尽）事件，表明缓冲区又有空间了。
    比如，下面这个函数会把数据写入流，而且会在可以写入更多数据时调用传入的回调：
    function write(stream, chunk, callback) {
        // 将指定的块写入指定的流
        let hasMoreRoom = stream.write(chunk)
        // 检查 write()方法的返回值
        if(hasMoreRoom) {
            setImmediate(callback); // 异步调用回调
        } else {
            stream.once('drain', callback);// 在 耗尽 事件发生时调用回调
        }
    }
	使用pipe()方法时，Node会自动为你处理背压。
    
    下面例子中的异步copy()函数演示了如何正确处理背压。注意，这个函数只实现了从来源流向目标流复制数据，调用copy(source, destination)很像是调用source.pipe(destination)：
    
    //这个函数将指定的块写入指定的流，并且返回
    // 一个期约，该期约将在可以再次写入时兑现
    //因为返回的是期约，所以可以使用await
    function function write(stream, chunk) {
        //将指定的块写入指定的流
        let let hasMoreRoom = stream.write(chunk);
        if (hasMoreRoom) { //如果缓冲区未满，返回
            return Promise.resolve(null); // 一个解决的期约对象
        }  else {
            return new Promise(resolve => { // 否则，返回一个在“耗尽
                stream.once("drain", resolve); // 事件发生时解决的期约
            })
        }
    }
    //从来源流向目标流复制数据
    //并且处理来自目标流的背压
    // 非常类似调用 source .pi pe( destination)
    async function  copy(source, destination) {
        //在目标流上设置错误处理程序，以防标准输出
        //意外关闭(比如，通过管道输出到“head”等)
        destination.on("error", err => process.exit());
        //使用for/await循环异步从输入流读取块
        for  await (letchunk  of source) {
            //写入块，并等到缓冲区有空间再继续 
            await write(destination, chunk);
        }
    )
    //从标准输入复制到标准输出
    copy(process.stdin, process.stdout);
        
   【注意】假设你写了一个HTTP服务器，可以通过网络发送文件，但你没有使用pipe()，也没有花时间处理write()方法的背压。攻击者可以写一个HTTP客户端，只请求大文件（如图片），但并不实际读取请求体。因为这个客户端不会通过网络读取数据，服务器又不会对背压作出反应，服务器上的缓冲区就可能溢出。只要攻击者的并发连接足够多，就会造成DoS（Denial of Service，拒绝服务）攻击，导致你的服务器响应变慢，甚至崩溃。
   
12、　通过事件读取流
	如果你不能在程序中使用管道或异步迭代，那就需要从这两种基于事件的API中选择一种来处理流。关键在于只能使用其中一种API，不能两种混用。
	-流动模式
        在流动模式下无须调用read()方法，只需要处理“data”事件。另外，新创建的流并非一开始就处于流动模式。注册“data”事件处理程序会把流切换为流动模式。更方便的是，这意味着流在你注册“data”事件处理程序之前，不会发送“data”事件。
        const fs = require('fs');
        // 流式的文件赋值函数，使用了'流动模式'
        // 将指定名字的源文件的内容赋值到指定名字的目标文件
        // 成功，则以 null 参数调用回调，出错，则以Error 对象作为参数调用回调
        function copyFile(sourceFilename, destinationFilename, callback) {
            let input = fs.createReadStream(sourceFilename);
            let output = fs.createWriteStream(destinationFilename);
            
            input.on('data', (chunk)=> {// 在取得新数据时
                let hasRoom = output.write(chunk);// 将其写入输出流
                if(!hasRoom) {// 如果输出流已满
                    input.pause();// 暂停输入流
                }
            });
            input.on('end', ()=> {// 在到达输入末尾时
                output.end()// 告知输出流结束
            })
            input.on('error', err=> {
                callback(err);// 以该错误调用回调
            })
            
            output.on('drain', ()=> {
                input.resume();// 回复输入流的 data 事件
            })
            output.on('error', err=> {
                callback(err);// 以该错误调用回调
                process.exxit();// 然后退出
            })
            output.on('finish', ()=> {// 在输出全部写完时
                callback(null);// 无错误调用回调
            })
        }
        
        // 下面是一个赋值文件的简单的命令行调用
        let from = process.argv[2], to = process.argv[3];
        console.log(`Copying file ${from} to ${to}...`);
        copyFile(from, to, err=> {
            if(err) {
                console.error(err)
            } else {
                console.log('done.')
            }
        })
       读取 test.md 写入到 test2.js 文件中。没有就新建 
       // 执行命令 node test1.js test.md  test2.js
       
        
        
13、暂停模式
    如果你不注册“data”事件处理程序，也不调用pipe()方法，那么可读流就一直处于暂停模式。在暂停模式下，流不会以“data”事件的形式向你推送数据。相反，你需要显式调用其read()方法从流中拉取数据。这个方法不是阻塞操作，且如果流中已经没有数据可读，它会返回null。因为没有同步API等待数据，所以暂停模式API也是基于事件的。
    可读流在暂停模式下会发送“readable”事件，表示流中有可读数据。相应地，你的代码应该调用read()方法读取该数据。而且，必须在一个循环中反复调用read()，直到它返回null。只有这样才能完全耗尽流的缓冲区，从而在将来再次触发新的“readable”事件。如果在仍然有可读数据的情况下停止调用read()，那么就不会再收到下一个“readable”事件，你的程序很可能会被挂起。
    如果你的程序从一个可读流读数据，向一个可写流写数据，那么暂停模式可能并非好的选择。为了正确处理背压，我们只希望在输入流可读且输出流未满时读取数据。而在暂停模式下，这意味着读取和写入要持续到read()返回null或者write()返回false，然后在“readable”或“drain”事件中再次开始读取或写入。这样处理起来很麻烦，不如流动模式（或管道）更简单
    
    下面的代码演示了如何为指定文件的内容计算SHA256散列值。它使用了暂停模式的可读流以块的形式读取文件内容，然后把每个块传给计算散列值的对象（注意，在Node 12及之后，使用for/await循环实现这个函数会更简单）。
       
const fs = requi.re("fs");
const crypto = requi_re(Hcrypto");
// 计算指定名字的文件内容的sha256散列值，并将散列值
// (以字符串形式)传给指定的错误在先的回调函数
function sha256(filename, callback) {
   let input = fs.createReadStrean(filename); // 数据流
   let hasher = crypto.createHash("sha256"); // 用于计算散列值
    input.on("readable", () => { 
        let chunk;
	    while(chunk = input.read()) { // 在可以读取数据时，读取一块，如果不返回null
            hasher.update(chunk); // 把它传给hasher,然后继续循环，直至没有数据可读
        }
    });
    input.on('end', () => { // 到达流的末尾，
        let hash = hasher.digest("hex"); // 计算散列值，
    	callback(null., hash);// 然后把它传回调
    });
    input.on("error", callback);// 出错时，调用回调
)
// 下面是一个计算文件散列值的简单的命令行调用
sha256(process.argv[2], (err, hash) => { // 从命令行传入文件名
   if (err) {                       // 如果出错
      console.error(err.toString()); // 把错误打印出来
   } else {                         // 否则，
      console.log(hash);            // 打印散列值字符串
   )
})；

【注意】实际开发中，可以使用Node定义的fs.copyFile()函数。


14、进程和操作系统细节
    全局Process对象有很多有用的属性和函数，通常与当前运行的Node进程的状态相关。Node文档中有这些属性和函数的详细说明，不过下面这些是需要注意的：
process.argv // 包含命令行参数的数组
process.arch // CPU架构：如x64
process.cwd() // 返回当前工作目录 
process.chdir() // 设置当前工作目录 
process.cpuUsage() // 报告CPU使用情况 
process.env // 环境变量对象 
process.execPath // Node可执行文件的绝对路径 
process.exit() // 终止当前程序
process.exitCode // 程序退出时报告的整数编码 
process.getuid() // 返回当前用户的Unix用户ID 
process.hrtime.bigint() // 返回“高分辨率”纳秒级时间践 
process.kill // 向另一个进程发送信号 
process.memoryUsage() // 返回一个包含内存占用细节的对象 
process.nextTick() // 类似于setlmmediateOut ,立刻调用一个函数 
process.pid // 当前进程的进程ID
process.ppid // 父进程ID
process.platform // 操作系统：如Linux、Da「wtn或Win32 
process.resourceUsage()// 返回包含资源占用细节的对象 
process.setuid() // 通过ID或名字设置当前用户 
process.title // 出现在ps列表中的进程名 
process.umask() // 设置或返回新文件的默认权限
process.uptime() // 返回Node正常运行的时间（秒）
process.version // Node的版本字符串
process.versions //Node依赖库的版本字符串

	os模块（与process不同，需要通过require()显式加载）提供对Node所在计算机和操作系统的类似的低级细节。尽管可能永远用不到这些特性，但应该知道Node暴露了这些信息：
    
```

![](D:\Typora\workSpace\resource\os.png)

### 操作文件

> Node的fs模块是用于操作文件和目录的综合性API。path模块是fs模块的补充，定义了操作文件和目录名的常用函数。

```js
1、fs 模块
fs模块定义了大量API，主要是因为每种基本操作都有很多变体。正如本章一开始所说的，其中大多数函数都像fs.readFile()一样是非阻塞、基于回调的、异步的。不过，通常每个这样的函数都会有一个同步阻塞的变体，如fs.readFileSync()。
    
2、路径、文件描述符和FileHandle
	fs.realpath()和fs.realpathSync()函数执行基于文件系统的规范化，它们会解析符号链接并相对于当前工作目录解释相对路径名。
    
3、读文件
	Node允许你一次性读取文件的内容，可以通过流，也可以通过低级API。
    -如果你的文件很小，或者内存占用或性能并非主要考虑的因素，那么通过一次调用读取文件的全部内容是最简单的。
    const fs = require('fs');
	let buffer = fs.readFileSync('test.data'); // 同步，返回缓冲区
	let text = fs.readFileSync('data.csv', 'utf8');// 同步，返回字符串

	// 异步文件读取
	fs.readFile('test.data', (err, buffer)=> {
        if(err) {
            // 在这里处理错误
        } else {
            // 在这里处理文件字节
        }
    })

	// 基于期约的异步函数读取
	fs.promises
		.readFile('data.csv', 'utf8')
		.then(processFileText)
		.catch(handleReadError);

	// 或者在 async 函数中使用 await 和期约 API
	async function processText(filename, encoding='utf8') {
        let text = await fs.promises.readFile(filename, encoding);
        // 这里处理文本
    }
	-用流的方式读取文件
		下面这个函数展示了如何使用流和pipe()方法将一个文件内容写入标准输出：
        function printFile(filename, encoding="utf8") {
            fs.createReadStream(filename, encoding).pipe(process.stdout);
        }
	如果你需要在更低层次上控制要读取文件的哪些字节，可以打开文件取得文件描述符，然后再使用fs.read()、fs.readSync()或fs.promises.read()，从文件中指定的来源位置将指定数量的字节读取到指定目标位置的指定缓冲区
    
4、写文件
	Node中有3种写文件的基本方式。如果你有字符串或缓冲区中全部的文件内容，可以调用fs.writeFile()（基于回调）、fs.writeFileSync()（同步）或fs.promises.writeFile()（基于期约）一次性写入全部内容
    eg: 
		let fs = require('fs');
		fs.writeFileSync(path.resolve(__dirname, 'settings.json'),JSON.stringify(settings),'utf8');

	相关函数fs.appendFile()、fs.appendFileSync()和fs.promises.appendFile()也类似，只不过它们会在指定文件存在时，把数据追加到已有数据的末尾，而不会重写已有的文件内容。
    
5、文件模式字符串
	在要写文件时，必须同时传入第二个字符串参数，用于指定你准备如何使用这个文件描述符。可用的几个标志字符串如下。
        "w"
        为写入打开。
        "w+"
        为写入和读取打开。
        "wx"
        为创建新文件打开。如果指定的文件存在则失败。
        "wx+"
        为创建文件打开，也允许读取。如果指定的文件存在则失败。
        "a"
        为追加数据打开。原有内容不会被重写。
        "a+"
        为追加数据打开，但也允许读取。
	如果没有给fs.open()或fs.openSync()传以上任何标志字符串，那它们会使用默认的“r”标志，返回只读文件描述符。注意，把这些标志传给其他文件写入方法同样有效：
    // 写入文件在原有内容基础追加，类似于 fs.appendFileSync()
    fs.writeFileSync('messages.log', 'hello', {flag: 'a'});
	// 打开一个写入流，如果文件存在则抛出错误。我们不想意外重写任何文件。注意参数flags
	fs.createWriteStream('message.log', {flags: 'wx'});

	可以通过fs.truncate()、fs.truncateSync()或fs.promises.truncate()截掉文件后面的内容。这几个函数以一个路径作为第一个参数，以一个长度作为第二个参数，将文件修改为指定的长度。如果省略长度参数，则使用默认值0，结果文件会变空。不用管这些函数的名字，通过它们还可以扩展文件。如果指定的长度超出了当前文件大小，则文件会以0字节扩展到新大小。如果已经打开了要修改的文件，可以对文件描述符或FileHandle使用ftruncate()或ftruncateSync()。
    
    如果想把数据强制写入磁盘，保证数据得到安全存储，可以使用fs.fsync()或fs.fsyncSync()。这两个函数只接收文件描述符，没有接收路径的版本。
    
6、文件操作
	文件复制
    
	前面在讨论Node流相关的类时，提到了两个copyFile()函数的例子。你在实际开发中应该不会使用它们，因为fs模块定义了自己的fs.copyFile()方法（当然还有fs.copyFileSync()和fs.promises.copyFile()）。
    这几个函数都接收原始文件的名字和副本的名字作为前两个参数。
    -参数一： 原始文件的名字（字符串、URL或Buffer对象）
    -参数二： 副本的名字（字符串、URL或Buffer对象）
    -参数三： 即一个整数（该整数的位用于指定标志，以控制复制操作的细节）
    -参数四： 对于基于回调的fs.copyFile()，最后一个参数是一个回调函数，将在复制完成时被无参数调用或在复制出错时以错误参数调用
    下面是两个例子：
    // 基本的同步文件复制
    fs.copyFileSync('ch15.txt', 'ch15.bak');
	// COPYFILE_EXCL 参数表示只在新文件不存在时复制
	// 这个参数可以防止复制操作重写已有文件
	fs.copyFile('ch15.txt', 'ch16.txt', fs.constants.COPYFILE_EXCL, err=> {
        // 这个回调将在复制完成时被调用。如果出错，err降为非空值
    })

	// 下面代码演示 copyFile 函数基于期约版
	// 两个标志以 | 组合，意味着已有的文件不会被重写，而且如果文件系统支持，副本将是原始内容或副本未被修改，则不需要占用额外的储存空间
	fs.promises.copyFile('Important data', 
                         `Important data ${new Date().toISOString()}`,
                        fs.constants.COPYFILE_EXCL | fs.constants.COPYFILE_FICLONE)
	.then(()=>{
        console.log('Backup complete');
    })
	.catch(err=> {
        console.error('Backup failed', err)
    })

	重命名
    	fs.rename()函数可以移动或重命名文件
        -参数一：当前文件路径
        -参数二: 期望的新文件路径
        -参数三：回调的版本接收回调作为第三个参数
		eg:
		 fs.renameSync('ch15.bak', 'backups/ch15.bak');

	函数fs.link()和fs.symlink()及其变体与fs.rename有相同的签名，行为则类似于fs.copyFile()，只是它们将分别创建硬链接和符号链接，而非创建一个副本。
    fs.unlink()、fs.unlinkSync()和fs.promises.unlink()是Node用来删除文件的函数.。调用这些函数时可以传入字符串、缓冲区或要删除文件的URL路径，如果使用基于回调的版本，还要传入一个回调：
	eg:
	 fs.unlinkSync('backups/ch15.bak')

7、文件元数据
	fs.stat()、fs.statSync()和fs.promises.stat()函数可以让你取得指定文件或目录的元数据。
    const fs = require('fs');
	let stats = fs.statSync('book/ch15.md');
	stats.isFile();// true 这是一个普通的文件
	stats.isDirectory();// false 它不是一个目录
	stats.size;// 文件大小（字节）
	stats.atime;// 访问时间，最后的读取的日期
	stats.mtime;// 修改时间，最后写入的日期
	stats.uid;// 文件所有者的用户 ID
	stats.gid;// 文件所有者的组 ID
	stats.mode.toSting(8);// 八进制字符串形式的文件权限
	...
    
	可以使用fs.utimes()和fs.futimes()及其变体设置文件或目录的访问时间和修改时间
    
8、操作目录
	在Node中要创建新目录，可以使用fs.mkdir()、fs.mkdirSync()或fs.promises.mkdir()。
    - 第一参数： 创建目录的路劲
    - 第二参数： 一个整数，表示新目录的模式（权限位）；或者也可以传入一个包含可选mode和recursive属性的对象
    如果recursive为true，则这个函数会创建路径中所有不存在的目录：
    eg:
		// 确保 dist/ 和 dist/lib/ 都会存在
		fs.mkdirSync('dist/lib', { recursive: true });
		
	fs.mkdtemp()及其变体接收一个传入的路径前缀，然后在后面追加一些随机字符（对于安全很重要），并以该名字创建一个目录，最后返回（或传给回调）这个目录的路径。
    
    要删除一个目录，使用fs.rmdir()或它的变体。注意，必须是空目录才能删除：
    // 创建一个随机临时目录并取得其路径
    // 创建完成后再删除它
    let tempDirPath;
	try {
        tempDirPath = fs.mkdtempSync(path.join(os.tmpdir(), 'd'));
        // 在这里对这个目录执行某些操作
    } finally {
        // 执行完操作之后删除这个临时操作目录
        fs.rmdirSync(tempDirPath);
    }

	fs模块提供了两组不同的API用于列出目录的内容。首先，fs.readdir()、fs.readdirSync()和fs.promises.readdir()一次性读取整个目录，然后返回一个字符串数组或一个指定了名字和类型（文件或目录）的Dirent对象的数组。这些函数返回的文件名就是这些文件的本地名，而非完整路径。下面是例子：
    let tempFiles = fs.readdirSync('/tmp');// 返回字符串数组

	// 使用基于期约的API取得　Dirent 数组
	// 然后打印出子目录的路径
	fs.promises.readdir('/tmp', {withFileTypes: true})
		.then(entries => {
        	entries.filter(entry => entry.isDirectory())
        		.map(entry => entry.name)
        		.forEach(name => console.log(path.join('/tmp/', name)));
        })
        .catch(console.error)


    如果预计要列出可能会包含数千个条目的目录，可以使用基于流的fs.opendir()及其变体。这些函数返回一个Dir对象，表示指定的目录。可以使用这个Dir对象的read()或readSync()方法每次读取一个Dirent对象。
    。如果给read()传了回调，那它会调用这个回调。如果省略了回调，那它会返回一个期约。在没有更多目录条目时，你会得到null而不是一个Dirent对象。
	使用Dir对象最简单的方式是将其作为异步迭代器，配合for/await循环。例如，下面这个函数会使用以上流API列出目录条目，调用每个条目的stat()方法，然后打印出文件和目录的名字及大小：
    const fs = require('fs');
	const path = require('path');
	
	async function listDirectory(dirpath) {
        let dir = await fs.promises.opendir(dirpath);
        for await (let entry of dir) {
            let name = entry.name;
            if(entry.isDirectory()) {
                name += '/';// 在子目录末尾加上一个斜杠
            }
            let stats = await fs.promises.stat(path.join(dirpath, name));
            let size = stats.size;
            console.log(String(size).padStart(10), name)
        }
    }
```

### HTTP客户端与服务器

```js
Node的http、https和http2模块是功能完整但相对低级的HTTP协议实现。这些模块定义了实现HTTP客户端和服务器的所有API。

1、发送 get、post 请求
    -http.get()
    -https.get()
    -参数一： URL 
    -参数二： 个回调，当服务器响应开始到达时这个回调会以一个IncomingMessage对象被调用,IncomingMessage对象是一个可读流。
    
	【注意】（如果是一个http://URL，必须使用http模块；如果是一个https://URL，必须使用https模块)
	
http.get()和https.get()是更通用的http.request()和https.request()函数的稍微简化的变体。
    下面这个postJSON()函数演示了如何使用https.request()发送一个包含JSON请求体的HTTPS POST请求。这个函数也期待一个JSON响应并返回一个期约，该期约将兑现为解析后的响应：
    
    const https = require('https');
	/*
	* 将 body 对象转换为 JSON 字符串， 然后通过 HTTPS POST 发送到指定 host 的指定 API 终端。
	*当响应到达时，将响应体作为 JOSN 解析，然后将其返回期约
	*/
	function postJSON(options = {host:'', endpoint:'',body:{},port:0,username:'',password:'',header: {}}) {
        // 立即返回期约对象，当请求成功或失败时调用 resolve/ reject
        let bodyText = JSON.stringify(body);
        // 配置HTTPS请求
        let requestOptions = {
            method: options.method || 'POST',
            host: options.host,
            path: options.endpoint,
            headers: {
                "Content-Type": 'application/json',
                "Content-Length": Buffer.byteLength(bodyText),
                ...options.header
            }
        }
        
        if(port) {
            requestOptions.port = options.port;
        }
        if(options.name && options.password) {
            requestOptions.auth = `${options.username}:${options.password}`
        }
        // 现在根据配置对象创建请求
        let request = https.request(requestOptions);
        
        // 写入 POST 请求体并结束请求
        request.write(bodyText);
        request.end();
        // 请求出错时失败
        request.on('error', e=> reject(e));
        
        // 当响应达到时处理响应
        request.on('response', response => {
            if(response.statusCode !== 200) {
                reject(new Error(`HTTP status ${response.statusCode}`));
                // 这里并不关心响应体，但不希望它逗留缓冲区，因此把流切换为流动模式，但不会注册”data“处理程序，因此响应体会被丢弃
                response.resume();
                return;
            }
        })
        
        // 我们想要文本，而非字节。假设文本时JSON格式，因此不检查 Content-Type 头部了
        response.setEncoding('utf8');
        
        response.on('end', ()=>{
            // 接收完响应体，尝试 作为JSON来解析为
            try {
                resolv(JSON.parse(body));
            } catch(e) {
                reject(e)
            }
        })
    }



2、除了发送HTTP和HTTPS请求，http和https模块也允许你编写响应这些请求的服务器。基本流程如下。
·创建一个新Server对象。
·调用它的listen()方法，开始监听指定端口的请求。
·为“request”事件注册处理程序，通过这个处理程序读取客户端请求（特别是request.url属性），然后写入你的响应。
	-不推荐写原生的，node框架 koa, express

```

### 非HTTP网络服务器及客户端

```js
1、如果你熟悉流的操作，那么网络这一块会相对简单，因为网络套接口就是一种双工流。net模块定义了Server和Socket类。要创建服务器，调用net.createServer()，然后调用返回对象的listen()方法告诉服务器监听哪个端口的连接。Server对象会在客户端连接到该端口时生成“connection”事件，而传给事件监听器的值就是一个Socket对象。这个Socket对象是一个双工流，可以使用它从客户端读取数据和向客户端写入数据。在这个Socket对象上调用end()可以断开链接。

	下面的代码演示了如何使用net模块写一个服务器。当客户端连接时，服务器会给它讲一个knock-knock笑话
	// 一个会讲 knock-knock 笑话 的TCP 服务器，监听端口 6789
	const net = require('net');
	const readline = require('readline');

	// 创建Server 对象，并开始监听连接
	let server = net.createServer();
	server.listen(6789, ()=> console.log('Delivering laughs no port 6789'));

	// 当客户端连接时， 给它讲一个 knock-knock 笑话
	server.on('connection', socket => {
        tellJoke(socket)
        	.then(()=> socket.end())// 讲完笑话就关闭
        	.catch(err => {
            	console.error(err);// 打印发生的错误
            	socket.end();
            })
    })
	
	// 这些笑话
	const jokes = {
        'Boo': 'Dont ary... its only a joke',
        'Lettuce: let us in! its frezing out here',
        'A little old lady: wow, idont konw you could yodel'
    };

	// 通过套接口交互表扬 knock-knock笑话， 不阻塞
	async function tellJoke(socket) {
        // 随机笑话
        let randowElement = a => a[Math.floor(Math.random()* a.length )];
        let who = randowElement(Object.keys(jokes));
        let punchline = jokes[who];
        
        let lineReader = readline.createInterface({
            input: socket,
            output: socket,
            prompt: '>>'
        });
        
        // 辅助函数
        function output(text, prompt= true) {
            socket.write(`${text}\r\n`);
            if (prompt) lineReader.prompt();
        }
        
        // knock-knock 是一种回应式结构
        // 不同阶段输入不同内容，然后再不同阶段输入后执行不同动作
        let stage = 0;
        
        // 传统方式
        output('Knock knock')
        
        // 现在异步从客户端读取， 知道笑话讲完
        for await(let inputLine of lineReader) {
            if(stage === 0) {
                if(inputLine.toLowerCase() ==== 'whos there?') {
                    output(who)
                    stage = 1;
                } else {
                    output('Please type Whos there?')
                }
            } else if(stage === 1) {
                if(inputLine.toLowerCase() === `${who.toLowerCase()} who?`) {
                    output(`${punchline}, false`)
                    return
                } else {
                    output(`Please type ${who} who?`)
                }
            }
        }
    }


// 客户端
	let socket = require('net').createConnection(6789, process.argv[2])
    socket.pipe(process.stdout);// 把数据从套接口引流到标准输出
	process.stdin.pipe(socket);// 把数据从标准输入引流至套接口
	socket.on('close', ()=> process.exit());// 关闭退出
```

### 　操作子进程

```js
　1、execSync()
	-execSync(要运行的命令, 编码)
	默认情况下，这个返回值是一个缓冲区，但你可以在可选的第二个参数中设置一个编码，从而得到一个字符串
    

	运行其他程序的最简单方式是使用child_process.execSync()。它会创建一个子进程，并在该进程中运行一个命令行解释器（shell），并使用该解释器执行你传入的命令。执行命令期间会阻塞，直到命令（及命令行解释器）退出。如果命令中存在错误，则execSync()会抛出异常。否则，execSync()将返回该命令写入其标准输出流的任何内容。如果命令向标准错误写入了任何输出，则该输出只会传给父进程的标准错误流。
    const child_process = require('child_process');
	let listing = child_process.execSync("ls -l web/*.html", {encoding: 'utf8'});

2、execFileSync()
	如果你不需要命令行的特性，可以使用child_process.execFileSync()来避免启动命令行的开销。
    -参数一： 入可执行文件
    -参数二： 传入命令行参数的数组
    -参数三： 可选参数对象
    let listing = child_process.execFileSync('ls', ["-l", 'web/'], {encodding: 'utf8'})
    
// 下面列出了其他你可能使用的比较重要的属性（注意，并非所有选项都适用于所有子进程函数）
    
cwd：指定子进程的当前工作目录。如果省略这个选项，那么子进程会继承process.cwd()的值。
    
env：指定子进程有权访问的环境变量。默认情况下，子进程简单地继承process.env，但你可以指定一个不同的对象。

input：指定应该作为子进程标准输入的输入数据的字符串或缓冲区。这个选项只能用于不返回ChildProcess对象的同步函数。

maxBuffer：指定exec函数可以收集的最大输出字节数（不适用于spawn()和fork()，它们使用流）。如果子进程产生的输出超过这个值，那它会被杀死并以错误退出。

shell：指定命令行解释器可执行文件的路径或true。对正常执行命令行程序的子进程函数，这个选项允许你指定使用哪个命令行。对正常不使用命令行的函数，这个选项允许你指定可以使用命令行（通过把这个属性设置为true）或指定具体使用哪个命令行。

timeout：指定允许子进程运行的最长毫秒数。如果到了这个时间它没有退出，它会被杀死并以错误退出（这个选项适用于exec()函数，但不适用于spawn()或fork()）。

uid：指定以哪个用户ID（数值）来运行程序。如果父进程在一个特权账号下运行，可以使用这个选项以较低权限运行子进程。

3、exec()
	顾名思义，execSync()和execFileSync()函数都是同步执行的，它们会阻塞直到子进程退出才会返回。
    -参数一： err
    -参数二: 子进程收集的发送到子进程标准输出流的输出
    -参数三：是发送到子进程标准错误流的输出

    exec()和execFile()与它们的同步变体相似，只不过会立即返回一个ChildProcess对象表示正在运行的子进程，而且接收一个错误在先的回调作为最后的参数。
	ChildProcess对象允许你终止子进程，向它写入数据
    
   // 此前介绍的各种exec函数，包括同步和异步函数，都是设计用来在子进程中执行简单且不产生太多输出的任务。即使异步的exec()和execFile()也不是流式的，它们都在进程退出之后一次性返回进程输出。
    
4、spawn()    
    child_process.spawn()函数允许在子进程运行期间流式访问子进程的输出。同时，它也允许向子进程写入数据（子进程将该数据作为自己标准输入流的输入）。这意味着可以动态与子进程交互，基于它的输出向它发送输入。
    spawn()默认不使用命令行解释器，因此必须像execFile()一样传入可执行文件及要传给它的命令行参数数组来调用它。spawn()与execFile()一样，也返回一个ChildProcess对象，但它不接收回调参数。虽然不使用回调，但可以监听这个ChildProcess对象或它的流发出的事件。
    
    ChildProcess对象: 
        -stdout和stderr 是可读流
        -stdin属性是可写的流
        -stdin属性是可写的流
		-kill()方法，用于终止子进程
5、fork()
	fork()接收与spawn()相同的参数，但第一个参数应该是JavaScript代码文件的路径而非可执行二进制文件的路径
    
	child_process.for()是一个特殊的函数，用于在一个Node子进程中运行一段JavaScript代码

    如前所述，使用fork()创建的子进程可以通过子进程的标准输入流和标准输出流与父进程通信。另外，fork()还在父进程和子进程之间提供了一种更简单的通信方式：
    在使用fork()创建子进程后，可以使用它返回的ChildProcess对象的send()方法向子进程发送一个对象的副本。可以监听这个ChildProcess的“message”事件，从子进程中接收消息。在子进程中运行的代码可以使用process.send()向父进程发送消息，也可以监听process的“message”事件，从父进程接收消息。
    eg:
		const child_process = require('child_process');
		// 启用一个新的Node进程， 运行我们目录中的 child.js
		let child = child_process.fork(`${__dirname}/child.js`);
		
        // 向子进程发送消息
		child.send({x: 4, y: 3});

		//收到子进程回应后把它打印出来
		child.on('message', message => {
            console.log(message.hypotenuse);// 5
            // 因为我们只发送一条消息，所以只期待一个回应
            // 收到回应后，我们调用 disconnect()终止父进程
            // 与子进程的连接。这样两个进程都可以明确退出
            child.disconnect();
        })


		子进程:
		// 等待父进程发来消息
		process.on('message', message => {
            // 收到消息后， 计算一个值， 把结果发回父进程
            process.send({hypotenuse: Math.hypot(message.x, message.y)})
        })

	启动子进程的代价是相当大的，如果子进程不能完成几个大数量级的计算，那么就不值得像这样使用fork()和进行进程间通信。如果你的程序需要保证对到来的事件快速响应，也需要执行耗时计算，那可以考虑使用一个独立的子进程去执行计算，从而不阻塞事件循环，也不影响父进程的响应速度（不过，在这种情况下，可能使用线程比使用进程更好，参见16.11节）。
    send()
    -参数一：会被JSON.stringify()序列化
    -参数二： 通过这个参数可以把Socket和Server对象（来自net模块）转移给子进程
```

## Stream 流

```js


测试：

// testServer.js
const fs = require('fs');
const server = require('http').createServer();


// big.file 是一个500M的大文件
server.on('request', (req, res) => {
    fs.readFile('./big.file', (err, data) => {
        if(err) throw err;
        res.end(data)
    })
})
server.listen(8000);

// 当访问端口 8000 时，node 内存占用 500M



// 流的方式
server.on('request', (req, res) => {
    // 创建可读流
    const src = fs.createReadStram('./big.file');
    // 将可读流导入可写流
  	src.pipe(res)  
})

此时访问 端口 8000，node 占用 ≈13M


流的类型

-Readable （可读流）
-Writeable （可写流）
-Duplex （双向流）
-Transform （转换流）


流的事件和方法

Readable Streams				
Events
	-data
	-end
	-error
	-close
	-readable
Functions
	-pipe(),unpipe()
	-read(),unshift(),resume()
	-pause(),isPaused()
	-setEncoding()

Writable Streams
Event
	-drain
	-finish
	-error
	-close
	-pipe/unpipe
Functions
	-write()
	-end()
	-cork(),uncork()
	-setDefaultEncoding()
```

![]()![node-stream](D:\Typora\workSpace\resource\node-stream.png)

## Path

```js
常用的 path 模块方法

1、basename,dirname,extname

const {basename,dirname,extname, resolve, join} = require('path');
consts filePaath = '/usr/local/bin/no.txt';

-basename(filePaath);// no.txt
-dirname(filePaath);// /usr/local/bin
-extname(filePaath);// .txt

2、join, resolve

-join 路径拼接
 join('/usr/', 'local', 'bin');// /usr/local/bin
-resolve 将相对路径转化为绝对路劲
 resolve('./');// /home/dex/web/nodejs

3、parse,format
const {parse, format} = require('path');
const filePath = '/usr/local/node_modules/n/iloveu.json'

-let ret =  parse(filePath)
{
    root: '/',
    dir: '/usr/local/node_modules/n',
    base: 'iloveu.json',
    ext: '.json',
    name: 'iloveu'
}

-format(ret); // /usr/local/node_modules/n/iloveu.json


4、
__dirname, __filename 总是返回文件的绝对路径
process.cwd() 总是返回执行 node 命令所在文件夹

相对路径./
在 require 方法中总是相对当前文件所在文件夹
在其他地方和 process.cwd() 一样，相对 node 启动文件夹
```

### 引入某个文件夹下所有文件内容

```js
1、echartOptions/index.js

const files = require.context('../echartOptions', true, /\.js$/);

返回一个对象
```

