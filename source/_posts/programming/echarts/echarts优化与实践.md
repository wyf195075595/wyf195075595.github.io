---
title: echarts 优化与实践
date: 2022-06-17 08:23:10
tags: echarts
categories: echarts
---



## series-graph

> 用于展现节点以及节点之间的关系数据。

本次示例展示关系图采用力引导布局方式，可以不用设置每个节点得位置（x,y）让他们自动生成。效果图如下【球中文字脱敏不显示】

![image-20220817152404686](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20220817152404686.png)

配置项如下：

<!--more-->

```js
/**
 * 实体关系图 echart 配置
 * @returns 
 */
 export const getEntityEchartOption = (graphOption = {}) => {
    let nodes = graphOption.nodes || [],
        categories = graphOption.categories || [],
        links = graphOption.links || [],
        text = graphOption.text;
    function findNameById(id) {
        let {name} = nodes.find(item=> item.id == id)||{name: ''};
        return name
    }
    const option = {
        color: ["#4DFB37", "#FF4500", "#FF8C00", "#FFD700", "#00BFFF", "#1cd389", "#668eff", "#ffc751", "#ff6e73", "#8683e6", 
        "#9692ff", "#EA7CCC", "#FF6347", "#D2691E", "#FFFF00", "#7CFC00", "#FF00FF", "#9400D3", "#FF1493", "#00FFFF", "#00FF7F",
        "#FFE4C4", "#FF69B4"],
        tooltip: {
            backgroundColor: "rgba(50,50,50,0.7)",
            textStyle: {
                color: "#fff"
            },
            formatter: function(params) {
                let data = params.data;
                if(data.source) {
                    let {source, target} = data;
                    let from = findNameById(source);
                    let to = findNameById(target);
                    return `${from} > ${to}`
                }
                return `
                    ${params.seriesName}<br/>
                    ${params.marker} ${params.name}<br/>
                    ${data.content?data.content: ''}
                `
            }
        },
        title: {
            text: text,
            show: true,
            textStyle: {
               color: 'white'
            },
            left: "center",
            top: 10,
            z: 3
        },
        toolbox: {
            feature: {
                saveAsImage: {
                    type: "svg",
                    title: "保存为 svg",
                    color: '#fff'
                }
            },
            right: 8,
            top: 8
        },
        grid: {
            containLabel: true,
            left: 15,
            right: 15,
            top: 30,
            bottom: 15,
        },  
        legend: [
            {
                data: categories.map(function (a) {
                    return a.name;
                }),
                orient: "vertical",
                left: 15,
                top: 10,
                textStyle: {
                    color: '#fff'
                }
            }
        ],
        series: [
        {
            name: '实体关系图',
            type: 'graph',
            // layout: 'none',
            layout: 'force',
            data: nodes,
            links: links,
            categories: categories,
            emphasis: {
                focus: "none",
                // focus: "adjacency",
                itemStyle: {
                    borderColor: "rgba(106, 198, 255)",
                    borderWidth: 1,
                    shadowColor: 'rgba(106, 198, 255, 0.7)',
                    shadowBlur: 20,
                }
            },
            force: {
                gravity: 0,
                repulsion: 120,
                edgeLength: [120, 200]
            },
            draggable: true,
            roam: true,
            symbol: 'circle',
            symbolSize: 65,
            edgeSymbol: ['', 'arrow'],
            edgeSymbolSize: [4, 12],
            label: {
                show: true,
                color: '#fff' 
            },
            edgeLabel: {
                show: true,
                color: '#fff',
                formatter: function(params) {
                    return params.data.name
                }
            },
            scaleLimit: {
                min: 0.4,
                max: 5
            },
            lineStyle: {
                color: 'source',
                curveness: 0.3
            }
        }
        ]
    }

    return option
}
```

### ehcarts 内置渐变色

```javascript

new echarts.graphic.LinearGradient(a,b,c,d,arr)

```

a ,b,c,d为0，1
a:1 arr中的颜色右到左
c:1 arr中的颜色左到右
b:1 arr中的颜色下到上
d:1 arr中的颜色上到下