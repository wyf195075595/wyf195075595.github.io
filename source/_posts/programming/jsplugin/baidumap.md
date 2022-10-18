---
title: 百度地图
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---

### 开始准备

```js
// 准备百度账号 注册申请ak
// 地址
https://lbs.baidu.com/apiconsole/key#/home

// 可以选择不同的服务端

```

<img src="https://raw.githubusercontent.com/wyf195075595/images/main/blog/servertype.png" alt="image-20210929143915541" style="zoom: 80%;" />

[开发指南](https://lbs.baidu.com/index.php?title=jspopularGL/guide/widget)

### 使用

<!--more-->

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <title>异步加载</title>
    <script>
        function loadScript() {
            var script = document.createElement("script");
            // 通过传入callback参数， 异步加载，完成后自动调用 initialize 方法
            script.src = "https://api.map.baidu.com/api?v=1.0&type=webgl&ak=OAqziQFn6g7DYulqkgApSwDGm82htREn&callback=initialize";
            document.body.appendChild(script);
        }

        window.onload = loadScript;
    </script>
    <style>
        #map {
            width: 60%;
            height: 60vh;
            margin: 0 auto;
        }
    </style>
</head>

<body>

    <div id="map"></div>


    <script type="text/javascript">
        // 快速创建了一张以天安门为中心的地图。
        function initialize() {
            // 位于BMapGL命名空间下的Map类表示地图，通过new操作符可以创建一个地图实例。其参数可以是元素id也可以是元素对象。
            var map = new BMapGL.Map('map');
            // 设置中心点坐标  (经度，维度)
            var point = new BMapGL.Point(116.404, 39.915);
            // 地图初始化，同时设置地图展示级别
            map.centerAndZoom(point, 11);
            //开启鼠标滚轮缩放
            map.enableScrollWheelZoom(true);
            // 设置地图的旋转角度和倾斜角度

            //1设置地图旋转角度
            map.setHeading(64.5);
            //2设置地图的倾斜角度
            map.setTilt(73);

            // 设置地图类型为地球模式
            // map.setMapType(BMAP_EARTH_MAP);

            // 禁止地图旋转和倾斜可以通过配置项进行设置
            // var map = new BMapGL.Map("allmap",{
            //     enableRotate: false,
            //     enableTilt: false
            // });
            
            var scaleCtrl = new BMapGL.ScaleControl();  // 添加比例尺控件
            map.addControl(scaleCtrl);
            var zoomCtrl = new BMapGL.ZoomControl();  // 添加缩放控件
            map.addControl(zoomCtrl);
            var cityCtrl = new BMapGL.CityListControl();  // 添加城市列表控件
            map.addControl(cityCtrl);
            
            // 添加控件
            // map.addControl(new BMapGL.ScaleControl(opts));
            // 移除控件
            // map.removeControl(new BMapGL.ScaleControl(opts));
        }
    </script>
</body>

</html>
```

### 添加控件

| **控件**     | **类名**         | **简介**                                                   |
| ------------ | ---------------- | ---------------------------------------------------------- |
| 抽象基类     | Control          | 所有控件均继承此类的方法、属性。通过此类您可实现自定义控件 |
| 比例尺       | ScaleControl     | 默认位于地图左下方，显示地图的比例关系                     |
| 缩放         | ZoomControl      | 默认位于地图右下方，控制地图级别的缩放                     |
| 定位         | LocationControl  | 默认位于地图左下方，用于获取定位                           |
| 城市选择列表 | CityListControl  | 默认位于地图左上方，用于进行城市选择定位                   |
| 版权         | CopyrightControl | 默认位于地图左下方，用于展示版权信息                       |

#### [关于获取 本地外网，内网ip]()

```
通过IP定位


https://api.map.baidu.com/location/ip?ak=您的AK&ip=您的IP&coor=bd09ll //HTTP协议 

https://api.map.baidu.com/location/ip?ak=您的AK&ip=您的IP&coor=bd09ll //HTTPS协议
```

请求参数：

| 参数名称 |                           参数含义                           |  类型  | 备注 |
| :------: | :----------------------------------------------------------: | :----: | :--: |
|    ip    | 用户上网的IP地址，请求中如果不出现或为空，会针对发来请求的IP进行定位。 如您需要通过IPv6来获取位置信息，请[提交工单](http://lbsyun.baidu.com/apiconsole/fankui)申请。 | string | 可选 |
|    ak    | 开发者密钥，可在[API控制台](http://lbsyun.baidu.com/apiconsole/key)申请获得 | string | 必填 |
|    sn    | 若用户所用AK的校验方式为SN校验时该参数必填（[什么是SN校验？](http://lbsyun.baidu.com/index.php?title=lbscloud/api/appendix)）。其他AK校验方式的可不填写 | string | 可选 |
|   coor   | 设置返回位置信息中，经纬度的坐标类型，分别如下： coor不出现、或为空：百度墨卡托坐标，即百度米制坐标 coor = bd09ll：百度经纬度坐标，在国测局坐标基础之上二次加密而来 coor = gcj02：国测局02坐标，在原始GPS坐标基础上，按照国家测绘行业统一要求，加密后的坐标 注意：百度地图的坐标类型为bd09ll，如果结合百度地图使用，请注意坐标选择 | string | 可选 |

返回结果（JSON格式）：

| 结果字段       | 字段含义                               | 类型               |        |
| -------------- | -------------------------------------- | ------------------ | ------ |
| address        | 详细地址信息                           | string             |        |
| content        | address                                | 简要地址信息       | string |
| address_detail | city                                   | 城市               | string |
| city_code      | 百度城市代码                           | string             |        |
| province       | 省份                                   | string             |        |
| point          | x                                      | 当前城市中心点经度 | string |
| y              | 当前城市中心点纬度                     | string             |        |
| status         | 结果状态返回码，请参考本章节后面的介绍 | string             |        |

接口错误码

| 状态码 | 定义                                                         | 注释                                                         |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0      | 正常                                                         |                                                              |
| 1      | 服务器内部错误                                               | 该服务响应超时或系统内部错误                                 |
| 10     | 上传内容超过8M                                               | Post上传数据不能超过8M                                       |
| 101    | AK参数不存在                                                 | 请求消息没有携带AK参数                                       |
| 102    | Mcode参数不存在，mobile类型mcode参数必需                     | 对于Mobile类型的应用请求需要携带mcode参数，该错误码代表服务器没有解析到mcode |
| 200    | APP不存在，AK有误请检查再重试                                | 根据请求的AK，找不到对应的APP                                |
| 201    | APP被用户自己禁用，请在控制台解禁                            |                                                              |
| 202    | APP被管理员删除                                              | 恶意APP被管理员删除                                          |
| 203    | APP类型错误                                                  | 当前API控制台支持Server(类型1), Mobile(类型2, 新版控制台区分为Mobile_Android(类型21)及Mobile_IPhone（类型22）及Browser（类型3），除此之外的其他类型被认为是APP类型错误 |
| 210    | APP IP校验失败                                               | 在申请Server类型应用的时候选择IP校验，需要填写IP白名单，如果当前请求的IP地址不在IP白名单或者不是0.0.0.0/0就认为IP校验失败 |
| 211    | APP SN校验失败                                               | SERVER类型APP有两种校验方式：IP校验和SN校验，当用户请求的SN和服务端计算出来的SN不相等的时候，提示SN校验失败 |
| 220    | APP Referer校验失败                                          | 浏览器类型的APP会校验referer字段是否存在，且在referer白名单里面，否则返回该错误码 |
| 230    | APP Mcode码校验失败                                          | 服务器能解析到mcode，但和数据库中不一致，请携带正确的mcode   |
| 240    | APP 服务被禁用                                               | 用户在API控制台中创建或设置某APP的时候禁用了某项服务         |
| 250    | 用户不存在                                                   | 根据请求的user_id, 数据库中找不到该用户的信息，请携带正确的user_id |
| 251    | 用户被自己删除                                               | 该用户处于未激活状态                                         |
| 252    | 用户被管理员删除                                             | 恶意用户被加入黑名单                                         |
| 260    | 服务不存在                                                   | 服务器解析不到用户请求的服务名称                             |
| 261    | 服务被禁用                                                   | 该服务已下线                                                 |
| 301    | 永久配额超限，限制访问                                       | 配额超限，如果想增加配额请[联系我们](http://lbsyun.baidu.com/apiconsole/fankui#?typeOne=产品需求&typeTwo=新服务/功能需求) |
| 302    | 天配额超限，限制访问                                         | 配额超限，如果想增加配额请[联系我们](http://lbsyun.baidu.com/apiconsole/fankui#?typeOne=产品需求&typeTwo=新服务/功能需求) |
| 401    | 当前并发量已经超过约定并发配额，限制访问                     | 并发控制超限，请控制并发量请[联系我们](http://lbsyun.baidu.com/apiconsole/fankui#?typeOne=产品需求&typeTwo=新服务/功能需求) |
| 402    | 当前并发量已经超过约定并发配额，并且服务总并发量也已经超过设定的总并发配额，限制访问 | 并发控制超限，请控制并发量请[联系我们](http://lbsyun.baidu.com/apiconsole/fankui#?typeOne=产品需求&typeTwo=新服务/功能需求) |
| 1001   | 没有IPv6地址访问的权限                                       | 如需通过IPv6来获取位置信息，请[提交工单](http://lbsyun.baidu.com/apiconsole/fankui)申请 |