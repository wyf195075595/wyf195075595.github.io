---
title: momentjs时间处理类库
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---

### 1、安装

```js
npm install moment --save   # npm
yarn add moment             # Yarn
Install-Package Moment.js   # NuGet
spm install moment --save   # spm
meteor add momentjs:moment  # meteor
```

### 2、日期格式化

| 格式代码 | 说明                       | 返回值例子                 |
| -------- | -------------------------- | -------------------------- |
| M        | 数字表示的月份，没有前导零 | 1到12                      |
| MM       | 数字表示的月份，有前导零   | 01到12                     |
| MMM      | 三个字母缩写表示的月份     | Jan到Dec                   |
| MMMM     | 月份，完整的文本格式       | January到December          |
| Q        | 季度                       | 1到4                       |
| D        | 月份中的第几天，没有前导零 | 1到31                      |
| DD       | 月份中的第几天，有前导零   | 01到31                     |
| d        | 星期中的第几天，数字表示   | 0到6，0表示周日，6表示周六 |
| ddd      | 三个字母表示星期中的第几天 | Sun到Sat                   |
| dddd     | 星期几，完整的星期文本     | 从Sunday到Saturday         |
| w        | 年份中的第几周             | 如42：表示第42周           |
| YYYY     | 四位数字完整表示的年份     | 如：2014 或 2000           |
| YY       | 两位数字表示的年份         | 如：14 或 98               |
| A        | 大写的AM PM                | AM PM                      |
| a        | 小写的am pm                | am pm                      |
| HH       | 小时，24小时制，有前导零   | 00到23                     |
| H        | 小时，24小时制，无前导零   | 0到23                      |
| hh       | 小时，12小时制，有前导零   | 00到12                     |
| h        | 小时，12小时制，无前导零   | 0到12                      |
| m        | 没有前导零的分钟数         | 0到59                      |
| mm       | 有前导零的分钟数           | 00到59                     |
| s        | 没有前导零的秒数           | 1到59                      |
| ss       | 有前导零的描述             | 01到59                     |
| X        | Unix时间戳                 | 1411572969                 |

<!--more-->

```js

1、引入
// require 方式
var moment = require('moment');

// import 方式
import moment from 'moment'; 

//浏览器方式引入
<script src="moment.js"></script>

//vue中引入
import moment from 'moment';
moment.locale('zh-cn');// 设置中文
Vue.prototype.$moment = moment; //原型挂载
this.$moment... //使用

2、设定时区为中国

// require 方式
require('moment/locale/zh-cn')：
moment.locale('zh-cn'); 

// import 方式
import 'moment/locale/zh-cn'
moment.locale('zh-cn'); 

3、使用 格式化时间

let time = Date.now();

moment(time).format('YYYY年MM月DD日')

4、常用
-本（上/下/...）周星期几 的日期
	moment().weekday(0)
	如果语言环境将星期一指定为一周的第一天，则 moment().weekday(0) 将会是星期一。 如果星期日是一周的第一天，则 moment().weekday(0) 将会是星期日。
与 moment#day 一样，如果超出范围，则它将会冒泡到其他星期。

-据今天（某天）前多少天（年/月/日/时/分/秒）
	moment().subtract(7, 'days');
	
-据今天（某天）后多少天（年/月/日/时/分/秒）
	moment().add(7, 'days');
    键		  快捷键
    years		 y
    quarters	 Q
    months		 M
    weeks		 w
    days		 d
    hours		 h
    minutes		 m
    seconds		 s
    milliseconds ms
    
    
    
    
    
    
moment.js获取本周本月本年的开始日期和结束日期
//获取本日
  const startDate = moment().format('YYYY-MM-DD'); 
  const startDate = moment().format('YYYY-MM-DD');
//获取本周
 const startDate = moment().week(moment().week()).startOf('week').format('YYYY-MM-DD');   //这样是年月日的格式
 const endDate = moment().week(moment().week()).endOf('week').valueOf(); //这样是时间戳的格式
          
//获取本月 
const startDate = moment().month(moment().month()).startOf('month').valueOf();
const endDate = moment().month(moment().month()).endOf('month').valueOf();

//获取本年
const startDate = moment().year(moment().year()).startOf('year').valueOf();
const endDate = moment().year(moment().year()).endOf('year').valueOf();
```

> 扩展连接
>
> [使用技巧]: https://www.cnblogs.com/renyi-fan/p/12743608.html
> [官方文档]: http://momentjs.cn/
>
> 