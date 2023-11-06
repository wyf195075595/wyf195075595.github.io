## 实时分析

### 分析任务管理

#### 列表查询：get

```
/system/analysisConfig/list
```

参数

```js
pageNum: 1 // 页码
pageSize: 10 // 每页条数
taskName: taskName // 任务名称
cameraId: 204 // 相机ID
algorithmCategory: 2 // 算法分类
algCode: 0101  // 算法编码
status: 2  // 任务状态
```

输出

```json
{
    "total": 1,
    "rows": [
        {
            "createBy": "",
            "createTime": null,
            "updateBy": "",
            "updateTime": null,
            "remark": null,
            "id": "c640444a3fbd11ee997220040f33fd14",
            "taskId": "c640444a3fbd11ee997220040ff3fd14",
            "taskName": "测试任务",
            "cameraId": "f2fa1dbf952540d5aeb4470c050dfb2b",
            "cameraName": "营业厅摄像机1",
            "algorithmCategory": "1",
            "algCode": "0101",
            "currentStatus": 0,
            "startTime": "2023-12-01",
            "endTime": "2023-09-07",
            "delFlag": "0",
            "status": 1
        }
    ],
    "code": 200,
    "msg": "查询成功"
}
```

#### 列表新增: post

```
/system/analysisConfig
```

参数

```js
algCode: "0101"  // 算法编码
algorithmCategory: "1"  // 算法分类
cameraId: "f2fa1dbf952540d5aeb4470c050dfb2b"  // 相机id
createBy: null
createTime: null
delFlag: null
endTime: "2023-10-31 00:00:00"  // 结束时间
id: null
startTime: "2023-10-19 00:00:00" // 开始时间
taskId: null
taskName: "test1"  // 任务名称
updateBy: null
updateTime: null
```

#### 列表删除：delete

```
/system/analysisConfig
```

参数：

```
id: xxxx
```

#### 启动/暂停：get

```
/system/analysisConfig/taskControl
```

参数：

```js
id: c640444a3fbd11ee997220040f33fd14 // 任务控制id
actionType: 1  // 任务状态 0：停止；1：启动
```

#### 导出：post

```
system/analysisConfig/export
```

参数：同查询



### 实时告警

#### 左侧树：get

```
/system/hallInfo/hallTree
```

无参

#### 列表查询：get

```
/system/analysisResult/listTodayResult
```

参数：

```js
pageNum: 1  // 页码
pageSize: 100  // 每页条数
hallName: 1 // 营业厅名称
hallIds: cdab4ad3a3f649828d198bf412846146 // 营业厅id
hallLevel: A // 营业厅等级
status: 0  // 告警状态 0未评价 1异常 2误报 3重报
algorithmCategory: 1  // 算法分类 2仪容仪表 1服务行为 5安全提醒 4风险防控 3窗口管理
```

返回值：

```
{
    hallName
    hallLevel
    hallAddress
    analyseResults
    analyseTime
    status
}
```



#### 异常/误报/重报: get

```
/system/analysisResult/updateStatus
```

参数：

```js
ids: b18d70ab3fbd11ee997220040ff3fd16  // id
status: 1 // 1"异常", 2"误报", 3"重报"
```





### 分析结果

#### 左侧树：get

```
/system/hallInfo/hallTree
```

无参

#### 列表查询：get

```
/system/analysisResult/listResult
```

参数：

```js
pageNum: 1 // 页码
pageSize: 10 // 每页条数
hallName: 明山区营业厅 // 营业厅名称
algorithmCategory: 1  // 算法分类
startDate: 2023-08-01  // 开始时间
endDate: 2023-08-31  // 结束时间
```

返回值

```
{
    "total": 2,
    "rows": [
        {
            "id": "b18d70ab3fbd11ee997220040ff3fd16",
            "hallId": "89a66da27fc843398b1c818ebf884bd4",
            "hallName": "明山区营业厅",
            "hallAddress": "辽宁省本溪市明山区水塔路与西芬街交汇南侧正北方向50米",
            "hallLevel": "B",
            "cameraName": "营业厅摄像机1",
            "cameraAliasname": "摄像机1",
            "algorithmCategory": "1",
            "algCode": "0102",
            "analyseResults": null,
            "analyseTime": "2023-08-31 09:14:48",
            "status": 1,
            "rawImageUrl": null
        },
        
    ],
    "code": 200,
    "msg": "查询成功"
}
```

#### 异常/误报/重报: get

```
/system/analysisResult/updateStatus
```

参数：

```js
ids: b18d70ab3fbd11ee997220040ff3fd16  // id
status: 1 // 1"异常", 2"误报", 3"重报"
```

#### 导出：post

```
system/analysisResult/exportResult
```

参数：同查询