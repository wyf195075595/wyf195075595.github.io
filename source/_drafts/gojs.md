---
title: gojs
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---
仅仅是一个前端插件

官网:https://gojs.net/latest/index.html

下载之后并不是所有的js文件都用的到，我们需要了解到只有三个

```js
"""
go.js				 正常必须要导入的文件
go-debug.js  会展示报错消息 类似于debug模式 线上肯定不会使用
Figures.js	 扩展图表(go.js自带的图表比较少，如果出现图标显示不出来的情况)
"""
# 总结:使用的时候导入go.js和Figures.js就不会有任何问题了
```

## 简单的使用

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/gojsBaseTest.png)

<!--more-->

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GOJS</title>
    <style>
        #myDiagramDiv {
            width:800px;
            height:500px;
            background-color: #DAE4E4;
        }
    </style>
</head>
<body>
    <!-- 划定用来渲染gojs图标的div区域，背景颜色等参数 -->
    <div id="myDiagramDiv"></div>
    <script src="../js/go-debug.js"></script>
    <!-- <script src="../js/go.js"></script> -->
    <script>
        // - 通用属性 -
        // stroke	边框颜色	null为无边框，可填"#87CEFA"，"red"等
        // margin	边框间距	 
        // visible	设置是元素是否可见	true为可见，false为不可见，
        // textAlign	文本位置	"center"居中
        // editable	文本是否可编辑	true，false
        // font	字体	"bold 8pt Microsoft YaHei, Arial, sans-serif"
        // fill	背景颜色	可填"#87CEFA"，"red"等
        // alignment	元素位置设置	go.Spot.BottomLeft/左下    go.Spot.BottomRight/右下   go.Spot.TopLeft/左上 go.Spot.TopRight/右上 alignment:go.Spot.TopRight
        // isMultiline	编辑时是否允许换行	默认true
        // maxLines:1,	设置文本显示的最大行数	 
        // minSize:	最小大小	new go.Size(10, 16)，控制了最大大小后，文本就会自动换行了
        // maxSize:	最大大小	 

        // 1、开始使用
        var $ = go.GraphObject.make;
        // 第一步：创建图表，用于在页面上画图
        var myDiagram = $(go.Diagram, "myDiagramDiv",
            {
                // initialContentAlignment: go.Spot.Center,
                isReadOnly: true,
                layout: $(go.TreeLayout, { 
                    angle: 0,
                    nodeSpacing: 20,
                    layerSpacing: 70
                }),// 设置布局
            }
        );

        // 第二步：创建一个文本节点，内容为donner

        var node = $(go.Node, $(go.TextBlock, {
            text: "donner1",// 文字内容
            stroke: "red",
            font: '14pt sans-serif',
            background: "lightblue",
            overflow: go.TextBlock.OverflowClip /* the default value */,
            maxLines: 2,
            margin: 2,
            width: 90
        }));
        // 第三步：将节点添加到div区域中
        myDiagram.add(node)

        //2、 创建图形
        var node1 = $(go.Node,
            $(go.Shape, 
            {
                figure: "Rectangle", // Ellipse-⚪，RoundedRectangle-圆角方型。。。
                width: 40,
                height: 40,
                fill: 'green',
                stroke:'red',

            })
        );
        myDiagram.add(node1);

        //3、 图形文本结合
        var node2 = $(go.Node,
            "Vertical",//"Auto",Horizontal"-水平方向, 控制节点中元素的位置关系
            {// 最外层节点样式配置
                background: 'white',
                padding: 5
            },
            $(go.Shape, {figure: "RoundedRectangle", width: 40, height: 40}),
            $(go.TextBlock, {text: "jason"})
        );
        myDiagram.add(node2);

        // 4、Link 箭头
        var startNode = $(go.Node, "Auto",
            $(go.Shape, {figure: "Ellipse", width: 40, height: 40, fill: '#79C900', stroke: '#79C900'}),
            $(go.TextBlock, {text: '开始', stroke: 'white'})
        );
        myDiagram.add(startNode);

        // 开始节点
        var downloadNode = $(go.Node, "Auto",
            $(go.Shape, {figure: "RoundedRectangle", height: 40, fill: 'red', stroke: '#79C900'}),
            $(go.TextBlock, {text: '下载代码', stroke: 'white'})
        );
        myDiagram.add(downloadNode);
        // 箭头
        var startToDownloadLink = $(go.Link,
            {fromNode: downloadNode, toNode: startNode,},
            $(go.Shape, {strokeWidth: 1, stroke: '#333'}),
            $(go.Shape, {toArrow: "OpenTriangle", fill: null, strokeWidth: 1, stroke: '#333'})
        );
        myDiagram.add(startToDownloadLink);

        // 5、数据驱动
        // 生成一个节点模版
        myDiagram.nodeTemplate = $(go.Node, "Auto",
            $(go.Shape, {
                figure: "RoundedRectangle",
                fill: 'yellow',
                stroke: 'yellow'
            },
            new go.Binding("figure", "figure"),// shape.figure = data.figure
            new go.Binding("fill", "color"),// shape.fill = data.color
            new go.Binding("stroke", "color")),// shape.stroke = data.color
            $(go.TextBlock, {margin: 8}, new go.Binding("text", "text"))// shape.text = data.text
        );
        // 生成一个箭头模版
        myDiagram.linkTemplate = $(go.Link,
            {routing: go.Link.Orthogonal},
            $(go.Shape, {stroke: 'yellow'}, new go.Binding('stroke', 'link_color')),// shape.stroke = data.link_color
            $(go.Shape, {toArrow: "OpenTriangle", stroke: 'yellow'}, new go.Binding('stroke', 'link_color'))// shape.stroke = data.link_color
        );
        // 数据集合  以后替换ajax请求   注意使用key和parent来规定箭头的指向
        var nodeDataArray = [
            {key: "start", text: '开始', figure: 'Ellipse', color: "lightgreen"},
            {key: "download", parent: 'start', text: '下载代码', color: "lightgreen", link_text: '执行中...'},
            {key: "compile", parent: 'download', text: '本地编译', color: "lightgreen"},
            {key: "zip", parent: 'compile', text: '打包', color: "red", link_color: 'red'},
            {key: "c1", text: '服务器1', parent: "zip"},
            {key: "c11", text: '服务重启', parent: "c1"},
            {key: "c2", text: '服务器2', parent: "zip"},
            {key: "c21", text: '服务重启', parent: "c2"},
            {key: "c3", text: '服务器3', parent: "zip"},
            {key: "c31", text: '服务重启', parent: "c3"},
        ];
        myDiagram.model = new go.TreeModel(nodeDataArray);

        // 动态控制节点颜色变化   后端给一个key值 即可查找图表并修改
        setTimeout(()=> {
            var node = myDiagram.model.findNodeDataForKey("zip");
            myDiagram.model.setDataProperty(node, "color", "lightgreen");
        }, 5000)
    </script>
</body>
</html>
```

## 自定义审批流程图

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/gojsCustomdefine.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GOJS</title>
    <style>
        #myDiagramDiv {
            width:100%;
            height:500px;
            background-color: #DAE4E4;
        }
    </style>
</head>
<body>
    <!-- 划定用来渲染gojs图标的div区域，背景颜色等参数 -->
    <div id="myDiagramDiv"></div>
    <script src="../js/go-debug.js"></script>
    <!-- <script src="../js/go.js"></script> -->
    <script>
        const successColor = "rgba(88,172,48,1)",
                failColor = "rgba(232,81,16,1)",
                stayColor = "rgba(2,166,234,1)",
                noColor = "rgba(0,0,0,0)";
        // 1、开始使用
        var $ = go.GraphObject.make;
        // 第一步：创建图表，用于在页面上画图
        var myDiagram = $(
            go.Diagram,
            "myDiagramDiv",
            {
                initialContentAlignment: go.Spot.Center,
                isReadOnly: true,
    
            }
        );
        // 默认流程节点模板
        myDiagram.nodeTemplateMap.add(
            "",
            $(
                go.Node, 
                "Auto",
                { locationSpot: go.Spot.Center },
                new go.Binding("location", "location", go.Point.parse).makeTwoWay(go.Point.stringify),
                $(
                    go.Shape, 
                    "Rectangle", 
                    new go.Binding("fill", "background"),
                    new go.Binding("stroke", "borderColor"),
                ),
                $(
                    go.Panel,
                    "Table",
                    $(
                        go.TextBlock,
                        { margin: new go.Margin(30, 5, 30, 30), stroke: "#fff", font: "bold 16px sans-serif", row: 0, column: 0},
                        new go.Binding("text", "text")
                    ),
                    $(
                        go.TextBlock,
                        { margin: new go.Margin(30, 15, 30, 10), stroke: "#fff", font: "bold 30px sans-serif", row: 0, column: 1},
                        new go.Binding("text", "text2"),
                    )
                ),
                
            )
        )
        // 添加锚点模板
        myDiagram.nodeTemplateMap.add(
            "pointTemplate",
            $(
                go.Node,
                "Auto",
                { locationSpot: go.Spot.Center },
                new go.Binding("location", "location", go.Point.parse).makeTwoWay(go.Point.stringify),
                $(
                    go.Shape, 
                    "Ellipse",
                    {
                        fill: noColor,
                        stroke: noColor,
                        height: 0,
                        width: 0
                    }
                ),  
            )
        );
        // 连接线带箭头模板
        myDiagram.linkTemplate = $(
            go.Link,
            new go.Binding("fromSpot", "fromSpot", go.Spot.parse),
            new go.Binding("toSpot", "toSpot", go.Spot.parse),
            new go.Binding("points").makeTwoWay(),
            $(
                go.Shape, 
                { strokeWidth: 3 },
                new go.Binding("stroke", "lineStroke"),
            ),
            $(
                go.Shape, 
                { toArrow: "Standard", scale: 2 },
                new go.Binding("stroke", "toArrowStroke"),
                new go.Binding("fill", "toArrowFill"),
            )
        );
        // 连接线不带箭头模板
        myDiagram.linkTemplateMap.add(
            "noToArrow",
            $(
                go.Link,
                new go.Binding("fromSpot", "fromSpot", go.Spot.parse),
                new go.Binding("toSpot", "toSpot", go.Spot.parse),
                $(
                    go.Shape, 
                    { strokeWidth: 3 },
                    new go.Binding("stroke", "lineStroke"),
                ),
            )
        )
        // 审批流程
        //                     -> 运检部专责3     3-top       -> 运检部领导6
        // 班组长1 -> 分部领导2                  - 3-middle                   -8    -> 公司领导9
        //                     -> 安监部专责4     3-bottom    -> 安监部领导7
        let nodeDataArray = [
            { key: "1", text: "班组长", location: "0 0", background: successColor, borderColor: successColor, text2: "√"}, 
            { key: "2", text: "分部领导", location: "300 0", background: stayColor, borderColor: stayColor }, 
            { key: "2-top", category: "pointTemplate", location: "450 -150"  },
            { key: "2-middle", category: "pointTemplate", location: "450 0"  },
            { key: "2-buttom", category: "pointTemplate", location: "450 150"  },
            { key: "3", text: "运检部专责", location: "600 -150", background: stayColor, borderColor: stayColor },
            { key: "4", text: "安监部专责", location: "600 150", background: stayColor, borderColor: stayColor },
            { key: "3-top", category: "pointTemplate", location: "750 -150"  },
            { key: "3-middle", category: "pointTemplate", location: "750 0"  },
            { key: "3-buttom", category: "pointTemplate", location: "750 150"  },
            { key: "6", text: "运检部领导", location: "1000 -150", background: stayColor, borderColor: stayColor },
            { key: "7", text: "安监部领导", location: "1000 150", background: stayColor, borderColor: stayColor },
            { key: "6-top", category: "pointTemplate", location: "850 -150"  },
            { key: "6-middle", category: "pointTemplate", location: "850 0"  },
            { key: "6-buttom", category: "pointTemplate", location: "850 150"  },
            { key: "7-top", category: "pointTemplate", location: "1150 -150"  },
            { key: "7-middle", category: "pointTemplate", location: "1150 0"  },
            { key: "7-buttom", category: "pointTemplate", location: "1150 150"  },
            { key: "8", text: "公司领导", location: "1300 0", background: stayColor, borderColor: stayColor },
        ];
        let linkDataArray = [
            { from: "1", to: "2", fromSpot: "Right", toSpot: "Left", lineStroke: successColor, toArrowStroke:  successColor, toArrowFill: successColor },
            { from: "2", to: "2-middle", fromSpot: "Right", toSpot: "Left" , lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},

            { from: "2-top", to: "3", fromSpot: "Right", toSpot: "Left" , lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "2-buttom", to: "4", fromSpot: "Right", toSpot: "Left" , lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "2-buttom", to: "2-top" , lineStroke: stayColor, category: "noToArrow"},

            { from: "3", to: "3-top",  lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "4", to: "3-buttom", lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "3-buttom", to: "3-top" , lineStroke: stayColor, category: "noToArrow"},

            { from: "6-top", to: "6",  lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "6-buttom", to: "7", lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "6-buttom", to: "6-top" , lineStroke: stayColor, category: "noToArrow"},

            { from: "3-middle", to: "6-middle" , lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},

            { from: "6", to: "7-top",  lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "7", to: "7-buttom", lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
            { from: "7-buttom", to: "7-top" , lineStroke: stayColor, category: "noToArrow"},

            { from: "7-middle", to: "8" , lineStroke: stayColor, toArrowStroke:  stayColor, toArrowFill: stayColor},
        ];

        myDiagram.model = new go.GraphLinksModel(nodeDataArray, linkDataArray);
    </script>
</body>
</html>
```

## 常用API  操作类API

|                                          | API                                                          | 例子                                                         | 应用场景                                   |
| ---------------------------------------- | :----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------ |
| 添加节点                                 | `myDiagram.model.addNodeData(node);`                         | `var node = {};    node["key"] = "节点Key";    node["loc"] = "0 0";//节点坐标    node["text"] = "节点名称"; myDiagram.model.addNodeData(node);` | 通过按钮点击，添加新的节点到画布           |
| 删除选中节点                             | `myDiagram.commandHandler.deleteSelection();`                | `if (myDiagram.commandHandler.canDeleteSelection()) {    myDiagram.commandHandler.deleteSelection();    return; }` | 页面上有个按钮点击，可以删除选择的节点和线 |
| 获取当前画布的json                       | `myDiagram.model.toJson()`                                   | `var model= myDiagram.model.toJson();获得整个画布的json //model=eval('('+model+')');若格式异常抓一下 var nodes=model.nodeDataArray;取出所有节点 var Links=model.linkDataArray;取出所有线` | 获取当前画布的所有元素的json,用来保存      |
| 加载json刷新画布                         | `myDiagram.model = go.Model.fromJson(model);`                | `model=myDiagram.model.toJson() myDiagram.model = go.Model.fromJson(model);` | 一般用来刷新和加载画布上的元素             |
| 通过节点key获取节点                      | `myDiagram.model.findNodeDataForKey('key')`                  | `var node = myDiagram.model.findNodeDataForKey('key');`      | 知道节点key ,拿到这个节点的详细信息        |
| 更改节点属性值                           | `myDiagram.model.setDataProperty(node, 'color', "#ededed");` | `var node = myDiagram.model.findNodeDataForKey('key');//首先拿到这个节点的对象 myDiagram.model.setDataProperty(node, 'color', "#ededed");//然后对这个对象的属性进行更改` | 更改节点的颜色，或者大小等                 |
| 获取获得焦点的第一个元素，可为节点或者线 | myDiagram.selection.first()                                  | `var node=myDiagram.selection.first();        console.log(node.data.key);` |                                            |
| 获取所有获得焦点的节点                   | myDiagram.nodes                                              | `var items='';  for(var nit=myDiagram.nodes;nit.next();){        var node=nit.value;       if(node.isSelected){           items+=node.data.key+",";         }     } console.log(items);` |                                            |
| 遍历整个画布的节点信息写法1              |                                                              | `for(var nit=myDiagram.nodes;nit.next();){                    var node=nit.value;                    var key=node.data.key;                    var text=node.data.text;                }` |                                            |
| 给节点添加线                             | `myDiagram.model.addLinkData({ from: "节点的Key", to: "连到另一节点的key" })` |                                                              |                                            |
| 选中节点                                 |                                                              | `var newdata = { "text":"AAAA", "key":-33,"loc":"0 0" };            myDiagram.model.addNodeData(newdata);            var newdat2= myDiagram.model.findNodeDataForKey('-3');            console.log(newdat2);            var node = myDiagram.findNodeForData(newdat2);            console.log(node); myDiagram.select(node);//选中节点` |                                            |
| 特殊案例API用法                          | `myDiagram.findNodeForData(objNode) myDiagram.findLinkForData(objLink)` | 其中objNode或者objLink,只能从画布的json 对象取出， 不能直接手写例如 var newdata={"text":"AAAA","key":-33,"loc":"0 0"}; var node = myDiagram.findNodeForData(newdat2); 除了刚好是新建的节点外，，不然是获取不到这个对象的，因为添加节点时，gojs会自动给节点或者线添加一个属性 ![img]() |                                            |

## 常用事件定义API，和用法

| 应用场景                    | 关键字                                                       | 例子                                                         | 例子描述 |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| 节点选中改变事件            | selectionChanged: 回调的函数方法名 //该属性要写在$(go.Node,）内用大括号括起来，如右侧例子 | `myDiagram.nodeTemplate =   $(go.Node, "Horizontal",        { selectionChanged: nodeSelectionChanged },          //节点选中改变事件，nodeSelectionChanged为回调的方法名        $(go.Panel, "Auto",           $(go.Shape,//节点形状和背景颜色的设置                { fill: "#1F4963" },                 new go.Binding("fill", "color")           ),        )    );//go.Node的括号  //回调方法 function nodeSelectionChanged(node) {      if(node.isSelected) {//         //节点选中执行的内容         console.log(node.data);//节点的属性信息         console.log(node.data.key);//拿到节点的Key,拿其他属性类似         var node1 = myDiagram.model.findNodeDataForKey(node.data.key);         myDiagram.model.setDataProperty(node1, "fill", "#ededed");      } else {         //节点取消选中执行的内容         var node1 = myDiagram.model.findNodeDataForKey(node.data.key);         myDiagram.model.setDataProperty(node1, 'fill', "1F4963 ");             } }`节点选中的时候是一种颜色，取消选中是另一种颜色 |          |
| 节点双击事件                | `doubleClick : function(e, node){  //node为当前双击的节点的对象信息 //该属性要写在$(go.node,）内用大括号括起来，如右侧例子 }` | `myDiagram.nodeTemplate =   $(go.Node, "Horizontal",        $(go.Panel, "Auto",           $(go.Shape,//节点形状和背景颜色的设置                { fill: "#1F4963" },                 new go.Binding("fill", "color")           ),           {doubleClick : function(e, node){// 双击事件                             handlerDC(e, node);//双击执行的方法                          }           }        )    );//go.Node的括号  //双击执行的方法 function handlerDC(e, obj) {     var node = obj.part;//拿到节点的json对象，后面要拿什么值就直接.出来     var procTaskId = node.data.key;     var procTaskName = node.data.text;     var description = node.data.description;     var procTmplId = node.data.tmplId; }`该例子主要应用场景为，双击节点，得到节点的详细信息，弹出窗口修改节点的信息， |          |
| 从Palette拖过来节触发的事件 | `myDiagram.addDiagramListener("externalobjectsdropped", function(e) {        console.log(e);    })` | ` myDiagram.addDiagramListener("externalobjectsdropped", function(e) {        e.subject.each(function(n){            //得到从Palette拖过来的节点                 console.log(n.data.key);         });    }) ` |          |
| 当前画布被拖动的节点        | 未测试，线保存                                               | `myDiagram.addDiagramListener("Modified", function(e) {        myDiagram.isModified = false;         iter = myDiagram.selection.iterator;                while (iter.next()) {            var part = iter.value;            if (part instanceof go.Link) {               if ( ( part.data.from == 0 || part.data.from == undefined) || ( part.data.to == 0 || part.data.to == undefined )  ) {                    myDiagram.model.setDataProperty(part.data, 'link-color', 'red');                    console.log(part.data);                    console.log("非法");               } else if(part.data.from > 0 || part.data.from < 0 ) {                    var node = myDiagram.findNodeForKey(part.data.from);                    if (!node) {                        console.log("非法");                    }               }else if(part.data.to > 0 || part.data.to < 0 ) {                    var node = myDiagram.findNodeForKey(part.data.to);                    if (!node) {                        console.log("非法");                    }               }            }        }` |          |

 

## 画布基本定义类API

|                                                |                                                              |                                                              |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义gojs在全局的简洁符号（扯淡不知道怎么描述） | var $ = go.GraphObject.make;                                 |                                                              |
| 定义画布的基本属性                             | `myDiagram = $(go.Diagram, "myDiagram", //画布绑定的Div的ID            {                  initialContentAlignment: go.Spot.Center, //画布的位置设置（居中，靠左等）                  allowDrop: true, // must be true to accept drops from the Palette                  "LinkDrawn": showLinkLabel, //                  "LinkRelinked": showLinkLabel,                  "animationManager.duration": 600, //画布刷新的加载速度                  "undoManager.isEnabled": true,                  //"toolManager.mouseWheelBehavior": go.ToolManager.WheelZoom,                  allowZoom: true    //允许缩放，false为否                  isReadOnly: false //是否禁用编辑 false否，     true是  //ismodelfied:true //禁止拖拽                               });` | 没写的注释的就是忘了， 这里绑定画布所用的div,并保存在myDiagram中，后续的节点设置均使用这个变量，意思是在这个画布上定义节点和操作节点 |
|                                                |                                                              |                                                              |

 

## 画布元素属性定义类API

|                                | api                                                       | 例子                                                         |
| ------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 定义单种节点                   | myDiagram.nodeTemplate=$(go.Node,***) //***为对节点的定义 | `myDiagram.nodeTemplate =   $(go.Node, "Horizontal",        { selectionChanged: nodeSelectionChanged },          //节点选中改变事件，nodeSelectionChanged为回调的方法名        $(go.Panel, "Auto",            $(go.Shape,//节点形状和背景颜色的设置                { fill: "#1F4963", stroke: null }            ),            $(go.TextBlock,                { font: "bold 13px Helvetica, bold Arial, sans-serif",//字体                  editable：true,                  stroke: "white",//颜色                  margin: 3 },                  new go.Binding("text", "key")            )//go.TextBlock,的括号       )//go.Panel 的括号    );//go.Node的括号` |
| 定义多种节点(画布上有多种节点) | myDiagram.nodeTemplateMap.add("A种节点",$(go.Node,***))   | `//一种类型的节点 myDiagram.nodeTemplateMap.add("Start",       $(go.Node, "Spot",            $(go.Panel, "Auto",                 $(go.Shape, "Circle", {                     minSize: new go.Size(40, 40),                     fill: "#79C900",                     stroke: null                 })            )        )//go.Node的括号 ); //另一种类型的节点 myDiagram.nodeTemplateMap.add("End",       $(go.Node, "Spot",            $(go.Panel, "Auto",                 $(go.Shape, "Circle", {                     minSize: new go.Size(40, 40),                     fill: "#DC3C00",                     stroke: null                 })            )       )//go.Node的括号 );  //添加不同种类的节点      var node = {};      node["text"] = "2222";      node["key"] = "33";      node["loc"] = "0 0";      node["category"] = "Start";//category      myDiagram.model.addNodeData(node);` |
| 定义线                         | myDiagram.linkTemplate=$(go.Link,***);                    |                                                              |

## 添加监听事件

| 监听节点生成事件 | `//添加监听节点生成事件 myDiagram.addDiagramListener("externalobjectsdropped", function(e) {        e.subject.each(function(n){                console.log(n.data.key);           });    })` |
| ---------------- | ------------------------------------------------------------ |
| 监听线生成事件   | `//添加监听线生成事件    myDiagram.addDiagramListener("LinkDrawn", function(e) {           console.log(e.subject.data.to);     })` |
| 监听删除事件     | `//监听节点删除事件    myDiagram.addDiagramListener("SelectionDeleted", function(e) {            e.subject.each(function(n){                console.log(n.data.key);           });    })` |

##  

## go.Shape属性(形状)

| 圆角矩形      | RoundedRectangle                                             |      |
| ------------- | ------------------------------------------------------------ | ---- |
| 更多请查看API | [gojs.net/latest/intr…](https://link.juejin.im/?target=https%3A%2F%2Fgojs.net%2Flatest%2Fintro%2Fshapes.html) |      |

## 通用属性

| stroke      | 边框颜色               | null为无边框，可填"#87CEFA"，"red"等                         |
| ----------- | ---------------------- | ------------------------------------------------------------ |
| margin      | 边框间距               |                                                              |
| visible     | 设置是元素是否可见     | true为可见，false为不可见，                                  |
| textAlign   | 文本位置               | "center"居中                                                 |
| editable    | 文本是否可编辑         | true，false                                                  |
| font        | 字体                   | "bold 8pt Microsoft YaHei, Arial, sans-serif"                |
| fill        | 背景颜色               | 可填"#87CEFA"，"red"等                                       |
| alignment   | 元素位置设置           | go.Spot.BottomLeft/左下   go.Spot.BottomRight/右下  go.Spot.TopLeft/左上 go.Spot.TopRight/又上 alignment:go.Spot.TopRight |
| isMultiline | 编辑时是否允许换行     | 默认true                                                     |
| maxLines:1, | 设置文本显示的最大行数 |                                                              |
| minSize:    | 最小大小               | new go.Size(10, 16)，控制了最大大小后，文本就会自动换行了    |
| maxSize:    | 最大大小               |                                                              |

 

## 特殊情况解决方案

| 出现的问题           | 解决的方案                                                   |
| -------------------- | ------------------------------------------------------------ |
| 节点或者线删除不了   | 检查，画布的全局设置是否禁用删除，或者节点，和线的设置禁用删除，关键字 isReadOnly，或者节点绑定的事件有问题 |
| 重复调用定义画布报错 | 不能重复定义被绑定的div,应该用重新加载数据的API              |



## 基础教程

一、创建画布

 

1、画布就是个div

<div id="myDiagramDiv" style="width:800px; height:300px; background-color: #DAE4E4;margin-bottom: 10px;"></div>

2、初始化画布

```js
var myDiagram = new go.Diagram("myDiagramDiv");

或者

var $ = go.GraphObject.make; //GraphObject对象的静态函数GraphObject.make

var myDiagram = $(go.Diagram, "stDiagram",
{ 
    initialContentAlignment: go.Spot.Center, // 画布内居中显示
    "grid.visible": true, //画布上面是否出现网格
    allowZoom: false, //画布是否可以缩放
});

画布定义注意：
GraphObject.make为GoJS定义的静态函数，用于生成对象并赋予其类。
GraphObject.make的第一个参数必须是一个类类型。通常是一个字符串，可以设定TextBlock.text，Shape.figure，Picture.source或Panel.type。
```

 

二、定义模型

```js
var myModel = $(go.Model);
 // model中的数据每一个js对象都代表着一个相应的模型图中的元素
myModel.nodeDataArray = [
      { key: "Amos"},
      { key: "Saint"},
];
myDiagram.model = myModel; //将模型数据绑定到画布图上

 

Gojs中的数据模型分三种。

Model：最基本的（不带连线，如上面的例子）
GraphLinksModel ：连线图
TreeModel：树形图
GraphLinksModel中用model.linkDataArray为node节点连线保存数据模型信息。在连线图中，可以设定节点的连接方式。例子：

var myModel = $(go.GraphLinksModel);
    myModel.nodeDataArray = [
        { key: "Amos"},
        { key: "Saint"}
    ];
    myModel.linkDataArray = [
        { from: "Amos", to: "Saint" }
    ];
myDiagram.model = myModel;



```

三、定义node节点

```js
myDiagram.nodeTemplate =
$(go.Node, "Horizontal",
    {
          locationSpot: go.Spot.Center, // 节点内居中显示
          background: "indigo" //每个节点背景色
    },
    [$(go.TextBlock,{ ... }), $(go.Picture,{ ... }), $(go.Shape,{ ... }), $(go.Panel,{ ... })]  //分别为文本快（可编辑），图片，图形，面板（来保存其他Node的集合
);

四种节点。分别为文本快（可编辑），图片，图形，面板（来保存其他Node的集合） 。

 Shape包括Rectangle（矩形）、RoundedRectangle（圆角矩形），Ellipse（椭圆形），Triangle（三角形），Diamond（菱形），Circle（圆形）等可以直接调用的形状。
 （GoJS默认提供了很多形状然而Api写的很一般，需要自己去官方文档进行对照O.O）


$(go.Shape,
    "RoundedRectangle",
    { margin: 10, width: 50, height: 50, figure: "Club",
      stroke: "#C2185B", fill: "#F48FB1", strokeWidth: 3
    },
    new go.Binding("figure", "fig")
)

$(go.Picture,
    { margin: 10, width: 50, height: 50, background: "#44CCFF" },
    new go.Binding("source", "url")
)
    
$(go.TextBlock,
    "default text",
    { margin: 12, stroke: "white", font: "14px sans-serif" },
    new go.Binding("text", "name")
 )

通过js添加节点

myDiagram.add($(
    go.Part, 'Horizontal',
    $(go.TextBlock,
        { text: '一个文本块'}
    ),
    $(go.TextBlock,
        { text: '一个有颜色的文本块', background: '#1ad', stroke: '#FFF', width:150, height:20, textAlign: 'center', alignment: go.Spot.Center, margin:10 }
    ),
    $(go.TextBlock,
        { text: '一个可编辑文本块', background: '#FFF', width:150, height:20, alignment: go.Spot.Center, textAlign: 'center', editable: true, margin:10 }
    ),
));
```

三、定义Panel面板

```js
面板中可以包含自己的node元素。每个面板建立自己的坐标系。面板有很多种类，诸如：

Panel.Position
Panel.Vertical
Panel.Horizontal
Panel.Auto
Panel.Spot
Panel.Table
Panel.Viewbox
Panel.Link
Panel.Grid
常用的有位置面板Panel.Position，纵向面板Panel.Vertical，横向面板Panel.Horizontal，表面板Panel.Table。

 Panel.Position：每个元素的位置是由position属性指定，默认定位在（0,0）左上角。例子：

myDiagram.add($(
    go.Part,
    go.Panel.Position,
    {
        background: '#eee',
        //position: new go.Point(0, 0), //定位面板相对diagram的位置
    },
    $(go.TextBlock,
        {text: '0, 0', background: '#6bc1ff' }
    ),
    $(go.TextBlock,
        {text: '100, 100', background: '#6bc1ff', position: new go.Point(100, 100) }
    ),
    $(go.Shape,
    {width: 50,height: 50,stroke: "#3385ff",fill: '#6bc1ff', position: new go.Point(100, 0) }
    ),
));

1、Panel.Vertical：
面板的所有元素垂直从上到下排列。
Panel.Horizo??ntal：
类似纵向面板，排列不同。
垂直和水平两个面板支持Panel.defaultAlignment和Panel.defaultStretch属性，可以不必设置每个元素的对齐方式。

$(go.Panel,'Horizontal',
    {
        background: '#eee', defaultAlignment: go.Spot.Bottom
    }
)

2、Panel.Table：
表格面板中具有GraphObject.row和GraphObject.column属性，面板会按照节点的行和列确定大小。
row和column的计数从0开始。

$(go.Panel,'Table',
    {
        //position: new go.Point(0, 0),
        background: '#eee',
    },
    $(go.TextBlock,
        {text: '一行一列', row: 0, column: 0, margin: 2, background: '#6bc1ff'}
    ),

     //当设置了行或列的长宽时，可以通过设置alignment: go.Spot.Left/Right/Center来设定表格内元素对齐方式。
    $(go.RowColumnDefinition,
        { column: 0, width: 200, background: '#eee', }
    ),

    $(go.Shape,
        'RoundedRectangle',
        { stroke: "#3385ff", fill: '#6bc1ff', row: 0, column: 0, alignment: go.Spot.Left, }
    ),

     //通过columnSpan和rowSpan属性，可以合并单元格。
     $(go.TextBlock,
        {text: '顶标题', row: 0, column: 0, columnSpan: 3, 
        stretch: go.GraphObject.Horizontal, margin: 2, background: '#6bc1ff'}
    )
)
    

```

 四、数据绑定

```js
GraphObject中的各种属性都可以进行数据绑定。在第一节我们建立基本模型图时已经用了数据绑定功能。
通常，在Template（nodeTemplate和linkTemplate）中，定义go.Binding的属性，并在DataArray（nodeDataArray和linkDataArray）中设定对应值。
-双向绑定
-单向绑定
//Template中：

myDiagram2.nodeTemplate =
$(go.Node, "Auto",
  new go.Binding('location', 'loc'),// 绑定 location 值为 数据源 loc字段
  $(go.Shape,
    { figure: "RoundedRectangle", fill: "white" },
    new go.Binding('fill', 'color'),// 绑定 fill 值为 数据源 color 字段
    new go.Binding('stroke', 'stroke')),// 绑定 stroke 值为 数据源 stroke 字段
  $(go.TextBlock,
    { margin: 5 },
    new go.Binding('text', 'text')),
);
myDiagram2.linkTemplate = 
$(go.Link,
    $(go.Shape,
        new go.Binding('stroke', 'stroke'),
        new go.Binding('strokeWidth', 'thick')
    ),
    $(go.Shape,
        { toArrow: 'OpenTriangle', fill: null}
    )
)

//DataArray中：

myModel2.nodeDataArray = [
    { key: "Amos", text: 'Amos', color: '#ff7a7a', stroke: "#ff4141", loc: new go.Point(0, 0) },
    { key: "Saint", text: 'Saint', color: '#6bc1ff', stroke: "#3385ff", loc: new go.Point(100, 100)}
];
myModel2.linkDataArray = [
    { 
        from: "Amos", to: "Saint", stroke: '#333', thick: 2 }
];

//设定数据绑定时，可以通过转换函数的第三个参数 绑定构造函数。
new go.Binding('location', 'loc', go.Point.parse),
//此处，go.Point.parse为一组二维数组。

myModel2.nodeDataArray = [
    { key: "Amos", text: 'Amos', color: '#ff7a7a', stroke: "#ff4141", loc: '0, 0' },
    { key: "Saint", text: 'Saint', color: '#6bc1ff', stroke: "#3385ff", loc: '100, 100'}
];
```

五、事件触发

```js
GraphObject中的各种属性都可以进行数据绑定。在第一节我们建立基本模型图时已经用了数据绑定功能。
通常，在Template（nodeTemplate和linkTemplate）中，定义go.Binding的属性，并在DataArray（nodeDataArray和linkDataArray）中设定对应值。

Gojs具备三种基本事件：

针对图表的“基础事件”DiagramEvent。
处理鼠标键盘的“输入事件”InputEvents。
以及改变元素属性的“更改事件”ChangedEvent。
Gojs提供各种函数处理图表的交互行为。

由于上一篇研究的是Html5拖拽交互行为，此处依旧以输入事件的拖拽为例。在这里将官方Planogram的例子进行简化，并按步骤生成。
最终效果为：



 针对拖拽，Gojs提供GraphObject.mouseDragEnter, GraphObject.mouseDragLeave, GraphObject.mouseDrop 三种操作。分别对应鼠标拖拽进入，离开，与释放。
```

### gojs	去水印

```js
2.1.4
// gojs/release/gojs
搜索
var a=x.document.createElement("canvas"),b=a.getContext("2d"),c=Qa("7ca11abfd022028846");

注释这个方法后面部分
```
![image-20220530115624591](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220530115624591.png)

