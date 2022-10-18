---
title: Rxjs
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---

> RxJS 是一个库，它通过使用 observable 序列来编写异步和基于事件的程序。它提供了一个核心类型 [Observable](https://cn.rx.js.org/manual/overview.html#observable)，附属类型 (Observer、 Schedulers、 Subjects) 和受 [Array#extras] 启发的操作符 (map、filter、reduce、every, 等等)，这些数组操作符可以把异步事件作为集合来处理。 通常集成在 Angular技术栈中
>
> 可以把 RxJS 当做是用来处理事件的 [Lodash](https://lodash.com/) 。
>
> ReactiveX 结合了 [观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)、[迭代器模式](https://en.wikipedia.org/wiki/Iterator_pattern) 和 [使用集合的函数式编程](http://martinfowler.com/articles/collection-pipeline/#NestedOperatorExpressions)，以满足以一种理想方式来管理事件序列所需要的一切。
>
> 在 RxJS 中用来解决异步事件管理的的基本概念是：
>
> - **Observable (可观察对象):** 表示一个概念，这个概念是一个可调用的未来值或事件的集合。
> - **Observer (观察者):** 一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
> - **Subscription (订阅):** 表示 Observable 的执行，主要用于取消 Observable 的执行。
> - **Operators (操作符):** 采用函数式编程风格的纯函数 (pure function)，使用像 `map`、`filter`、`concat`、`flatMap` 等这样的操作符来处理集合。
> - **Subject (主体):** 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
> - **Schedulers (调度器):** 用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 `setTimeout` 或 `requestAnimationFrame` 或其他。
>
> 官网 https://rxjs.dev/
>
> https://www.youtube.com/watch?v=BA1vSZwzkK8 
>
> 官网：https://rxjs-dev.firebaseapp.com/ 
>
> 图解rxjs: https://reactive.how/
>
> rxjs可视化工具： https://rxviz.com/

使用

<!--more-->

```js



1、创建 Observable  (可观察对象)
let obs$ = rxjs.Observable.create(function(observer) {
    try {
        observer.next(1);
        observer.complete();
    } catch (err) {
        observer.error(err)
    }
})


let click$ = rxjs.fromEvent(Dom, 'click')// 返回一个可观察对象

2、Subscription 订阅
// 直接传 函数
click$.Subscription(event=> {
    console.log('点击了：', event)
})

// 对象
var observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete:()=> console.log('结束了')
};
let subscription = obs$.Subscription(observer)
// 取消订阅
subscription.unsubscribe();

eg: 
.建立可观察的 Observable 物件
	var click$ = rxjs.fromEvent(document, 'click');
.建立观察者物件 （Observer）
	var observer = {next: x=> console.log(x)}
.建立订阅物件（订阅 Observable物件，并传入 Observer 观察者物件）
	var subs$ = click$.subscribe(observer)
.取消订阅 Subscription 物体
	subs$.unsubscribe();

3、Subject (主体)
.建立主体物件 （Subject）（之后要考这个主体物件进行广播）
    var subject = new Rx.Subject();
.建立可观察的 Observable 物件
	var click$ = rxjs.fromEvent(document, 'click');
.设定最多取得两个事件资料就将 Observable 物件设为完成
	click$ = click$.pipe(take(2));
.设定 click$ 全部交由 subject 主物件进行广播
	click$.subscribe(subject);
.最后再由 subject 去建立 Observer 观察者物件
	var sub1$ = subject.subscribe(x=> console.log(x.clientX))
	var sub2$ = subject.subscribe(x=> console.log(x.clientY))
.取消订阅 Subscription 物件
	sub1$.unsubscribe();
	sub2$.unsubscribe();


4、操作符
const {interval} = rxjs;
const {take} = rxjs.oprators

interval(500)// 没500毫秒 触发一次
    .pipe(
    	map(x=> x*x),// 运算符， 结果 变成平方运算
		take(4)// 运算符，只取其中前四次
	)
    .subscribe(console.log)// 订阅
```

### 操作符

```js
动画演示操作符功能 https://rxmarbles.com/

操作符分类
操作符有着不同的用途，它们可作如下分类：创建、转换、过滤、组合、错误处理、工具，等等。在下面的列表中，你可以按分类组织好的所有操作符。

创建操作符
    ajax
    bindCallback
    bindNodeCallback
    create
    defer
    empty
    from
    fromEvent
    fromEventPattern
    fromPromise
    generate
    interval
    never
    of
    repeat
    repeatWhen
    range
    throw
    timer
转换操作符
    buffer
    bufferCount
    bufferTime
    bufferToggle
    bufferWhen
    concatMap
    concatMapTo
    exhaustMap
    expand
    groupBy
    map
    mapTo
    mergeMap
    mergeMapTo
    mergeScan
    pairwise
    partition
    pluck
    scan
    switchMap
    switchMapTo
    window
    windowCount
    windowTime
    windowToggle
    windowWhen
过滤操作符
    debounce
    debounceTime
    distinct
    distinctKey
    distinctUntilChanged
    distinctUntilKeyChanged
    elementAt
    filter
    first
    ignoreElements
    audit
    auditTime
    last
    sample
    sampleTime
    single
    skip
    skipLast
    skipUntil
    skipWhile
    take
    takeLast
    takeUntil
    takeWhile
    throttle
    throttleTime
组合操作符
    combineAll
    combineLatest
    concat
    concatAll
    exhaust
    forkJoin
    merge
    mergeAll
    race
    startWith
    switch
    withLatestFrom
    zip
    zipAll
多播操作符
    cache
    multicast
    publish
    publishBehavior
    publishLast
    publishReplay
    share
    错误处理操作符
    catch
    retry
    retryWhen
    工具操作符
    do
    delay
    delayWhen
    dematerialize
    finally
    let
    materialize
    observeOn
    subscribeOn
    timeInterval
    timestamp
    timeout
    timeoutWith
    toArray
    toPromise
    条件和布尔操作符
    defaultIfEmpty
    every
    find
    findIndex
    isEmpty
    数学和聚合操作符
    count
    max
    min
    reduce
```



> Rxjs并不等同于“响应式编程”，它只是“函数式编程”和“响应编程式”相结合的一种实现，如果喜欢，就请熟练掌握它，如果不喜欢，就请换种方式实现它，适合自己团队的技术，才是最好的。