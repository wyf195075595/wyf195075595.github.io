---
title: echarts 实用技巧
date: 2022-06-17 08:23:10
tags: 实用技巧
categories: echarts
---
echarts.图标案例

#### layui + jq 项目中

<!--more-->

```js
// 普通折线图
drawInfraredLineChart() {
    let echartObj = null;
    return (HTMLDivElement, config = {}) => {
        let htmlDom = document.querySelector(HTMLDivElement);
        if(!htmlDom) return
        if(echartObj) echartObj.dispose();
        let xAxis = config.xAxis || [],
            solid_data = config.solid_data || [],
            dashed_data = config.dashed_data || [],
            loadCurrent_data = config.loadCurrent_data || [],
            eventCallback = config.eventCallback;
        let option = {
            color: ["#ffcb00","#15faff","#FF00FF","#00FF7F","#FF4500","#00FFFF","#FF00FF"],
            tooltip: {
                trigger: 'axis',    
                backgroundColor: 'rgba(0,0,0,.8)',
                formatter: params => {
                    let str = ''
                    params.forEach((item, index) => {
                        if(index == 2) {
                            str += `${item.marker} ${item.seriesName} ${item.value}A`

                        } else {
                            str += `${item.marker} ${item.seriesName} ${item.value}℃<br/>`
                        }
                    })
                    return  str

                }
            },
            legend: {
                textStyle: {
                    fontSize: 12,
                    color: '#fff',
                },
                top: '2%',
                right: 'center',
            },
            grid: {
                top: "13%",
                left: '3%',
                right: '3%',
                bottom: '10%',
                containLabel: true
            },
            axisPointer: {
                lineStyle: {
                    color: "#15faff",
                }
            },
            xAxis: {
                type: 'category',
                boundaryGap: true,
                axisLine: {
                    show: true,
                    lineStyle: {
                        color: '#15faff',
                    },

                },
                axisTick: {
                    show: false
                },
                axisLabel: {
                    show: true,
                    color: '#fff',
                    textStyle: {
                        fontSize: 12
                    },
                },
                data: xAxis,
            },
            yAxis: {
                type: 'value',
                axisLabel: {
                    // formatter: '{value}%',
                    color: '#fff',
                    textStyle: {
                        fontSize: 12
                    },
                },
                axisLine: {
                    show: true,
                    lineStyle: {
                        color: '#15faff',
                    },

                },
                axisTick: {
                    show: false
                },
                splitLine: {
                    lineStyle: {
                        color: 'rgba(56, 224, 243, 0.25)',
                        type: 'dashed',
                    }
                }
            },
            series: [
                {
                    name: "预测的发热",
                    type: 'line',
                    symbol: 'emptyCircle',
                    symbolSize: 8,
                    lineStyle: {
                        width: 1,
                        type: "dashed",
                    },
                    data: dashed_data,
                },
                {
                    name: "实际发热",
                    type: 'line',
                    symbol: 'emptyCircle',
                    symbolSize: 8,
                    lineStyle: {
                        width: 1,
                        type: "solid",
                    },
                    data: solid_data,
                },
                {
                    name: "负荷电流",
                    type: 'line',
                    symbol: 'emptyCircle',
                    symbolSize: 8,
                    lineStyle: {
                        width: 1,
                        type: "solid",
                    },
                    data: loadCurrent_data,
                }
            ],
        }

        echartObj = echarts.init(htmlDom);
        echartObj.setOption(option)

        $(window).on("resize", function() {
            echartObj.resize()
        });
        echartObj.on("click", params => {
            eventCallback && eventCallback(params);
        })
    }
}
```

#### Vue项目中

```html
// 普通折线图

<template>
    <div class="page_item_slide margin_top_10px margin_left_10px">
        <div class="page_main_title">
            <span>负责人本周生产力预测</span>
        </div>
        <div class="page_main_context">
            <div id="lineEchartExample" class="page_main_echart" ref="productivityPredictionLine"></div>
        </div>
    </div>
</template>

<script >
    import { productivityForecastThisWeekLine } from '***/line.js'
	export default {
        data(){
            return {
                lineData: {
                    xAxis: ["周一","周二","周三","周四","周五","周六","周日"],
                    solid_data: [0,0,0,0,0,0,0],
                    dashed_data: [0,0,0,0,0,0,0],
                },
            }
        },
        methods: {
          // 折线图 
            drawPoductivityPredictionLine: function (config) {

                let htmlDom = this.$refs.productivityPredictionLine;
                if(!htmlDom) return ;
                if(this.echartsLine) {
                    this.echartsLine.dispose();
                }
                let options =  productivityForecastThisWeekLine(config)
                this.echartsLine = this.$echarts.init(htmlDom);
                this.echartsLine.setOption(options)

                this.$bus.$on('window-resize', ()=> {
                    this.echartsLine && this.echartsLine.resize()
                })
            },
            getCylindricalChartData: async function () {
                let rs = await api.getProductivityForecastThisWeekLineData({groupId: this.classId});
                if(rs.connected && rs.data) {
                    this.lineData.solid_data = [],
                    this.lineData.dashed_data = [];
                    rs.data[0].forEach((valueItem, valueIndex) => {
                         this.lineData.dashed_data.push({
                            value: valueItem,
                            peopleCount: rs.data1[0][valueIndex],
                        })
                         this.lineData.solid_data.push({
                            value: rs.data[1][valueIndex],
                            peopleCount: rs.data1[1][valueIndex]
                        });
                    })
                }
            },
        }
    }
    
</script>


line.js

<script>
/**
 * @description xxx
 * @param {object} config 
 * @returns 
 */
 export const productivityForecastThisWeekLine = (config = {}) => {

    let xAxis = config.xAxis || [],
        solid_data = config.solid_data || [],
        dashed_data = config.dashed_data || [];
    let option = {
        color: ["#ffcb00","#15faff","#FF00FF","#00FF7F","#FF4500","#00FFFF","#FF00FF"],
        tooltip: {
            textStyle: {
                fontSize: 12,
                color: "#fff"
            },
            backgroundColor: "rgba(50,50,50,0.7)",
            borderColor: "#333",
            trigger: 'axis',
            formatter: params => {
                let showStr = '';
                params.forEach && params.forEach((showItem, showIndex) => {
                    if(showIndex == 0){
                        showStr += `${showItem.name} <br />`;
                    }
                    showStr += `${showItem.marker}${showItem.seriesName}：${showItem?.data?.value}%，${showItem?.data?.peopleCount}人`;
                    showStr += showIndex == 0 ? "<br />" : "";
                })
                return showStr
            }
        },
        legend: {
            textStyle: {
                fontSize: 12,
                color: '#fff',
            },
            top: '2%',
            right: 'center',
        },
        grid: {
            top: "13%",
            left: '3%',
            right: '3%',
            bottom: '10%',
            containLabel: true
        },
        axisPointer: {
            lineStyle: {
                color: "#15faff",
            }
        },
        xAxis: {
            type: 'category',
            boundaryGap: true,
            axisLine: {
                show: true,
                lineStyle: {
                    color: '#15faff',
                },

            },
            axisTick: {
                show: false
            },
            axisLabel: {
                show: true,
                color: '#fff',
                // textStyle: {
                    fontSize: 12
                // },
            },
            data: xAxis,
        },
        yAxis: {
            type: 'value',
            axisLabel: {
                formatter: '{value}%',
                color: '#fff',
                // textStyle: {
                    fontSize: 12
                // },
            },
            axisLine: {
                show: true,
                lineStyle: {
                    color: '#15faff',
                },

            },
            axisTick: {
                show: false
            },
            splitLine: {
                lineStyle: {
                    color: 'rgba(56, 224, 243, 0.25)',
                    type: 'dashed',
                }
            }
        },
        series: [
            {
                name: "实际",
                type: 'line',
                symbol: 'emptyCircle',
                symbolSize: 8,
                lineStyle: {
                    width: 1,
                    type: "solid",
                },
                data: solid_data,
            },
            {
                name: "计划",
                type: 'line',
                symbol: 'emptyCircle',
                symbolSize: 8,
                lineStyle: {
                    width: 1,
                    type: "dashed",
                },
                data: dashed_data,
            }
        ],
    }

    return option
}
</script>



// 组件形式

<!-- 
    调用参数模板
    <project-bar :data="data"></project-bar>
    data: {
        width: '100%',
        height: '100%',
        id: 'bar04',
        title: '项目关闭',
        seriesData: [
            {
                data: [
                    {name:'应关闭',value:0, key: 'gbZs'},
                    {name:'实际关闭',value:0, key: 'sjgbZs'},
                    {name:'未按计划关闭',value:0, key: 'wjhgbZs'}
                ]
            }
        ],
    }
-->
<template>
	<div class="projectBar">
        <div :style="{width: data.width, height: data.height}" class="echartsBox" id="data.id"></div>
    </div>
</template>

<script >
    export default {
        name: 'projectBar',
        props: {
            data: {
                type: Object,
                required: true
            }
        },
        mounted() {
            this.initEchart(this.data)
        },
        watch: {
            data: {
                handler: function(val) {
                    this.initEchart(val)
                },
                immediate: true
            }
        },
        methods: {
            initEchart1(data) {
                if(data.seriesData.data.length) {
                    
                }
            },
            initEchart(data) {
                if(!data) {
                    data = this.data
                }
                let echarts = this.$echarts;
                let htmlDom = document.getElementById(data.id);
                if(!htmlDom) return
                // if(this.myChart) {
                //     this.myChart.clear();
                // }
                let instance = echarts.getInstanceByDom(htmlDom)

                if(!instance) {
                    this.myChart = echarts.init(htmlDom)
                } else {
                    this.myChart = instance
                }
                
                let option = {
                    // color: ['rgb(50 206 253)', 'rgb(199 195 26)', 'rgb(197 63 63)'],
                    title: {
                        text: 　data.title || '项目管理',
                        textStyle: {
                            color: 'white'
                        },
                        left: 'center',
                        top: '5%'
                    },
                    tooltip: {
                        transitionDuration: 0,
                        backgroundColor: "rgba(50,50,50,0.7)",
                        borderColor: "#333",
                        textStyle: {
                            color: "#fff"
                        },
                    },
                    grid: {
                        top: 0,
                        left: 0,
                        right: 0,
                        bottom: 0
                    },
                    legend: {
                        show: true,
                        right: '5%',
                        top: '5%',
                        orient: 'vertical',
                        textStyle: {
                            color: 'white',
                        }
                    },
                    series: []
                }
                if(data.options) {
                    option = echarts.util.merge(option, data.options)
                }
                let defaultSeries = {
                    type: 'pie',
                    center: ['50%', '65%'],
                    radius: '50%',
                    z: 10,
                    // minAngle: 45,
                    labelLine: {
                        show: true,
                    },
                    itemStyle: {
                        borderWidth: 1,
                        color : function(params) {
                            var colorList = ['rgb(50 206 253)', 'rgb(199 195 26)', 'rgb(197 63 63)'];      
                            var newcolorList = ['rgb(50 206 253)', 'rgb(199 195 26)', 'rgb(197 63 63)'];
                            return new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                                offset: 0,
                                color: colorList[params.dataIndex]
                            },
                            {
                                offset: 1,
                                color: newcolorList[params.dataIndex]
                            }])
                        }
                        // color : function(params) {
                        //     var colorList = ['rgb(50 206 253)', 'rgb(199 195 26)', 'rgb(197 63 63)'];      
                        //     return colorList[params.dataIndex]
                        // }

                    },
                    label: {
                        show: true,
                        fontSize: 12,
                        align: 'left',
                        color: '#fff',
                        position: 'outside',
                        formatter: (params) => {
                            return `${params.data.name} : ${params.data.value}`
                        }
                    },
                    data: []
                }
                data.seriesData.forEach((item, i)=> {
                    option.series.push({...defaultSeries, ...item}) 
                })

                this.myChart.setOption(option)
            }
        }
    }
</script>

// 调用
 <project-pie :data="barData1"></project-pie>
```

#### 业务流程图

![](D:\Typora\workSpace\resource\lct.png)

```js
//具体的绘制流程图的方法
let option = {
    animationDurationUpdate: 1500,
    animationEasingUpdate: 'quinticInOut',
    textStyle: {
        color: '#fff'
    },
    series: [{
        type: 'graph',
        layout: 'none',
        symbol: 'rect',
        symbolSize: 10,
        roam: true,
        //focusNodeAdjacency:true,
        label: {
            normal: {
                show: true,
                position: 'inside',
                textStyle: {
                    fontSize: 16,
                    color: '#fff',
                    fontWeight: 'BOLD',
                },
            }
        },
        itemStyle: {
            color: "rgba(232,81,16,1)"
        },
        edgeSymbol: ['none', 'arrow'],
        edgeSymbolSize: [8, 16],
        edgeLabel: {
            normal: {
                textStyle: {
                    fontSize: 18
                }
            }
        },
        //所有节点的位置都是根据自己设置的x, y坐标来设置的
        data: coordData,
        //这是点与点之间的连接关系
        links: linksData,
        //线条的颜色
        lineStyle: {
            show: true,
            color: '#02A6EA',
            width: 2,
            opacity: 1,
        },
        emphasis: {
            lineStyle: {
                color: '#364645',
                width: 3
            }
        },
    }]
};
kaiGuanEchart = echarts.init(kaiGuanDom);
kaiGuanEchart.setOption(option)
```



### echarts区域缩放如何手动触发还原（back）事件

```js
// 找到属性名
let tmp = ''
	Object.keys(this.chart._componentsMap).map((item) => {
    if (item.indexOf('series') !== -1 && item.indexOf('_toolbox') !== -1) {
        tmp = item
    }
	})
// 手动触发 back 事件
if (this.chart._componentsMap[tmp]._features) {
	this.chart._componentsMap[tmp]._features.dataZoom.onclick(null, null, 'back')
}
```

