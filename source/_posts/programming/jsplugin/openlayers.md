---
title: OpenLayers
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---
> OpenLayers 5的体系架构可见，可把整个地图看成一个容器（Map），核心为地图图层（Layer）、对应图层的数据源（Source）与矢量图层样式（Style）、与地图表现相关的地图视图（View）.

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/openLayers.png)

## 工作原理

```js
1、数据组织

    从表现形态来看，空间数据的矢量数据由点、线、面三类要素构成.
    
    矢量数据的抽象主要由ol.geom.Geometry基类下的几何对象子类实现

	几何对象子类，即
    -Point与MultiPoint（点与多点）
	-LineString与MultiLineString（线与多线）
    -Polygon与MultiPolygon（区与多区）
    -LinearRing（线性环）
	-Circle（圆），均继承于SimpleGeometry子类

```

<!--more-->

![](../../resource/geometry.png)

```js
地图数据根据数据源（Source）可分为Image、Tile、Vector三大类

-Image类为单一图像基类，其子类作为画布（canvas）元素、服务器图片、单个静态图片、WMS单一图像等的数据源
-Tile类为瓦片抽象基类，其子类作为各类瓦片数据的数据源
-Vector则为矢量基类，可直接实例化创建矢量数据的数据源（支持各种格式的矢量数据），其子类则为扩展的某类矢量数据的数据源

2．数据解析

上述可知:
	Image为图片数据源，Tile为瓦片数据源，两者本质基本相同，均为图片或图片集。Vector为矢量基类，由其Format属性设置解析数据类型，即通过ol.format.Feature类的子类进行各种格式矢量数据的解析。
	
	-解析数据API
	ol.format.Feature类用于读/写各种格式的数据，并创建了多种格式的子类，即各类数据解析器，包括XML、Text、JSON类型的各种格式数据解析器。

3．数据渲染

    渲染功能由渲染器（即Renderer相关类）实现，通过Map的Renderer属性设置渲染方式，然后根据渲染方式（Canvas、WebGL）、与图层类型（Image、Tile、Vector）匹配渲染器将图层数据渲染出来
    
    
    图层数据主要由ol.renderer.Layer子类与相关子类负责渲染，即分别通过Canvas、WebGL两大渲染类型的相关子类实现。ol.layer.Image子类的图像数据基于ImageLayer渲染器渲染，ol.layer.Tile子类的瓦片数据基于TileLayer渲染器渲染，ol.layer.Vector子类的矢量数据则由VectorLayer渲染器渲染。其中，对于矢量数据的渲染，在VectorLayer渲染器中是通过ol.render.VectorContext的相关子类来实现的。
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/layerRender.png)

```js
4．地图表现
    在Web网页中，地图容器通过div层来表现，通过Map类的target属性关联作为地图容器的HTML元素——div层，即将此地图容器div层与JavaScript的map对象绑定，然后将layers（含对应数据源source）、controls等内容加载到Map中，表现为地图。
    对地图的渲染:
	-必须由一个或多个图层类（Layers）
	-一个地图视图类（View）
	-一个目标容器（div层）实现
即在创建一个Map实例时必须设置其图层（layers）、视图（view）与目标容器（target），这也是地图显示必备的三要素

var map = new ol.Map({
        target: 'map',
        layers: [
          new ol.layer.Tile({
            source: new ol.source.OSM()
          }),
          // 加载瓦片类图层数据
          new ol.layer.Tile({
              title: '天地矢量图层',
              source: new ol.source.XYZ({
                  url: 'http://t0.tianditu.gov.cn/img_w/wmts?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=img&STYLE=default&TILEMATRIXSET=w&FORMAT=tiles&TILEMATRIX={z}&TILEROW={x}&TILECOL={y}&tk=f9894ea408c66f303817c78bfbeed547',
                  attributions: '天地图的属性描述',
                  wrapX: false
              })
          }),
        ],
        view: new ol.View({
          center: ol.proj.fromLonLat([0, 0]),
          zoom: 10
        })
      });

    在实例化map对象时，仅设置了地图显示必备的三要素：图层（layers）、视图（view）和目标容器（target）。如果没有设置图层与数据源，则在客户端渲染一个空白的地图容器
    地图交互功能，就得依靠地图控件（Control）、地图交互功能（Interaction）、叠加层（Overlay）等各个功能类，并结合Map类的属性、方法、事件来实现。
    
    
5、事件机制

● 地图事件类（ol.MapEvent）：包含moveend、postrender事件。
● 地图浏览器事件类（ol.MapBrowserEvent）：包含singleclick、click、dbclick、pointermove、pointerdrag事件。
● 对象事件类（ol.Object.Event）：包含change、propertychange事件。
● 选择控件事件类（ol.interaction.Select.Event）：包含select事件。
● 绘制控件事件类（ol.interaction.Draw.Event）：包含drawstart、drawend事件。
● 修改控件事件类（ol.interaction.Modify.Event）：包含modifystart、modifyend事件。
● 集合事件类（ol.Collection.Event）：包含add、remove事件。


```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/openlayers5Api.png)



## 选择数据源

```js

const {
    getTopLeft,
    getWidth
} = ol.extent;
const {
    get: getProjection
} = ol.proj;
const TileLayer = ol.layer.Tile;
const {
    XYZ: XYZ,
    WMTS: WMTS,
    TileWMS: TileWMS,
    TileImage: TileImage,
} = ol.source;

const {
    WMTS: WMTSTileGrid,
    TileGrid: TileGrid,
} = ol.tilegrid;
// 天地图的tk
const tk = 'f9894ea408c66f303817c78bfbeed547';
// 定义一些常量
const projection = getProjection('EPSG:3857');
const projectionExtent = projection.getExtent();
const size = getWidth(projectionExtent) / 256;
const resolutions = new Array(18);
const matrixIds = new Array(19);

for (let z = 0; z < 19; ++z) {
    resolutions[z] = size / Math.pow(2, z);
    matrixIds[z] = z;
}
// 百度地图参数
const resolutions2 = [];
for (let z = 0; z < 19; ++z) {
    resolutions2[z] = Math.pow(2, 18 - z);
}
let tilegrid = new TileGrid({
    origin: [0, 0],
    resolutions: resolutions2
});



// 天地图卫星影像地图服务
function tdtImageMap(map) {
    return addTdtWmtsLayer(map, 'http://t0.tianditu.gov.cn/img_w/wmts', tk, 'img', 'w', 'tiles');
}
// 天地图影像注记地图服务
function tdtImageLabelMap(map) {
    return addTdtWmtsLayer(map, 'http://t0.tianditu.gov.cn/cia_w/wmts', tk, 'cia', 'w', 'tiles');
}

// 天地图矢量底图地图服务
function tdtVecMap(map) {
    return addTdtWmtsLayer(map, 'http://t0.tianditu.gov.cn/vec_w/wmts', tk, 'vec', 'w', 'tiles');
}
// 天地图矢量注记地图服务
function tdtVecLabelMap(map) {
    return addTdtWmtsLayer(map, 'http://t0.tianditu.gov.cn/cva_w/wmts', tk, 'cva', 'w', 'tiles');
}

// 增加天地图WMTS地图服务图层
function addTdtWmtsLayer(map, url, key, layerName, matrixSet, format) {
    let source = new WMTS({
        url: `${url}?tk=${tk}`,
        layer: layerName, // 图层名字
        matrixSet: matrixSet, // 矩阵集标识符
        format: format, // 图像格式
        projection: projection, // 预测默认为视图投影
        tileGrid: new WMTSTileGrid({ // 瓦片网格
            origin: getTopLeft(projectionExtent),
            resolutions: resolutions,
            matrixIds: matrixIds
        }),
        style: 'default', // WMTS功能中公布的样式名称
        wrapX: true, // 是否水平环绕世界
        crossOrigin: 'anonymous' // 为加载的图像创建了交叉原点属性。请注意，如果要使用画布渲染器访问像素数据，必须提供交叉原点值
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}

// 移除某个地图图层
function removeMapLayer(map, layer) {
    map.removeLayer(layer);
}


// arcgis卫星影像地图服务
function arcgisImageMap(map) {
    return addXYZLayer(map,
        'https://services.arcgisonline.com/arcgis/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}');
}
// arcgis矢量底图+注记地图服务
function arcgisVecMap(map) {
    return addXYZLayer(map,
        'https://services.arcgisonline.com/arcgis/rest/services/World_Street_Map/MapServer/tile/{z}/{y}/{x}');
}

// 高德卫星影像地图服务
function gdImageMap(map) {
    return addXYZLayer(map,
        'https://webst03.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}');
}
// 高德矢量底图地图服务
function gdVecMap(map) {
    return addXYZLayer(map,
        'https://webrd03.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}');
}

// 百度影像地图服务
function bdImageMap(map) {
    let source = new TileImage({
        projection: projection,
        tileGrid: tilegrid,
        tileUrlFunction: function (tileCoord, pixelRatio, proj) {
            if (!tileCoord) {
                return "";
            }
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = -tileCoord[2] - 1;
            if (x < 0) {
                x = "M" + (-x);
            }
            if (y < 0) {
                y = "M" + (-y);
            }
            return "http://shangetu1.map.bdimg.com/it/u=x=" + x + ";y=" + y + ";z=" + z + ";v=009;type=sate&fm=46";
        },
        crossOrigin: 'anonymous'
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}
// 百度影像注记地图服务
function bdImageLabelMap(map) {
    let source = new TileImage({
        projection: projection,
        tileGrid: tilegrid,
        tileUrlFunction: function (tileCoord, pixelRatio, proj) {
            if (!tileCoord) {
                return "";
            }
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = -tileCoord[2] - 1;
            if (x < 0) {
                x = "M" + (-x);
            }
            if (y < 0) {
                y = "M" + (-y);
            }
            return "http://online1.map.bdimg.com/tile/?qt=tile&x=" + x + "&y=" + y + "&z=" + z + "&styles=sl&v=020";
        },
        crossOrigin: 'anonymous'
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}
// 百度矢量底图地图服务
function bdVecMap(map) {
    let source = new TileImage({
        projection: projection,
        tileGrid: tilegrid,
        tileUrlFunction: function (tileCoord, pixelRatio, proj) {
            if (!tileCoord) {
                return "";
            }
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = -tileCoord[2] - 1;
            if (x < 0) {
                x = "M" + (-x);
            }
            if (y < 0) {
                y = "M" + (-y);
            }
            return "http://online3.map.bdimg.com/onlinelabel/?qt=tile&x=" + x + "&y=" + y + "&z=" + z +
                "&styles=pl&udt=20151021&scaler=1&p=1";
        },
        crossOrigin: 'anonymous'
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}

// 增加XYZ格式的地图服务图层
function addXYZLayer(map, url) {
    let source = new XYZ({
        url: url,
        wrapX: true,
        crossOrigin: 'anonymous'
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}

// 增加WMS格式的地图服务图层
function addWMSLayer(map, url, layerName) {
    let source = new TileWMS({
        url: url,
        params: {
            'LAYERS': layerName,
            'TILED': true
        },
        crossOrigin: 'anonymous'
    })
    let layer = new TileLayer({
        source: source
    })
    map.addLayer(layer);
    return layer;
}
```

## 初始化地图

```js
var map = new ol.Map({
  target: 'map',
  layers: [
    // new ol.layer.Tile({
    //   source: new ol.source.OSM()
    // }),
    new ol.layer.Tile({
      title: '经纬度投影影像底图',
      source: new ol.source.XYZ({
        url: 'http://t0.tianditu.gov.cn/ibo_w/wmts?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=img&STYLE=default&TILEMATRIXSET=w&FORMAT=tiles&TILEMATRIX={z}&TILEROW={x}&TILECOL={y}&tk=f9894ea408c66f303817c78bfbeed547',
        attributions: '天地图的属性描述',
        wrapX: true
      })
    }),
  ],
  view: new ol.View({
    rotation: Math.PI /6,// 选择角度
    center: [0, 0],// 中心
    zoom: 2,// 缩放倍数
    minZoom: 1,// 最小倍数
    maxZoom: 12// 最大倍数
  })
});



1、图层操作
    // 增加图层 通过 map.addLayer
    tdtImageMap(map)
    tdtImageLabelMap(map)

	// 查找图层
	 -map.getLayers()	获取地图上注册过的所有图层
    	.item(index)	通过索引获取某一个图层
	// 删除图层
	-map.removeLayer(layer)

2、鼠标控件
实例化鼠标位置控件:
var mousePositionControl = new ol.control.MousePosition({
  coordinateFormat: ol.coordinate.createStringXY(4),// 坐标格式
  projection: "EPSG:4326",// 地图投影坐标系（否则输出默认投影坐标）
  className: "custom-mouse-position",// 坐标信息样式 默认 ol-mouse-position
  target: $('#mouse-position'),// 鼠标位置显示容器
  undefinedHTML: "&nbsp;"// 未定义坐标的标记
})
参数：
●coordinateFormat：坐标值的显示格式。
● projection：投影坐标系，将当前鼠标位置的坐标点设置为当前坐标系下的相应值来显示。
● target：关联显示其坐标点信息的目标容器，即最外层容器，此示例中为新创建的ID为mouse-position的div层。
● className：坐标信息显示样式的类名，即坐标值文本的样式类名，此示例中新定义的样式类名为custom-mouse-position。

加载使用：
var map = new ol.Map({
  target: 'map',
  layers: [
      ...
  ],
  view: new ol.View({
    ...
  }),
  controls: ol.control.defaults().extend([mousePositionControl])
});


3、 比例尺控件
	// 实例化比例尺控件
	var scaleLineControl = new ol.control.ScaleLine({
        units: "metric",// 设置比例尺单位 degree、imperial、us、nautical、metric
        
    })
    
4、鹰眼控件
	地图鹰眼俗称地图的鸟瞰图或缩略图。在看小范围地区时，鹰眼展示大地图
    
    var overviewMapControl = new ol.control.OverviewMap({
        className: 'ol-overviewmap ol-custom-overviewmap',// 样式类名
        // 在鹰眼中加载同坐标系下不同数据源的图层
        layers: [
            new ol.layer.Tile({
          title: '经纬度投影影像底图',
          source: new ol.source.XYZ({
            url: 'http://t0.tianditu.gov.cn/img_w/wmts?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=img&STYLE=default&TILEMATRIXSET=w&FORMAT=tiles&TILEMATRIX={z}&TILEROW={x}&TILECOL={y}&tk=f9894ea408c66f303817c78bfbeed547',
            attributions: '天地图的属性描述',
            wrapX: false
          })
        }),
        ],
        collapseLabel: '\u00BB',// 鹰眼控件折叠功能按钮上的标识
        collasped: false,// 初始化展开显示方式
    })
参数：
● layers：在鹰眼容器内加载图层与地图容器类似，可以根据需要加载不同于主图的图层数据，但要确保主图与鹰眼略缩图在同一投影坐标系下。
● collapseLabel：在鹰眼控件展开时功能按钮上的标识可设置为网页JS的字符编码。
● label：在鹰眼控件折叠时功能按钮上的标识与collapseLabel对应的功能相反。
● collapsed：在鹰眼控件初始化加载时是否展开显示，本示例设置为展开显示。
● className：鹰眼控件的类名，可根据类名来定义整个鹰眼控件的样式，此示例中新定义的样式类名为ol-overviewmap和ol-custom-overviewmap。
    
5、全屏显示控件
	var fullScreen = new ol.control.FullScreen()
    
6、图层探查控件
	当有多个图层叠加显示时，上层图层会遮盖下层图层。图层探查控件可方便地查看下层图层数据，可用于辅助功能操作或分析，是一个非常实用的工具。图层探查的原理是在客户端上裁剪上层图层，将上层图层裁剪一部分，让下层图层数据变得可见。
    // 探查半径
    var radius = 75;
	
	// 为视图添加 鼠标的mousemove与mouseout事件监听，通过调用Map类的getEventPixel方法获取当前鼠标焦点的坐标点
	var mousePosition = null
    $('#map').addEventListener('mousemove', function(e){
        mousePosition = map.getEventPixel(event);
        map.render();// 重新渲染
    })
	$('#map').addEventListener('mouseout', function(e){
        mousePosition = null;
        map.render();// 重新渲染
    })

	// 渲染前进行裁剪
	// layers 为要进行裁剪的图层
	layers.on('prerender', function(event) {
        var ctx = exent.context; // 影像图画布
        var pixelRatio = event.frameState.pixelRatio;
        ctx.save()
        ctx.beginPath()
        if(mousePosition) {
            // 只显示一个以鼠标为中心的圆圈
            ctx.arc(mousePosition[0]*pixelRatio, mousePosition[1]*pixelRatio, radius*pixelRatio, 0, 2*Math.PI)
            ctx.lineWidth = 5*pixelRatio;// 圆边框的宽，5个像素
            ctx.strokeStyle = `rgba(0,0,0,0.5)`;//圆边框样式
            ctx.stroke()
        }
        ctx.clip();// 剪裁画布
    })

	// 呈现下层图层后，恢复画布的背景
	layers.on('postrender', function(event) {
        var ctx = event.context;
        ctx.restore()
    })
【注意】：此控件圆圈位置老不对，啧啧啧


7、动画效果控件
	动画效果由ol.View类的animate方法提供，在使用该方法时，可通过传递一个或者多个对象参数来实现多种动画效果，如旋转、弹性移动、反弹等，多个动画结合使用亦可实现飞行定位的特效
	animate方法动画参数进行简单介绍。
● center:ol.Coordinate类型，动画结束时的视图中心。
● zoom:number类型，动画结束时视图的缩放级别。
● resolution:number类型，动画结束时的视图分辨率。如果已经提供zoom参数，那么这个选项将被忽略。
● rotation:number类型，动画结束时视图的旋转角度。
● anchor:ol.Coordinate类型，在旋转或者视图缩放级别（视图分辨率）发生变化时动画过程中保持固定的点。
● duration:number类型，动画的持续时间（以毫秒为单位，默认为1000毫秒）。
● easing:function类型，动画中使用的进度控制功能（默认为ol.easing.inAndOut）。该函数会返回一个介于0和1之间的数字，表示目标状态的进度。
	function positionCity () {
      var shengyang = ol.proj.fromLonLat([123.24, 41.50])
      var beijing = ol.proj.fromLonLat([116.28, 39.54])
      var shanghai = ol.proj.fromLonLat([121.29, 31.14])
      var wuhan = ol.proj.fromLonLat([114.21, 30.37])
      var guangzhou = ol.proj.fromLonLat([113.15, 23.08])
      var haikou = ol.proj.fromLonLat([110.20, 20.02])

      // 旋转定位
      $('#animation1').addEventListener('click', function () {
        var center = view.getCenter();
        view.animate(
          // 过程1
          {
            // 动画结束时的视图中心，即当前视图中心同目标视图中心连线的中心点
            center: [
              center[0] + (shengyang[0]-center[0])/2,
              center[1] + (shengyang[1]-center[1])/2,
            ],
            rotation: Math.PI,// 动画结束时的旋转角度，即180°
            easing: ol.easing.easeIn// 控制动画速度，开始缓慢逐渐加速
          },
          // 过程2
          {
            center: shengyang,// 结束时的的视图中心
            rotation: 2*Math.PI,// 动画结束时旋转角度，即360°
            easing: ol.easing.easeOut// 控制动画速度，开始块逐渐缓慢
          }
        )
      }, false)
      // 弹性伸缩
      $('#animation2').addEventListener('click', function () {
        view.animate(
          {
            center: beijing,// 动画结束时的视图中心，
            duration: 2000,// 动画持续时间
            easing: elastic// 控制动画持续时间函数
          }
        )
      }, false)
      // 反弹效果
      $('#animation3').addEventListener('click', function () {
        view.animate(
          {
            center: shanghai,// 动画结束时的视图中心，
            duration: 2000,// 动画持续时间
            easing: bounce// 控制动画持续时间函数
          }
        )
      }, false)
       // 自旋转效果
       $('#animation4').addEventListener('click', function () {
        var rotation = view.getRotation();
        view.animate(
          // 过程1
          {
            anchor: wuhan,// 自旋中心点
            rotation: rotation + Math.PI,// 动画结束时的旋转角度，即180°
            easing: ol.easing.easeIn// 控制动画速度，开始缓慢逐渐加速
          },
          // 过程2
          {
            anchor: wuhan,// 自旋中心点
            rotation: rotation + 2*Math.PI,// 动画结束时旋转角度，即360°
            easing: ol.easing.easeOut// 控制动画速度，开始块逐渐缓慢
          }
        )
      }, false)
      // 飞行效果
      $('#animation5').addEventListener('click', function () {
        var duration = 2000;// 动画持续时间
        var zoom = view.getZoom();
        var parts = 2;
        var called = false;
        // 动画1
        view.animate(
          {
            center: guangzhou,// 动画结束时的视图中心，
            duration: duration,// 动画持续时间
          }, callback
        )
        // 动画2
        view.animate(
          {
            zoom: zoom-1,// 动画结束时的视图中心，
            duration: duration/2,// 动画持续时间
          },
          {
            zoom: zoom,// 动画结束时的视图中心，
            duration: duration/2,// 动画持续时间
          }, callback
        )
        // 动画完成时回调函数
        function callback (complete) {
          --parts;
          if(called) return
          if(parts === 0 || !complete) {
            called = true;
            done(complete);
          }
        }
      }, false)

      // 获取反弹值
      function bounce (t) {
        var s = 7.5626, p = 2.75, l;
        if(t < (1/p)) {
          l = s*t*t
        } else {
          if(t < (2/p)) {
            t -= (1.5/p);
            l = s*t*t+0.75;
          } else {
            t -= (2.625/p);
            l = s*t*t+0.984375;
          }
        }
        return l;
      }
      // 获取弹性伸缩值
      function elastic (t) {
        return Math.pow(2, -10*t)*Math.sin((t-0.075)*(2*Math.PI/0.3)) + 1
      }

    }

8、测量控件
	实现了距离测量和面积测量的功能。
    

```

```vue
// 自定义测量组件

<template>
  <div class="measure-tool">
    <div class="ol-control"><button @click="measure('distence')">距</button></div>
    <div class="ol-control"><button @click="measure('area')">面</button></div>
    <div class="ol-control"><button @click="measure('angle')">角</button></div>
  </div>
</template>

<script>
import { Draw } from "ol/interaction";
import { Vector as VectorSource } from "ol/source";
import { Vector as VectorLayer } from "ol/layer";
import Overlay from 'ol/Overlay';
import { Polygon, LineString } from 'ol/geom';
import Feature from 'ol/Feature';
import { unByKey } from 'ol/Observable'
import { getLength, getArea } from 'ol/sphere';
import Style from "ol/style/Style";
import Stroke from "ol/style/Stroke";
import Fill from "ol/style/Fill";
import Circle from "ol/style/Circle";
export default {
  props: ["map"],//地图组件传值
  data() {
    return {
      measureType: "diatence",
      draw: null,
      vectorLayer: null,
      tipDiv: null,
      pointermoveEvent: null, // 地图pointermove事件
      sketchFeature: null, // 绘制的要素
      geometryListener: null, // 要素几何change事件
      measureResult: "0" // 测量结果
    }
  },
  methods: {
    creatDraw(type) {
      let maxPoints = null;
      if (this.measureType == "angle") maxPoints = 3
      else maxPoints = null

      // 矢量图层源
      let vectorSource = new VectorSource({
        wrapX: false
      });
      // 矢量图层
      this.vectorLayer = new VectorLayer({
        source: vectorSource,
        style: new Style({
          fill: new Fill({
            color: 'rgba(252, 86, 49, 0.1)'
          }),
          stroke: new Stroke({
            color: '#fc5531',
            width: 3
          }),
          image: new Circle({
            radius: 0,
            fill: new Fill({
              color: '#fc5531'
            })
          })
        }),
        name: "测量图层"
      });
      this.map.addLayer(this.vectorLayer)
      this.draw = new Draw({
        source: vectorSource,
        type: type,
        maxPoints: maxPoints,
        style: new Style({
          fill: new Fill({
            color: 'rgba(252, 86, 49, 0.1)'
          }),
          stroke: new Stroke({
            color: '#fc5531',
            lineDash: [10, 10],
            width: 3
          }),
          image: new Circle({
            radius: 0,
            fill: new Fill({
              color: '#fc5531'
            })
          })
        }),
        // 绘制时点击处理事件
        condition: (evt) => {
          // 测距时添加点标注
          if (this.measureResult != "0" && !this.map.getOverlayById(this.measureResult) && this.measureType == "distence")
            this.creatMark(null, this.measureResult, this.measureResult).setPosition(evt.coordinate)
          return true
        }
      });
      this.map.addInteraction(this.draw);

      /**
       * 绘制开始事件
       */
      this.draw.on("drawstart", e => {
        this.sketchFeature = e.feature
        let proj = this.map.getView().getProjection()
        //******距离测量开始时*****//
        if (this.measureType == "distence") {
          this.creatMark(null, "起点", "start").setPosition(this.map.getCoordinateFromPixel(e.target.downPx_))
          this.tipDiv.innerHTML = "总长：0 m</br>单击确定地点，双击结束";
          this.geometryListener = this.sketchFeature.getGeometry().on('change', (evt) => {
            this.measureResult = this.distenceFormat(getLength(evt.target, { "projection": proj, "radius": 6378137 }))
            this.tipDiv.innerHTML = "总长：" + this.measureResult + "</br>单击确定地点，双击结束";
          })
        }
        //******面积测量开始时*****//
        else if (this.measureType == "area") {
          this.tipDiv.innerHTML = "面积：0 m<sup>2</sup></br>继续单击确定地点";
          this.geometryListener = this.sketchFeature.getGeometry().on('change', (evt) => {
            if (evt.target.getCoordinates()[0].length < 4) this.tipDiv.innerHTML = "面积：0m<sup>2</sup></br>继续单击确定地点";
            else {
              this.measureResult = this.formatArea(getArea(evt.target, { "projection": proj, "radius": 6378137 }))
              this.tipDiv.innerHTML = "面积：" + this.measureResult + "</br>单击确定地点，双击结束";
            }
          })
        }
        //******角度测量开始时*****//
        else if (this.measureType == "angle") {
          this.tipDiv.innerHTML = "继续单击确定顶点";
          this.geometryListener = this.sketchFeature.getGeometry().on('change', (evt) => {
            if (evt.target.getCoordinates().length < 3) this.tipDiv.innerHTML = "继续单击确定顶点";
            else {
              this.measureResult = this.formatAngle(evt.target)
              this.tipDiv.innerHTML = "角度：" + this.measureResult + "</br>继续单击结束";
            }
          })
        }
      });

      /**
       * 绘制开始事件
       */
      this.draw.on("drawend", e => {
        let closeBtn = document.createElement('span');
        closeBtn.innerHTML = "×";
        closeBtn.title = "清除测量"
        closeBtn.style = "width: 10px;height:10px;line-height: 12px;text-align: center;border-radius: 5px;display: inline-block;padding: 2px;color: rgb(255, 68, 0);border: 2px solid rgb(255, 68, 0);background-color: rgb(255, 255, 255);font-weight: 600;position: absolute;top: -25px;right: -2px;cursor: pointer;";
        closeBtn.addEventListener('click', () => {
          this.clearMeasure()
        })
        //******距离测量结束时*****//
        if (this.measureType == "distence") {
          this.creatMark(closeBtn, null, "close1").setPosition(e.feature.getGeometry().getLastCoordinate());
          this.creatMark(null, "总长：" + this.measureResult + "", "length").setPosition(e.feature.getGeometry().getLastCoordinate())
          this.map.removeOverlay(this.map.getOverlayById(this.measureResult))
        }
        //******面积测量结束时*****//
        else if (this.measureType == "area") {
          this.creatMark(closeBtn, null, "close2").setPosition(e.feature.getGeometry().getInteriorPoint().getCoordinates());
          this.creatMark(null, "总面积：" + this.measureResult + "", "area").setPosition(e.feature.getGeometry().getInteriorPoint().getCoordinates())
        }
        //******角度测量结束时*****//
        else if (this.measureType == "angle") {
          this.creatMark(closeBtn, null, "close3").setPosition(e.feature.getGeometry().getCoordinates()[1]);
          this.creatMark(null, "角度：" + this.measureResult + "", "angle").setPosition(e.feature.getGeometry().getCoordinates()[1])
        }
        // 停止测量
        this.stopMeasure();
      });
    },
    /**
     * 测量
     */
    measure(type) {
      if (this.draw != null) return false; // 防止在绘制过程再创建测量
      this.measureType = type;
      if (this.vectorLayer != null) this.clearMeasure();
      this.tipDiv = document.createElement('div');
      this.tipDiv.innerHTML = '单击确定起点';
      this.tipDiv.className = "tipDiv";
      this.tipDiv.style = "width:auto;height:auto;padding:4px;border:1px solid #fc5531;font-size:12px;background-color:#fff;position:relative;top:60%;left:60%;font-weight:600;"

      let overlay = new Overlay({
        element: this.tipDiv,
        autoPan: false,
        positioning: "bottom-center",
        id: "tipLay",
        stopEvent: false //停止事件传播到地图
      });
      this.map.addOverlay(overlay);

      this.pointermoveEvent = this.map.on("pointermove", evt => {
        overlay.setPosition(evt.coordinate)
      })
      if (this.measureType == "distence" || this.measureType == "angle") {
        this.creatDraw("LineString")
      }
      else if (this.measureType == "area") {
        this.creatDraw("Polygon")
      }
    },
    /**
     * 创建标记
     */
    creatMark(markDom, txt, idstr) {
      if (markDom == null) {
        markDom = document.createElement('div');
        markDom.innerHTML = txt
        markDom.style = "width:auto;height:auto;padding:4px;border:1px solid #fc5531;font-size:12px;background-color:#fff;position:relative;top:60%;left:60%;font-weight:600;"
      }
      let overlay = new Overlay({
        element: markDom,
        autoPan: false,
        positioning: "bottom-center",
        id: idstr,
        stopEvent: false
      });
      this.map.addOverlay(overlay)
      return overlay;
    },
    /**
     * 格式化距离结果输出
     */
    distenceFormat(length) {
      let output;
      if (length > 100) {
        output = (Math.round(length / 1000 * 100) / 100) + ' ' + 'km'; //换算成km单位
      } else {
        output = (Math.round(length * 100) / 100) + ' ' + 'm'; //m为单位
      }
      return output;//返回线的长度
    },
    /**
     * 格式化面积输出
     */
    formatArea(area) {
      let output;
      if (area > 10000) {
        output = (Math.round(area / 1000000 * 100) / 100) + ' ' + 'km<sup>2</sup>'; //换算成km单位
      } else {
        output = (Math.round(area * 100) / 100) + ' ' + 'm<sup>2</sup>';//m为单位
      }
      return output; //返回多边形的面积
    },
    /**
     * 计算角度输出
     */
    formatAngle(line) {
      var coordinates = line.getCoordinates();
      var angle = '0°';
      if (coordinates.length == 3) {
        const disa = getLength(new Feature({
          geometry: new LineString([coordinates[0], coordinates[1]])
        }).getGeometry(), {
          radius: 6378137,
          projection: this.map.getView().getProjection()
        });

        const disb = getLength(new Feature({
          geometry: new LineString([coordinates[1], coordinates[2]])
        }).getGeometry(), {
          radius: 6378137,
          projection: this.map.getView().getProjection()
        });

        const disc = getLength(new Feature({
          geometry: new LineString([coordinates[0], coordinates[2]])
        }).getGeometry(), {
          radius: 6378137,
          projection: this.map.getView().getProjection()
        });
        var cos = (disa * disa + disb * disb - disc * disc) / (2 * disa * disb); // 计算cos值
        angle = (Math.acos(cos) * 180) / Math.PI; // 角度值
        angle = angle.toFixed(2); // 结果保留两位小数
      }
      if (isNaN(angle)) return "0°"
      else return angle + "°"; // 返回角度
    },
    /**
     * 停止测量
     */
    stopMeasure() {
      this.tipDiv = null
      this.map.removeInteraction(this.draw); // 移除绘制组件
      this.draw = null;
      this.map.removeOverlay(this.map.getOverlayById("tipLay")) // 移除动态提示框
    },
    /**
     * 清除测量
     */
    clearMeasure() {
      this.vectorLayer.getSource().clear()
      this.map.getOverlays().clear()
      //移除监听事件
      unByKey(this.pointermoveEvent) // 清除鼠标在地图的pointermove事件
      unByKey(this.geometryListener) // 清除绘制图像change事件
      this.pointermoveEvent = null;
      this.geometryListener = null;
      this.measureResult = "0"
    }
  }
}
</script>

<style scoped>
.measure-tool {
  z-index: 1;
  position: absolute;
  left: 0.5em;
  top: 6em;
  width: 50px;
  height: 40px;
}
.measure-tool div:nth-child(2) {
  margin-top: 2em;
}
.measure-tool div:nth-child(3) {
  margin-top: 4em;
}
</style>


// 使用
<template>
  <div id="map">
    <measureTool v-if="map" :map="map"></measureTool>
  </div>
</template>
```

## OpenLayers之多源数据展示

> ​	随着Web技术的推进，使得GIS拥有了更大的空间，通过网络渠道快速传递空间信息，普及GIS了应用。随着互联网地图应用的不断发展，目前涌现了大量网络地图服务资源，包括Google地图、OpenStreetMap、Bing地图、Yahoo地图、百度地图、高德地图、天地图等。此外，还有ESRI、中地数码、超图等大型GIS厂商提供的自定格式的GIS数据，以及其他企事业单位或研究机构提供的各种格式的GIS数据等。数据来源丰富，数据格式各异，如何将这些多源异构数据加载到Web端展示，实现数据的无缝融合，这在WebGIS应用中是一个首先要解决的关键问题

### GIS数据加载一般原理

```js
1、GIS地图加载的一般原理
	在Web端加载瓦片地图与矢量地图的原理与方法基本相同。
    
▣瓦片地图
		瓦片地图是指网格中多个类似瓦片的图片集，对矢量地图文档或影像数据进行预处理，采用高效的缓存机制形成的缓存图片集，可在网页中快速加载，并且效果较好。
     在Web端加载瓦片地图：
     -直接读取缓存加载，即读取磁盘中以目录方式存储的瓦片图片
     -调用瓦片地图服务接口

	瓦片地图加载原理：
    通过Ajax请求瓦片地图服务或数据，根据瓦片地图的级数、行列号分别获取对应的瓦片地图，将其按照请求的空间范围组织好（即按照网格组织瓦片地图），从而形成一幅地图并显示在网页中。
    
▣矢量地图
	在Web端加载矢量地图，一般也有两种方式
    -直接读取矢量地图文件，即存储在磁盘中的GML、KML等格式的矢量地图文件
	-调用矢量地图服务接口

	矢量地图加载原理：通过Ajax请求矢量地图服务或数据，根据请求到的矢量地图，在Web端实时生成矢量地图（一张jpg、gif或png的图片）并显示在网页中。
    
    
    
两者差异：
	瓦片地图加载与矢量地图加载的主要区别是数据的预处理机制。瓦片地图经过了裁剪处理，已形成缓存图片集，在加载时比实时生成图片快，事半功倍。瓦片地图加载的速度快，效果好，可满足基本网络环境配置下的快速出图要求，适用于地图变更要求少的应用。
    矢量地图能满足Web端上实时的数据处理、分析等要求，大多数WebGIS平台在矢量地图加载中都进行了优化，还提供了负载均衡机制，加载速度较快，能支持海量数据的发布，适用范围广泛。矢量地图与瓦片地图可叠加显示，强强联合，可满足更多的应用需求。
    
OpenLayers 5加载地图的原理与方法：
	（1）瓦片地图：一般使用ol.layer.tile+ol.source.tile这种方式加载，ol.source.tile的子类为封装的各种瓦片地图数据源，可以根据具体的数据源类型扩展封装。瓦片地图也可以使用ol.layer.Image +ol.source.Image这种方式加载，同样可以扩展开发。

	（2）矢量地图：使用ol.layer.Vector+ol.source.Vector方式加载。矢量地图数据源一般有两种设置方式，一种为features方法，即设置features参数静态加载矢量地图，或者调用addFeature（）或addFeatures（）动态加载矢量地图；另一种则为url+format方法，即设置矢量地图的url地址与数据格式format来加载矢量地图。
```

### 开放数据

```js
	这些新的空间数据格式包括KML、GML、GeoJSON、GPX等，这些都是开放的数据格式，可以在多种软件、平台、工具或程序中使用，能够很好地满足Web端使用GIS数据的要求，让GIS数据的使用更灵活、应用范围更广泛
    
    1、KML
    	你知道谷歌地球的前身——那个流行的名为Keyhole的三维地球浏览器吗？如果你知道，那么对这个基于XML的谷歌地球的文件格式（被称为KML）
        
    2、GML（地球标记语言）
    	由OGC于1999年提出的，并得到了许多公司的大力支持，如Oracle、Galdos、MapInfo、CubeWerx等。GML能够表示地理空间对象的空间数据和非空间数据
        
    3、GPX
    	是一种基于XML格式为应用软件设计的通用GPS数据格式，它可以用来描述路点、轨迹、路程。GPX是免费的，可以在不需要付任何许可费用的前提下使用，它的标签可以保存位置、海拔和时间，可以在不同的GPS设备和软件之间交换。
        
    4、GeoJSON
    	一种对各种空间数据结构进行编码的格式，是基于JavaScript对象表示法（JSON）的地理空间信息数据交换格式
        
 
    OpenLayers 5均提供了解析这些格式数据的解析类，即对应的Format子类，可通过矢量图层（ol.layer.Vector）与矢量数据源（ol.source.Vector）加载数据




-加载 GeoJSON 数据
	// 数据源
	var vectorSource = new ol.source.Vector({
        url: '../hubei.json',
        format: new ol.format.GeoJSON()
    })
    // 图层
    var vectorLayer = new ol.layer.Vector({
        source: vectorSource,
        //样式设置
        style: styleFunction
    })
    /*矢量地图的样式*/
    var image = new ol.style.Circle({
      radius: 5,
      fill: null,
      stroke: new ol.style.Stroke({
        color: 'red',
        width: 1
      })
    });
    var styles = {
      'Point': [
        new ol.style.Style({
          //点样式
          image: image
        })
      ],
      'LineString': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //线的边界样式
            color: 'green',
            width: 1
          })
        })
      ],
      'MultiLineString': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //多线的边界样式
            color: 'green',
            width: 1
          })
        })
      ],
      'MultiPoint': [
        new ol.style.Style({
          //多点的点样式
          image: image
        })
      ],
      '  MultiPolygon': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //多区的边界样式
            color: 'yellow',
            width: 1
          }),
          fill: new ol.style.Fill({
            //多区的填充样式
            color: 'rgba(255, 255, 0, 0.1)'
          })
        })
      ],
      'Polygon': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //区的边界样式
            color: 'blue',
            lineDash: [4],
            width: 3
          }),
          fill: new ol.style.Fill({
            //区的填充样式
            color: 'rgba(0, 0, 255, 0.1)'
          })
        })
      ],
      'GeometryCollection': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //集合要素的边界样式
            color: 'magenta',
            width: 2
          }),
          fill: new ol.style.Fill({
            //集合要素的填充样式
            color: 'magenta'
          }),
          image: new ol.style.Circle({
            //集合要素的点样式
            radius: 10,
            fill: null,
            stroke: new ol.style.Stroke({
              color: 'magenta'
            })
          })
        })
      ],
      'Circle': [
        new ol.style.Style({
          stroke: new ol.style.Stroke({
            //圆的边界样式
            color: 'red',
            width: 2
          }),
          fill: new ol.style.Fill({
            //圆的填充样式
            color: 'rgba(255,0,0,0.2)'
          })
        })
      ]
    };
    var styleFunction = function (feature, resolution) {
      //根据要素类型设置矢量地图的样式
      return styles[feature.getGeometry().getType()];
    };




    除了在示例中使用url+format设置矢量数据源的方式，还可以调用数据解析类（format）的readFeatures方法读取矢量要素集，通过features参数设置矢量数据源。同时，也可以通过矢量数据源对象调用addFeature方法加载单个矢量要素。

	var vectorSource = new ol.source.Vector({
        // readFeatures 方法读取矢量要素集
        features: (new ol.format.GeoJSON()).readFeatures(data)// 读取矢量要素集
    })
    vectorSource.addFeature(new ol.Feature(new ol.geom.Circle([5e6,7e6])));// 单个矢量要素
```

### 公共地图数据

```js
	目前OpenLayers 5封装了部分公共地图数据，如OpenStreetMap、Bing地图的数据源等，可以将它们直接实例化数据源对象，结合瓦片图层来加载对应的地图数据。而对于未封装的公共地图数据，如百度地图、天地图数据等，则可以使用ol.source.XYZ来加载，但要根据其地图数据请求地址设置其相应参数。

1、 加载OpenStreetMap
var map = new ol.Map({
    target: 'map',
    layers: [
        // 加载瓦片地图数据
        new ol.layer.Title({
            // 图层对应数据，此处为加载 OpenStreeMap在线瓦片地图数据
            source: new ol.source.OSM()
        })
    ],
    view: new ol.View({
        center: [0,0],
        zoom: 2
    })
})

2、加载显示Bing地图

// 设置 Bing 地图请求 key
var key = `xxxxx...`
// 实例化瓦片数据
var roads = new ol.layer.Title({
    // 设置数据源Bing地图
    source: new ol.source.BingMaps({
        key,// Bing地图的许可
        imagerySet: 'Road'// 参数则为地图类型
    })
})
var map = new ol.Map({
    target: 'map',
    layers: [roads],
    view: new ol.View({
        center: [0,0],
        zoom: 2
    })
})


在实例化Bing地图数据时，采用的是ol.layer.Tile+ ol.sourc e.BingMaps方式。

3、加载百度地图
//坐标参考系
    var projection = ol.proj.get("EPSG:3857");
    //分辨率
    var resolutions = [];
    for (var i = 0; i < 19; i++) {
      resolutions[i] = Math.pow(2, 18 - i);
    }
    var tilegrid = new ol.tilegrid.TileGrid({
      origin: [0, 0],
      resolutions: resolutions
    });
    //连接百度地图的瓦片地图请求地址
    var baidu_source = new ol.source.TileImage({
      //设置坐标参考系
      projection: projection,
      //设置分辨率
      tileGrid: tilegrid,
      //百度地图的瓦片地图请求地址
      tileUrlFunction: function (tileCoord, pixelRatio, proj) {
        if (!tileCoord) {
          return "";
        }
        var z = tileCoord[0];
        var x = tileCoord[1];
        var y = tileCoord[2];
        if (x < 0) {
          x = "M" + (-x);
        }
        if (y < 0) {
          y = "M" + (-y);
        }
        return "http://online3.map.bdimg.com/onlinelabel/? qt=tile&x=" + x + "&y=" + y + "&z=" + z +
          "&styles=pl&udt=20151021&scaler=1&p=1";
      }
    });
    //百度地图
    var baidu_layer = new ol.layer.Tile({
      source: baidu_source
    });
    //地图容器
    var map = new ol.Map({
      target: 'mapCon',
      layers: [baidu_layer],
      view: new ol.View({
        center: [0, 0],
        zoom: 2
      })
    }); 
})

// 百度瓦片请求地址

ol.source.TileImage 参数：
● tileUrlFunction：设置瓦片地图的URL，先设置百度地图瓦片地图请求地址为
"http://online3.map.bdimg.com/onlinelabel/? qt=tile&x=" + x + "&y=" + y + "&z=" + z +"&styles=pl&udt=20151021&scaler=1&p=1"
其瓦片级数与行列号（即x、y、z）需要通过tileUrlFunction动态设置。
● projection：地图投影坐标系，百度地图采用EPSG:3857投影坐标系。
● tilegrid：瓦片网格，实例化ol.tilegrid.TileGrid类，设置瓦片地图的原点origin为[0,0]、瓦片级数对应的分辨率数组resolutions。


4、加载高德地图
	基于ol.source. XYZ接口加载高德地图
	
var gaodeMapLayer = new ol.layer.Tile({
  title: "高德地图",
  source: new ol.source.XYZ({
    url: 'http://wprd0{1-4}.is.autonavi.com/appmaptile? lang=zh_cn&size=1&style=7&x={x}&y={y}&z = {z}', 
    wrapX: false
  })
});
//创建地图容器
var map = new ol.Map({
  layers: [gaodeMapLayer],
  view: new ol.View({
    center: [12958752, 4848452],
    projection: 'EPSG:3857',
    zoom: 8,
    minZoom: 1
  }),
  target: 'mapCon'
});

5、加载谷歌地图
	基于ol.source.XYZ接口加载谷歌地图

  //实例化map对象并加载地图
    var map = new ol.Map({
      //地图容器div层的ID
      target: 'mapCon',
      //在地图容器中加载的图层
      layers: [
        //加载瓦片地图数据
        new ol.layer.Tile({
          title: "谷歌地图",
          source: new ol.source.XYZ({
            url: "http://mt2.google.cn/vt/lyrs=m@167000000&hl=zh-CN&gl=cn&x={x}&y={y}&z={z}",
            wrapX: false
          })
        })
      ],
      //地图视图的设置
      view: new ol.View({
        //地图初始中心点
        center: [0, 0],
        zoom: 3
      })
    })
    
6、加载天地图
	采用的是ol.layer.Tile+ ol.sourc e. XYZ方式
//实例化map对象并加载地图
    var map = new ol.Map({
      //地图容器div层的ID
      target: 'mapCon',
      //在地图容器中加载的图层
      layers: [
        //加载瓦片图层数据
        new ol.layer.Tile({
          title: "天地图矢量图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        }),
        new ol.layer.Tile({
          title: "天地图矢量注记图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        })
      ],
      //地图视图的设置
      view: new ol.View({
        //地图初始中心点
        center: [0, 0],
        //地图初始显示级别
        zoom: 3,
        //设置投影坐标系
        projection: "EPSG:4326"
      })
    })
```

### 矢量瓦片

```js
    矢量瓦片是指不仅能够提供完整的样式设计灵活性，还能能够快速渲染海量数据的矢量地图，其本质是矢量地图，等效于在网页上绘制的栅格图片（矢量块）
    
    ● 矢量瓦片相比于栅格图片更加灵活，可以更细粒度地访问矢量要素；
    ● 数据信息接近于无损，但体量更小，可直接在客户端获取请求指定地物的信息，无须再次请求服务器；
    ● 样式可改变和定制（重点），矢量瓦片可以在客户端或者服务器渲染，也可以按照用户赋予的样式渲染；
    ● 相比于原始矢量数据，矢量瓦片更小巧，进行了重新编码并切分，在被请求时可以只返回请求区域和相应级别的数据；
    ● 数据更新快，甚至可以说是实时的，当数据库中的空间数据变化后，再次请求的数据是更新后的数据。
    
    矢量瓦片通过一种紧凑、结构化的格式存储矢量数据的地理几何信息和元数据信息（如道路名、地名、房屋编号）。矢量瓦片常用的数据格式有GeoJSON、TopoJSON、PBF、MVT等，而数据源既可以是Mapbox，也可以是OSM，还可以是自己的数据（如使用ArcGIS、MapGIS等GIS平台生成的矢量瓦片）。
    
    //设置访问Mapbox资源的key(获得访问权限)
var key = 'pk.eyJ1Ijoid29ya2luZ2RvZyIsImEiOiJjamQyZmszenczMHRoMzRuczVzaGthbGhnIn0.HTkYTE-R82N3azqscSyHkA'；
//实例化map对象并加载地图
var map = new ol.Map({
  layers: [
    //添加矢量瓦片
    new ol.layer.VectorTile({
      declutter: true, //设为true时,矢量瓦片渲染模式将会覆盖地图渲染模式,详情请查看API
      //设置矢量瓦片的资源类型
      source: new ol.source.VectorTile({
        attributions: '©＜a href=″https://www.mapbox.com/map-feedback/″＞Mapbox＜/a＞' +
          '© ＜a href=″https://www.openstreetmap.org/copyright″＞' +
          'OpenStreetMap contributors＜/a＞',
        format: new ol.format.MVT(),
        url: 'http://a.tiles.mapbox.com/v4/mapbox.mapbox-streets-v6/' +
          '          {z}/{x}/{y}.mvt？access_token=' + key,
        style: createMapboxStreetsV6Style(ol.style.Style, ol.style.Fill, ol.style.Stroke,
          ol.style.Icon, ol.style.Text) //根据图层类型设置图层样式
      })
    })
  ],
  target: 'map',
  view: new ol.View({
    center: [0, 0],
    zoom: 2
  })
})

上述代码中的key可通
	过Mapbox官网https://www.mapbox.com/account
获取；
	图层样式可通过mapbox-streets-v6-style脚本库中的createMapboxStreetsV6Style函数设置，该函数的功能是根据Mapbox矢量瓦片的图层类型设置OpenLayers中点、线、区的样式。
```

###  多源数据叠加

```js
OpenLayers 5支持多源异构数据在同一个地图容器中叠加显示，数据叠加也是OpenLayers 5的地图表现的实现机制
	数据叠加非常简单，根据应用需求按照一定的先后顺序添加对应的图层对象到地图容器中即可，但前提是这些多源数据都在同一个投影坐标系下，否则叠加数据将无法吻合
    
// 分别实例化加载GeoJSON格式的矢量地图数据、ArcGIS Server的瓦片地图数据的图层对象，并加载到地图容器Map中。
//实例化map对象并加载地图
var map = new ol.Map({
  target: 'map', //地图容器div层的ID
  layers: [ //在地图容器中加载的图层
    new ol.layer.Tile({
      source: new ol.source.OSM() //加载OSM瓦片地图
    })
  ],
  view: new ol.View({ // 地图视图的设置
    center: [-10997148, 4569099], //地图初始中心点
    zoom: 3 //地图初始显示级别
  })
})
//叠加GeoJSON格式的矢量地图数据
var vectorSource = new ol.source.Vector({
  url: "../data/geojson/countries.geojson",
  format: new ol.format.GeoJSON()
})
var vectorLayer = new ol.layer.Vector({
  source: vectorSource // 矢量地图数据源
})
map.addLayer(vectorLayer)
//叠加ArcGIS的瓦片地图数据
var arcGISSource = new ol.source.TileArcGISRest({
    url: 'http://sampleserver1.arcgisonline.com/ArcGIS/rest/services/' +
      'Specialty/ESRI_StateCityHighway_USA/MapServer'
    })
var arcGISLayers = new ol.layer.Tile({
    source: arcGISSource,
    extent: [-13884991, 2870341, -7455066, 6338219]
})
map.addLayer(arcGISLayers)

【叠加顺序】：按照先后顺序进行叠加的，即最后添加的图层将显示到地图最上层，最先添加的图层则位于地图的底层
```

## 网格信息显示

```js
瓦片地图都是基于金字塔策略裁剪后的图片集，因此瓦片地图由级数、行列号对应的单张图片按照网格划分组织而成

    //实例化瓦片地图数据源对象
    var TiandituSource = new ol.source.XYZ({
      url: "http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
      wrapX: false
    })
    //实例化map对象并加载地图
    var map = new ol.Map({
      target: 'map', // 地图容器div层的ID
      //在地图容器中加载的瓦片地图
      layers: [
        //加载瓦片地图数据
        new ol.layer.Tile({
          source: TiandituSource
        }),
        //加载瓦片地图网格信息
        new ol.layer.Tile({
          source: new ol.source.TileDebug({
            projection: 'EPSG:3857', //地图投影坐标系
            tileGrid: TiandituSources.getTileGrid() //获取瓦片地图的网格信息
          })
        })
      ],
      //地图视图的设置
      view: new ol.View({
        center: [12900000, 4900000], //地图初始中心点
        zoom: 8 //地图初始显示级别
      })
    })
    
在实例化ol.source.TileDebug时，一般需要设置以下两个关键参数。
● projection：地图投影坐标系，设置为瓦片地图的坐标系；
● tileGrid：网格信息，设置为瓦片地图的网格信息，一般由瓦片地图的数据源对象调用getTileGrid方法得到。
```

## 地图打印（导出图片）

```js
Web端的地图打印功能，最简单的就是输出当前视窗范围内的地图，即将当前地图导出为一张图片并存储到客户端.也可以通过截屏功能实现。
	加载地图时默认使用Canvas渲染方式，并使用Canvas的toDataURL方法来实现导出当前地图的功能
    
    var exportPNGElement = document.getElementById('export-png'); // 导出地图功能
    if ('download' in exportPNGElement) {
      exportPNGElement.addEventListener('click', function (e) {
        map.once('postcompose', function (event) {
          var canvas = event.context.canvas; //地图渲染容器
          exportPNGElement.href = canvas.toDataURL('image/png'); //导出地图
        });
        map.renderSync();
      }, false);
    } else {
      alert("浏览器不支持导出地图的功能！");
    }

【有问题】 event.context 为undefined 

var exportPNGElement = $('#export-png'); // 导出地图功能
exportPNGElement.addEventListener('click', function (e) {
  map.once('rendercomplete', function () {
    const mapCanvas = document.createElement('canvas');
    const size = map.getSize();
    mapCanvas.width = size[0];
    mapCanvas.height = size[1];
    const mapContext = mapCanvas.getContext('2d');
    Array.prototype.forEach.call(
      map.getViewport().querySelectorAll('.ol-layer canvas, canvas.ol-layer'),
      function (canvas) {
        if (canvas.width > 0) {
          const opacity =
            canvas.parentNode.style.opacity || canvas.style.opacity;
          mapContext.globalAlpha = opacity === '' ? 1 : Number(opacity);

          const backgroundColor = canvas.parentNode.style.backgroundColor;
          if (backgroundColor) {
            mapContext.fillStyle = backgroundColor;
            mapContext.fillRect(0, 0, canvas.width, canvas.height);
          }

          let matrix;
          const transform = canvas.style.transform;
          if (transform) {
            // Get the transform parameters from the style's transform matrix
            matrix = transform
              .match(/^matrix\(([^\(]*)\)$/)[1]
              .split(',')
              .map(Number);
          } else {
            matrix = [
              parseFloat(canvas.style.width) / canvas.width,
              0,
              0,
              parseFloat(canvas.style.height) / canvas.height,
              0,
              0,
            ];
          }
          // Apply the transform to the export map context
          CanvasRenderingContext2D.prototype.setTransform.apply(
            mapContext,
            matrix
          );
          mapContext.drawImage(canvas, 0, 0);
        }
      }
    );
    mapContext.globalAlpha = 1;
    if (navigator.msSaveBlob) {
      // link download attribute does not work on MS browsers
      navigator.msSaveBlob(mapCanvas.msToBlob(), 'map.png');
    } else {
      // exportPNGElement.href = mapCanvas.toDataURL();
      let downloadLink = document.createElement('a')
          downloadLink.href =  mapCanvas.toDataURL()
          downloadLink.download =  'map.png'
          document.body.appendChild(downloadLink)
          downloadLink.click()
          document.body.removeChild(downloadLink)
    }
  });
  map.renderSync();
}, false);
```

## OpenLayers之图形绘制篇

> 图形绘制功能，是指在地图容器中绘制图形，包括点、线、圆、矩形与多边形等，可通过鼠标交互绘制，也可直接添加绘制好的图形

### 图形交互绘制原理

```js
	图形表现的内容十分丰富，有基本几何图形，即点、线、圆、矩形、多边形等；也有方向图形，即各种式样的方向箭头；还有标识某一内容或含义的图形，如指北针、小旗子等。
    
    图形绘制的基础是空间坐标，任何图形都是由空间坐标组成的:

	-一种是空间坐标已知，通常根据已知的空间坐标信息直接添加图形，实现图形绘制功能；
    -另一种则通过鼠标获取空间坐标（这也是图形绘制常用的方法），通常通过鼠标在地图上的操作来获取所需的空间范围信息，并以此空间坐标为基础来绘制图形，这种方式也称为交互式图形绘制

	
```

### 几何图形的绘制

```js
    基本几何图形包括点、折线、圆、矩形、多边形等。本示例主要实现这些基本几何图形的绘制，包括点、圆、线、多边形，以及规则的正方形、长方形的绘制

// 绘制图形的原理：
    即新建一个图层，并绘制需要的形状，再将图层添加到地图上
    
<!-- 绘制基本图形 -->
<div id="menu1">
  <label>几何图形类型:&nbsp; </label>
  <select id="type1">
    <option value="None">无</option>
    <option value="Point">点</option>
    <option value="LineString">线</option>
    <option value="Polygon">多边形</option>
    <option value="Circle">圆</option>
    <option value="Square">正方形</option>
    <option value="Box">长方形</option>
  </select>
</div>
    
    
// 基本图形绘制
baseDraw()
function baseDraw() {
  var typeSelect = $('#type1'); //几何图形类型
  var draw; //绘制对象
  //实例化一个矢量地图 vector
  var source = new ol.source.Vector({
    wrapX: false
  });
  var vector = new ol.layer.Vector({
    source: source,
    style: new ol.style.Style({
      fill: new ol.style.Fill({ //填充样式
        color: 'rgba(255, 255, 255, 0.2)'
      }),
      stroke: new ol.style.Stroke({ //边界样式
        color: '#ff0000',
        width: 2
      }),
      image: new ol.style.Circle({ //点要素样式
        radius: 7,
        fill: new ol.style.Fill({
          color: '#ff0000'
        })
      })
    })
  });
  map.addLayer(vector); // 将矢量地图添加到地图容器中
  /* 用户更改绘制类型触发的事件
   *@param{Event}e更改事件*/
  typeSelect.onchange = function (e) {
    map.removeInteraction(draw); //移除绘制的图形
    addInteraction(); // 添加交互式图形绘制控件
  };
  addInteraction(); // 添加交互式图形绘制控件

  //根据几何图形类型进行绘制
  function addInteraction() {
    var value = typeSelect.value; //几何图形类型
    if (value !== 'None') {
      if (source == null) {
        source = new ol.source.Vector({
          wrapX: false
        });
        vector.setSource(source); //添加数据源
      }
      var geometryFunction, maxPoints;
      if (value === 'Square') { //正方形
        value = 'Circle'; //设置几何图形类型为Circle(圆形)
        //设置几何图形类型,即创建正多边形
        geometryFunction = ol.interaction.Draw.createRegularPolygon(4);
      } else if (value === 'Box') { //长方形
        value = 'LineString'; //设置绘制类型为LineString(线)
        maxPoints = 2; //设置最大点数为2
        //设置几何图形类型,即设置长方形的坐标点
        geometryFunction = function (coordinates, geometry) {
          var start = coordinates[0];
          var end = coordinates[1];
          if (!geometry) {
            //多边形
            geometry = new ol.geom.Polygon([
              [start, [start[0], end[1]], end, [end[0], start[1]], start]
            ]);
          }
          geometry.setCoordinates([
            [start, [start[0], end[1]], end, [end[0], start[1]], start]
          ]);
          return geometry;
        };
      }
      //实例化交互式图形绘制控件并添加到地图容器中
      draw = new ol.interaction.Draw({
        source: source, //数据源
        type: /**@type{ol.geom.GeometryType}*/ (value), //几何图形类型
        geometryFunction: geometryFunction, //几何图形变更时调用函数
        maxPoints: maxPoints //最大点数
      });
      map.addInteraction(draw);
    } else {
      source = null;
      vector.setSource(source); //清空绘制的图形
    }
  }
}

	上述代码的关键是实例化交互式图形绘制控件ol.interaction.Draw，设置其关键参数并将此控件添加到地图容器中。对于点、线、圆、多边形图形，在实例化交互式图形绘制控件时，直接设置控件对象的type参数即可；正方形和长方形图形作为规则多边形，需要另外通过geometryFunction参数单独处理。

● source：数据源，即要绘制的几何图形要素的数据源。
● type：几何图形类型，即ol.geom.GeometryType，包括Point、LineString、Polygon、MultiPoint、MultiLineString、MultiPolygon、Circle等。
● geometryFunction：当几何图形的坐标更新时调用此函数，当几何图形类型为Square和Box时，需要通过此函数设置其几何图形类型。当为Square时通过交互式图形绘制控件对象的createRegularPolygon（4）创建；当为Box时则调用多边形（ol.geom.Polygon）的setCoordinates方法设置多边形的坐标来实现。
● maxPoints：图形绘制结束前多边形或线的最大点数，线默认为2
```

### 图形样式编辑

```js
    几何图形，如点、线、区等，都有对应的样式，如线型、线颜色、线宽、线透明度、填充颜色等。如果没有设置几何图形的样式，交互式图形绘制控件则会使用默认的样式进行绘图
    
        <!-- 绘制图形样式 -->
    <div id="editGeomStyle" class="easyui-window" data-options="title:'要素样式',closed:false, maximizable:false, minimizable:false, closable:false, resizable:false, shadow:
false" sty le="width: 310px; padding: 5px; left: 60px; top: 20px; ">
      <div id="tt" class="easyui-tabs" data-options="headerWidth:26, tabPosition:'left', plain:true, border:true"
        style="width: 100%">
        <div title="点要素样式" data-options="iconCls:'icon-point'" style="padding: 10px; ">
          <fieldset id="layer0">
            <input id="refresh-points" class="refreshBtn" type="button" value="更新" /><br />
            <!-- 图形样式-->
            <label class="title">图形样式:</label><br />
            <label>点大小(radius):</label>
            <input type="text" value="10" id="points-size" /><br />
            <label>填充颜色(color):</label>
            <input type="text" value="#aa3300" id="points-fill-color" /><br />
            <label>边框颜色(color):</label>
            <input type="text" value="red" id="points-stroke-color" /><br />
            <label>边框宽度(width):</label>
            <input type="text" value="2" id="points-stroke-width" /><br />
            <!-- 文字样式-->
            <label class="title">文字样式:</label><br />
            <label>位置(align): </label>
            <select id="points-text-align">
              <option value="center" selected="selected">Center</option>
              <option value="end">End</option>
              <option value="left">Left</option>
              <option value="right">Right</option>
              <option value="start">Start</option>
            </select>
            <br />
            <label>基线(baseline): </label>
            <select id="points-text-baseline">
              <option value="alphabetic">Alphabetic</option>
              <option value="bottom">Bottom</option>
              <option value="hanging">Hanging</option>
              <option value="ideographic">Ideographic</option>
              <option value="middle" selected="selected">Middle</option>
              <option value="top">Top</option>
            </select>
            <br />
            <label>旋转角度(rotation): </label>
            <select id="points-text-rotation">
              <option value="0">0°</option>
              <option value="0.785398164">45°</option>
              <option value="1.570796327">90°</option>
            </select>
            <br />
            <label>字体(font): </label>
            <select id="points-text-font">
              <option value="Arial" selected="selected">Arial</option>
              <option value="Courier New">Courier New</option>
              <option value="Quattrocento Sans">Quattrocento</option>
              <option value="Verdana">Verdana</option>
            </select>
            <br />
            <label>字体粗细(weight): </label>
            <select id="points-text-weight">
              <option value="bold">Bold</option>
              <option value="normal" selected="selected">Normal</option>
            </select>
            <br />
            <label>字体大小(size): </label>
            <input type="text" value="12px" id="points-text-size" />
            <br />
            <label>X偏移量(offset x):</label>
            <input type="text" value="0" id="points-text-offset-x" />
            <br />
            <label>Y偏移量(offset y):</label>
            <input type="text" value="0" id="points-text-offset-y" />
            <br />
            <label>字体颜色(color):</label>
            <input type="text" value="blue" id="points-text-color" />
            <br />
            <label title="Outline Color">文字外框颜色(O.Color):</label>
            <input type="text" value="#ffffff" id="points-text-outline-color" />
            <br />
            <label title="Outline Width">文字外框宽度(O.Width):</label>
            <input type="text" value="3" id="points-text-outline-width" />
          </fieldset>
        </div>
        <div title="线要素样式" data-options="iconCls:'icon-line'" style="padding: 10px; ">
          <fieldset id="layer1">
            <input id="refresh-lines" class="refreshBtn" type="button" value="更新" /><br />
            <label class="title">图形样式:</label><br />
            <label>线颜色(color):</label>
            <input type="text" value="green" id="lines-stroke-color" /><br />
            <label>线宽(width):</label>
            <input type="text" value="2" id="lines-stroke-width" /><br />
            <!-- 文字样式-->
            <label class="title">文字样式:</label><br />
            <label>位置(align): </label>
            <select id="lines-text-align">
              <option value="center" selected="selected">Center</option>
              <option value="end">End</option>
              <option value="left">Left</option>
              <option value="right">Right</option>
              <option value="start">Start</option>
            </select>
            <br />
            <label>基线(baseline): </label>
            <select id="lines-text-baseline">
              <option value="alphabetic">Alphabetic</option>
              <option value="bottom">Bottom</option>
              <option value="hanging">Hanging</option>
              <option value="ideographic">Ideographic</option>
              <option value="middle" selected="selected">Middle</option>
              <option value="top">Top</option>
            </select>
            <br />
            <label>旋转角度(rotation): </label>
            <select id="lines-text-rotation">
              <option value="0">0°</option>
              <option value="0.785398164">45°</option>
              <option value="1.570796327">90°</option>
            </select>
            <br />
            <label>字体(font): </label>
            <select id="lines-text-font">
              <option value="Arial" selected="selected">Arial</option>
              <option value="Courier New">Courier New</option>
              <option value="Quattrocento Sans">Quattrocento</option>
              <option value="Verdana">Verdana</option>
            </select>
            <br />
            <label>字体粗细(weight): </label>
            <select id="lines-text-weight">
              <option value="bold">Bold</option>
              <option value="normal" selected="selected">Normal</option>
            </select>
            <br />
            <label>字体大小(size): </label>
            <input type="text" value="12px" id="lines-text-size" />
            <br />
            <label>X偏移量(offset x):</label>
            <input type="text" value="0" id="lines-text-offset-x" />
            <br />
            <label>Y偏移量(offset y):</label>
            <input type="text" value="0" id="lines-text-offset-y" />
            <br />
            <label>字体颜色(color):</label>
            <input type="text" value="blue" id="lines-text-color" />
            <br />
            <label title="Outline Color">文字外框颜色(O.Color):</label>
            <input type="text" value="#ffffff" id="lines-text-outline-color" />
            <br />
            <label title="Outline Width">文字外框宽度(O.Width):</label>
            <input type="text" value="3" id="lines-text-outline-width" />
          </fieldset>
        </div>
        <div title="区要素样式" data-options="iconCls:'icon-polygon'" style="padding: 10px; ">
          <fieldset id="layer2">
            <input id="refresh-polygons" class="refreshBtn" type="button" value="更新" /><br />
            <label class="title">图形样式:</label><br />
            <label>填充颜色(color):</label>
            <input type="text" value="rgba(255, 255, 255, 0.5)" id="polygons-fill-color" /><br />
            <label>边框颜色(color):</label>
            <input type="text" value="#000000" id="polygons-stroke-color" /><br />
            <label>边框宽度(width):</label>
            <input type="text" value="2" id="polygons-stroke-width" /><br />
            <!-- 文字样式 -->
            <label class="title">文字样式:</label><br />
            <label>位置(align): </label>
            <select id="polygons-text-align">
              <option value="center" selected="selected">Center</option>
              <option value="end">End</option>
              <option value="left">Left</option>
              <option value="right">Right</option>
              <option value="start">Start</option>
            </select>
            <label>基线(baseline): </label>
            <select id="polygons-text-baseline">
              <option value="alphabetic">Alphabetic</option>
              <option value="bottom">Bottom</option>
              <option value="hanging">Hanging</option>
              <option value="ideographic">Ideographic</option>
              <option value="middle" selected="selected">Middle</option>
              <option value="top">Top</option>
            </select>
            <br />
            <label>旋转角度(rotation): </label>
            <select id="polygons-text-rotation">
              <option value="0">0°</option>
              <option value="0.785398164">45°</option>
              <option value="1.570796327">90°</option>
            </select>
            <br />
            <label>字体(font): </label>
            <select id="polygons-text-font">
              <option value="Arial" selected="selected">Arial</option>
              <option value="Courier New">Courier New</option>
              <option value="Quattrocento Sans">Quattrocento</option>
              <option value="Verdana">Verdana</option>
            </select>
            <br />
            <label>字体粗细(weight): </label>
            <select id="polygons-text-weight">
              <option value="bold">Bold</option>
              <option value="normal" selected="selected">Normal</option>
            </select>
            <br />
            <label>字体大小(size): </label>
            <input type="text" value="12px" id="polygons-text-size" />
            <br />
            <label>X偏移量(offset x):</label>
            <input type="text" value="0" id="polygons-text-offset-x" />
            <br />
            <label>Y偏移量(offset y):</label>
            <input type="text" value="0" id="polygons-text-offset-y" />
            <br />
            <label>字体颜色(color):</label>
            <input type="text" value="blue" id="polygons-text-color" />
            <br />
            <label title="Outline Color">文字外框颜色(O.Color):</label>
            <input type="text" value="#ffffff" id="polygons-text-outline-color" />
            <br />
            <label title="Outline Width">文字外框宽度(O.Width):</label>
            <input type="text" value="3" id="polygons-text-outline-width" />
          </fieldset>
        </div>
      </div>
    </div>
    
    
    // 获取DOM
    function $(selector) {
      return document.querySelector(selector)
    }
    //点、线、区要素样式对象
    var myDom = {
      points: {
        align: document.getElementById('points-text-align'),
        baseline: document.getElementById('points-text-baseline'),
        rotation: document.getElementById('points-text-rotation'),
        font: document.getElementById('points-text-font'),
        weight: document.getElementById('points-text-weight'),
        size: document.getElementById('points-text-size'),
        offsetX: document.getElementById('points-text-offset-x'),
        offsetY: document.getElementById('points-text-offset-y'),
        color: document.getElementById('points-text-color'),
        outlineColor: document.getElementById('points-text-outline-color'),
        outlineWidth: document.getElementById('points-text-outline-width')
      },
      lines: {
        align: document.getElementById('lines-text-align'),
        baseline: document.getElementById('lines-text-baseline'),
        rotation: document.getElementById('lines-text-rotation'),
        font: document.getElementById('lines-text-font'),
        weight: document.getElementById('lines-text-weight'),
        size: document.getElementById('lines-text-size'),
        offsetX: document.getElementById('lines-text-offset-x'),
        offsetY: document.getElementById('lines-text-offset-y'),
        color: document.getElementById('lines-text-color'),
        outlineColor: document.getElementById('lines-text-outline-color'),
        outlineWidth: document.getElementById('lines-text-outline-width')
      },
      polygons: {
        align: document.getElementById('polygons-text-align'),
        baseline: document.getElementById('polygons-text-baseline'),
        rotation: document.getElementById('polygons-text-rotation'),
        font: document.getElementById('polygons-text-font'),
        weight: document.getElementById('polygons-text-weight'),
        size: document.getElementById('polygons-text-size'),
        offsetX: document.getElementById('polygons-text-offset-x'),
        offsetY: document.getElementById('polygons-text-offset-y'),
        color: document.getElementById('polygons-text-color'),
        outlineColor: document.getElementById('polygons-text-outline-color'),
        outlineWidth: document.getElementById('polygons-text-outline-width')
      }
    };
    /* 创建文字样式函数
     *@param{ol.Feature}feature要素
     *@param do m要素样式HTML对象*/
    var createTextStyle = function (feature, dom) {
      console.log(dom);
      //读取当前要素样式面板设置的样式值
      var align = dom.align.value; //位置
      var baseline = dom.baseline.value; //基线
      var size = dom.size.value; //字体大小
      var offsetX = parseInt(dom.offsetX.value, 10); //X偏移量
      var offsetY = parseInt(dom.offsetY.value, 10); //Y偏移量
      var weight = dom.weight.value; //字体粗细
      var rotation = parseFloat(dom.rotation.value); //旋转角度
      var font = weight + '' + size + '' + dom.font.value; //文字样式(字体粗细、字体大小、字体颜色)
      var fillColor = dom.color.value; //字体颜色
      var outlineColor = dom.outlineColor.value; //文字外框颜色
      var outlineWidth = parseInt(dom.outlineWidth.value, 10); //文字外框宽度
      //返回实例化的文字样式对象(ol.style.Text)
      return new ol.style.Text({
        textAlign: align, //位置
        textBaseline: baseline, //基线
        font: font, //文字样式
        text: feature.get('name'), //文字内容
        fill: new ol.style.Fill({
          color: fillColor
        }), //文字填充样式(即文字颜色)
        stroke: new ol.style.Stroke({
          color: outlineColor,
          width: outlineWidth
        }), //文字外框样式(文字颜色与文字宽度)
        offsetX: offsetX, //X偏移量
        offsetY: offsetY, //Y偏移量
        rotation: rotation //旋转角度
      });
    };
    /* 创建点要素样式函数
     *@param{ol.Feature}feature点要素*/
    var createPointStyleFunction = function (feature) {
      var radius = document.getElementById('points-size').value;
      var fillColor = document.getElementById('points-fill-color').value;
      var strokeColor = document.getElementById('points-stroke-color').value;
      var strokeWidth = document.getElementById('points-stroke-width').value;
      //返回的设置点要素样式函数
      return function (feature, resolution) {
        var style = new ol.style.Style({
          image: new ol.style.Circle({
            radius: radius,
            fill: new ol.style.Fill({
              color: fillColor
            }),
            stroke: new ol.style.Stroke({
              color: strokeColor,
              width: strokeWidth
            })
          }),
          text: createTextStyle(feature, myDom.points)
        });
        return [style];
      };
    };
    /* 创建线要素样式函数
     *@param{ol.Feature}feature线要素*/
    var createLineStyleFunction = function (feature) {
      var strokeColor = document.getElementById('lines-stroke-color').value;
      var strokeWidth = document.getElementById('lines-stroke-width').value;
      //返回设置的线要素样式函数
      return function (feature, resolution) {
        var style = new ol.style.Style({
          stroke: new ol.style.Stroke({
            color: strokeColor,
            width: strokeWidth
          }),
          text: createTextStyle(feature, myDom.lines)
        });
        return [style];
      };
    };
    /* 创建区要素样式函数
     *@param{ol.Feature}feature区要素*/
    var createPolygonStyleFunction = function (feature) {
      var fillColor = document.getElementById('polygons-fill-color').value;
      var strokeColor = document.getElementById('polygons-stroke-color').value;
      var strokeWidth = document.getElementById('polygons-stroke-width').value;
      //返回设置的区要素样式函数
      return function (feature, resolution) {
        var style = new ol.style.Style({
          stroke: new ol.style.Stroke({
            color: strokeColor,
            width: strokeWidth
          }),
          fill: new ol.style.Fill({
            color: fillColor
          }),
          text: createTextStyle(feature, myDom.polygons)
        });
        return [style];
      };
    };


    // 分别将要绘制的点、线、区等几何图形并添加到地图容器中
    //要绘制的几何图形
    var pointFeature = new ol.Feature({
      geometry: new ol.geom.Point([114.1947, 30.5255]),
      name: 'Point Feature'
    });
    var lineFeature = new ol.Feature({
      geometry: new ol.geom.LineString([
        [114.2218, 30.5695],
        [114.2829, 30.4912]
      ]),
      name: 'Line Feature'
    });
    var polygonFeature = new ol.Feature({
      geometry: new ol.geom.Polygon([
        [
          [114.2757, 30.5822],
          [114.3526, 30.5879],
          [114.3608, 30.5367],
          [114.3234, 30.5187],
          [114.2826, 30.5530]
        ]
      ]),
      name: 'Polygon Feature'
    });
    //分别实例化点、线、区对应的矢量地图
    var vectorPoints = new ol.layer.Vector({
      source: new ol.source.Vector({
        features: [pointFeature]
      }),
      style: createPointStyleFunction(pointFeature)
    });
    var vectorLines = new ol.layer.Vector({
      source: new ol.source.Vector({
        features: [lineFeature]
      }),
      style: createLineStyleFunction(lineFeature)
    });
    var vectorPolygons = new ol.layer.Vector({
      source: new ol.source.Vector({
        features: [polygonFeature]
      }),
      style: createPolygonStyleFunction(polygonFeature)
    });
    // 实例化map对象并加载地图,底图加载MapQuest地图
	var map = new ol.Map({
      target: 'map',
      layers: [
        // new ol.layer.Tile({
        //   source: new ol.source.OSM()
        // }),
        //加载瓦片图层数据
        new ol.layer.Tile({
          title: "天地图矢量图层",
          source: tiandiLayerSource1
        }),
        //加载瓦片地图网格信息
        new ol.layer.Tile({
          source: new ol.source.TileDebug({
            projection: 'EPSG:3857', //地图投影坐标系
            tileGrid: tiandiLayerSource1.getTileGrid() //获取瓦片地图的网格信息
          })
        }),
        vectorPoints,
        vectorLines,
        vectorPolygons,
        new ol.layer.Tile({
          title: "天地图矢量注记图层",
          source: new ol.source.XYZ({
            url: `http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=${tk}`,
            wrapX: false
          })
        })
      ],
      view: new ol.View({
          projection: "EPSG:4326",
          rotation: Math.PI / 6,
          center: [12950000, 4860000],
          zoom: 5,
          minZoom: 1,
          maxZoom: 12
        })
    });


    // 单击事件处理函数中调用矢量地图setStyle方法来统一设置要素样式

    //为点、线、区要素的样式更新按钮绑定单击事件的处理函数
    $('#refresh-points').addEventListener('click',function () {
      vectorPoints.setStyle(createPointStyleFunction(pointFeature));
    });
    $('#refresh-lines').addEventListener('click',function () {
      vectorLines.setStyle(createLineStyleFunction(lineFeature));
    });
    $('#refresh-polygons').addEventListener('click',function () {
      vectorPolygons.setStyle(createPolygonStyleFunction(polygonFeature));
    });
    
```

### 图形交互编辑

```js
    我们不仅需要编辑图形的样式，同样也要修改已绘制图形的几何信息。为了方便操作，修改图形的几何信息一般采用鼠标交互方式实现。针对图形的修改，OpenLayers 5提供了交互式编辑控件ol.interaction.Modify，可以结合选择要素控件ol.interaction.Select一起使用。

添加了一个点、线、区图形，实现对图形要素进行编辑的功能：

OpenLayers 5封装了一个Modify控件，分别实现初始化init方法，设置事件的setEvents方法、设置是否激活控件的setActive方法。在调用时先调用Modify的init方法进行初始化，然后调用setActive方法来激活控件。
● init：在初始化时先加载选择要素控件（ol.interaction.Select），然后加载一个交互式编辑控件（ol.interaction.Modify），在实例化交互式编辑控件时设置修改的features为当前选中的要素，最后调用setEvents方法进行选择控件激活状态变更的处理。
● setEvents：在此方法中为当前选择的控件添加激活变更事件，在其事件处理函数中返回当前选择要素集的第一要素。
● setActive：在此方法中分别调用选择要素控件和交互式编辑控件的setActive方法，对这两个控件的状态是否激活进行控制


<!-- 绘制类型 -->
<form class="form-inline">
    <label for="type">几何类型</label>
    <select id="type">
        <option value="Point">点</option>
        <option value="LineString">线</option>
        <option value="Polygon">折线</option>
        <option value="Circle">圆</option>
    </select>
</form>
<div id="map"></div>

// 将多世界平移限制为真实世界以东和以西的一个世界。
// 几何坐标必须在该范围内。
const extent = ol.proj.get('EPSG:3857').getExtent().slice();
extent[0] += extent[0];
extent[2] += extent[2];
// 图层数据源
const source = new ol.source.Vector();
// 绘画图层
const vector = new ol.layer.Vector({
    source: source,
    style: new ol.style.Style({
        fill: new ol.style.Fill({
            color: 'rgba(255, 255, 255, 0.2)',
        }),
        stroke: new ol.style.Stroke({
            color: '#ffcc33',
            width: 2,
        }),
        image: new ol.style.Circle({
            radius: 7,
            fill: new ol.style.Fill({
                color: '#ffcc33',
            }),
        }),
    }),
});


// 天地矢量图层
var map = new ol.Map({
    target: "map",
    layers: [
        //加载瓦片图层数据
        new ol.layer.Tile({
            title: "天地图矢量图层",
            source: new ol.source.XYZ({
                url: `http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=${tk}`,
                wrapX: false,
            }),
        }),
        new ol.layer.Tile({
            title: "天地图矢量注记图层",
            source: new ol.source.XYZ({
                url: `http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=${tk}`,
                wrapX: false,
            }),
        }),
        vector
    ],
    view: new ol.View({
        center: [12622513, 2636878], //地图初始中心点
        zoom: 9,  //地图初始显示级别
        extent
    }),
    controls: ol.control.defaults().extend([]),
});


const modify = new ol.interaction.Modify({source: source});
map.addInteraction(modify);

let draw, snap; // global so we can remove them later
const typeSelect = document.getElementById('type');

function addInteractions() {
    // 用于绘制指定几何图形
    draw = new ol.interaction.Draw({
        source: source, // 绘制图层数据源
        type: typeSelect.value,// 几何图形类被
    });
    map.addInteraction(draw);
    // 用于修改
    snap = new ol.interaction.Snap({source: source});
    map.addInteraction(snap);
}

/**
 * Handle change event.
 */
typeSelect.onchange = function () {
    map.removeInteraction(draw);
    map.removeInteraction(snap);
    addInteractions();
};

addInteractions();



勾绘draw、选择要素select、modify、捕捉吸附snap 和 鼠标自定义事件pointer


1、draw interaction - 绘图功能
-features，绘制的要素的目标集合；
-source，绘制的要素的目标图层来源，即目标图层的 source属性 ；
-snapTolerance，自动吸附完成点的像素距离，也就是说当鼠标位置距离闭合点小于该值设置的时候，会自动吸附到闭合点，默认值是 12;

-type，绘制的地理要素类型，ol.geom.GeometryType类型，包含 Point、 LineString、 Polygon、MultiPoint、MultiLineString 或者 MultiPolygon；

-minPointsPerRing，绘制一个多边形需要的点数最小值，数值类型，默认是 3；
-style，要素素描的样式，是 ol.style.Style对象之一；
-geometryName，绘制的地理要素的名称，string类型；
-condition，一个函数，接受一个ol.MapBrowserEvent类型的参数，返回一个布尔值表示是否应该调用事件处理函数。默认情况下，增加一个顶点，类ol.events.ConditionType。

必须指定 source和type属性：

var draw = new ol.interaction.Draw({
  source: source,
  type: "Polygon"
});

2、 snap interaction - 鼠标捕捉
当修改和绘制矢量要素时，鼠标距离某个要素一定距离之内，自动吸附到要素。
使用方法如下：

new ol.interaction.Snap({
    features: 要素集(ol.Collection),
    pixelTolerance: 捕捉发生的距离，像素数，默认为10，
    source: 具有捕捉功能的图层(ol.source.Vector)
})

3、modify 和 select interaction
	select 就像名字暗示的一样，是用来选中要素的；而 modify 是修改要素的，主要是通过拖拽方式修改点的坐标。
模拟选中并修改要素的交互功能需要添加如下代码：

var select = new ol.interaction.Select();
var moddify = new ol.interaction.Modify({
    features:select.getFeatures()
});
features:select.getFeatures()目的为修改选中的要素。 然后将两个交互功能添加到 map 中就可以使用其功能了。

4、pointer interaction - 自定义鼠标事件
      针对鼠标的行为按下（Down）、移动（Move）和抬起（Up），自定义事件。这三个事件发生有先后顺序，首先是触发按下，之后是移动事件，最后是抬起事件。只要配置相关的属性，包含handleDownEvent，handleDragEvent，handleMoveEvent，handleUpEvent分别对应鼠标的 down、drag、move和up四种事件。例如配置鼠标的按下左键事件，当按下鼠标左键时候，就会触发 functionName函数 ：

new ol.interaction.Pointer({ handleDownEvent: functionName })

5. draganddrop interaction - 拖拽文件到地图
      将空间数据使用鼠标或者手指拖拽到地图中，解析成一个图层添加到地图中。目前只支持矢量数据，未来可能支持更多的空间数据格式。目前，支持的格式包括 GeoJSON, GML, KML, GPX, OSMXML, TopoJSON 和 IGC。首先实例化一个 draganddrop interaction：

var dragAndDropInteraction = new ol.interaction.DragAndDrop({
  formatConstructors: [
    ol.format.GeoJSON,
    ol.format.KML
  ]
});
formatConstructors 表示想要支持的格式，这里我选择了支持两种常见的格式：GeoJSON 和 KML，然后将该交互添加到地图中：

 var interactions = ol.interaction.defaults().extend([
    new ol.interaction.DragRotateAndZoom(),
    dragAndDropInteraction
 ]);
      
	如果想在添加数据的时候定义一些额外行为，比如缩放到添加到数据的范围，需要注册 interaction 的事件-dragAndDropInteraction.on('addfeatures', function(event) {});， 以下为拖拽一个KML文件到地图中：
      
      
      
      
【拓展】https://www.ktanx.com/blog/p/2656


封装一个编辑操作类

// 在地图容器中创建一个交互式编辑控件(ol.interaction.Modify)来实现几何编辑功能
    //选中修改
    var Modify = {
        init: function (options) {
            // 传入的绘制图层
            this.drawLayer = options.drawLayer
            if (this.drawLayer) {
                this.source = this.drawLayer.getSource()
            }
            //初始化一个选择要素控件并添加到地图容器中
            this.select = new ol.interaction.Select();
            map.addInteraction(this.select);
            //初始化一个交互式编辑控件,并添加到地图容器中
            this.modify = new ol.interaction.Modify({
                features: this.select.getFeatures() //选中的要素集
            });
            this.modify.on('modifystart', function (event) {
                console.log('modifystart:', event);
            });

            this.modify.on('modifyend', function (event) {
                console.log('modifyend:', event);
            });
            map.addInteraction(this.modify);
            //设置激活状态变更的处理函数
            this.setEvents();
            // 默认不激活
            this.setActive(false)
        },
        setEvents: function () {
            let that = this;
            var selectedFeatures = this.select.getFeatures(); //选中的要素集
            // 要素选择时触发
            this.select.on('select', function (e) {
                // 记录当前选中的要素
                that.currentFeature = e.selected[0]
            })
            //添加选中要素变更事件
            this.select.on('change:active', function (e) {
                //遍历选择要素集,返回当前第一个要素(即移除的要素)
                selectedFeatures.forEach(selectedFeatures.remove, selectedFeatures);
            });

        },
        removeFeature: function () {
            // 移除画布上选中的要素
            if (this.source && this.source.removeFeature) {
                this.source.removeFeature(this.currentFeature)
            }
        },
        setActive: function (active) {
            this.select.setActive(active); // 激活选择要素
            this.modify.setActive(active); // 激活修改要素
        }
    };
    Modify.init({
        drawLayer: vector
    }); //初始化Modify控件
    Modify.setActive(true); //激活Modify控件
    document.getElementById('undo').addEventListener('click', function () {
        Modify.removeFeature()
    });
```

## OpenLayers之OGC服务篇

```js
	开放地理信息系统协会（Open GIS Consortium, OGC）是一个非营利性的行业协会，成立于1994年，致力于促进采用新的技术和商业方式来提高地理信息处理的互操作性

    开放地理数据互操作规范（Open Geodata Interoperation Specifications,OpenGIS）是由开放地理信息系统协会（OGC）提出和制定的关于空间数据互操作的规程及一系列规范。OGC制定的一系列规范，极大地促进了WebGIS的发展以及地理信息的共享与互操作。许多厂商都已认识到其重要性，表示支持OGC规范，并已推出支持OGC规范的WebGIS产品。由此可见，遵循OGC规范已成为WebGIS的一个发展趋势。

OGC规范具有如下特点：
● 互操作性：在不同的信息系统之间实现无障碍的连接和交换。
● 开放性：公开接口规范，方便其他系统进行调用。
● 可移植性：平台无关性，可跨越多种硬件、操作系统、软件环境。
● 兼容性：兼容其他广泛应用的工业标准。

为实现地理信息的共享与互操作，OGC定义了一系列Web地理信息服务的抽象接口与实现规范，包括WMS、WFS、WMTS、WCS等

OGC规范支持HTTP GET/POST、SOAP、REST、KVP等通信协议，支持XML、GML、KML和GeoJSON等格式的开放数据。作为Web服务体系成员的OGC规范，支持各种方式的Web客户端的调用，包括JavaScript、Flex、Silverlight等；同时也支持Web后台程序请求访问。
```

### OGC规范的加载原理

```js
（1）WMTS：即Web瓦片地图服务，WMTS的GetTile接口返回的就是单张瓦片地图，其调用方式与其他类型的瓦片地图相同。可以使用ol.layer.Tile + ol.source.WMTS加载WMTS，由数据源（ol.source.WMTS）内部处理与请求WMTS，加载所请求范围内的瓦片地图。
（2）WMS：即Web地图服务，WMS的GetMap接口返回指定范围内的地图，因此可以以图片图层的方式加载，也可以以瓦片地图的方式加载，即可以使用ol.layer.Image +ol.source.ImageWMS的方式加载，或者使用ol.layer.Tile +ol.source.TileWMS的方式加载。WCS与WMS类似，WCS的GetCoverage接口返回指定范围内的影像图片，因此WCS的加载方式与WMS基本相同。
（3）WFS：即Web要素服务，WFS的GetFeature接口返回GML等格式的矢量地图，其调用方式与其他类型的矢量地图相同。因此，WFS的加载方式与矢量地图类似，可以使用ol.layer.Vector+ ol.source.Vector的方式加载。


```

### OGC规范数据显示

```js
    OGC服务的功能越来越强大，但OGC规范数据的加载显示是最基础的部分，必须由相应的GIS服务器发布OGC服务，提供服务资源的支持
    ArcGIS OnLine、GeoServer等可在线提供OGC服务
    
1、加载WMTS
	WMTS提供瓦片地图服务，通过WMTS可以将发布的瓦片地图加载到客户端。本示例基于OpenLayers 5加载WMTS，将天地图发布的WMTS的瓦片地图加载到地图容器中。
    //WMTS数据源
    var wmtsLayer;
    //通过计算范围得到分辨率数组
    var projection = ol.proj.get('EPSG:4326');
    var projectionExtent = projection.getExtent();
    var size = ol.extent.getWidth(projectionExtent) / 256;
    var resolutions = new Array(14);
    var matrixIds = new Array(14);
    for (var z = 0; z < 14; ++z) {
      //生成WMTS分辨率数组和matrixIds
      resolutions[z] = size / Math.pow(2, z);
      matrixIds[z] = z;
    }
    //数据源信息
    var attribution = new control.ol.Attribution({
      html: 'Tiles&copy;<a href="http://t0.tianditu.cn/cva_c/wmts? LAYER=cva"＞天地图矢量注记</a＞'
    });
    //实例化ol.layer.Tile和ol.source.WMTS
    wmtsLayer = new ol.layer.Tile({
      opacity: 1, //图层透明度
      source: new ol.source.WMTS({
        attributions: [attribution], //数据源信息
        url: 'http://t0.tianditu.gov.cn/cva_c/wmts? LAYER=cva&tk=您的天地图密钥', //WMTS服务器地址
        matrixSet: 'c', //投影坐标系设置矩阵
        format: 'png', //图片格式
        projection: projection, //数据的投影坐标系
        //瓦片网格对象参数
        tileGrid: new ol.tilegrid.WMTS({
          origin: ol.extent.getTopLeft(projectionExtent), // 原点(左上角)
          resolutions: resolutions, //分辨率数组
          matrixIds: matrixIds //矩阵标识列表,与地图级数保持一致
        }),
        style: 'default',
        wrapX: true
      })
    });
    需要设置以下一些关键参数。
    ● url：请求WMTS的URL地址参数，即WMTS的服务地址。
    ● format：请求WMTS返回的瓦片地图格式，一般用png格式。
    ● projection:WMTS瓦片地图的投影坐标系参数，在此设置为经纬度坐标。
    ● tileGrid：瓦片网格对象信息参数，此参数最为关键，可使用ol.tilegrid.WMTS的对象设置。其中，origin为瓦片地图原点，resolutions为分辨率数组（与瓦片级数相对应）, matrixIds则为矩阵标识列表，与瓦片地图级别保持一致。

2、加载WMS
	WMS提供Web地图服务，可通过WMS将发布的地图加载到客户端。本示例基于OpenLayers 5加载WMS，将WMS（即USA地图）加载到地图容器中
    //在本示例中，使用基于GeoServer发布的两个WMS：一个WMS的服务地址为http://demo.boundlessgeo.com/geoserver/wms，请求的图层名（layers）为topp:states；另一个WMS的服务地址为http://demo.boundlessgeo.com/geoserver/ne/wms/，请求的图层名（layers）为ne:ne_10m_admin_0_countries。
	三种方式加载： img/title/tilegrid
    let wmsLayer;
    /* 根据选择的类型加载WMS
     *@param{string}type类型参数*/
    function loadWMSMap(type) {
      if (wmsLayer != null) {
        map.removeLayer(wmsLayer); //移除WMS
        wmsLayer = null;
      }
      //使用img图层
      if (type == "img") {
        //实例化ol.layer.Image和ol.source.ImageWMS
        wmsLayer = new ol.layer.Image({
          extent: [-13884991, 2870341, -7455066, 6338219], //数据范围
          source: new ol.source.ImageWMS({
            //WMS的服务地址
            url: 'http://demo.boundlessgeo.com/geoserver/wms',
            params: {
              'LAYERS': 'topp:states'
            }, //图层参数
            serverType: 'geoserver' //服务类型
          })
        });
      }
      //使用tile图层
      else if (type == "tile") {
        //实例化ol.layer.Tile和ol.source.TileWMS
        wmsLayer = new ol.layer.Tile({
          source: new ol.source.TileWMS({
            url: 'http://demo.boundlessgeo.com/geoserver/ne/wms', //WMS的服务地址
            params: {
              'LAYERS': 'ne:ne_10m_admin_0_countries',
              'TILED': true
            }, //参数
            serverType: 'geoserver' //服务类型
          })
        })
      }
      //使用自定义tileGrid的tilegrid图层
      else if (type == "tilegrid") {
        //通过计算范围得到分辨率数组resolutions
        var projExtent = ol.proj.get('EPSG:3857').getExtent();
        var startResolution = ol.extent.getWidth(projExtent) / 256;
        var resolutions = new Array(22);
        for (var i = 0, ii = resolutions.length; i < ii; ++i) {
          resolutions[i] = startResolution / Math.pow(2, i);
        }
        //实例化ol.tilegrid.TileGrid
        var tileGrid = new ol.tilegrid.TileGrid({
          extent: [-13884991, 2870341, -7455066, 6338219], //数据范围
          resolutions: resolutions, //分辨率数组
          tileSize: [512, 256] //瓦片地图的大小
        });
        //用ol.layer.Tile实例化WMS图层,设置ol.source.TileWMS的tileGrid参数
        wmsLayer = new ol.layer.Tile({
          source: new ol.source.TileWMS({
            url: 'http://demo.boundlessgeo.com/geoserver/wms', //WMS的服务地址
            params: {
              'LAYERS': 'topp:states',
              'TILED': true
            }, //图层参数
            serverType: 'geoserver', //服务类型
            tileGrid: tileGrid //瓦片网格信息参数(瓦片大小为512×256)
          })
        })
      }
      map.addLayer(wmsLayer); //添加WMS
    }
上述代码分别用三种方式加载WMS，其关键为实例化WMS数据源，需要设置一些关键参数。
  方式一：使用ol.layer.Image + ol.source.ImageWMS方式，在实例化ImageWMS数据源时设置以下关键参数。
    ● url：请求WMS的URL地址，即WMS的服务地址。
    ● params：图层参数，与WMS的GetMap接口保持一致，设置为WMSGetMap接口的各个参数项，如layers表示请求的图层名参数。
    ● serverType：请求的服务类型，即提供WMS服务的服务器类型，如MapServer、GeoServer、QGIS等。
方式二：使用ol.layer.Tile + ol.source.TileWMS方式，在实例化TileWMS数据源时需要设置的关键参数与方式一相同。

方式三：使用ol.layer.Tile + ol.source.TileWMS方式，与前两种方式不同的是使用512×256的瓦片网格加载，即通过TileWMS数据源的tileGrid参数设置瓦片网格信息参数，设置为实例化ol.tilegrid.TileGrid的网格对象

3、 加载WFS
	WFS提供了Web要素服务，类似于常用的矢量地图服务，通过WFS可以将发布的矢量地图加载到客户端
    
    // 在本示例中使用基于GeoServer发布的WFS，其服务地址为http://demo.boundlessgeo.com/geoserver/wfs，请求的typename为osm:ne_110m_admin_0_boundary_lines_land，投影坐标系为EPSG:4326。
    
    var wfsLayer; //WFS图层
    var vectorSource; //矢量数据源
    //创建ol.format.GeoJSON对象,用来解析WF S的GetFeature接口的响应结果
    var geojsonFormat = new ol.format.GeoJSON();
    //实例化矢量数据源对象(使用ajax方法请求WFS)
    vectorSource = new ol.source.Vector({
      loader: function (extent, resolution, projection) {
        //WFS的GetFeature接口访问地址,指定服务回调函数
        var url = 'http://demo.boundlessgeo.com/geoserver/wfs? service=WFS&' +
          'version=1.1.0&request=GetFeature&typename=osm:ne_110m_admin_0_boundary_lines_land&' +
          'outputFormat=text/javascript&format_options=callback:loadFeatures' +
          '&srsname=EPSG:4326&bbox=' + extent.join(', ') + ', EPSG:4326';
        //使用jQuery的ajax方法进行请求,使用jsonp方式,设置参数jsonp为false,以防止jQuery添加callback回调函数
        $.ajax({
          url: url,
          dataType: 'jsonp',
          jsonp: false
        });
      },
      //设置加载策略
      strategy: ol.loadingstrategy.tile(ol.tilegrid.createXYZ({
        maxZoom: 19
      }))
    });
    /*jsonp WFS的回调函数(callback)
     *@param{Object}response响应对象*/
    window.loadFeatures = function (response) {
      //解析WFS的GetFeature接口响应结果,将解析得到的矢量地图添加到矢量数据源中
      vectorSource.addFeatures(geojsonFormat.readFeatures(response));
    };
    //实例化ol.layer.Vector对象并加载WFS
    wfsLayer = new ol.layer.Vector({
      //矢量数据源(WFS的矢量要素)
      source: vectorSource,
      //矢量要素样式
      style: new ol.style.Style({
        stroke: new ol.style.Stroke({
          color: 'rgba(0, 0, 255, 1.0)',
          width: 2
        })
      })
    });
	// 加载WFS的关键是通过ajax方式请求WFS的GetFeature接口，在ajax请求的回调函数中用数据解析类（ol.format.GeoJSON）解析返回的矢量数据，最后将解析后的矢量要素加载到矢量数据源中
```

## OpenLayers之高级功能篇

> 地图标注是将空间位置信息点与地图关联，通过图标、窗口等形式把相关的信息展现到地图上。地图标注也是WebGIS中的比较重要的功能之一，在大众应用中较为常见。基于地图标注，可以为用户提供更多个性化的地图服务，如标注兴趣点等



### 地图标注功能

```js
地图标注的基本原理是：
    获取标注点的空间位置（X、Y逻辑坐标），在该位置上叠加显示图标（或包含信息的小图片），必要时以窗口形式显示详细的信息。其中，在获取标注点X、Y逻辑坐标值时要注意，通过鼠标在地图上单击可得到窗口坐标后，一般需要将窗口坐标转为逻辑坐标
    
1、图文标注
	方式一： 矢量图层方式添加
    var beijing = ol.proj.fromLonLat([116.28, 39.54]);
    
    //实例化矢量点要素，通过矢量图层添加到地图容器中
    //这样就实现了预先加载图文标注
    var iconFeature = new ol.Feature({
      geometry: new ol.geom.Point(beijing),
      name: '北京市', //名称属性
      population: 2115 //人口数（万）
    });
    //设置点要素样式
    iconFeature.setStyle(createLabelStyle(iconFeature));
    //矢量标注的数据源，可以新增很多点位置
    var vectorSource = new ol.source.Vector({
      features: [iconFeature]
    });
    //矢量标注图层
    var vectorLayer = new ol.layer.Vector({
      source: vectorSource
    });
    map.addLayer(vectorLayer);

    //矢量标注样式设置函数，设置image为图标ol.style.Icon
    function createLabelStyle(feature) {
      return new ol.style.Style({
        image: new ol.style.Icon({
          anchor: [0.5, 60], //锚点
          anchorOrigin: 'top-right', //锚点源
          anchorXUnits: 'fraction', //锚点X值单位
          anchorYUnits: 'pixels', //锚点Y值单位
          offsetOrigin: 'top-right', //偏移原点
          opacity: 0.75,
          src: './images/icon.png' //图标的URL
        }),
        text: new ol.style.Text({
          textAlign: 'center', //位置
          textBaseline: 'middle', //基准线
          font: 'normal 14px 微软雅黑', //文字样式
          text: feature.get('name'), //文本内容
          fill: new ol.style.Fill({ //文本填充样式（即文字颜色)
            color: '#000'
          }),
          stroke: new ol.style.Stroke({
            color: '#F00',
            width: 2
          })
        })
      });
    }
    ol.Feature的关键参数如下：
    ● geometry：几何图形对象，标注点一般设置为ol.geom.Point对象，可通过其逻辑坐标点设置。
    ● 自定义的属性信息，如name、population等，可根据需要自行定义，这些属性信息可通过调用get方法获取，如feature.get（′name′）。

	方式二：非图层方式叠加到地图容器，可以自定义样式
    	// 点模板
    	<div id="marker" class="marker" title="Marker">
          <a href="#" id="address" class="address">标注点</a>
        </div>
    	var wuhan = ol.proj.fromLonLat([114.21, 30.37])
        
        //实例化叠加层标注,并添加到地图容器中
        var marker = new ol.Overlay({
          position: wuhan,
          positioning: 'center-center',
          element: document.getElementById('marker'),
          stopEvent: false
        });
        map.addOverlay(marker);
        marker.getElement().title = "武汉市";
        var text = new ol.Overlay({
          position: wuhan,
          element: document.getElementById('address')
        });
        map.addOverlay(text);
        text.getElement().innerText = marker.getElement().title;

	class为marker的是叠加层标注的样式，主要设置标注的图形信息，一般可设置为img图标或者圆点；
    class为address叠加层标注的文本标签样式，可以设置文本的字体、颜色、大小等样式
    

叠加层标注：
	实例化叠加层标注（ol.Overlay）对象，关联页面中创建的HTML元素，调用map的addOverlay方法将其加载到地图容器中。在本示例中添加图文标注，需要分别创建图标与文本两个ol.Overlay对象，并分别在关联的地容容器（label）里创建的图形标签元素（marker）和文本标签元素（address）。
	ol.Overlay的关键参数如下：
        ● position：叠加对象的地理位置参数，标注点设置为其逻辑坐标点。
        ● element：目标元素，即Overlay对象关联的HTML元素，承载Overlay的页面元素。
        
        
2、Popup标注
	Popup标注是通过冒泡方式显示标注点的详细信息的，一般结合基本标注点使用。Popup标注比较灵活，可以结合HTML、CSS等自定义标注信息的展现样式，在弹出框中描述标注点详细信息.
    
    <style type="text/css">
    #menu {
      width: 100%;
      height: 20px;
      padding: 5px 10px;
      font-size: 14px;
      font-family: "微软雅黑";
      left: 10px;
      text-align: center;
    }

    .ol-popup {
      position: absolute;
      background-color: white;
      -webkit-filter: drop-shadow(0 1px 4px rgba(0, 0, 0, 0.2));
      filter: drop-shadow(0 1px 4px rgba(0, 0, 0, 0.2));
      padding: 15px;
      border-radius: 10px;
      border: 1px solid#cccccc;
      bottom: 12px;
      left: -50px;
    }

    .ol-popup:after,
    .ol-popup:before {
      top: 100%;
      border: solid transparent;
      content: "";
      height: 0;
      width: 0;
      position: absolute;
      pointer-events: none;
    }

    .ol-popup:after {
      border-top-color: white;
      border-width: 10px;
      left: 48px;
      margin-left: -10px;
    }

    .ol-popup:before {
      border-top-color: #cccccc;
      border-width: 11px;
      left: 48px;
      margin-left: -11px;
    }

    .ol-popup-closer {
      text-decoration: none;
      position: absolute;
      top: 2px;
      right: 8px;
    }

    .ol-popup-closer:after {
      content: "✖";
    }

    #popup-con tent {
      font-size: 14px;
      font-family: "微软雅黑";
    }

    #popup-con tent .markerInfo {
      font-weight: bold;
    }
  </style>
/* 实现Popup标注的HTML元素*/
    var container = document.getElementById('popup');
    var content = document.getElementById('popup-content');
    var closer = document.getElementById('popup-closer');
    /* 在地图容器中创建一个Overlay对象*/
    var popup = new ol.Overlay( /** @type {olx.OverlayOptions} */ ({
      element: container,
      autoPan: true,
      positioning: 'bottom-center',
      stopEvent: false,
      autoPanAnimation: {
        duration: 250
      }
    }));
    map.addOverlay(popup);
    /* 添加关闭按钮的单击事件(隐藏Popup标注)
     * @return {boolean} Don't follow the href.*/
    closer.onclick = function () {
      popup.setPosition(undefined); //未定义Popup标注位置
      closer.blur(); //失去焦点
      return false;
    };
    /* 动态创建Popup标注的具体内容
     * @param {string} title */
    function addFeatrueInfo(info) {
      var elementA = document.createElement('a'); //新增a元素
      elementA.className = "markerInfo";
      elementA.href = info.att.titleURL;
      //el  ementA.innerText=info.att.title;
      setInnerText(elementA, info.att.title);
      content.appendChild(elementA); //新建的div元素添加a子节点
      var elementDiv = document.createElement('div'); //新增div层元素
      elementDiv.className = "markerText";
      setInnerText(elementDiv, info.att.text);
      content.appendChild(elementDiv); //为content添加div子节点
      var elementImg = document.createElement('img'); //新增img元素
      elementImg.className = "markerImg";
      elementImg.src = info.att.imgURL;
      content.appendChild(elementImg); //为content添加img子节点
    }
    /* 动态设置标注的文本内容(兼容)*/
    function setInnerText(element, text) {
      if (typeof element.textContent == "string") {
        element.textContent = text;
      } else {
        element.innerText = text;
      }
    }

    // 
    //示例标注点北京市的信息对象
    var featuerInfo = {
      geo: ol.proj.fromLonLat([116.28, 39.54]),
      att: {
        title: "北京市( 中华人民共和国首都)", //标注信息的标题内容
        titleURL: "http: //www.openlayers.org/",  //标注详细信息链接
          text: "北京( Beijing)， 简称京， 中华人民共和国首都、 直辖市， 中国的政治、 文化和国际交往中心……", //标注内容简介
        imgURL:".. / https: //res.weread.qq.com/wrepub/web/26924461/label/bj.png"    //标注的图片
      }
    }
    /* 为map添加单击事件监听，渲染弹出Popup标注*/
    map.on('click', function (evt) {
      //判断当前单击处是否有要素，捕获到要素时弹出Popup标注
      var feature = map.forEachFeatureAtPixel(evt.pixel, function (feature, layer) {
        return feature;
      });
      if (feature) {
        content.innerHTML = ''; //清空Popup标注的内容容器
        addFeatrueInfo(featuerInfo); //在Popup标注中加载当前要素的具体信息
        if (popup.getPosition() == undefined) {
          popup.setPosition(featuerInfo.geo); //设置Popup标注的位置
        }
      }
    });
    /* 为map对象添加鼠标移动监听事件，当指向标注时改变鼠标的光标形状*/
    map.on('pointermove', function (e) {
      var pixel = map.getEventPixel(e.originalEvent);
      var hit = map.hasFeatureAtPixel(pixel);
      map.getTargetElement().style.cursor = hit ? 'pointer' : '';
    });


3、聚合标注
	聚合标注是指在不同地图分辨率下，通过聚合方式展现标注点信息的一种方式，其目的是为了减少当前视窗下地图添加标注点的数量，提升客户端渲染速度
    ol.source.Cluster的关键参数如下：
    ● source：聚合标注的数据源，本示例设置的是添加10000个随机矢量要素的矢量数据源对象；
    ● distance：聚合标注距离参数，当标注间距离小于此值时进行聚合，本示例设置的参数是40个像素。
    
    
    //创建10000个随机矢量要素
    var count = 10000;
    var features = new Array(count);
    var e = 4500000;
    for (var i = 0; i < count; ++i) {
      var coordinates = [2 * e * Math.random() - e, 2 * e * Math.random() - e];
      var iconFeature = new ol.Feature({
        geometry: new ol.geom.Point(coordinates),
      });
      //设置点要素样式
      iconFeature.setStyle(createLabelStyle(iconFeature));
      features[i] = iconFeature
    }
    //矢量要素数据源
    var source = new ol.source.Vector({
      features: features
    });
    //聚合标注数据源, 将矢量要素图层当作数据源
    var clusterSource = new ol.source.Cluster({
      distance: 40, //聚合标注的距离参数,即当标注间距离小于此值时进行聚合
      source: source //聚合标注的数据源,即矢量要素数据源对象
    });
    //添加聚合标注的矢量图层
    var styleCache = {};
    var clusters = new ol.layer.Vector({
      source: clusterSource,
    });
    //添加聚合标注
    map.addLayer(clusters)
```

### 投影转换

```js
	在Web端加载显示地图数据时，都要设置地图数据的投影坐标系。OpenLayers 5在地图视图对象中有一个投影坐标系参数（projection），在加载地图时可通过此参数设置地图的投影坐标系。在实际应用中，叠加图层数据需要确保在统一的投影坐标系下
    
    OpenLayers 5已定义了两种常用的投影坐标系，即
    地理经纬度的EPSG:4326和Web墨卡托的EPSG:3857, EPSG:3857为CRS:84等
    EPSG:4326则包括EPSG:102100、EPSG:102113、EPSG:900913。
    
    var transformMap;
    var vectLayer = new ol.layer.Vector({
      source: new ol.source.Vector({
        url: '../../data/geojson/countries.geojson',
        format: new ol.format.GeoJSON()
      })
    });
    var map = new ol.Map({
      layers: [
        new ol.layer.Tile({
          title: "天地图矢量图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        }),
        vectLayer,
        new ol.layer.Tile({
          title: "天地图矢量注记图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        })
      ],
      target: 'map1', // 地图容器div层的ID
      view: new ol.View({
        projection: ol.proj.get('EPSG:3857'), //投影坐标系
        center: [0, 0],
        zoom: 1
      })
    });
    //定义球形摩尔魏特投影坐标系,对应ESRI的编号为53009
    proj4.defs('ESRI:53009', '+proj=moll +lon_0=0 +x_0=0 +y_0=0 +a=6371000 ' + '+b=6371000+units=m+no_defs');
    ol.proj.proj4.register(proj4);
    var sphereMollweideProjection = new ol.proj.Projection({
      code: 'ESRI:53009', //编号
      extent: [-9009954.605703328, -9009954.605703328, 9009954.605703328, 9009954.605703328], //范围
      worldExtent: [-179, -90, 179, 90] //地理经纬度范围
    });

    //开始投影坐标系的转换
    document.getElementById('projection').onclick = function () {
      if (transformMap == null || transformMap == undefined) {
        transformMap = new ol.Map({
          layers: [
            new ol.layer.Vector({
              source: new ol.source.Vector({
                url: ' ../../data/geojson/countries.geojson',
                format: new ol.format.GeoJSON()
              })
            })
          ],
          target: 'map2',
          view: new ol.View({
            projection: sphereMollweideProjection, //投影坐标系
            center: [0, 0],
            zoom: 1
          })
        });
        //参考系标线
        var graticule = new ol.Graticule({
          map: transformMap //显示参考系标线的地图
        });
      }
    };
    
    上述代码的关键是创建自定义投影坐标系对象，先通过proj4.defs（）定义球形摩尔魏特投影坐标系（ESRI:53009），然后实例化ol.proj.Projection对象，创建ESRI:53009对应的投影坐标系对象。基于创建的投影坐标系对象，将加载的矢量地图投影到此投影坐标系中显示。
```

### 视图联动

```js
针对OpenLayers 5的两种渲染方式分别创建两个地图容器，在这两个地图容器中实现地图的视图联动

//加载Canvas渲染方式的地图并设置为主图,默认情况下为Canvas渲染方式
    var canvasMap = new ol.Map({
      layers: [
        new ol.layer.Tile({
          title: "天地图矢量图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        }),
        new ol.layer.Tile({
          title: "天地图矢量注记图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            wrapX: false
          })
        })
      ],
      target: 'canvasMap', //地图容器div层的ID
      view: new ol.View({
        center: [12723048, 3575638], //地图初始中心点
        minZoom: 2,
        zoom: 6
      })
    })
    //加载WebGL渲染方式的地图作为联动图
    var webglMap = new ol.WebGLMap({
      layers: [
        new ol.layer.Tile({
          title: "天地图矢量图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=img_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            crossOrigin: "anonymous",
            wrapX: false
          })
        }),
        new ol.layer.Tile({
          title: "天地图矢量注记图层",
          source: new ol.source.XYZ({
            url: "http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=您的天地图密钥",
            crossOrigin: "anonymous",
            wrapX: false
          })
        })
      ],
      target: 'webglMap', //地图容器div层的ID
      view: canvasMap.getView() // 设置为主图的视图对象
    })
    
    通过ol.Map（默认情况下为Canvas渲染方式）将Canvas渲染方式的地图作为主图，通过ol.WebGLMap实例化另一个采用WebGL渲染方式地图时，将view参数设置为主图的view对象，即通过主图对象的getView（）得到主图视图对象，并且在采用WebGL渲染方式的地图容器中渲染瓦片数据源时，必须设置crossOrigin属性为anonymous，否则会报跨域的错误。
```

### 地图导航

```
	实现实时定位功能的关键是加载定位导航控件（ol.Geolocation），调用定位导航控件对象的setTracking方法来启动位置跟踪功能，同时为此控件对象绑定以下三个事件。
● 导航位置变更事件（change:position）：当导航位置变更事件被触发时，在事件的回调函数中获取当前定位坐标点，在地图上添加矢量标注点，并以此定位点为地图中心进行定位。
● 定位导航控件变更事件（change）：当控件信息变更时，更新页面右上角定位信息面板中的导航位置等相关信息。
● 精确模式定位事件（change:accuracyGeometry）：当缩放到一定分辨率时，响应此事件，在事件回调函数中设置精确定位点要素的几何信息。



```

### 热点图

```js
    热点图也称为热力图，是一种以特殊高亮的形式显示事物密度分布、变化趋势等地理区域的图示，通常用不同颜色的区块叠加在地图上实时描述，以颜色变化展现事物的状态与变化趋势等。例如，景区人群分布热点图，可以在地图上很直观地表示景区拥挤指数等。
    <body>
        <form style="position: absolute;left: 200px;z-index: 999;color: blue;">
            <label>半径大小</label>
            <input id="radius" type="range" min="1" max="50" step="1" value="20" />
            <label>模糊大小</label>
            <input id="blur" type="range" min="1" max="50" step="1" value="25" />
        </form>

        <div id="map"></div>
    </body>

    // 开始做热力图相关功能
    let blur = document.getElementById("blur");
    let radius = document.getElementById("radius");

    var wandaVector = new ol.source.Vector({
        url: "./data/wanda.geojson",
        format: new ol.format.GeoJSON()
    });
    //定义热力图图层
    let vector = new ol.layer.Heatmap({
        source: wandaVector,
        blur: parseInt(blur.value, 10),
        radius: parseInt(radius.value, 10),
    });
        
    // 模糊按钮的回调函数
    let blurHandler = function (){
        vector.setBlur(parseInt(blur.value, 10));
    };
    blur.addEventListener("input", blurHandler);
    blur.addEventListener("change", blurHandler);

    // 半径按钮的回调函数
    let radiusHandler = function () {
        vector.setRadius(parseInt( radius.value, 10));
    };
    radius.addEventListener("input", radiusHandler);
    radius.addEventListener("change", radiusHandler);
    // 天地矢量图层
    var map = new ol.Map({
        target: "map",
        layers: [
            //加载瓦片图层数据
            new ol.layer.Tile({
                title: "天地图矢量图层",
                source: new ol.source.XYZ({
                    url: `http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=${tk}`,
                    wrapX: false,
                }),
            }),
            new ol.layer.Tile({
                title: "天地图矢量注记图层",
                source: new ol.source.XYZ({
                    url: `http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=${tk}`,
                    wrapX: false,
                }),
            }),
            vector
        ],
        loadTilesWhileInteracting: true,
        view: new ol.View({
            center: [12622513, 2636878], //地图初始中心点
            zoom: 9,  //地图初始显示级别
        }),
        controls: ol.control.defaults().extend([]),
    });

	实现热点图的关键是基于ol.layer.Heatmap创建热点图图层对象，根据热点图层的热点半径（radius）、模糊尺寸（blur）等渲染热点图。在本示例中，分别通过调用热点图层的setRadius、setBlur接口，设置渲染热点图因子——热点半径与模糊尺寸。另外，还通过热点图层数据源的addfeature事件，在加载热点图层矢量要素时根据地震震级属性设置矢量要素的weight属性值。
```

### 热区功能



```js
![toolOPen](../../resource/toolOPen.png)	热区功能的实现过程如下：
    ● 基于空间坐标点，在客户端绘制几何图形，同时添加鼠标监听事件对绘图进行控制；
    ● 在鼠标事件的回调函数中，需要判断鼠标当前点是否在绘制的区域图形内，若是则加载文字标注和toolTip标注信息，否则不做任何操作
    
    例如：省市级地理区域范围绘制
    
    
    var changeSource = new ol.source.Vector({
        url: "./data/changsha.geojson",
        format: new ol.format.GeoJSON()
    });
    //长沙区域图层
    let vector = new ol.layer.Vector({
        source: changeSource,
        
    });
	// 图层样式设置
    vector.setStyle(
        new ol.style.Style({
            fill: new ol.style.Fill({ color: 'skyblue' }),
            stroke: new ol.style.Stroke({
                width: 3,
                color: [71, 137, 227, 1]
            })
        })
    )

    // 天地矢量图层
    var map = new ol.Map({
        target: "map",
        layers: [
            //加载瓦片图层数据
            new ol.layer.Tile({
                title: "天地图矢量图层",
                source: new ol.source.XYZ({
                    url: `http://t0.tianditu.com/DataServer? T=vec_w&x={x}&y={y}&l={z}&tk=${tk}`,
                    wrapX: false,
                }),
            }),
            new ol.layer.Tile({
                title: "天地图矢量注记图层",
                source: new ol.source.XYZ({
                    url: `http://t0.tianditu.com/DataServer? T=cva_w&x={x}&y={y}&l={z}&tk=${tk}`,
                    wrapX: false,
                }),
            }),
            vector
        ],
        loadTilesWhileInteracting: true,
        view: new ol.View({
            center: [12622513, 2636878], //地图初始中心点
            zoom: 9,  //地图初始显示级别
        }),
        controls: ol.control.defaults().extend([]),
    });
```

###  军事标绘功能

![toolOPen](https://raw.githubusercontent.com/wyf195075595/images/main/blog/toolOPen.png)

```js
	
基于上述扩展原理，OpenLayers 5扩展封装了军事标绘绘制工具（MilStd. Tool、MilStdDrawTool）和其编辑工具（MilStd.ModifyTool、MilStd.DragPan等），分别封装到功能脚本文件MilStdDraw.js和MilStdModify.js中。

	OpenLayers 5的交互式图形绘制控件（ol.interaction.Draw）提供了基本的图形绘制接口，可满足大众化应用的需求，同时具备扩展性。对于某些特殊行业而言，若基本的图形绘制接口不能满足需求，可以在此基础上进行相应扩展。例如，GIS领域中常用的军事标绘功能，需要绘制表示相关功能类型的图形，如各种箭头、集结区域、旗帜等，这就需要进行扩展开发。

三方拓展
1、openlayers 扩展标绘V2.0

军事标绘功能，支持openlayers3，4。


```

## 拓展

### ol-hashed

> 由于我们会经常重新加载页面，如果地图在重新加载时保持在我们离开它的位置就好了.ol-hashed 解决这个问题

```js
import sync from 'ol-hashed';
const map = new Map({})


sync(map);
```

### 拖放

> 将一个 geijson 文件拖入地图，然后渲染

```js
import DragAndDrop from 'ol/interaction/DragAndDrop';
// 创建一个空地图
const map = new Map({
    target: '',
    layer: [],
    view: {}
})

// 创建一个没有初始数据的矢量源。此源将存储用户拖放到地图上的要素。
const source = new VectorSource();
const layer = new VectorLayer({
  source: source,
});
map.addLayer(layer);

// 创建一个拖放交互，将其配置为与我们的矢量源一起使用，并将其添加到地图中
map.addInteraction(
  new DragAndDrop({
    source: source,
    formatConstructors: [GeoJSON],
  })
);

修改特征：
import Modify from 'ol/interaction/Modify';

map.addInteraction(
  new Modify({
    // 此数据源为上面托进来的数据源
    source: source,
  })
);
【删除顶点】：使用 Alt+Click


绘制新特征（点，线，折线，圆等几何图形）：
import Draw from 'ol/interaction/Draw';
// 创建一个绘制交互
let draw = new Draw({
    type: 'Polygon',
    source: source,
  })
map.addInteraction(draw);
// 切换绘制类型时，需移出上一个绘制类型
map.removeInteraction(draw); //移除绘制的图形


捕捉，吸附：
	您可能已经注意到，很容易绘制与现有特征不匹配的特征。此外，在修改特征时，我们可以破坏拓扑——在之前相邻的多边形之间添加一个空隙。Snap交互可用于在绘制和编辑要素时帮助保留拓扑。
import Snap from 'ol/interaction/Snap';
map.addInteraction(
  new Snap({
    source: source,
  })
);

	随着绘制、修改和捕捉交互全部处于活动状态，我们可以在保持拓扑结构的同时编辑数据

    
下载、清除数据源：

清除：source.clear();
下载: 
    const format = new GeoJSON({featureProjection: 'EPSG:3857'});
    const download = document.getElementById('download');
// 数据源变化，就设置a 标签href. 有数据时就可以点击导出了
    source.on('change', function () {
      const features = source.getFeatures();
      const json = format.writeFeatures(features);
      download.href = 'data:application/json;charset=utf-8,' + encodeURIComponent(json);
    });
  // download 为一个a 标签，<a id="download" download="features.json">Download</a>


给图层添加样式：
import {Style, Fill, Stroke} from 'ol/style';
// 静态设置
const layer = new VectorLayer({
  source: source,
  style: new Style({
    fill: new Fill({
      color: 'red'
    }),
    stroke: new Stroke({
      color: 'white'
    })
  })
});
// 动态设置
const layer = new VectorLayer({
  source: source,
  style: function(feature, resolution) {
    const name = feature.get('name').toUpperCase();
    return name < "N" ? style1 : style2; // assuming these are created elsewhere
  }
});

// eg: 根据图形面积，设置颜色
需要引入 colormap 包
import colormap from 'colormap';
import {getArea} from 'ol/sphere';

// 计算几何图形面积
const min = 1e8; // 最小面积
const max = 2e13; // 最大面积
const steps = 50;
const ramp = colormap({
  colormap: 'blackbody',
  nshades: steps,
});

function clamp(value, low, high) {
  return Math.max(low, Math.min(value, high));
}

function getColor(feature) {
  const area = getArea(feature.getGeometry());
  const f = Math.pow(clamp((area - min) / (max - min), 0, 1), 1 / 2);
  const index = Math.round(f * (steps - 1));
  return ramp[index];
}

// 根据几何面积，创建具有填充颜色的样式
const layer = new VectorLayer({
  source: source,
  style: function (feature) {
    return new Style({
      fill: new Fill({
        color: getColor(feature),
      }),
      stroke: new Stroke({
        color: 'rgba(255,255,255,0.8)',
      }),
    });
  },
});
```

> ### [demo示例](https://codepen.io/wyf195075595/pen/ExGJZzv)

### 绘制点线面获取坐标

```vue
<template>
    <main class="map_container">
      <div id="lonlatText1" class="lonlatText1"></div>
      <!-- <div class="mapCard">
        <div :class="['weixin-map', mapType == 1 ? 'show-map' : '']" @click="changMapType(1)"><span>卫星</span></div>
        <div :class="['putong-map', mapType == 2 ? 'show-map' : '']" @click="changMapType(2)"><span>普通</span></div>
      </div> -->
      <div class="tools">
        <el-button-group>
          <el-button type="primary" v-if="props.type == 'Point'" @click="addPointInteraction">点</el-button>
          <el-button type="primary" v-if="props.type == 'LineString'"  @click="addLineInteraction">线</el-button>
          <el-button type="primary" v-if="props.type == 'Polygon'"  @click="addPolygonInteraction">面</el-button>
          <!-- <el-button type="primary" @click="getMapPosition">我的位置</el-button> -->
          <el-button type="primary" @click="resetDrawing">重置</el-button>
          <el-tooltip
            class="box-item"
            content="点击绘制类型按钮开始绘制，绘制线面时，按回车键结束绘制"
            placement="top-start"
          >
            <el-button type="primary" :icon="QuestionFilled">提示</el-button>
          </el-tooltip>
        </el-button-group>
      </div>
      <div class="map_openlayer" ref="mapContainer"></div>
    </main>
  </template>
  
  <script setup>
  import { QuestionFilled } from '@element-plus/icons-vue'
  import "ol/ol.css";
  import { Map as olMap, View } from "ol";
  import TileLayer from 'ol/layer/Tile';
  import TileWMS from 'ol/source/TileWMS';
  import XYZ from 'ol/source/XYZ';
  import { get, fromLonLat } from 'ol/proj';
  import { defaults } from 'ol/control';
  import controlMousePosition from 'ol/control/MousePosition';
  import controlZoomToExtent from 'ol/control/ZoomToExtent';
  import controlZoomSlider from 'ol/control/ZoomSlider';
  import controlFullScreen from 'ol/control/FullScreen';
  import controlScaleLine from 'ol/control/ScaleLine';
  
  import { format } from 'ol/coordinate';
  import VectorSource from 'ol/source/Vector';
  import Cluster from 'ol/source/Cluster';
  import VectorLayer from 'ol/layer/Vector';
  import Style from 'ol/style/Style';
  import Icon from 'ol/style/Icon';
  import Text from 'ol/style/Text';
  import Fill from 'ol/style/Fill';
  import Stroke from 'ol/style/Stroke';
  import Feature from 'ol/Feature';
  import Point from 'ol/geom/Point';
  import { containsExtent } from 'ol/extent';
  import * as olInteraction from 'ol/interaction';
  import GeoJSON from 'ol/format/GeoJSON';
  import anhuiJSON from '@/assets/json/fx.json';
  import { onMounted, ref } from "vue";
  import mapPointIcon from '@/assets/images/icon_map_point.png'
  const { proxy } = getCurrentInstance();
  const props = defineProps({
    type: {
      type: String,
      default: 'Point'
    }
  })
  let emitPosition = defineEmits(['update:position']);
  const defaultPointStyle = new Style({
    image: new Icon({
      anchor: [0.5, 1],
      src: mapPointIcon
    })
  });
  const useMap = () => {
    let map = null;
    const mapPosition = ref({
      // 点
      point: [],
      // 线
      line: [],
      // 面
      polygon: [],
    })
    const vector = new VectorLayer({
      zIndex: 999,
      visible: true,
      name: "绘制图层",
      source: new VectorSource({ wrapX: false }),
      style: function (feature) {
        if (feature.getGeometry().getType() === 'Point') {
          return defaultPointStyle;
        }
        return null;
      }
    });
    const mapInit = () => {
      map = new olMap({
        target: proxy.$refs.mapContainer, // 地图的容器，可以是元素本身，也可以是元素的 id
        layers: [ // 图层 按照提供的顺序呈现的（后面层级越高）
          new TileLayer({
            name: "普通",
            zIndex: 1,
            source: new XYZ({
              url: GLOBAL_CONFIG.gisServerUrl + "/{z}/{x}/{y}.png"
            }),
          }),
          vector
        ],
        view: new View({
          center: [117.25, 31.68],
          projection: get("EPSG:4326"),
          zoom: 9,
          minZoom: 9,
          maxZoom: 16
        }),
        controls: defaults({
          attribution: false,
          rotate: false,
          zoom: false,
          zoomOptions: { delta: 0.5 }
        }).extend([
            /* 视图重置跳转控件 */
            new controlZoomToExtent({
                extent: [110, 30, 160, 30],
            }),
            /* 放大缩小条控件 */
            new controlZoomSlider({
                zoomInTipLabel: '放大',
                zoomOutTipLabel: '缩小'
            }),
            //全屏控件
            new controlFullScreen({

            }),
            // 比例尺
            new controlScaleLine({

            }),
            new controlMousePosition({
                // 坐标格式
                coordinateFormat: (coordinate) => {
                const zoom = map.getView().getZoom();
                return format(coordinate, '经度：{x}°，纬度：{y}°' + '，层级：' + zoom, 4);
                },
                //地图投影坐标系（若未设置则输出为默认投影坐标系下的坐标）
                projection: "EPSG:4326",
                //坐标信息显示样式类名，默认是'ol-mouse-position'
                className: "custom-mouse-position",
                //显示鼠标位置信息的目标容器
                // target: document.getElementById("lonlatText1"),
                //未定义坐标的标记
                undefinedHTML: ""
            }),
        ]),
      });
  
    }
    let draw;
    const source = vector.getSource();
    document.addEventListener('keydown', function (event) {
      if (event.key === 'Enter' && draw) {
        // 按下回车键结束绘制
        finishDrawing();
      }
    });
    function addPointInteraction() {
        draw = new olInteraction.Draw({
          source: source,
          type: 'Point'
        });
        draw.on('drawend', function (event) {
          const feature = event.feature;
          const geometry = feature.getGeometry();
          const coordinates = geometry.getCoordinates();
          mapPosition.value.point = coordinates;
          finishDrawing();
        });
        map.addInteraction(draw);
    }
  
    function addLineInteraction() {
        draw = new olInteraction.Draw({
            source: source,
            type: 'LineString'
        });
        draw.on('drawend', function (event) {
          const feature = event.feature;
          const geometry = feature.getGeometry();
          const coordinates = geometry.getCoordinates();
          mapPosition.value.line = coordinates;
          finishDrawing();
        });
        map.addInteraction(draw);
    }
  
    function addPolygonInteraction() {
        draw = new olInteraction.Draw({
            source: source,
            type: 'Polygon'
        });
        draw.on('drawend', function (event) {
          const feature = event.feature;
          const geometry = feature.getGeometry();
          const coordinates = geometry.getCoordinates();
          mapPosition.value.polygon = coordinates;
          finishDrawing();
        });
        map.addInteraction(draw);
    }
  
    function finishDrawing() {
      if (draw) {
        draw.finishDrawing();
        map.removeInteraction(draw);
        draw = null;
      }
      emitPosition('update:position', mapPosition.value);
    }
    function resetDrawing() {
      source.clear();
      mapPosition.value.polygon = [];
      mapPosition.value.line = [];
      mapPosition.value.point = [];
    }
    function getMapPosition() {
      if ('geolocation' in navigator) {
          // 获取当前位置
          navigator.geolocation.getCurrentPosition(function (position) {
              const lon = position.coords.longitude;
              const lat = position.coords.latitude;
              // 将经纬度转换为地图投影坐标
              const center = fromLonLat([lon, lat]);
              console.log('我的位置：',center, [lon, lat]);
              
              // 定位地图视图到当前位置
              map.getView().animate({
                center: [lon, lat],
                zoom: 15
              })
              // map.getView().setCenter([lon, lat]);
              // map.getView().setZoom(15);
          }, function (error) {
              console.error('获取位置失败:', error.message);
          });
      } else {
          console.error('浏览器不支持地理定位');
      }
    }
    onMounted(() => {
      resetDrawing();
      mapInit();
    });
    // onBeforeUnmount(() => {
    // })
  
    return {
      addPointInteraction,
      addLineInteraction,
      addPolygonInteraction,
      finishDrawing,
      resetDrawing,
      getMapPosition
    }
  }
  
  const { 
    addPointInteraction,
    addLineInteraction,
    addPolygonInteraction,
    finishDrawing,
    resetDrawing,
    getMapPosition
  } = useMap();
  </script>
  
  <style lang="scss" scoped>
  .map_container {
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    overflow: hidden;
    position: relative;
  
    .lonlatText1 {
      position: absolute;
      width: fit-content;
      left: 50%;
      transform: translateX(-50%);
      bottom: 5px;
      background: #1e1e2b;
      border-radius: 5px;
      opacity: 0.65;
      color: #fff;
      font-size: 12px;
      line-height: 24px;
      padding: 0 10px;
      z-index: 9;
    }
  
    .map_openlayer {
      height: 100%;
      box-sizing: border-box;
      position: relative;
      z-index: 1;
    }
    .tools {
      position: absolute;
      left: 30px;
      top: 30px;
      z-index: 8;
      display: flex;
    }
    .mapCard {
      position: absolute;
      right: 30px;
      top: 30px;
      z-index: 8;
      display: flex;
  
      >div {
        display: none;
        width: 86px;
        height: 60px;
        border: 2px solid white;
        cursor: pointer;
  
        span {
          background: #2d3122;
          padding: 2px 5px;
          font-size: 12px;
          color: #fff;
          display: inline-block;
        }
      }
  
      >div.show-map {
        display: block;
  
        span {
          background: #3385FF;
        }
      }
  
      &:hover>div {
        display: block;
      }
  
      .weixin-map {
        background-image: url("@/assets/images/maptype.png");
        background-position: 0 -60px;
      }
  
      .putong-map {
        background-image: url("@/assets/images/maptype.png");
        background-position: 0 0;
      }
    }
  }</style>
```



### 自定义控件

> ol组件拓展

#### loading 组件

> 自定义一个地图loading面板，请求加载时，地图不可操作，相当于加一个透明蒙版

```js
ol.control.LoadingControl = function(opt_options){
    const _options = opt_options || {}
    
    const _root = document.createElement('div')
    _root.className = 'ol-layer-data-loading'
    _root.style = {
        'width':'100%',
        'height':'100%',
        'background-color':'rgba(255,255,255,0.5)'
    }
    
    const _box = document.createElement('span')
    _box.className = 'ol-layer-spin-dot-spin'
    
    for(let i = 0 ; i < 4; i++){
        const _i = document.createElement('i')
        _i.className = 'ol-layer-spin-dot-item dot'+i
        _box.appendChild(_i)
    }
    _root.appendChild(_box)
    
    ol.control.Control.call(this,{
        element:_root,
        target:_options.target
    })
}

//继承ol.control.Control
ol.inherits(ol.control.LoadingControl,ol.control.Control)

//暴露出去的方法
ol.control.LoadingControl.prototype.show = function(){
    this.element.style.display = 'block'
}

//暴露出去的方法
ol.control.LoadingControl.prototype.hidden = function(){
    this.element.style.display = 'none'
}

```

蒙版样式

```css
.ol-layer-data-loading{
    width:100%;
    height:100%;
    box-sizing:border-box;
    margin:0;
    padding:0;
    font-size:14px;
    font-variant: tabular-nums;
    line-height:1.5;
    list-style:none;
    font-feature-settings:'tnum';
    position:absolute;
    top: 0;
    left: 0;
    display:none;
    color:#6996ff;
    text-align:center;
    vertical-align:middle;
    transition:transform 0.3s cubic-bezier(0.78,0.14,0.15,0.86);
    background-color:rgba(255,255,255,0.5)
}
.ol-layer-spin-dot-spin{
    position:relative;
    top:50%;
    font-size:32px;
    transform:rotate(45deg);
    animation:antRotate 1.2s infinite linear;
    display:inline-block;
    width:1em;
    height:1em;
}

.ol-layer-spin-dot-item{
    position:absolute;
    display:block;
    width:9px;
    height:9px;
    background-color:#6996ff;
    border-radius:100%;
    transform:scale(0.75);
    transform-origin: 50% 50%;
    animation:antSpinMove 1s infinite linear alternate;
    -webkit-animation:antSpinMove 1s infinite linear alternate;
}
.ol-layer-spin-dot-item > .dot0{
    top:0;
    left:0;
    opacity:1;
}

.ol-layer-spin-dot-item > .dot1{
    top:0;
    right:0;
    opacity:0.6;
}

.ol-layer-spin-dot-item > .dot2{
    bottom:0;
    right:0;
    opacity:0.3;
}

.ol-layer-spin-dot-item > .dot3{
    bottom:0;
    left:0;
    opacity:0.1;
}
```

引用控件

> 注册控件时添加id， 通过map对象获取注册的控件。
>
> 然后就可以调用控件方法了

```js
// 自定义 loading 控件
let loadControl = new ol.control.LoadingControl()
loadControl.set('id','loading') //标识控件，后续方便查找
map.addControl(loadControl);
// 根据id查找添加到地图上的控件
function getControlById(id,map){
    let _controlArray = map.getControls().getArray()
    let filter = _controlArray.filter(a=>{
        return a.get('id') == id
    }) 

    return filter.length > 0 ? filter[0]:undefined
}

let _load = getControlById('loading',map)

_load.show() // 显示控件
_load.hidden() //隐藏控件
```

