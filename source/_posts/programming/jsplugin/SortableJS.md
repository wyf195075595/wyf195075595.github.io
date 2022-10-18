---
title: SortableJS
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---

> 功能强大的JavaScript 拖拽库
>
> 可以实现同组内上下拖动元素，也可以实现不同组内互相拖动元素。

###  兼容性好

支持触屏设备和大部分浏览器

### 简单

简单的API，方便使用

### 原生

基于原生HTML5中的拖放API

### CSS框架兼容性

支持所有的css框架，像Bootstrap

### 零依赖

不依赖Jquery等其他框架

### SPA支持良好

支持多种框架（angular、vue、react等）

<!--more-->

### 一、安装

```js
npm i sortablejs -S
```

### 二、sortablejs 最基本的示例

<div id="itxst">
  <div data-id="a">item 1</div>
  <div data-id="b">item 2</div>
  <div data-id="c">item 3</div>
</div>
```js
var el = document.getElementById('itxst');
//设置配置
var ops = {
    animation: 200,
    group: "name",
    //拖动结束
    onEnd: function (evt) {
        //获取拖动后的排序
        var arr = sortable.toArray();
        // arr数组里的值是 data-id 的顺序
        console.log({evt, arr})
    },
};
//初始化
var sortable = Sortable.create(el, ops);
```

### 三、常用配置

```js
const config = {
  //一个网页存在多个分组时设置，组名相同的组之间元素可以相互拖拽
  group: "name",
  //2种group写法选一种就可以了
  group: { 
    name: 'name',
    pull: 'clone', //克隆元素
  },
  //是否允许元素内部排序，如果为false当有多个排序组时,多个组之间可以拖拽，本身不能拖拽（默认true）
  sort: true,
  //是否禁用拖拽和排序
  disabled: false,
  //动画效果持续时间（不设置或0都没有过渡效果）
  animation: 150,
  //点击指定class类的元素才能拖拽（比如点击元素内的图标才能拖拽元素，可以给图标设置my-handle class）
  //class可以定义在元素本身上，也可以定义在子元素上
  handle: ".my-handle",
  // class为ignore的元素不能拖动
  filter: ".ignore",
  //含有item 类的元素可以被拖拽（class只能定义在元素本身上）
  draggable: ".item",
  //指定获取拖动后排序的属性
  dataIdAttr: 'data-id',
  //给停靠位置添加的class（可以给这个class定义样式）
  ghostClass: "ghost",
  //选中元素添加的类（包括悬浮的元素和停靠位置的元素）
  chosenClass: "chosen",
  //拖拽对象移动时添加的类
  dragClass: "drag",
  //禁用html5原生拖拽
  forceFallback: false,
  ...

  //克隆事件
  onClone: function (evt) {
      //被克隆的对象（被移到另外地方的那个元素）
      var origEl = evt.item;
      //克隆后的对象（还是在原来位置的元素）
      var cloneEl = evt.clone;
      cloneEl.innerHTML = "clone出的元素";
  },
  ...
}
```


