---
title: 常用快捷键
date: 2022-06-17 08:23:10
tags: vscode
categories: js
---

#### 编辑器与窗口管理

Ctrl+Shift+P: 打开命令面板。

Ctrl+Shift+N: 新建窗口。

Ctrl+Shift+W: 关闭窗口。

切分窗口：Ctrl+1/Ctrl+3/Ctrl+3

Ctrl+H：最小化窗口

Ctrl+B：显示/隐藏侧边栏

Ctrl+"+/-"：放大/缩小界面

#### 文件操作

<!--more-->

Ctrl+N：新建文件

Ctrl+W：关闭文件

Ctrl+Tab：文件切换

#### 格式调整

Ctrl+C/Ctrl+V：复制或剪切当前行/当前选中内容

Alt+Up/Down：向上/下移动一行

Shift+Alt+Up//Down：向上/下复制一行

Ctrl+Delete：删除当前行

Shift+Alt+Left/Right：从光标开始向左/右选择内容

#### 代码编辑

Ctrl+D：选中下一个相同内容

Ctrl+Shift+L：选中所有相同内容

Ctrl+F：查找内容

Ctrl+Shit+F：在整个文件夹中查找内容

### 常用设置

我们可以在settings.json中手动进行一些设置，让我们的编辑器更好用。

#### 关闭标签介绍信息

我们在编写代码的时候鼠标移动到某个标签上，经常会自动弹出一些介绍信息，挡住部分代码，给我们的阅读带来了很大的困难，一直没有找到关闭它的方法，目前可以通过设置时间延迟暂时实现这个效果，我设置的5000毫秒，你可以设置的更大一些，基本上它就不会弹出来了。

```
"editor.hover.delay": 5000
```

![图片](https://mmbiz.qpic.cn/mmbiz/1NOXMW586uuPicba2bejQib2WhzviciaYuW9mRmbBcWWW4b0YfyGrBejKvMHBLJmPdnmChOOjaTELnRMrHjFHeTkaw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image

#### 自动折行

设置代码根据编辑器窗口大小自动折行

```
"editor.wordWrap": "on"
```

![图片](https://mmbiz.qpic.cn/mmbiz/1NOXMW586uuPicba2bejQib2WhzviciaYuW9Y9XJQiaTrFVu6aHZgEjcSzkZxyEv1SHXFtru0nVa1Fibl62KNsiakvsDQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)image

#### 字体设置

```js
   // 一款适合代码显示的字体包（需要将字体包下载到本地）
      "editor.fontFamily": "Source Code Pro, 'Source Code Pro'",
      // 设置代码字体大小
      "editor.fontSize": 15,
```

#### 自动保存

目前有四个选项：

- off：关闭自动保存。
- afterDelay：当文件修改后的时间超过"Files：Auto Save Delay"中配置的值时自动进行保存。
- onFocusChange：编辑器失去焦点时自动保存更新后的文件。
- onWindowChange：窗口失去焦点时自动保存更新后的文件。

```js
"files.autoSave": "off"
```

#### 关闭代码提示

```js
"editor.quickSuggestions": { "other": false, "comments": false, "strings": false }
```