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

```html
<script>
 function testPlan() {
    let kaiGuanEchart = null
    return (HTMLDivElement, flowsheetId) => {
        let kaiGuanDom = document.querySelector(HTMLDivElement)
        if (!kaiGuanDom) return

        if (kaiGuanEchart) kaiGuanEchart.dispose()

        let labelName = new Map([
                ["1", "站长"],
                ["1A", "站长-top"],
                ["1B", "站长-bottom"],
                ["2", "分部领导"],
                ["2A", "分部领导-top"],
                ["3", "分部运检专责"],
                ["3A", "分部运检专责-bottom"],
                ["4", "运检部运维专责"],
                ["4A", "运检部运维专责-top"],
                ["5", "安监部运维专责"],
                ["5A", "安监部运维专责-bottom"],
                ["6", "公司领导"],
                ["6A", "公司领导-top"],
                // 辅助线的点
                ['7', 'linePoint1'],
                ['8', 'linePoint2'],
                ['9', 'linePoint3'],
                ['10', 'linePoint4'],
                ['11', 'linePoint5'],
                ['12', 'linePoint6'],
                ['13', 'linePoint7'],
                ['14', 'linePoint8'],
                ['15', 'linePoint9'],
                ['16', 'linePoint10'],
                ['17', 'linePoint11'],
                ['18', 'linePoint12'],
            ])
            /**
             * @description successColor(流程通过) failColor(流程未通过) stayColor(流程未审核)
             *  rgba(88,172,48,1) 绿 rgba(232,81,16,1) 红 rgba(2,166,234,1) 蓝
             */
        const successColor = "rgba(88,172,48,1)",
            failColor = "rgba(232,81,16,1)",
            stayColor = "rgba(2,166,234,1)";

        const gsld_color = Symbol("公司领导"),
            ajbywzz_color = Symbol("安监部运维专责"),
            yjbywzz_color = Symbol("运检部运维专责"),
            fbyjzzr_color = Symbol("分部运检专责"),
            fbld_color = Symbol("分部领导"),
            zz_color = Symbol("站长");

        // 初始化节点颜色为蓝
        let itemStyleColor = {
            [gsld_color]: stayColor,
            [ajbywzz_color]: stayColor,
            [yjbywzz_color]: stayColor,
            [fbyjzzr_color]: stayColor,
            [fbld_color]: stayColor,
            [zz_color]: stayColor
        }

        // 流程退回
        let flag = [];
        /**
         * @description 流程图大小 不通过线条颜色  为了应对经常多变的需求
         */
        const symbolSize = [145, 145],
            failLineColor = "red"

        let datas = [
            { "auditNodeIndex": 1, "auditStatus": 1, "auditRoleName": "站长" },
            { "auditNodeIndex": 1, "auditStatus": 1, "auditRoleName": "分部领导" },
            { "auditNodeIndex": 2, "auditStatus": 1, "auditRoleName": "分部运检专责" },
            { "auditNodeIndex": 2, "auditStatus": 0, "auditRoleName": "运检部运维专责" },
            { "auditNodeIndex": 3, "auditStatus": 2, "auditRoleName": "安监部运维专责" },
            { "auditNodeIndex": 3, "auditStatus": 2, "auditRoleName": "公司领导" },
        ]
        new Promise((resolve, reject) => {
            resolve(datas)
                // $.ajax({
                //     url: mainWorkTextApi.getFlowsheetList,
                //     dataType: 'json',
                //     data: {
                //         id: flowsheetId
                //     },
                //     type: 'post',
                //     success: (flowsheetList) => {
                //         resolve(flowsheetList.data)
                //     },
                //     error: (error) => {
                //         resolve([])
                //     }
                // })
        }).then((flowsheetData) => {
            // 只要有 flowId 说明站长已经发送 即通过
            // itemStyleColor[zz_color] = successColor

            flowsheetData.forEach((flowsheetItem) => {
                if (['站长'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[zz_color] = failColor
                            break;
                        case 1:
                            itemStyleColor[zz_color] = successColor
                            break
                        case 2:
                            itemStyleColor[zz_color] = stayColor
                            break
                        default:
                            break;
                    }
                }
                if (['分部领导'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[fbld_color] = failColor
                            flag.push('分部领导')
                            break;
                        case 1:
                            itemStyleColor[fbld_color] = successColor
                            break
                        case 2:
                            itemStyleColor[fbld_color] = stayColor
                            break
                        default:
                            break;
                    }
                }
                if (['分部运检专责'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[fbyjzzr_color] = failColor
                            flag.push('分部运检专责')
                            break;
                        case 1:
                            itemStyleColor[fbyjzzr_color] = successColor
                            break
                        case 2:
                            itemStyleColor[fbyjzzr_color] = stayColor
                            break
                        default:
                            break;
                    }
                }
                if (['安监部运维专责'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[ajbywzz_color] = failColor
                            flag.push('安监部运维专责')
                            break;
                        case 1:
                            itemStyleColor[ajbywzz_color] = successColor
                            break
                        case 2:
                            itemStyleColor[ajbywzz_color] = stayColor
                            break
                        default:
                            break;
                    }
                }
                if (['运检部运维专责'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[yjbywzz_color] = failColor
                            flag.push('运检部运维专责')
                            break;
                        case 1:
                            itemStyleColor[yjbywzz_color] = successColor
                            break
                        case 2:
                            itemStyleColor[yjbywzz_color] = stayColor
                            break
                        default:
                            break;
                    }
                }

                if (['公司领导', '作业文本公司领导'].includes(flowsheetItem.auditRoleName)) {
                    // 2 待审核 1 审核通过 0 审核未通过
                    switch (flowsheetItem.auditStatus) {
                        case 0:
                            itemStyleColor[gsld_color] = failColor
                            flag.push('公司领导')
                            break;
                        case 1:
                            itemStyleColor[gsld_color] = successColor
                            break
                        case 2:
                            itemStyleColor[gsld_color] = stayColor
                            break
                        default:
                            break;
                    }
                }
            })

            // 是否展示 √
            let zzLabelFormatter = itemStyleColor[zz_color] == successColor ? `${labelName.get("1")}  {a|}` : `${labelName.get("1")}`, // 站长
                fbldLabelFormatter = itemStyleColor[fbld_color] == successColor ? `${labelName.get("2")}  {a|}` : `${labelName.get("2")}`, // 分部领导
                fbyjzzLabelFormatter = itemStyleColor[fbyjzzr_color] == successColor ? `${labelName.get("3")}  {a|}` : `${labelName.get("3")}`, // 分部运检专责
                yjbywzzLabelFormatter = itemStyleColor[yjbywzz_color] == successColor ? `${labelName.get("4")}  {a|}` : `${labelName.get("4")}`, // 运检部运维专责
                ajbywzzLabelFormatter = itemStyleColor[ajbywzz_color] == successColor ? `${labelName.get("5")}  {a|}` : `${labelName.get("5")}`, // 安监部运维专责
                gsldLabelFormatter = itemStyleColor[gsld_color] == successColor ? `${labelName.get("6")}  {a|}` : `${labelName.get("6")}`; // 公司领导
            // 点
            let coordData = [{
                        // 站长
                        name: labelName.get("1"),
                        x: 0,
                        y: 600,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[zz_color]
                        },
                        label: {
                            show: true,
                            formatter: zzLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 站长上
                        name: labelName.get("1A"),
                        x: 0,
                        y: 0,
                        symbolSize: 0,
                    },
                    {
                        // 站长下
                        name: labelName.get("1B"),
                        x: 0,
                        y: 1200,
                        symbolSize: 0,
                    },
                    {
                        // 分部领导
                        name: labelName.get("2"),
                        x: 600,
                        y: 300,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[fbld_color]
                        },
                        label: {
                            show: true,
                            formatter: fbldLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 分部领导上
                        name: labelName.get("2A"),
                        x: 600,
                        y: 0,
                        symbolSize: 0,
                    },
                    {
                        // 分部运检专责
                        name: labelName.get("3"),
                        x: 600,
                        y: 900,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[fbyjzzr_color]
                        },
                        label: {
                            show: true,
                            formatter: fbyjzzLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 分部运检专责下
                        name: labelName.get("3A"),
                        x: 600,
                        y: 1200,
                        symbolSize: 0,
                    },
                    {
                        // 运检部运维专责
                        name: labelName.get("4"),
                        x: 1200,
                        y: 300,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[yjbywzz_color]
                        },
                        label: {
                            show: true,
                            formatter: yjbywzzLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 运检部运维专责上
                        name: labelName.get("4A"),
                        x: 1200,
                        y: 0,
                        symbolSize: 0,
                    },
                    {
                        // 安监部运维专责
                        name: labelName.get("5"),
                        x: 1200,
                        y: 900,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[ajbywzz_color]
                        },
                        label: {
                            show: true,
                            formatter: ajbywzzLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 运检部运维专责下
                        name: labelName.get("5A"),
                        x: 1200,
                        y: 1200,
                        symbolSize: 0,
                    },

                    {
                        // 公司领导
                        name: labelName.get("6"),
                        x: 1800,
                        y: 600,
                        symbolSize: symbolSize,
                        itemStyle: {
                            color: itemStyleColor[gsld_color]
                        },
                        label: {
                            show: true,
                            formatter: gsldLabelFormatter,
                            rich: {
                                a: {
                                    backgroundColor: {
                                        image: '../../images/workTextAssignment/success.png'
                                    },
                                    width: 26,
                                    height: 26
                                }
                            }
                        }
                    },
                    {
                        // 公司领导上
                        name: labelName.get("6A"),
                        x: 1800,
                        y: 0,
                        symbolSize: 0,
                    },

                    // 连线之间需要的点
                    {
                        name: labelName.get("7"),
                        x: 300,
                        y: 300,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("8"),
                        x: 300,
                        y: 600,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("9"),
                        x: 300,
                        y: 900,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("10"),
                        x: 850,
                        y: 300,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("11"),
                        x: 850,
                        y: 600,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("12"),
                        x: 850,
                        y: 900,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("13"),
                        x: 950,
                        y: 300,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("14"),
                        x: 950,
                        y: 600,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("15"),
                        x: 950,
                        y: 900,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("16"),
                        x: 1500,
                        y: 300,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("17"),
                        x: 1500,
                        y: 600,
                        symbolSize: 0,
                    },
                    {
                        name: labelName.get("18"),
                        x: 1500,
                        y: 900,
                        symbolSize: 0,
                    },

                    {
                        name: "max",
                        x: 1800,
                        y: 1200,
                        symbolSize: 0,
                    },
                    {
                        name: "min",
                        x: 0,
                        y: 0,
                        symbolSize: 0,
                    }
                ]
                // 线
            let linksData = [{
                    source: labelName.get("1"),
                    target: labelName.get("8"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("7"),
                    target: labelName.get("9"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("7"),
                    target: labelName.get("2"),
                },
                {
                    source: labelName.get("9"),
                    target: labelName.get("3"),
                },

                {
                    source: labelName.get("2"),
                    target: labelName.get("10"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("3"),
                    target: labelName.get("12"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("10"),
                    target: labelName.get("12"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("11"),
                    target: labelName.get("14"),
                },
                {
                    source: labelName.get("13"),
                    target: labelName.get("15"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("13"),
                    target: labelName.get("4"),
                },
                {
                    source: labelName.get("15"),
                    target: labelName.get("5"),
                },
                {
                    source: labelName.get("4"),
                    target: labelName.get("16"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("5"),
                    target: labelName.get("18"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("16"),
                    target: labelName.get("18"),
                    symbol: 'none'
                },
                {
                    source: labelName.get("17"),
                    target: labelName.get("6"),
                },
            ]
            if (flag.includes("分部领导")) {
                linksData.push({
                    source: labelName.get("2"),
                    target: labelName.get("2A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("2A"),
                    target: labelName.get("1A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("1A"),
                    target: labelName.get("1"),
                    lineStyle: {
                        color: failLineColor
                    }
                })
            }

            if (flag.includes("分部运检专责")) {
                linksData.push({
                    source: labelName.get("3"),
                    target: labelName.get("3A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("3A"),
                    target: labelName.get("1B"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("1B"),
                    target: labelName.get("1"),
                    lineStyle: {
                        color: failLineColor
                    }
                })
            }

            if (flag.includes("运检部运维专责")) {
                linksData.push({
                    source: labelName.get("4"),
                    target: labelName.get("4A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("4A"),
                    target: labelName.get("1A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("1A"),
                    target: labelName.get("1"),
                    lineStyle: {
                        color: failLineColor
                    }
                }, )
            }

            if (flag.includes("公司领导")) {

                linksData.push({
                    source: labelName.get("6"),
                    target: labelName.get("6A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("6A"),
                    target: labelName.get("1A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("1A"),
                    target: labelName.get("1"),
                    lineStyle: {
                        color: failLineColor
                    }
                }, )
            }

            if (flag.includes("安监部运维专责")) {
                linksData.push({
                    source: labelName.get("5"),
                    target: labelName.get("5A"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("5A"),
                    target: labelName.get("1B"),
                    symbol: 'none',
                    lineStyle: {
                        color: failLineColor
                    }
                }, {
                    source: labelName.get("1B"),
                    target: labelName.get("1"),
                    lineStyle: {
                        color: failLineColor
                    }
                }, )
            }
            //具体的绘制流程图的方法
            let option = {
                tooltip: {
                    trigger: 'item',
                    formatter: function(para) {
                        let canShowName = ["分部领导", "分部运检专责", "运检部运维专责", "安监部运维专责", "站长", "运检部专责", "安监部专责", "运检部副主任", "安监部副主任", "公司领导",
                            "运检部变电检修专责", "运检部领导", "安监部大一次专责", "安监部领导", "公司领导", "检部继电保护专责", "安监部大二次专责", "运检部变压器专责",
                            "运检部四小器专责", "班组长"
                        ]
                        if (canShowName.includes(para.name)) {
                            return para.name
                        } else {
                            return ''
                        }
                    },
                    backgroundColor: "rgba(13,129,246,.9)"
                },
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

            $(window).off("resize").on("resize", function() {
                kaiGuanEchart.resize()
            })
        }).catch((err) => { console.log(err); })

    }
}


    var test = testPlan()

    detailBox("关联方案", `<div style="height:100%;width:100%;box-sizing: border-box;padding: 0 1.8%;">
            <div id="detailTableContain" style="height:100%;width:100%;"></div>
        </div>`,
        function(index, layero){
            test("#detailTableContain",'', true)

        },
        function(index, layero){
            layer.close(index)
        },
    '12',
    '7.5',
    'my-skin')
</script>
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

