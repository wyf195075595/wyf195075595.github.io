---
title: 多文件上传
date: 2022-06-17 08:23:10
tags: js
categories: js
---

## upload函数

```js
// 绑定上传按钮事件
$('#upload_btn').off('click').on('click',function(){
	$('.file').trigger('click')
})
//文件选择监听
$('.file').on('change',function(e){
    let files = e.target.files;
    console.log('文件',e.target,files);
    if(!files.length) return;
    let formData = new FormData();
    for (let i = 0; i < files.length; i++) {
       formData.append('file',files[i],files[i].name)
    }
    console.log(formData.getAll('upFile'));
    $.ajax({
        url:api.uploadMoreFiles,
        type:"post",
        data:formData,
        // 设置 processData 选项为 false，防止自动转换数据格式
        processData: false,
        // contentType 设置为 false 避免 JQuery 对其操作
        contentType: false,
        timeout: -1,
        cache: false, //上传文件无需缓存
        success:function(data){
        	console.log('上传',data)
        },
        error:function(){
            showTips('失败')
        },
        complete:function(){
            $(".file").val(null)
        }
    })
    console.log('over');
})


同时限制上传.xls、.xlsx文件

<input 
    class="note-image-input form-control" 
    type="file" 
    name="files" 
    accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet,application/vnd.ms-excel " 
    multiple="multiple"
>
        
        
```

### 后缀

<!--more-->

|  后缀  |                  格式                   |                类型                |
| :----: | :-------------------------------------: | :--------------------------------: |
| *.3gpp |         audio/3gpp, video/3gpp          |          3GPP Audio/Video          |
| *.ac3  |                audio/ac3                |             AC3 Audio              |
| *.asf  |         allpication/vnd.ms-asf          |     Advanced Streaming Format      |
|  *.au  |               audio/basic               |              AU Audio              |
| *.css  |                text/css                 |       Cascading Style Sheets       |
| *.csv  |                text/csv                 |       Comma Separated Values       |
| *.doc  |           application/msword            |          MS Word Document          |
| *.dot  |           application/msword            |          MS Word Template          |
| *.dtd  |           application/xml-dtd           |      Document Type Definition      |
| *.dwg  |              image/vnd.dwg              |      AutoCAD Drawing Database      |
| *.dxf  |              image/vnd.dxf              | AutoCAD Drawing Interchange Format |
| *.gif  |                image/gif                |     Graphic Interchange Format     |
| *.htm  |                text/html                |     HyperText Markup Language      |
| *.html |                text/html                |     HyperText Markup Language      |
| *.jp2  |                image/jp2                |             JPEG-2000              |
| *.jpe  |               image/jpeg                |                JPEG                |
| *.jpeg |               image/jpeg                |                JPEG                |
| *.jpg  |               image/jpeg                |                JPEG                |
|  *.js  | text/javascript, application/javascript |             JavaScript             |
| *.json |            application/json             |     JavaScript Object Notation     |
| *.mp2  |         audio/mpeg, video/mpeg          | MPEG Audio/Video Stream, Layer II  |
| *.mp3  |               audio/mpeg                |    MPEG Audio Stream, Layer III    |
| *.mp4  |          audio/mp4, video/mp4           |         MPEG-4 Audio/Video         |
| *.mpeg |               video/mpeg                |    MPEG Video Stream, Layer II     |
| *.mpg  |               video/mpeg                |    MPEG Video Stream, Layer II     |
| *.mpp  |       application/vnd.ms-project        |         MS Project Project         |
| *.ogg  |       application/ogg, audio/ogg        |             Ogg Vorbis             |
| *.pdf  |             application/pdf             |      Portable Document Format      |
| *.png  |                image/png                |     Portable Network Graphics      |
| *.pot  |      application/vnd.ms-powerpoint      |       MS PowerPoint Template       |
| *.pps  |      application/vnd.ms-powerpoint      |      MS PowerPoint Slideshow       |
| *.ppt  |      application/vnd.ms-powerpoint      |     MS PowerPoint Presentation     |
| *.rtf  |        application/rtf, text/rtf        |          Rich Text Format          |
| *.svf  |              image/vnd.svf              |        Simple Vector Format        |
|*.tif |	image/tiff |	|
|*.tiff|	image/tiff	|Tagged Image Format File|
|*.txt	|text/plain	|Plain Text|
|*.wdb	|application/vnd.ms-works	|MS Works Database|
|*.wps	|application/vnd.ms-works	|Works Text Document|
|*.xhtm|l	application/xhtml+xml	|Extensible HyperText Markup Language|
|*.xlc	|application/vnd.ms-excel	|MS Excel Chart|
|*.xlm	|application/vnd.ms-excel|	MS Excel Macro|
|*.xls	|application/vnd.ms-excel	|MS Excel Spreadsheet|
|*.xlt	|application/vnd.ms-excel	|MS Excel Template|
|*.xlw	|application/vnd.ms-excel|	MS Excel Workspace|
|*.xml	|text/xml, application/xml	|Extensible Markup Language|
|*.zip	|aplication/zip	|Compressed Archive|