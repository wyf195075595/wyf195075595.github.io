---
title: mongodb 基础知识
date: 2022-06-17 18:23:10
tags: mongodb
categories: mongodb
---
# mongodb

<!--more-->

## 1、权限设置

> 【安装完成后设置】
>
> 创建用户
>
> ```js
> //
> user 用户名
> pwd  密码
> role 用户类型
> db   在哪个数据库设置权限
> 
> Built-In Roles（内置角色）：
>     1. 数据库用户角色：read、readWrite;
>     2. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
>     3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
>     4. 备份恢复角色：backup、restore；
>     5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
>     6. 超级用户角色：root  
>     // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
>     7. 内部角色：__system
> 
> 具体角色的功能： 
> 	Read：允许用户读取指定数据库
>     readWrite：允许用户读写指定数据库
>     dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
>     userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
>     clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
>     readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
>     readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
>     userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
>     dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
>     root：只在admin数据库中可用。超级账号，超级权限
> //
>     
> //创建超级管理员
> db.createUser(
>     { 
>         user: "admin"
>         , pwd: "admin"
>         , roles: [
>             { 
>                 role: "root"
>                 , db: "admin" 
>             }
>         ] 
>     }
> )
> //创建单个数据库用户
> db.createUser(
>     { 
>         user: "blogadmin"
>         , pwd: "123456"
>         , roles: [
>             { 
>                 role: "dbAdmin"
>                 , db: "blog" 
>             }
>         ] 
>     }
> )
> ```
>
> 修改配置文件 开启数据库验证
>
> 找到mongodb安装路径  eg: C:\Program Files\MongoDB\Server\4.0\bin       下的mongod.cfg文件
>
> 修改  
>
> #security:
>
> 为 ： 
>
> security:
>    authorization: enabled
>
> 最后重启
>
> 【注意】如果修改遇到拒绝访问，复制内容新建txt修改完替换文件名即可，修改内容不支持tab，链接数据库时需要账户密码
>
> const url = 'mongodb://admin:123456@localhost:27017/test?authSource=admin'
>
> 常用用户操作命令
>
> ```js
> //查看当前库下用户
> use dbName
> show users
> //删除用户
> db.dropUser('username')
> //修改用户密码
> db.updateUser('admin',{pwd:'password'})
> //密码认证
> db.auth('admin','password')
> ```
>
> 

## 2、聚合管道查询

| 参数名   | 描述                           | 参数                                                         |
| -------- | ------------------------------ | ------------------------------------------------------------ |
| $project | 控制值得显示、重命名、加减乘除 | $project : { <br />_id: 0    1显示 0隐藏<br />,sort : { "$multipy": [ "$sort",2 ] }    将sort字段乘以2输出<br />, positionName : "$name"} }   将positionName重命名name<br />加法（$add）、减法（$subtract）、乘法（$multipy）、除法（$divide） |
| $macth   | 筛选符合条件得值               | $match: { user_id: { $in: array } }   *查找array里包含的user_id的数据* |
| $group   | 给数据分组计算                 | $group : {    _id : "$item",   total: { $sum : "$price"},   avg: { $avg: "$quantity" }    }<br />求price的和 与 quantity平均数，放到total,avg字段显示 |
| $sort    | 排序                           | $sort: { created_at: -1 }       根据created_at字段降序   1升序 |
| $limit   | 限制条数                       | 分页用的                                                     |
| $skip    | 跳过条数                       | 分页用的                                                     |
| $lookup  | 表与表关联查询                 | $lookup: {      <br />     from: 'bs',  *// 要查询的表（带s是因为mongodb表自动加s，这里的名字必须是真正的表名）*     <br />    localField: 'b_id',*// 根据什么字段，这里是a里的b_id*      <br />    foreignField: '_id',*// 要匹配的字段，找b里面_id=b_id的数据*     <br />    as: 'b_data'  }       *// 放入的字段* |



```js
1、$project	控制值得显示、重命名、加减乘除 
 $project : { 
    _id: 0   // 1显示 0隐藏
    ,sort : { "$multipy": [ "$sort",2 ] }  //  将sort字段乘以2输出
    , positionName : "$name"} }  // 将positionName重命名name<br />
    //加法（$add）、减法（$subtract）、乘法（$multipy）、除法（$divide）
    
2、$macth	筛选符合条件得值
 $match: { user_id: { $in: array } }  //*查找array里包含的user_id的数据*

3、$group	给数据分组计算
 $group : {   
     _id : "$item", 
     total: { $sum : "$price"},   
     avg: { $avg: "$quantity" }    }
	//求price的和 与 quantity平均数，放到total,avg字段显示

4、$sort	排序
 $sort: { created_at: -1 }       
 //根据created_at字段降序   1升序

5、$limit	限制条数 分页用的
 
6、$skip	跳过条数 分页用的
 
7、$lookup	表与表关联查询
 $lookup: {    
    from: 'bs',  *// 要查询的表（带s是因为mongodb表自动加s，这里的名字必须是真正的表名）*     
    localField: 'b_id',*// 根据什么字段，这里是a里的b_id*      
    foreignField: '_id',*// 要匹配的字段，找b里面_id=b_id的数据*  
 }
8、递归查询
//数据库
{ "_id" : 1, "name" : "Dev" }
{ "_id" : 2, "name" : "Eliot", "reportsTo" : "Dev" }
{ "_id" : 3, "name" : "Ron", "reportsTo" : "Eliot" }
{ "_id" : 4, "name" : "Andrew", "reportsTo" : "Eliot" }
{ "_id" : 5, "name" : "Asya", "reportsTo" : "Ron" }
{ "_id" : 6, "name" : "Dan", "reportsTo" : "Andrew" }
     
 db.employees.aggregate([
     {$match: { name: 'Dan' }}
     ,{$graphLookup:{
         from: "employees",
         startWith:"$reportsTo",
         connectFromField:"reportsTo",
         connectToField:"name",
         as:"报告层级"
     }}
 ])
     
 //查询结果
 { 
     "_id" : 6, 
      "name" : "Dan", 
       "reportsTo" : "Andrew", 
        "报告层级" : [ 
            { "_id" : 1, "name" : "Dev" }, 
            { "_id" : 2, "name" : "Eliot", "reportsTo" : "Dev" }, 
            { "_id" : 4, "name" : "Andrew", "reportsTo" : "Eliot" } 
        ] 
 }
     
//联表查询
//在user_authorities表中找到uId为‘cfaeb4e7-73ac-4e93-b248-37410af0c093’的数据集（有多个）
// {uId:'xx',aId:'xx'},{uId:'xx',aId:'xx'},{uId:'xx',aId:'xx'}
//，在把多个uId相同的数据分组，将aid聚合 {_id:'cfaeb4e7-73ac-4e93-b248-37410af0c093',aid:[xx,xx,xx]}
//再根据aid数组里的值查询authorities表中对应的数据放在qxs集合中
db.user_authorities.aggregate([
    {$match: {uId:'cfaeb4e7-73ac-4e93-b248-37410af0c093'}},
    {$group: {_id:'$uId', aId: {$push:'$aId'} } },
    {$lookup:{
        from:"authorities",
        localField:"aId",
        foreignField:"id",
        as:"qxs"} //设置为aId可覆盖原数据
        
    }
    ])
     
  //结果
     {
         _id:'cfaeb4e7-73ac-4e93-b248-37410af0c093',
         aid:['xx','xx','xx'],
         qxs:[
             {xxx},
             {xxx}
         ]
     }
```

