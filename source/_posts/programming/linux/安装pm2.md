---
title: pm2 进程管理
date: 2022-06-17 18:23:10
tags: pm2
categories: linux
---
# pm2安装

```js
1、全局安装

 npm install pm2 -g

2、创建软连接

ln -s /node-v12/bin/pm2  /usr/local/bin/

3、查看进程

pm2 list
```

> ![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/pm2.png)
>
> [注意]创建软连接的源文件路径可以在安装时找到    如上图  /node-v12/bin/pm2

## 常用命令

<!--more-->

```
1. 启动

    # pm2 start app.js
    # pm2 start app.js --name my-api #my-api为PM2进程名称
    # pm2 start app.js -i 0 #根据CPU核数启动进程个数
    # pm2 start app.js --watch #实时监控app.js的方式启动，当app.js文件有变动时，pm2会自动reload
2. 查看进程
    # pm2 list
    # pm2 show 0 或者 # pm2 info 0 #查看进程详细信息，0为PM2进程id
3. 监控
	# pm2 monit
4. 停止
    # pm2 stop all #停止PM2列表中所有的进程
    # pm2 stop 0 #停止PM2列表中进程为0的进程
5. 重载
    # pm2 reload all #重载PM2列表中所有的进程
    # pm2 reload 0 #重载PM2列表中进程为0的进程
6. 重启
    # pm2 restart all #重启PM2列表中所有的进程
    # pm2 restart 0 #重启PM2列表中进程为0的进程
7. 删除PM2进程
    # pm2 delete 0 #删除PM2列表中进程为0的进程
    # pm2 delete all #删除PM2列表中所有的进程
8. 日志操作
	# pm2 logs [--raw] #Display all processes logs in streaming
    # pm2 flush #Empty all log file
    # pm2 reloadLogs #Reload all logs
9. 升级PM2
    # npm install pm2@lastest -g #安装最新的PM2版本
    # pm2 updatePM2 #升级pm2
10. 更多命令参数请查看帮助
    # pm2 --help


安装：

    npm install pm2 -g //安装pm2
    pm2 update // 更新pm2
    pm2 uninstall pm2 //移除pm2
开启关闭：

    pm2 start server.js //启动server.js进程
    pm2 start server.js -i 4 //启动4个server.js进程
    pm2 restart server.js //重启server.js进程
    pm2 stop all // 停止所有进程
    pm2 stop server.js //停止server.js进程
    pm2 stop 0 //停止编号为0的进程
配置启动信息:

//创建app.json，内容如下
{
  "apps" : [{
    "script"    : "server.js",  //进程名
    "instances" : "max",   //开启进程数，可为数值，也可为max。与服务器cpu核数相关
    "exec_mode" : "cluster" // 可选：fork(服务器单核推荐) cluster(多核推荐)
  }]
}

pm2 start app.json
查看：


    pm2 list //查看当前正在运行的进程
    pm2 show 0 //查看执行编号为0的进程
实时监控:

    pm2 monit //监控当前所有的进程
    pm2 monit 0 //监控批评行编号为0的进程
    pm2 monit server.js //监控名称为server.js的进程
日志：

pm2 logs //显示所有日志
pm2 logs 0 //显示执行编号为0的日志
pm2 logs server.js //显示名称为server.js的进程
pm2 flush  //清洗所有的数据[注：我没有试出来效果]


```


# pm2常用命令记录

```js
$ pm2 start app.js              // 启动app.js应用程序

$ pm2 start app.js -i 4     // cluster mode 模式启动4个app.js的应用实例

// 4个应用程序会自动进行负载均衡

$ pm2 start app.js --name="api" // 启动应用程序并命名为 "api"

$ pm2 start app.js --watch   // 当文件变化时自动重启应用

$ pm2 start script.sh      // 启动 bash 脚本

$ pm2 list           // 列表 PM2 启动的所有的应用程序

$ pm2 monit           // 显示每个应用程序的CPU和内存占用情况

$ pm2 show [app-name]      // 显示应用程序的所有信息

$ pm2 logs           // 显示所有应用程序的日志

$ pm2 logs [app-name]      // 显示指定应用程序的日志

$ pm2 flush            // 清空所有日志文件

$ pm2 stop all         // 停止所有的应用程序

$ pm2 stop 0          // 停止 id为 0的指定应用程序

$ pm2 restart all        // 重启所有应用

$ pm2 reload all        // 重启 cluster mode下的所有应用

$ pm2 gracefulReload all    // Graceful reload all apps in cluster mode

$ pm2 delete all        // 关闭并删除所有应用

$ pm2 delete 0         // 删除指定应用 id 0

$ pm2 scale api 10       // 把名字叫api的应用扩展到10个实例

$ pm2 reset [app-name]     // 重置重启数量

$ pm2 startup          // 创建开机自启动命令

$ pm2 save           // 保存当前应用列表

$ pm2 resurrect         // 重新加载保存的应用列表

$ pm2 update          // Save processes, kill PM2 and restore processes

$ pm2 generate         // Generate a sample json configuration file
```

> pm2文档地址：http://pm2.keymetrics.io/docs/usage/quick-start/

```
$ npm install pm2 -g     # 命令行安装 pm2 
$ pm2 start app.js -i 4  # 后台运行pm2，启动4个app.js 
                         # 也可以把'max' 参数传递给 start
                         # 正确的进程数目依赖于Cpu的核心数目
$ pm2 start app.js --name my-api # 命名进程
$ pm2 list               # 显示所有进程状态
$ pm2 monit              # 监视所有进程
$ pm2 logs               # 显示所有进程日志
$ pm2 stop all           # 停止所有进程
$ pm2 restart all        # 重启所有进程
$ pm2 reload all         # 0 秒停机重载进程 (用于 NETWORKED 进程)
$ pm2 stop 0             # 停止指定的进程
$ pm2 restart 0          # 重启指定的进程
$ pm2 startup            # 产生 init 脚本 保持进程活着
$ pm2 web                # 运行健壮的 computer API endpoint (http://localhost:9615)
$ pm2 delete 0           # 杀死指定的进程
$ pm2 delete all         # 杀死全部进程

```

### pm2 参数说明

参数说明： 
–watch：监听应用目录的变化，一旦发生变化，自动重启。如果要精确监听、不见听的目录，最好通过配置文件。

-i –instances：启用多少个实例，可用于负载均衡。如果-i 0或者-i max，则根据当前机器核数确定实例数目。

–ignore-watch：排除监听的目录/文件，可以是特定的文件名，也可以是正则。比如–ignore-watch=”test node_modules “some scripts”“

-n –name：应用的名称。查看应用信息的时候可以用到。

-o –output ：标准输出日志文件的路径。

-e –error ：错误输出日志文件的路径。

–interpreter ：the interpreter pm2 should use for executing app (bash, python…)。比如你用的coffee script来编写应用。 