---
title: git 配置
date: 2022-06-17 18:23:10
tags: git
categories: git
---
### git 密码验证不通过问题

> 私有仓库 账户密码身份验证在2021-08-13日已取消，换成令牌的认证方式

```
git remote set-url origin  https://<your_token>@github.com/<USERNAME>/<REPO>.git

git clone https://<TOKEN>@github.com/<user_name>/<repo_name>.git
```



## git 验证优化

```js
// clone 项目后每次拉去提交代码 需要输入密码验证

// 执行后 输入一次账户密码就可以不用在输入了
git config credential.helper store

// 后边 加 -glob 可以全局设置所有仓库，不加默认当前仓库生效
```

<!--more-->

## git 撤销提交

#### 一、撤销提交

一种常见的场景是，提交代码以后，你突然意识到这个提交有问题，应该撤销掉，这时执行下面的命令就可以了。

> ```bash
> $ git revert HEAD
> ```

上面命令的原理是，在当前提交后面，新增一次提交，抵消掉上一次提交导致的所有变化。它不会改变过去的历史，所以是首选方式，没有任何丢失代码的风险。

`git revert` 命令只能抵消上一个提交，如果想抵消多个提交，必须在命令行依次指定这些提交。比如，抵消前两个提交，要像下面这样写。

> ```bash
> $ git revert [倒数第一个提交] [倒数第二个提交]
> ```

`git revert`命令还有两个参数。

> - `--no-edit`：执行时不打开默认编辑器，直接使用 Git 自动生成的提交信息。
> - `--no-commit`：只抵消暂存区和工作区的文件变化，不产生新的提交。

#### 二、丢弃提交

如果希望以前的提交在历史中彻底消失，而不是被抵消掉，可以使用`git reset`命令，丢弃掉某个提交之后的所有提交。

> ```bash
> $ git reset [last good SHA]
> ```

`git reset`的原理是，让最新提交的指针回到以前某个时点，该时点之后的提交都从历史中消失。

默认情况下，`git reset`不改变工作区的文件（但会改变暂存区），`--hard`参数可以让工作区里面的文件也回到以前的状态。

> ```bash
> $ git reset --hard [last good SHA]
> ```

执行`git reset`命令之后，如果想找回那些丢弃掉的提交，可以使用`git reflog`命令，具体做法参考[这里](https://github.blog/2015-06-08-how-to-undo-almost-anything-with-git/#redo-after-undo-local)。不过，这种做法有时效性，时间长了可能找不回来。

#### 三、替换上一次提交

提交以后，发现提交信息写错了，这时可以使用`git commit`命令的`--amend`参数，可以修改上一次的提交信息。

> ```bash
> $ git commit --amend -m "Fixes bug #42"
> ```

它的原理是产生一个新的提交对象，替换掉上一次提交产生的提交对象。

这时如果暂存区有发生变化的文件，会一起提交到仓库。所以，`--amend`不仅可以修改提交信息，还可以整个把上一次提交替换掉。

#### 四、撤销工作区的文件修改

如果工作区的某个文件被改乱了，但还没有提交，可以用`git checkout`命令找回本次修改之前的文件。

> ```bash
> $ git checkout -- [filename]
> ```

它的原理是先找暂存区，如果该文件有暂存的版本，则恢复该版本，否则恢复上一次提交的版本。

注意，工作区的文件变化一旦被撤销，就无法找回了。

#### 五、从暂存区撤销文件

如果不小心把一个文件添加到暂存区，可以用下面的命令撤销。

> ```bash
> $ git rm --cached [filename]
> ```

上面的命令不影响已经提交的内容。

#### 六、撤销当前分支的变化

你在当前分支上做了几次提交，突然发现放错了分支，这几个提交本应该放到另一个分支。

> ```bash
> # 新建一个 feature 分支，指向当前最新的提交
> # 注意，这时依然停留在当前分支
> $ git branch feature
> 
> # 切换到这几次提交之前的状态
> $ git reset --hard [当前分支此前的最后一次提交]
> 
> # 切换到 feature 分支
> $ git checkout feature
> ```

上面的操作等于是撤销当前分支的变化，将这些变化放到一个新建的分支。

### github-action

```
前端自动化部署到 GitHub
```

## 四、实例：React 项目发布到 GitHub Pages

下面是一个实例，通过 GitHub Actions 构建一个 React 项目，并发布到 GitHub Pages。最终代码都在[这个仓库](https://github.com/ruanyf/github-actions-demo)里面，发布后的参考网址为[ruanyf.github.io/github-actions-demo](https://ruanyf.github.io/github-actions-demo)。

第一步，GitHub Actions 目前还处在测试阶段，需要[申请](https://github.com/features/actions/signup/)测试资格。申请以后，可能需要几天才能通过。据说，2019年11月就会放开。

获得资格后，仓库顶部的菜单会出现`Actions`一项。

![img](https://www.wangbase.com/blogimg/asset/201909/bg2019091106.jpg)

第二步，这个示例需要将构建成果发到 GitHub 仓库，因此需要 GitHub 密钥。按照[文档](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)，生成一个密钥。然后，将这个密钥储存到当前仓库的`Settings/Secrets`里面。

![img](https://www.wangbase.com/blogimg/asset/201909/bg2019091107.jpg)

上图是储存秘密的环境变量的地方。环境变量的名字可以随便起，这里用的是`ACCESS_TOKEN`。如果你不用这个名字，后面脚本里的变量名也要跟着改。

第三步，本地计算机使用[`create-react-app`](https://github.com/facebook/create-react-app)，生成一个标准的 React 应用。

> ```bash
> $ npx create-react-app github-actions-demo
> $ cd github-actions-demo
> ```

然后，打开`package.json`文件，加一个`homepage`字段，表示该应用发布后的根目录（参见[文档](https://create-react-app.dev/docs/deployment#building-for-relative-paths)）。

> ```javascript
> "homepage": "https://[username].github.io/github-actions-demo",
> ```

上面代码中，将`[username]`替换成你的 GitHub 用户名，参见[范例](https://github.com/ruanyf/github-actions-demo/blob/master/package.json#L10)。

第四步，在这个仓库的`.github/workflows`目录，生成一个 workflow 文件，名字可以随便取，这个示例是`ci.yml`。

我们选用一个别人已经写好的 action：[JamesIves/github-pages-deploy-action](https://github.com/marketplace/actions/deploy-to-github-pages)，它提供了 workflow 的范例文件，直接拷贝过来就行了（[源码](https://github.com/ruanyf/github-actions-demo/blob/master/.github/workflows/ci.yml)）。

> ```javascript
> name: Build and Deploy
> title: Test title
> description:  Test description
> on: [push]
> jobs:
>   build-and-deploy:
>     concurrency: ci-${{ github.ref }} 
>     runs-on: ubuntu-latest
>     steps:
>       - name: Checkout
>         uses: actions/checkout@v2
> 
>       - name: Install and Build 🔧 
>         run: |
>           npm ci
>           npm run build
> 
>       - name: Deploy
>         uses: JamesIves/github-pages-deploy-action@v4.2.3
>         with:
>           branch: gh-pages 
>           folder: build
> ```

上面这个 workflow 文件的要点如下。

> 1. 整个流程在`master`分支发生`push`事件时触发。
> 2. 只有一个`job`，运行在虚拟机环境`ubuntu-latest`。
> 3. 第一步是获取源码，使用的 action 是`actions/checkout`。
> 4. 第二步是构建和部署，使用的 action 是`JamesIves/github-pages-deploy-action`。
> 5. 第二步需要四个环境变量，分别为 GitHub 密钥、发布分支、构建成果所在目录、构建脚本。其中，只有 GitHub 密钥是秘密变量，需要写在双括号里面，其他三个都可以直接写在文件里。

第五步，保存上面的文件后，将整个仓库推送到 GitHub。

GitHub 发现了 workflow 文件以后，就会自动运行。你可以在网站上实时查看[运行日志](https://github.com/ruanyf/github-actions-demo/commit/24fbf6a875351297f31434fd44bc3146accf9e59/checks)，日志默认保存30天。

最后页面可能还是打不开：There isn't a GitHub Pages site here. 404

​	再仓库界面，点击settings -> pages 选择一个主题，后面就可以了



## github、gitee 生成设置 ssh key

```js
1、github 

创建一个 SSH key 

$ ssh-keygen -t rsa -C "your_email@example.com"
代码参数含义：

-t 指定密钥类型，默认是 rsa ，可以省略。
-C 设置注释文字，比如邮箱。
-f 指定密钥文件存储文件名。或者到C:\Users\19507\.ssh 目录下运行就可以不指定目录

以上代码省略了 -f 参数，因此，运行上面那条命令后会让你输入一个文件名，用于保存刚才生成的 SSH key 代码

接着：你也可以不输入文件名，使用默认文件名（推荐），那么就会生成 id_rsa 和 id_rsa.pub 两个秘钥文件。

再然后：提示你输入两次密码（该密码是你push文件的时候要输入的密码，而不是github管理者的密码），
当然，你也可以不输入密码，直接按回车。那么push的时候就不需要输入密码，直接提交到github上了

最后：
	拷贝 id_rsa.pub 文件的内容，去GitHub 上 设置 ssh key
    
测试： ssh -T git@github.com

2、gitee

ssh-keygen -t ed25519 -C "xxxxx@xxxxx.com"  
文件名
密码

测试：ssh -T git@gitee.com
```

### git push 一直弹出输入用户名密码提示，或者报错 问题

```
原因：
clone的时候用的是https链接

在项目目录.git/config文件

修改原地址： 
[remote "origin"]
	url = https://github.com/xxx.git

为
[remote "origin"]
	url = git@github.com:xxx.git

```

## git多账号管理

```js
在 .ssh 文件夹下新建 config 文件，内容如下

# gitee
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_ed25519
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_id

提交代码时为避免全局 global 影响，要设置 local
```



## [搭建一个最简单的服务器](https://www.ruanyifeng.com/blog/2022/10/git-server.html)

[参考文章](https://www.gibbard.me/using_git_offline/)

熟悉 Git 的同学可能知道，Git 默认支持两种传输协议：SSH 和 HTTP/HTTPS。

服务器一般都自带 SSH，这意味着，**我们可以什么都不安装，只通过 SSH 就把仓库推到远程服务器。**

所以，一条命令就够了。我们只要在远程服务器上，建立同名的 Git 仓库，服务器就搭建好了。

> ```bash
> $ git init --bare [仓库名].git
> ```

上面命令中，各个部分的含义如下。

（1）`git init`：初始化一个 Git 仓库。

（2）`--bare`：表示新仓库不需要工作目录，只建立 Git 数据目录。

（3）`[仓库名].git`：指定仓库名，比如仓库名是`example`，那么就要建立一个叫做`example.git`的 Git 数据目录。

执行这条命令以后，一个最简易的 Git 服务器就诞生了。后面，我们就可以通过 SSH 连接，把本地代码推送到这个远程 Git 仓库了。

### 远程服务器操作

下面的操作都在远程服务器完成，假设你已经通过 SSH 登录上去了。不熟悉 SSH 的同学可以看参考这篇[《SSH 入门》](https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)。

登录远程服务器的目的，主要是新建一个专门的用户，所有的 Git 操作都通过该用户完成。这一步其实不是必需的，但是这样后期操作比较灵活（比如仓库可以让多人共享）。

> ```bash
> $ sudo mkdir /home/git
> $ sudo useradd git
> $ sudo mkdir -m 700 /home/git/.ssh
> $ sudo cp ~/.ssh/authorized_keys /home/git/.ssh/
> ```

上面命令的含义如下。

（1）新建新用户的主目录`/home/git`。

（2）新建一个用户，用户名为`git`。

（3）新建新用户的 SSH 目录`/home/git/.ssh`。

（4）把当前用户的公钥拷贝给`git`用户，以便密钥登陆，详细解释可以参考[《SSH 密钥登录》](https://wangdoc.com/ssh/key.html)。

如果你只用密码登录，不使用密钥登录，那么上面第三步和第四步是不需要的，但是需要为`git`用户设定密码，命令如下。

> ```bash
> $ sudo passwd git
> ```

### 4.2 本机计算机操作

后面的操作都在本地计算机完成。

假定上一小节的远程服务器的 IP 地址是`192.168.1.25`，本地的 Git 仓库名为`example`。

> ```bash
> $ ssh git@192.168.1.25 git init --bare example.git
> ```

上面命令中，`ssh git@192.168.1.25`表示以`git`用户的身份，登录到远程服务器。后面的部分是 SSH 的一种语法，表示登录后在远程服务器执行的命令，即新建一个远程 Git 数据目录`example.git`。

这条命令运行完，就有了一个 Git 服务器了，然后就可以推送代码了。

> ```bash
> $ cd example
> $ git remote add myServer git@192.168.1.25:example.git
> $ git push myServer master
> ```

上面的命令先进入本地仓库，为远程服务器加一个别名，然后把代码推送过去。



## 遇到的错误

### hexo deploy 提交代码 报错

>  fatal: unable to access 'https://github.com/jgraph/drawio-desktop.git/': OpenSSL SSL_read: Connection was reset, errno 10054
>
> 翻译成中文`打开SSL SSL_read：连接已重置，错误 10054`。
>
> 这样解释可能也比较模糊，通俗点说服务器的[SSL证书](https://so.csdn.net/so/search?q=SSL证书&spm=1001.2101.3001.7020)灭有经过第三方机构的签署。但也有人说可能是网络不稳定，连接超时导致。
>
> 解决：
>
> git config --global http.sslVerify "false"
>
> git config --global https.sslVerify "false"
>
> 

### 本地仓库ssh提交失败

> 报错：
>
> fatal: Could not read from remote repository.

原因：客户端与服务端的ssh key不匹配

重写生成ssh key.

1. 先删除 .ssh 目录下之前得  id_rsa以及id_rsa.pub这两个文件

2. 生成新的

	```shell
	ssh-keygen -t rsa -C "youremail@example.com"
	```

3. 将SSH key 添加到 ssh-agent

	```shell
	# id_rsa 以生成得文件名为主
	ssh-add ~/.ssh/id_rsa
	
	#如果出现“Could not open a connection to your authentication agent.”的错误可以使用以下两种方式解决：
	eval "$(ssh-agent -s)"
	eval `ssh-agent`
	
	# 然后再次执行指令。
	ssh-add ~/.ssh/id_rsa 
	```

4. 去GitHub 添加 sshKey

5. 测试

	```shell
	ssh -T git@github.com
	```

	

### SSH连接时出现Host key verification failed

> 用OpenSSH的人都知ssh会把你每个你访问过计算机的公钥(public key)都记录在~/.ssh/known_hosts。当下次访问相同计算机时，OpenSSH会核对公钥。如果公钥不同，OpenSSH会发出警告，避免你受到DNS Hijack之类的攻击。
>
> SSH对主机的public_key的检查等级是根据StrictHostKeyChecking变量来配置的。默认情况下，StrictHostKeyChecking=ask。

简单说下它的三种配置值：

- StrictHostKeyChecking=no
	最不安全的级别，当然也没有那么多烦人的提示了，相对安全的内网时建议使用。如果连接server的key在本地不存在，那么就自动添加到文件中（默认是known_hosts），并且给出一个警告。

- StrictHostKeyChecking=ask
	默认的级别，就是出现刚才的提示了。如果连接和key不匹配，给出提示，并拒绝登录。

- StrictHostKeyChecking=yes
	最安全的级别，如果连接与key不匹配，就拒绝连接，不会提示详细信息。

解决：

删除~/.ssh/known_hosts文件

### Failed to connect to github.com port 443 : Timed out

**问题分析：** git 所设端口与系统代理不一致，需重新设置。

解决：

1. **打开 设置>网络与Internet>代理** 找到代理地址 如： **127.0.0.1:7890**

2. **修改git的网络设置**

	```shell
	git config --global http.proxy http://127.0.0.1:7890 
	git config --global https.proxy http://127.0.0.1:7890
	```

	



