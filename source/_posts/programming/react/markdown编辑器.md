---
title:  markdown 编辑器
date: 2022-06-17 08:23:10
tags: markdown
categories: react
---
## 1、安装依赖

```js
npm i markdown-it -S
npm i react-markdown-editor-lite -S
npm i highlight.js -S

```

<!--more-->

## 2、引入依赖

```js
//markdown语法转译器
import MarkdownIt from 'markdown-it'
//markdown编辑器
import MdEditor from 'react-markdown-editor-lite'
// markdowm样式
import 'react-markdown-editor-lite/lib/index.css';
// 代码块高亮js
import hljs from 'highlight.js'; 
// 代码块高亮css
import "highlight.js/styles/atom-one-dark.css";
//引入插件
import FileUpload from '../.../FileUpload'
```

## 3、使用

```js
//注册插件
MdEditor.use(FileUpload);
// markdownIt语法编译器
const mdParser = new MarkdownIt({
    //配置代码块高亮语法
    highlight: function (str, lang) {
      if (lang && hljs.getLanguage(lang)) {
        try {
          return hljs.highlight(lang, str).value;
        } catch (err) {}
      }
      return ''; // 使用额外的默认转义
    }
});

function Edit(){
    const [md,setMd] = useState('');
    const handleEditorChange = ({html, md})=>{
        setMd(md)
    }
    const submitData = ()=>{
        if (mdEditor.current) {
            //获取markdown原生代码
            let markdown = mdEditor.current.getMdValue();
            //获取转译后的html
            let parsehtml = mdEditor.current.getHtmlValue();
        }
    }
    return(
        <>
    	 <MdEditor
            ref={mdEditor}
            value={md}
            //本地图片上传base64展示
            onImageUpload={(file)=>{
                //返回图片base64位
                 return new Promise((resolve,reject)=>{
                     const reader = new FileReader();
                     reader.readAsDataURL(file);
                     reader.onload = () => resolve(reader.result);
                     reader.onerror = error => reject(error);
                 })
             }}
            style={{ height: "600px" }}
            //将markdown转html
            renderHTML={(text) => mdParser.render(text)}
            //监听编辑
            onChange={handleEditorChange}
            />
             
            <Bottom onClick={submitData} />
          </>
    )
}



//自定义插件 FileUpload.js 代码
//实现上传本地图片并实时预览
import React from 'react';
import {PluginComponent} from 'react-markdown-editor-lite';
import { Upload, message } from "antd";
import { UploadOutlined } from '@ant-design/icons';

class FileUpload extends PluginComponent{
    // 这里定义插件名称，注意不能重复
  static pluginName = "file-upload";
  // 定义按钮被防止在哪个位置，默认为左侧，还可以放置在右侧（right）
  static align = "left";

  constructor(props) {
    super(props);
  }

  //图片上传回调
  uploadChange = (info) => {
    if (info.file.status === "done") {
      // 插入Markdown
      let str = `![${info.file.response.data.path}](http://127.0.0.1:5000/upload${info.file.response.data.path})`;
      this.editor.insertText(str);
    } else if (info.file.status === "error") {
      message.error("图片上传失败");
    }
  };

  render() {
    return (
      <span className="button button-type-counter" title="插入本地图片">
        <Upload
          action="http://127.0.0.1:5000/uploadTest"
          onChange={(info) => this.uploadChange(info)}
        >
          <UploadOutlined />
        </Upload>
      </span>
    );
  }
}

export default FileUpload;
```

## 4、拓展

> react-markdown-editor-lite插件官网： https://harrychen0506.github.io/react-markdown-editor-lite/
>
> markdown-it官网：[http://markdown-it.docschina.org/#%E7%94%A8%E6%B3%95%E7%A4%BA%E4%BE%8B](http://markdown-it.docschina.org/#用法示例)

## Easy-Mde

```
支持markdown 语法，自带效果预览
```



### 处理 markdown 图片地址

```react
const MarkdownIt = require('markdown-it');  

// 创建一个 MarkdownIt 实例  
const md = new MarkdownIt();  

// 自定义图片渲染规则  
md.renderer.rules.image = (tokens, idx, options, env, self) => {  
  const token = tokens[idx];  
  const src = token.attrGet('src');  

  // 替换图片地址（这里只是一个示例，你可以根据需要进行替换）  
  const newSrc = src.replace('old-domain.com', 'new-domain.com');  

  // 更新 token 的 src 属性  
  token.attrSet('src', newSrc);  

  // 使用默认的渲染器渲染图片  
  return self.renderToken(tokens, idx, options);  
};  

// 测试 Markdown 内容  
const markdown = `  
![Alt Text](https://old-domain.com/image.jpg)  
`;  

// 将 Markdown 转换为 HTML  
const html = md.render(markdown);  

console.log(html);
```

