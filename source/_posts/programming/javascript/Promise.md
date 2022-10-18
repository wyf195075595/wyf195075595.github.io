---
title: 如何理解Promise
date: 2022-06-17 08:23:10
tags: js
categories: js
---

> Promise 是一个对象，可以产生一个值或者它会返回一个没有产生值的原因。解决了回调地狱

```
1、同步的代码（最高）

2、微任务的异步代码（次高）.then()的优先级高于setTimeOut

3、宏任务的异步代码（最低）
```

手写一个promise

<!--more-->

```js
const State = {
    pedding : 'pedding',
    resolved : 'resolved',
    rejected : 'rejected'
}
const noop = ()=>{};
class MyPromise {
    constructor(cb) {
        // 测试1
        cb(this._resolve.bind(this), this._reject.bind(this));
    }
    _state = State.pedding;
    _value;
    _resArr = [];
    _resolve(val) {
        console.log('---test resolve---');

        if(this._state === State.pedding) {
            this._value = val;
            this._state = State.resolved
            // 执行 then 传入的 res 函数
            this._runResArr()
        }
        
    }
    _reject() {
        this._state = State.rejected
    }
    _runResArr() {
        while (this._resArr.length > 0) {
            const item = this._resArr.shift()

            const result = item.handle(this._value)

            if(result instanceof MyPromise) {
                result.then(val=> item.promise._resolve(val))
            } else {
                item.promise._resolve(result)
            }
        }
    }
    then(res, rej= noop) {
        let rs = new MyPromise(()=>{})
        let item = {promise: rs, handle: res}
        this._resArr.push(item)
        if(this._state === State.resolved) {
            this._runResArr()
            
        }
        console.log('---test then---');
        return rs;
    }
}


export default MyPromise

// 几种Promise的实现
// https:/lgithub.com/ericyang89/my-promise
// https://lgithub.com/vividbytes/implementing-promises
// https://tgithub.com/iam91/zpromise/blob/master/src/zpromises
```





```js
/**
 * @description 组合 函数式编程 (从右向左执行)
 * @param  {...any} funcs 执行函数 
 * @returns 
 */
export function compose(...funcs) {
  if (funcs.length === 0) {
    return args => args
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduceRight((a, b) => (...args) => b(a(...args)))
}

/**
 * @description 组合 函数式编程 异步 (从右向左执行)
 * @param  {...any} funcs 执行函数
 * @returns 
 */
export function composePromise(...funcs) {
  if (funcs.length === 0) {
    return args => args
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduceRight((a, b) => async (...args) => b(await a(...args)))
}
```

