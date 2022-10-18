---
title: Date日期对象
date: 2022-06-17 08:23:10
tags: js
categories: js
---
# Date日期对象

<!--more-->

```js
1、创建date对象

let now = new Date(); // 当前时间

2、传入数值

let epoch = new Date(0); // 会将其解释为自1970年至今经过的毫秒数：

3、传入多个整数参数

// 解析为本地时区的 年、月、日、时、分、秒和毫秒
//【注】Date() 构造函数默认它们都为0，将时间设置为半夜12点。
let century = new Date(2020, 8, 1, 2, 3, 4, 5);

// 可以使用Date.UTC()。这个静态方法接收与Date()构造函数同样的参数，但使用UTC来解释它们，并返回毫秒时间戳，可以传给Date()构造函数
UTC（Universal Coordinated Time，通用协调时间；也称GMT，即Greenwich Mean Time，格林尼治标准时间）

let century = new Date(Date.UTC(2020, 8, 1, 2, 3, 4, 5));


4、传入字符串

它会尝试按照日期和时间格式来解析该字符串。

let t = new Date('2020/12/08 10:10:10')

5、时间戳

Date.now() ; // 返回 绝对时间 当前时间的时间戳， 精确到毫秒
performance.now(); // 相对于网页加载完成后或Node进程启动后经过的时间。精确到分秒

6、日期计算

日期对象可以使用 比较操作符（>, >=, <, <=,）和基本运算符（+ - * ...）。实际是对日期的时间戳进行操作

要完成涉及天数、月数和年数的计算，可以使用setDate()、setMonth()和setYear()

出现溢出情况时（如月份超出 12，天超出当前月份最大天数 ）会自动进位 月、年到下个月份和年份要完成涉及天数、月数和年数的计算，可以使用setDate()、setMonth()和setYear()

7、日期格式化

toString()
	这个方法使用本地时区但不按照当地惯例格式化日期和时间。

toUTCString()
	这个方法使用UTC时区但不按照当地惯例格式化日期。

toISOString()
	这个方法以标准的ISO-8601“年-月-日时:分:秒:毫秒”格式打印日期和时间。字母“T”在输出中分隔日期部分和时间部分。时间以UTC表示，可以通过输出末尾的字母“Z”看出来。

toLocaleString()
	这个方法使用本地时区及与用户当地惯例一致的格式。

toDateString()
	这个方法只格式化Date的日期部分，忽略时间部分。它使用本地时区，但不与当地惯例适配。

toLocaleDateString()
	这个方法只格式化日期部分。它使用本地时区，也适配当地惯例。

toTimeString()
	这个方法只格式化时间部分。它使用本地时区，但不与当地惯例适配。

toLocaleTimeString()
	这个方法只格式化时间部分。它使用本地时区，也适配当地惯例。、

7、Date.parse
	接收一个字符串参数，并尝试将其作为日期和时间来解析，返回一个表示该日期的时间戳。
```



| 赋某日期为日期类型                                           | new Date(myYear,myMonth,myDate);//当myDate为0时，返回当前月份的上月末日期 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 当前日期now                                                  | new Date()                                                   |
| 设置日期分钟为0                                              | now.setMinutes(0)                                            |
| 设置当前日期秒为1                                            | now.setSeconds(1）                                           |
| 当前日期为本周的第几天,星期三返回3nowDayOfWeek               | now.getDay()                                                 |
| 当前日nowDay                                                 | now.getDate()                                                |
| 当前月nowMonth                                               | now.getMonth();//0为一月，1为2月，...                        |
| 当前年nowYear                                                | nowYear = now.getYear();nowYear += nowYear<2000?1900:0;      |
| 格式化日期：yyyy-MM-dd                                       | function formatDate(date,fmt) {  var myyear = date.getFullYear();  var mymonth = date.getMonth()+1;  var myweekday = date.getDate();  if(mymonth < 10){  mymonth = "0" + mymonth;  }  if(myweekday < 10){  myweekday = "0" + myweekday;  }  return (myyear+"-"+mymonth + "-" + myweekday);  } |
| 格式化日期：任意格式                                         | function dateFtt(fmt,date) {  var o = {   "M+" : date.getMonth()+1, //月份   "d+" : date.getDate(), //日   "h+" : date.getHours(), //小时   "m+" : date.getMinutes(), //分   "s+" : date.getSeconds(), //秒   "q+" : Math.floor((date.getMonth()+3)/3), //季度   "S" : date.getMilliseconds() //毫秒  };  if(/(y+)/.test(fmt))   fmt=fmt.replace(RegExp.$1, (date.getFullYear()+"").substr(4 - RegExp.$1.length));  for(var k in o)   if(new RegExp("("+ k +")").test(fmt))    fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));  return fmt; } |
| 计算月份的天数                                               | function getMonthDays(nowYear,myMonth){ //此处计算的是一个月的天数 var monthStartDate = new Date(nowYear, myMonth, 1);  var monthEndDate = new Date(nowYear, myMonth + 1, 1);  var days = (monthEndDate - monthStartDate)/(1000 * 60 * 60 * 24);  return days;  } |
|                                                              |                                                              |
| 本周的开始日期 weekStartDate                                 | new Date(nowYear, nowMonth, nowDay - nowDayOfWeek);          |
| 本周的结束日期 weekEndDate                                   | new Date(nowYear, nowMonth, nowDay + (6 - nowDayOfWeek));    |
| 本月初monthStartDate                                         | new Date(nowYear, nowMonth, 1);                              |
| now.setDate(1);                                              |                                                              |
| 本月末monthEndDate                                           | var = new Date(nowYear, nowMonth, getMonthDays(nowMonth));   |
| 本月所属季度nowQuarter                                       | nowMonth ++;parseInt(nowMonth/3+(nowMonth%3==0?0:1))         |
| nowMonth ++;Math.ceil(nowMonth/3)                            |                                                              |
| 上月初lastMonthStart                                         | new Date(nowYear, lastMonth, 1);                             |
| lastMonthDate = new Date();lastMonthDate.setDate(1);lastMonthDate.setMonth(lastMonthDate.getMonth()-1) |                                                              |
| new Date(myYear,myMonth-1,1)                                 |                                                              |
| 上月末lastMonthEnd                                           | new Date(nowYear, lastMonth, getMonthDays(lastMonth));       |
| new Date(myYear,myMonth,0);                                  |                                                              |
| 本季度开始月份quarterStartMonth                              | if(nowMonth<3){  return 0;  }else if(2<nowMonth && nowMonth<6){  return 3;  }else if(5<nowMonth && nowMonth<9){  return 6;  }else{ //if(nowMonth>8){  return 9;  } |
| 本季度的开始日期quarterStartDate                             | new Date(nowYear, quarterStartMonth, 1);                     |
| 本季度的结束日期quarterEndDate                               | var quarterEndMonth = quarterStartMonth + 2;  var quarterStartDate = new Date(nowYear, quarterEndMonth, getMonthDays(quarterEndMonth)); |
| var quarterEndMonth = quarterStartMonth + 2;  var quarterStartDate = new Date(nowYear, quarterEndMonth+1, 0); |                                                              |
| 取与今天偏差为o天的日期                                      | new Date(now.getTime()+o*24*60*60*1000)                      |
| 计算两个日期的相差天数                                       | function dateDiff(startDate, endDate){  var start = new Date(startDate.getFullYear(), startDate.getMonth(), startDate.getDate());   var end = new Date(endDate.getFullYear(), endDate.getMonth(), endDate.getDate());   return parseInt(Math.abs(end - start ) / 1000 / 60 / 60 /24);}; |



## 实例

### 获取本周、本月，上周、上月的开始结束时间

```js
  //获取本周、上周、本月、上月 的开始日期和结束日期 年-月-日
  //optType: 1 获取周、 2 获取月
  //optPageNum: 0 本周/本月； 上周/月 1 , 1+n
 function getAppointedDate(optType,optPageNum){
  　　let now = new Date(), //当前日期
  　　nowDayOfWeek = now.getDay(), //今天本周的第几天
  　　nowDay = now.getDate(), //当前日
  　　nowMonth = now.getMonth(), //当前月
  　　nowYear = now.getFullYear(), //当前年
  　　lastMonth = nowMonth -1 ;
  　　let startDate = '', endDate= '';
      function getMonthDays(nowYear,myMonth) {
            　　let monthStartDate = new Date(nowYear, myMonth, 1);
            　　let monthEndDate = new Date(nowYear, myMonth + 1, 1);
            　　let days = (monthEndDate - monthStartDate) / (1000 * 60 * 60 * 24);
            　　return days;
        }
      function formatDateFn(date) {
            　　let myyear = date.getFullYear();
            　　let mymonth = date.getMonth() + 1;
            　　let myweekday = date.getDate();
            　　if (mymonth < 10) {
            　　　　mymonth = "0" + mymonth;
            　　}
            　　if (myweekday < 10) {
            　　myweekday = "0" + myweekday;
            　　}
            　　return (myyear + "-" + mymonth + "-" + myweekday);
            
        }
  　　if(optType==1){
  　　　　startDate = formatDateFn (new Date(nowYear, nowMonth, nowDay - nowDayOfWeek + 1 - (7*optPageNum)))
  　　　　endDate = formatDateFn (new Date(nowYear, nowMonth, nowDay + 7 - nowDayOfWeek -(7*optPageNum) ))
  　　} else if(optType==2) {
  　　　　startDate = formatDateFn (new Date(nowYear, lastMonth+1-(1*optPageNum), 1))
  　　　　endDate = formatDateFn (new Date(nowYear, lastMonth+1-(1*optPageNum) , getMonthDays(nowYear,lastMonth+1-(1*optPageNum)) ))
  　　}
  　　return {startDate: startDate, endDate: endDate}
}
```

### 获取当前日期前n天时间

```js
//datt = '2018-07-07'
function getBeforeDate(datt, n) {
    // var datt = '2018-07-07'.split('-');//这边给定一个特定时间
    datt = datt.split('-');
    var newDate = new Date(datt[0], datt[1] - 1, datt[2]);
    var befminuts = newDate.getTime() - 1000 * 60 * 60 * 24 * parseInt(n); //计算前几天用减，计算后几天用加，最后一个就是多少天的数量 n为向前或者向后天数
    var beforeDat = new Date;
    beforeDat.setTime(befminuts);
    var befMonth = beforeDat.getMonth() + 1;
    var mon = befMonth >= 10 ? befMonth : '0' + befMonth;
    var befDate = beforeDat.getDate();
    var da = befDate >= 10 ? befDate : '0' + befDate;
    var newDate = beforeDat.getFullYear() + '-' + mon + '-' + da;
    return newDate
}
```

### 日期格式化

```js
function timeFormater(t,format){
    format = format ||  'yyyy-MM-dd HH:mm:ss';
    let date = new Date(t),
    mounth = date.getMonth() + 1,
    day = date.getDate(),
    hour = date.getHours(),
    m = date.getMinutes(),
    s = date.getSeconds();
  const config = {
      yyyy: date.getFullYear(),
      MM:mounth.toString().padStart(2,'0'),
      dd: day.toString().padStart(2,'0'),
      HH: hour.toString().padStart(2,'0'),
      mm: m.toString().padStart(2,'0'),
      ss:  s.toString().padStart(2,'0')
  };
  for (const key in config) {
      format = format.replace(key, config[key]);
  }
  return format;
}

console.log(timeFormater(Date.now()))
```

### 时间单位

```js
时间戳： 为了方便计算机处理时间而诞生，表示从1970年1月1日开始到现在时间的毫秒数
可以理解为时间精确度（1毫秒级）

1秒 = 1000 毫秒

1毫秒 = 1000 微妙

1微妙 = 1000 纳秒

因此， 1毫秒 = 1000000（一百万） 纳秒

1毫秒 = 10000  100纳秒（面对时间精度为100纳秒的时间s，时间戳需*10000）
```

