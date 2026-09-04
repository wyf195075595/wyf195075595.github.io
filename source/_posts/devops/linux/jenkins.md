---
title: jenkins自动部署
date: 2023-03-14 10:23:10
updated: 2024-01-11 11:54:10
tags: jenkins
categories: linux
---

> 想试一试jenkins 自动部署得功能，此处记录一下。
>
> ubuntu+ docker + jenkins + svn

<!--more-->

## 安装jenkens

[参考文章](https://juejin.cn/post/6844903669381464071?searchId=202403141627237F4C70A53DE7E58C2879#heading-8)

[参考文章2](https://juejin.cn/post/6844904097510850573?searchId=202403141656133CCB4F7A51652F8F4CCC)

### 拉取最新镜像

```shell
docker pull jenkins/jenkins
```

### 启动jenkins

```shell
sudo docker run -d -u 0 --privileged  --name jenkins_node1 -p 49003:8080 -v /root/jenkins_node1:/var/jenkins_home jenkins/jenkins:latest
```

1. -u 0

	指的是传入root账号ID，覆盖容器中内置的账号 

2.  -v /root/jenkins_node1:/var/jenkins_home

	指的是 将docker容器内的目录**/var/jenkins_home**映射到宿主机 **/root/jenkins_node1**目录上

3. --name jenkins_node1

	将容器命名为 jenkins_node1

4. -p 49003:8080

	端口映射，将容器的8080端口映射到宿主机的49003端口

5. --privileged

	赋予最高权限

整条命令的意思

运行一个镜像为 jenkins:latest 的容器，命名为 jenkins_node1，使用 root 账号覆盖容器中的账号，赋予最高权限，将容器的 **/var/jenkins_home**映射到宿主机的 **/root/jenkins_node1**目录下，映射容器中**8080**端口到宿主机**49003**端口

执行完成后，等待几十秒，等待 jenkins 容器启动初始化。到浏览器中输入 **localhost:49003** 查看jenkins是否启动成功

### 获取密码

```shell
sudo cat /root/jenkins_node1/secrets/initialAdminPassword
```

## 安装插件

1. [Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh)

	ssh 插件用于连接服务器 进行上传代码等操作。在全局配置中设置高级选项中可以设置密码/密码文件

	![image-20240315115316393](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315115316393.png)

2. NodeJS

	配置前端 node 环境，可以新增多个版本。后续构建环境时可供选择

	![image-20240315115606148](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315115606148.png)

3. Subversion

	svn 源代码管理，在新建工程时 源码管理可配置svn.需要提供账户，密码，svn代码地址

	![image-20240315115738003](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315115738003.png)

## 新增工程

源码管理与构建环境之外，还有 常规配置。跟备注差不多

![image-20240315120117978](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315120117978.png)

### **新增构建步骤**

安装依赖，打包并压缩成压缩包

![image-20240315120232707](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315120232707.png)

### 构建后操作

将压缩包上传到指定目录后得操作，备份，解压

![image-20240315134525846](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315134525846.png)

采用日期后缀备份，不自动删除备份文件

```shell
full_filename="dist$(date +"%Y_%m_%d_%H_%M_%S")"
touch "${full_filename}"
```



### 触发器设置

如果需要 实现 svn 代码提交就触发对应任务构建。需选择远程触发，github 选择 github hook

[参考文章](https://www.cnblogs.com/huangyuanni/p/16100988.html)

![image-20240315135518419](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20240315135518419.png)

**访问路径**

```json
// JENKINS_URL 为 jenkins根路径，TOKEN 为上边输入框内容
JENKINS_URL/me/my-views/view/all/job/suzhouTest/build?token=TOKEN_NAME 
 
JENKINS_URL/me/my-views/view/all/job/suzhouTest/buildWithParameters?token=TOKEN_NAME&cause=CauseText
```

**配置 svn 钩子**

接下来应用到svn仓库的hooks/post-commit.tmpl就行了 进入svn所在服务器后台，复制一份（注意文件执行权限）

cp -rp /docker/svn/hyn/hooks/post-commit.tmpl post-commit

注释掉原有内容，加入一行：

curl -X post -v -u jenkins用户名:jenkins密码 最终url

```
curl -X post -v -u admin:3fb9d43c15bb4fa68cdfb52c44a19cbe http://192.168.101.66:8080/job/DM/build?token=suzhou-svn-hook
```

**可惜构思svn服务器没有开放权限，这个功能没有测试成功**

