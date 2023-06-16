---
title: 多文件上传
date: 2022-06-17 08:23:10
tags: js
categories: js
---

### upload函数

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



### 大文件切片文件上传

前端：

```html
<fieldset>
    <legend>切片上传</legend>
    <progress id="progress"  max="100" value="0"></progress>
    <span id="tips"></span>
    <label for="files1" >
        <span>文件：</span>
        <input id="files1" type="file" name="files" />
    </label>
    <button id="btn-submit">传送</button>
</fieldset>

<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
<script>
	 const oProgress = document.getElementById("progress");
        const oFile = document.getElementById("files1");
        const oTips = document.getElementById("tips");
        const TIP_INFO = {
            "NOT_SURPORT": "不支持此类型文件",
            "NO_FILE": "未选择文件",
            "UPLOAD_FAIL": "上传文件失败",
            "UPLOAD_SUCCESS": "上传文件成功",
        }
        const API = {
            upload: "http://localhost:8000/uploadFile"
        }
        // 切片大小
        const CHUNK_SIZE = 1024 * 1024;
        // 已上传大小
        let uploadSize = 0;
        const uploadReslut = null;
        document.getElementById("btn-submit").onclick = async function() {
            const { files: [files] } = oFile;
            console.log(files);
            if(!files) {
                oTips.innerText = TIP_INFO.NO_FILE;
                console.warn(TIP_INFO.NO_FILE);
            }
            const { name, type, size} = files;
            oProgress.max = size;
            oTips.innerText = "";
            while(uploadSize <= size) {
                const fileName = new Date().getTime() + "_" + name;
                const fileChunk = files.slice(uploadSize, uploadSize + CHUNK_SIZE);
                const formData = createFormData({name, type, size, fileName, uploadSize, file: fileChunk});
                try {
                    uploadReslut = await axios.post(API.upload, formData);
                } catch (error) {
                    oTips.innerText = TIP_INFO.UPLOAD_FAIL;
                }
                uploadSize += fileChunk.size;
                if(fileChunk.size == 0) break;
                oProgress.value = uploadSize;
            }
            oTips.innerText = TIP_INFO.UPLOAD_SUCCESS;
            uploadSize = 0;
            function createFormData ({name, type, size, fileName, uploadSize, file}) {
               const fd =  new FormData();
               fd.append("name", name);
               fd.append("type", type);
               fd.append("size", size);
               fd.append("fileName", fileName);
               fd.append("uploadSize", uploadSize);
               fd.append("file", file);
               return fd;
            }
        }
</script>
```

