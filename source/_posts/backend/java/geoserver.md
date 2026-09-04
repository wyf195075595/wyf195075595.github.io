---
title: GeoServer
date: 2023-10-09 11:00:00
tags: WebGIS
categories: WebGIS
---

> GeoServer 是一个优秀得开源的GIS服务器，它相较于商用的GIS软件 ArcGIS Server\SuperMap iServer 等服务器软件，它能实现大多数场景下项目对GIS的功能要求，而且有活跃的社区支持，在实际生产中也越来越受到开发者的青睐。
>
> 

### **GeoServer的诞生历史**

> GeoServer于2001年由总部位于纽约的非盈利技术孵化器open planning project (TOPP),启动TOPP正在创建一套工具，以实现开放民主，并帮助政府更加透明。第一个是GeoServer，这是因为人们认识到，共享空间数据的能力将(成为) 大大增强公民参与政府和城市规划的一套工具。
>
> GeoServer的创始人设想了一个类似于万维网的地理空间网。通过万维网，人们可以搜索和下载文本。通过地理空间网，人们可以搜索和下载空问数据。数据提供者将能够直接将他们的数据发布到这个网站上，用户可以直接访问它，而不是现在存在的间接和繁琐的数据共享方法。
>
> GeoServer的相关人员创建了**GeoTools**项目，这是一个开源的GIS Java工具包。通过GeoTools，增加了对shapefile、Oracle数据库、ArcSDE集成等的支持
>
> 其他项目变得相互关联。Refractions Research创建了一个免费开放的空间数据库**PostGIS**，使GeoServer能够连接到一个免费的数据库。另外，MetaCarta最初创建了**OpenLayers**.一个基于开源浏览器的地图查看工具。这些工具一起增强了GeoServer的功能。
>
> **这使得使用开源技术路线构建GIS工作流成为可能，数据可以从PostGIS流入GeoServer、再应用OpenLayers将GeoServer发布的地图服务在前端进行展现和互操作。**

### WebGIS开发环境搭建

[下载](https://pan.baidu.com/s/19pQDJzOeI_-7VtA-JVtwHQ)完成后可参考b站视频[教程](https://www.bilibili.com/video/BV1vk4y127P8/?spm_id_from=333.337.search-card.all.click&vd_source=727712d64f5d5487bf0a89c7b9de461d)

<!--more-->

### 安装 GeoServer

[下载地址](https://geoserver.org/download/): 选择stable 稳定的版本。此软件不需要安装，解压后进入 bin 目录，找到 startup.bat/sh。 windows,linux，ios都行，执行对应脚本即可。

> 提示：选择jdk11+版本环境，低版本不支持

启动

> http://ip:8080/geoserver
>
> 找到对应ip然后访问
>
> 系统默认账户密码
>
> admin	geoserver

### 发布 shapefile 

1. 准备shapefile文件数据

	[测试数据链接](https://docs.geoserver.org/stable/en/user/_downloads/30e405b790e068c43354367cb08e71bc/nyc_roads.zip)

2. 创建工作区

3. 创建数据存储

4. 发布图层

5. 图层预览，客户端调用



### 发布GeoTIFF 影像文件

> **TIFF**(Tag Image File Format) 图像文件是图形图像处理中常用的格式之一,其图像格式很复杂,但由于它对图像信息的存放灵活多变,可以支持很多色彩系统,而且独立于操作系统
> 因此得到了广泛应用。在各种地理信息系统、摄影测量与遥感等应用中,要求图像具有地理编码信息,例如图像所在的坐标系、比例尺、图像上点的坐标、经纬度、长度单位及角度单位等等。对于存储和读取这些信息,纯TIFF 格式的图像文件是很难做到的,而GeOTIFF 作为TIFF 的一种扩展,在TIFF 的基础上定义了一些GeoTag (地理标签),来对各种坐标系统、椭球基准、投影信息等进行定义和存储,使图像数据和地理数据存储在同一图像文件中,这样就为广大用户制作和使用带有地理信息的图像提供了方便的途径。

发布步骤和上面基本一致，只是在创建数据存储时选择 TIFF 格式文件



### 使用 postgreSQL 创建 postgis 数据库

> 储存空间数据的数据库

1. 安装 postgreSQL 数据库管理系统

2. 安装 postgis 数据库

3. 使用postgreSQL 创建 postgis 数据库

	postgreSQL  创建数据库后执行下面语句创建一个 postgis 数据库

	```shell
	CREATE EXTENSION postgis;
	CREATE EXTENSION postgis_topology;
	CREATE EXTENSION postgis_sfcgal;
	CREATE EXTENSION fuzzystrmatch;
	CREATE EXTENSION address_standardizer;
	CREATE EXTENSION address_standardizer_data_us;
	CREATE EXTENSION postgis_tiger_geocoder;
	```

	[测试数据](https://docs.geoserver.org/stable/en/user/_downloads/bbd4b941ceb36074c9d19feab3f57609/nyc_buildings.zip)，下载后是一个sql文件。导入数据库

4. GeoServer 访问 postgreSQL 数据库



### 使用 openlayer  访问wms服务

下边的参数配置可以去图层预览页面，openlayer 预览页面可以查看请求信息找到下面的参数

```js
new ol.layer.Tile({
    source: new ol.source.TileWMS({
        ratio: 1,
        url: 'http://192.168.94.128:8080/geoserver/test/wms',
        params: {
            'FORMAT': 'image/png8',
            'VERSION': '1.1.1',
            "LAYERS": 'test:nyc_roads',
            "exceptions": 'application/vnd.ogc.se_inimage',
            "SRS": "EPSG:2908"
        }
    })
})
```

重要的一点，要配置好正确的坐标系，不然显示不出来

```js
view: new ol.View({
    center: [984018.1663741902 , 207673.09513056703], //地图初始中心点
    zoom: 7,  //地图初始显示级别
    projection: new ol.proj.Projection({
        code: 'EPSG:2908',
        units: 'm',
        global: false
    })
}),
```



### 使用geoserver生产wmts瓦片

1. 发布一个wms服务

2. 在wms服务器基础上，生产wmts瓦片

	瓦片缓存地址可以在geoserver目录下的 web.xml 文件中修改

	```xml
	<context-param>
	   <param-name>GEOSERVER_DATA_DIR</param-name>
	    <param-value>C:\eclipse\workspace\geoserver_trunk\cite\confCiteWFSPostGIS</param-value>
	</context-param>
	```

	瓦片生成可以在选择 切图策略，在 gridsets 中设置。添加策略需要在图层中的 瓦片策略中添加

3. 发布wmts 服务

### 瓦片的迁移使用

1. 在目标GIS服务器上发布wms服务，不生产瓦片。(或者发布空服务，只是为了构建bbox )
2. 在其他GeoServer服务器上创建wms服务，生产wmts瓦片。
3. 将所有其他GeoServer服务器上生产的wmts瓦片复制到目标GeoServer服务器对应服务瓦片目录下。



### [geoserver进行属性和几何要素的增删改查](https://zhuanlan.zhihu.com/p/381601300)

> 通过操作，影响图层数据