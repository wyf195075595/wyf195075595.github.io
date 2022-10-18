---
title: jquery
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---

### ajax

<!--more-->

```js
1、$.ajax(url,[settings]) 
// 全局设置ajax默认选项
$.ajaxSetup({
  url: "/xmlhttp/",
  global: false,
  type: "POST"
});

$.ajax({
    url: '',
    async: true,
    beforeSend: function(XHR){},
    cache: false, // 设置为 false 将不缓存此页面
    complete: function(XHR, TS){},
    contents: '',// 一个以"{字符串:正则表达式}"配对的对象，用来确定jQuery将如何解析响应，给定其内容类型。
	contentType: 'application/x-www-form-urlencoded',
    context: '',// 设置Ajax相关回调函数的上下文。也就是说，让回调函数内this指向这个对象（如果不设定这个参数，那么this就指向调用本次AJAX请求时传递的options参数）。
    converters:  {
        "* text": window.String, 
        "text html": true, "text json": jQuery.parseJSON, 
        "text xml": jQuery.parseXML
    },// 一个数据类型对数据类型转换器的对象。每个转换器的值是一个函数，返回响应的转化值
	crossDomain: false, // 是否跨域请求
    data: {},
    dataFilter: function(data, type) {
        // data是Ajax返回的原始数据，type是调用jQuery.ajax时提供的dataType参数。
        return data
    },
    dataType: '',
	// "xml": 返回 XML 文档，可用 jQuery 处理。
    // "html": 返回纯文本 HTML 信息；包含的script标签会在插入dom时执行。
    // "script": 返回纯文本 JavaScript 代码。不会自动缓存结果。除非设置了"cache"参数。'''注意：'''在远程请求时(不在同一个域下)，所有POST请求都将转为GET请求。(因为将使用DOM的script标签来加载)
    // "json": 返回 JSON 数据 。
    // "jsonp": JSONP 格式。使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。
    // "text": 返回纯文本字符串
	error: function (XMLHttpRequest, textStatus, errorThrown) {
        // 通常 textStatus 和 errorThrown 之中
        // 只有一个会包含信息
        this; // 调用本次AJAX请求时传递的options参数
    },
    global: true, // 是否触发全局 AJAX 事件。设置为 false 将不会触发全局 AJAX 事件，如 ajaxStart 或 ajaxStop 可用于控制不同的 Ajax 事件。
	headers: {},// 覆盖beforeSend函数范围内的任何设置
    ifModified: false,// 仅在服务器数据改变时获取新数据
    jsonp: '',// 重写jsonp回调函数的名字
    jsonpCallback: '', // 指定回调函数名
	...


})

2、load(url,[data],[callback]) 
// 载入远程 HTML 文件代码并插入至 DOM 中。默认使用 GET 方式 - 传递附加参数时自动转换为 POST 方式。jQuery 1.2 中，可以指定选择符，来筛选载入的 HTML 文档，DOM 中将仅插入筛选出的 HTML 代码。
 
$("#feeds").load("feeds.php", {limit: 25}, function(){
   alert("The last 25 entries in the feed have been loaded");
 });

3、$.get(url,[data],[fn],[type]) 

4、$.getJSON(url,[data],[fn]) 
// jsonp 方式请求
$.getJSON("http://api.flickr.com/services/feeds/photos_public.gne?tags=cat&tagmode=any&format=json&jsoncallback=?", function(data){
  $.each(data.items, function(i,item){
    $("<img/>").attr("src", item.media.m).appendTo("#images");
    if ( i == 3 ) return false;
  });
});
5、$.getScript(url,[callback]) 
// 获取第三方脚本，可跨域
jQuery.getScript("http://dev.jquery.com/view/trunk/plugins/color/jquery.color.js", function(){
  $("#go").click(function(){
    $(".block").animate( { backgroundColor: 'pink' }, 1000)
      .animate( { backgroundColor: 'blue' }, 1000);
  });
});
6、$.post(url,[data],[fn],[type]) 

```

### ajax 事件

```html
1、AJAX 请求完成时执行函数
ajaxComplete(callback) 
2、AJAX 请求发生错误时执行函数
ajaxError(callback) 
3、AJAX 请求发送前执行函数。Ajax 事件。
ajaxSend(callback) 
4、AJAX 请求开始时执行函数。Ajax 事件。
ajaxStart(callback) 
5、AJAX 请求结束时执行函数。Ajax 事件
ajaxStop(callback) 
6、AJAX 请求成功时执行函数。Ajax 事件。
ajaxSuccess(callback) 


eg: ajaxSend 元素进行ajax请求时触发

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>欢迎来到蝴蝶教程</title>
//此版本是百度cdn 1.11.1，当然你可以使用更高的版本，从2.0版本以上的是不支持ie6-8的
<script type="text/javascript" src="http://libs.baidu.com/jquery/1.11.1/jquery.min.js"></script>
<script>
   $(document).ready(function () {
      $(document).ajaxSend(function(e,xhr,opt){
         $("div").append("<p>Requesting " + opt.url + "</p>");
      });
      $("button").click(function(){
         $("div").load("/jc_script/jqAjax/demo_test.txt");
      });
   });
</script>
</head>
<body>
      <div><h2>使用 AJAX 修改文本</h2></div>
      <button>修改内容</button>
</body>
</html>
```



### callback

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/jquery_callback.png)

```js
1、callback.add(callbacks)
	// 添加一个或一组回调函数
2、callback.remove(callbacks)
	// 从回调函数列表中删除一个或一组函数列表
3、callback.has(callback)
	// 检查函数列表中是否存在某指定函数
4、callback.empty()
	// 清空函数列表
5、callback.disable()
	// 禁用函数列表，不做任何事
6、callback.disabled()
	// 检查函数列表是否被禁用
7、callback.lock()
	// 锁定函数列表
8、callback.locked()
	// 判断函数列表是否被锁定
9、callback.fireWith(ctx, [...args])
	// 使用指定上下文和参数触发回调列表中所用函数
10、callback.fire(arguments)
	// 使用指定参数触发回调函数列表中所有回调函数
11、callback.fired()
	// 判断回调函数列表是否被触发过

【注意】 fire,fireWith 都是触发列表所有函数，不能指定调用列表中某一个函数。类似于发布订阅，
可以通过传递参数作为是否触发标识
```

### deferred

```js
jQuery的deferred对象详解
（1） $.Deferred() 生成一个deferred对象。

function getVideoList(curriculumid) {
    let deferred = $.Deferred();
    var that = this;
    var params = { "curriculumid": curriculumid };
    try {
        requsestSer.post(camEmotion.lessonVideo, params).then(function(data) {
            if (data.meta.success) {
                deferred.resolve();
            } else {
                deferred.reject();
            }
        }, function(response) {
            deferred.reject();
        });
    } catch (e) {
        deferred.reject();
    }
    return deferred.promise();
}


（2） deferred.done() 指定操作成功时的回调函数

（3） deferred.fail() 指定操作失败时的回调函数

（4） deferred.promise() 没有参数时，返回一个新的deferred对象，该对象的运行状态无法被改变；接受参数时，作用为在参数对象上部署deferred接口。

（5） deferred.resolve() 手动改变deferred对象的运行状态为"已完成"，从而立即触发done()方法。

（6）deferred.reject() 这个方法与deferred.resolve()正好相反，调用后将deferred对象的运行状态变为"已失败"，从而立即触发fail()方法。

（7） $.when() 为多个操作指定回调函数。

除了这些方法以外，deferred对象还有二个重要方法，上面的教程中没有涉及到。

（8）deferred.then()

有时为了省事，可以把done()和fail()合在一起写，这就是then()方法。

$.when($.ajax( "/main.php" ))
.then(successFunc, failureFunc );
如果then()有两个参数，那么第一个参数是done()方法的回调函数，第二个参数是fail()方法的回调方法。如果then()只有一个参数，那么等同于done()。

（9）deferred.always()

这个方法也是用来指定回调函数的，它的作用是，不管调用的是deferred.resolve()还是deferred.reject()，最后总是执行。
```



```js


源码

// String to Object options format cache
// {"once": true, "memory": true}
var optionsCache = {};
  
// Convert String-formatted options into Object-formatted ones and store in cache
function createOptions( options ) {
  // 变量object和flagsCache[ flags ]指向了同一个空对象，object改变的时候，optionsCatche也会改变
  var object = optionsCache[ options ] = {};
  // core_rnotwhite = /\S+/g,
  // console.log("111 222 333".match(/\S+/g));
  // ["111", "222", "333"]
  jQuery.each( options.match( core_rnotwhite ) || [], function( _, flag ) {
    // {"once": true, "memory": true}
    object[ flag ] = true;
  });
  return object;
}
/**
 * 四个参数的作用：
 * 1. once ：fire() 只能触发一次
 * 2. memory ：只要写入Callbacks里面的，不管先后顺序，都可以触发
 * 3. unique ：不会重复触发相同的函数
 * 4. stopOnFalse ：某个回调函数返回false之后中断后面的回调函数
 */
jQuery.Callbacks = function( options ) {
  
  // Convert options from String-formatted to Object-formatted if needed
  // (we check in cache first)
  // options 可以传入一个字符串或一个对象
  // {"test": "true", "one": "true", "two": "true", "three": "true"}
  // 先尝试从缓存对象flagsCache中获取标记字符串flags对应的标记对象，如果没有找到，再调用工具函数createFlags(flags)将标记字符串flags解析为标记对象，并放入缓存对象flagsCache中
  options = typeof options === "string" ?
    ( optionsCache[ options ] || createOptions( options ) ) :
    // jQuery.extend() 函数用于将一个或多个对象的内容合并到目标对象。如果目标对象有相同属性，后面的覆盖前面的
    jQuery.extend( {}, options );

  var // Last fire value (for non-forgettable lists)
    // 最近一次触发回调的参数
    // 变量memory的初始值为undefined，表示当前回调函数列表未被触发过
    memory,
    // Flag to know if list was already fired
    // 是否回调列表已经执行过至少一次
    fired,
    // Flag to know if list is currently firing
    // 回调列表是否在执行中
    firing,
    // First callback to fire (used internally by add and fireWith)
    // 初始执行的位置
    firingStart,
    // End of the loop when firing
    // 执行过程的结尾
    firingLength,
    // Index of currently firing callback (modified by remove if needed)
    // 正在执行回调函数的索引
    firingIndex,
    // Actual callback list
    // 回调列表
    list = [],
    // Stack of fire calls for repeatable lists
    // 可重复的回调函数堆栈，用于控制触发回调时的参数列表
    // once的作用在这里体现（once：只能触发一次）
    // stack = false
    stack = !options.once && [],

    /**
     * 我终于看懂啥意思了，首先看add函数，看完了吧，之后咱看fire函数
     */
    // Fire callbacks
    // 触发回调函数列表
    // fire(context,args)，参数context用于指定回调函数执行时的上下文，即关键字this所引用的对象，参数args用于指定调用回调函数时传入的参数
    fire = function( data ) {
      memory = options.memory && data;
      // 是否回调列表已经执行过至少一次
      fired = true;
      firingIndex = firingStart || 0;
      firingStart = 0;
      firingLength = list.length;
      // 回调列表是否在执行中
      firing = true;
      for ( ; list && firingIndex < firingLength; firingIndex++ ) {
        // 有关stopOnFalse的代码
        // db.fire();   /   db.fire(context, "XXX") 可以传参数，也可以不传参数
        // 如果执行这个函数的返回值是false，且user需要stopOnFalse，就终止循环
        // 且如果有memory，也改为false
        if ( list[ firingIndex ].apply( data[ 0 ], data[ 1 ] ) === false && options.stopOnFalse ) {
          memory = false; // To prevent further calls using add
          break;
        }
      }
      firing = false;
      // 如果list里面有内容，说明是刚才存下来的，得把里面的参数都执行了
      // 为什么不用while循环执行stack里面的内容，因为是递归，下次还会检查list里面是否有内容
      if ( list ) {
        if ( stack ) {
          // stack = !options.once && [],
          if ( stack.length ) {
            // shift () 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值
            // 删除，说明这个已经执行过了，之后不用再执行了
            fire( stack.shift() );
          }
          // once + memory 模式
          // 则清空数组list，后续添加的回调函数还会立即执行
        } else if ( memory ) {
          list = [];
        } else {
          self.disable();
        }
      }
    },
    // self是最后返回的对象
    // 添加一个或多个回调函数到数组list中。如果是unique模式，并且待添加的回调函数已添加过，则不会添加。该函数通过闭包机制引用数组list
    self = {
      // Add a callback or a collection of callbacks to the list
      // 方法callbacks.add()用于添加一个或一组回调函数到回调函数列表中，通过调用工具函数add(args)实现；在memory模式下，如果回调函数列表未在执行中，并且已经被触发过，则立即执行新添加的回调函数。
      add: function() {
        // 如果list存在
        if ( list ) {
          // 从list的末尾开始添加
          var start = list.length;
          // 匿名函数自执行 add the function to list
          // cb.add(aaa, bbb);
          // 把回调函数逐个添加到数组list中。添加时检查args[i]的类型，如果args[i]是数组，则迭代调用工具函数add(args)把数组中的回调函数添加到数组list中；如果args[i]是函数并且不是unique模式，或者是unique模式但未添加过，才会添加args[i]到数组list中。函数以外的类型，则被忽略。
          (function add( args ) {
            jQuery.each( args, function( _, arg ) {
              var type = jQuery.type( arg );
              if ( type === "function" ) {
                // if options.unique == true, this is said user need unique
                // so have to check list has arg or not
                if ( !options.unique || !self.has( arg ) ) {
                  list.push( arg );
                }
              // arg is function or sameFunction ([] or {0: , 1: , 2: , length: 3})
              // cb.add([aaa, bbb], ccc);
              } else if ( arg && arg.length && type !== "string" ) {
                // 递归调用
                add( arg );
              }
            });
          })( arguments );
          // Do we need to add the callbacks to the
          // current firing batch?
          // 如果正在fire list里面的内容
          // 改变firingLength的长度
          // 修正结束下标firingLength，使得新添加的回调函数也得以执行。
          if ( firing ) {
            firingLength = list.length;
          // With memory, if we're not firing then
          // we should call right away
          // 如果在调用这个函数之前，设置了memory状态，直接将这个函数之后所有函数fire一遍
          } else if ( memory ) {
            firingStart = start;
            fire( memory );
          }
        }
        return this;
      },
      // Remove a callback from the list
      // cb.remove(aaa, bbb);
      remove: function() {
        if ( list ) {
          jQuery.each( arguments, function( _, arg ) {
            var index;
            // 万一有多个，所以用while
            while( ( index = jQuery.inArray( arg, list, index ) ) > -1 ) {
              list.splice( index, 1 );
              // Handle firing indexes
              // 则会修正结束下标firingLength和当前下标firingIndex，确保移除的同时不会遗漏执行回调函数。
              if ( firing ) {
                if ( index <= firingLength ) {
                  firingLength--;
                }
                if ( index <= firingIndex ) {
                  firingIndex--;
                }
              }
            }
          });
        }
        return this;
      },
      // Check if a given callback is in the list.
      // If no argument is given, return whether or not list has callbacks attached.
      // 如果!!( list && list.length )成立，fn为null undefined 都返回true
      has: function( fn ) {
        return fn ? jQuery.inArray( fn, list ) > -1 : !!( list && list.length );
      },

      // Remove all callbacks from the list
      empty: function() {
        list = [];
        firingLength = 0;
        return this;
      },
      // Have the list do nothing anymore
      // 禁用回调列表中的回调
      disable: function() {
        list = stack = memory = undefined;
        return this;
      },
      // Is it disabled?
      // 判断是否处于disabled状态，返回true或false
      disabled: function() {
        return !list;
      },
      // Lock the list in its current state
      // 回调列被锁死，再调用callbacks.fire()或callbacks.fireWith()都将失效
      // callbacks有memory标记：当前fire()或fireWith()方法中没有执行的回调会继续执行，但回调中的callbacks.fire()和callbacks.fireWith()都不会再起作用。
	  // callbacks无memory标记：所有回调全部被清空，也就是说后面的回调都不再执行。
      lock: function() {
        stack = undefined;
        if ( !memory ) {
          self.disable();
        }
        return this;
      },
      // Is it locked?
      locked: function() {
        return !stack;
      },
      // Call all callbacks with the given context and arguments
      // 使用指定的上下文context和参数args调用数组list中的回调函数。该函数通过闭包机制引用数组list
      // 在memory模式下，回调函数列表的方法callbacks.add()在添加回调函数后，如果回调函数列表未禁用并且已经被触发过，也会调用工具函数fire(context,args)来立即执行新添加的回调函数。
      fireWith: function( context, args ) {
        // fired是是否至少执行过一次，如果没执行过，是false，可以执行下面的内容
        // 如果之前执行过，再看看stack是否存在，如果stack存在，也可以执行下面的内容
        if ( list && ( !fired || stack ) ) {
          args = args || [];
          args = [ context, args.slice ? args.slice() : args ];
          if ( firing ) {
            // 如果执行函数的时候，不需要参数，args就是[]
            // 如果执行函数的时候，需要函数，args里面就是有内容的
            // stack表示需要执行几次list里面的内容，并附带有context和args
            // 如果list正在firing，将args放入stack，之后再执行
            stack.push( args );
          } else {
            // 如果list不在firing，直接执行
            fire( args );
          }
        }
        return this;
      },
      // Call all the callbacks with the given arguments
      fire: function() {
        // this是context，arguments是cb.fire()里面带的参数，意思就是执行list函数的时候，函数都可以带着arguments里面的参数
        self.fireWith( this, arguments );
        return this;
      },
      // To know if the callbacks have already been called at least once
      fired: function() {
        return !!fired;
      }
    };

  return self;
};
```

