---
title: 拓展
date: 2023-02-22 16:00:00
tags: 拓展
categories: java
---



### 扫描远程文件

```java
    /**
     * 连接sftp服务器
     *
     * @param host     主机
     * @param port     端口
     * @param username 用户名
     * @param password 密码
     * @return
     */
    public static ChannelSftp connect(String host, int port, String username,String password) {
        ChannelSftp sftp = null;
        try {
            JSch jsch = new JSch();
            //jsch.getSession(username, host, port);
            Session sshSession = jsch.getSession(username, host, port);
            System.out.println("Session created.");
            sshSession.setPassword(password);
            Properties sshConfig = new Properties();
            sshConfig.put("StrictHostKeyChecking", "no");
            sshSession.setConfig(sshConfig);
            sshSession.connect();
            System.out.println("Session connected.");
            System.out.println("Opening Channel.");
            Channel channel = sshSession.openChannel("sftp");
            channel.connect();
            sftp = (ChannelSftp) channel;
            System.out.println("Connected to " + host + ".");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return sftp;
    }

    /**
     * 列出目录下的文件
     *
     * @param directory 要列出的目录
     * @param sftp
     * @return
     * @throws SftpException
     */
    public static Vector listFiles(String directory, ChannelSftp sftp) throws SftpException {
        return sftp.ls(directory);
    }

// 测试：

ChannelSftp sftp = connect(HOST, PORT, USER, PWD);
```

### 本地文件扫描

> [参考](https://blog.csdn.net/codeLife1993/article/details/81906996)

<!-- more -->

### 解析JSON文件

> 我们用来解析json格式的jar包有很多，jackson，[fastjson](https://so.csdn.net/so/search?q=fastjson&spm=1001.2101.3001.7020)，gson都行。但本人喜欢用fastjson。所以本篇都是以fastjson来解析json文件。
>
> [参考](https://blog.csdn.net/Jeck_wu/article/details/118755971)

### 文件上传

```html
<fieldset>
    <legend>文件上传</legend>
    <form action="http://127.0.0.1:18080/api/upload-test" method="post" enctype="multipart/form-data">
        <input type="file" name="files" webkitdirectory mozdirectory />
        <input type="submit" value="上传文件夹" />
    </form>
    <form action="http://127.0.0.1:18080/api/upload-test" method="post" enctype="multipart/form-data">
        <input type="file" name="files" multiple />
        <input type="submit" value="上传文件" />
    </form>
</fieldset>
```

#### 保存到本地

```java
@RequestMapping(value="/upload-test", method = RequestMethod.POST)
public void uploadTest(@RequestParam("files") MultipartFile[] files) {
    for (MultipartFile file: files) {
        try {
            file.transferTo(new File("D:/Download/" + file.getOriginalFilename()));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 保存至远程服务器

```java
@RequestMapping(value="/upload-test", method = RequestMethod.POST)
public void uploadTest(@RequestParam("files") MultipartFile[] files) {
    for (MultipartFile file: files) {
        try {
            String[] strings = file.getOriginalFilename().split("\\/");
            String fileName = strings[strings.length - 1];
            String newPath = file.getOriginalFilename().replace(fileName, "");
            sftpUtil.upload(uploadAnnotateFile + newPath, fileName , file.getInputStream());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    System.out.println("上传结束");
}
```

#### 远程连接配置

> [参考](https://github.com/fengfangithub/study-sftp)

1. pom.xml 引入依赖

	```xml
	<!-- sftp -->
	<dependency>
	    <groupId>com.jcraft</groupId>
	    <artifactId>jsch</artifactId>
	    <version>0.1.55</version>
	</dependency>
	<!-- 连接池 -->
	<dependency>
	    <groupId>org.apache.commons</groupId>
	    <artifactId>commons-pool2</artifactId>
	</dependency>
	```

2. application-xxx.yml 文件配置参数

	```yml
	# 远程访问服务器配置
	sftp:
	  #服务器ip
	  host: 192.168.1.245
	  #ssh端口
	  port: 22
	  #用户名
	  username: zkrd
	  #密码
	  password: zkrd12#$
	  # 上传标注文件服务器路径
	  uploadAnnotateFile: /home/zkrd/label_system/
	  #连接池参数
	  pool:
	    #对象池中管理的最多对象个数。默认值是8
	    max-total: 10
	    #对象池中最大的空闲对象个数。默认值是8
	    max-idle: 10
	    #对象池中最小的空闲对象个数。默认值是0
	    min-idle: 5
	```

3. 封装类

	SftpFactory.java

	```java
	import com.jcraft.jsch.ChannelSftp;
	import com.jcraft.jsch.JSch;
	import com.jcraft.jsch.JSchException;
	import com.jcraft.jsch.Session;
	import org.apache.commons.pool2.BasePooledObjectFactory;
	import org.apache.commons.pool2.PooledObject;
	import org.apache.commons.pool2.impl.DefaultPooledObject;
	import org.springframework.beans.factory.annotation.Value;
	
	import java.util.Properties;
	
	public class SftpFactory extends BasePooledObjectFactory<ChannelSftp> {
	    @Value("${sftp.host}")
	    private String host;
	    @Value("${sftp.port}")
	    private int port;
	    @Value("${sftp.username}")
	    private String username;
	    @Value("${sftp.password}")
	    private String password;
	
	    @Override
	    public ChannelSftp create() throws JSchException {
	        JSch jsch = new JSch();
	        Session sshSession = jsch.getSession(username, host, port);
	        sshSession.setPassword(password);
	        Properties sshConfig = new Properties();
	        sshConfig.put("StrictHostKeyChecking", "no");
	        sshSession.setConfig(sshConfig);
	        sshSession.connect();
	        ChannelSftp channel = (ChannelSftp) sshSession.openChannel("sftp");
	        channel.connect();
	        return channel;
	    }
	
	    @Override
	    public PooledObject<ChannelSftp> wrap(ChannelSftp channelSftp) {
	        return new DefaultPooledObject<>(channelSftp);
	    }
	
	    /**
	     * 销毁对象
	     *
	     * @param p
	     * @return
	     * @author fengfan
	     * @date 2022/1/14 15:26
	     */
	    @Override
	    public void destroyObject(PooledObject<ChannelSftp> p) {
	        ChannelSftp channelSftp = p.getObject();
	        channelSftp.disconnect();
	    }
	
	    /**
	     * 激活连接池里面的sftp连接
	     *
	     * @param p
	     * @throws Exception
	     */
	    @Override
	    public void activateObject(PooledObject<ChannelSftp> p) throws Exception {
	        ChannelSftp channelSftp = p.getObject();
	        if(!channelSftp.isConnected()){
	            channelSftp.connect();
	        }
	
	    }
	}
	
	```

	SftpGenericObjectPool.java 

	```java
	import com.jcraft.jsch.ChannelSftp;
	import org.apache.commons.pool2.impl.GenericObjectPool;
	import org.apache.commons.pool2.impl.GenericObjectPoolConfig;
	
	public class SftpGenericObjectPool {
	    private final GenericObjectPool<ChannelSftp> genericObjectPool;
	
	    public SftpGenericObjectPool(SftpFactory sftpFactory, GenericObjectPoolConfig<ChannelSftp> sftpPoolConfig) {
	        this.genericObjectPool = new GenericObjectPool<>(sftpFactory, sftpPoolConfig);
	    }
	
	    public ChannelSftp borrowObject() throws Exception {
	        return genericObjectPool.borrowObject();
	    }
	
	    public void returnObject(ChannelSftp obj) {
	        genericObjectPool.returnObject(obj);
	    }
	}
	```

	SftpUtil.java

	```java
	import com.jcraft.jsch.ChannelSftp;
	import com.jcraft.jsch.SftpException;
	
	import java.io.InputStream;
	
	public class SftpUtil {
	    //连接池
	    private SftpGenericObjectPool pool;
	
	    public SftpUtil(SftpGenericObjectPool pool) {
	        this.pool = pool;
	    }
	
	    /**
	     * 下载文件
	     *
	     * @param dir  远程目录
	     * @param name 远程文件名
	     * @return 文件字节数组
	     */
	    public InputStream download(String dir, String name) throws Exception {
	        ChannelSftp sftp = pool.borrowObject();
	        try {
	            sftp.cd(dir);
	            return sftp.get(name);
	        } finally {
	            pool.returnObject(sftp);
	        }
	    }
	
	    /**
	     * 上传文件
	     *
	     * @param dir  远程目录
	     * @param name 远程文件名
	     * @param in   输入流
	     */
	    public void upload(String dir, String name, InputStream in) throws Exception {
	        ChannelSftp sftp = pool.borrowObject();
	        try {
	            mkdirs(sftp, dir);
	            sftp.cd(dir);
	            sftp.put(in, name);
	        } finally {
	            pool.returnObject(sftp);
	        }
	    }
	
	    /**
	     * 删除文件
	     *
	     * @param dir  远程目录
	     * @param name 远程文件名
	     */
	    public void delete(String dir, String name) throws Exception {
	        ChannelSftp sftp = pool.borrowObject();
	        try {
	            sftp.cd(dir);
	            sftp.rm(name);
	        } finally {
	            pool.returnObject(sftp);
	        }
	    }
	
	    /**
	     * 递归创建多级目录
	     *
	     * @param dir 多级目录
	     */
	    private void mkdirs(ChannelSftp sftp, String dir) throws SftpException {
	        String[] folders = dir.split("/");
	        sftp.cd("/");
	        for (String folder : folders) {
	            if (folder.length() > 0) {
	                try {
	                    sftp.cd(folder);
	                } catch (Exception e) {
	                    sftp.mkdir(folder);
	                    sftp.cd(folder);
	                }
	            }
	        }
	    }
	
	    public SftpGenericObjectPool getPool() {
	        return pool;
	    }
	
	    public void setPool(SftpGenericObjectPool pool) {
	        this.pool = pool;
	    }
	}
	```

4. 使用

	```java
	// 注册实例化 sftpUtil
	@Resource
	private SftpUtil sftpUtil;
	```



#### 下载文件

> java在response中设置文件流，在浏览器中直接显示或直接下载
>
> 在浏览器地址栏输入文件请求url，能在浏览器上直接显示文件，而不直接下载
>
> 解决方式设置文件ContentType类型
>
> 当设置了ContentType为“image/jpg”时，浏览器可以直接显示图片；
>
> 没设置ContentType时，浏览器会直接下载图片到本地。
>
> 备注：不能设置[header](https://so.csdn.net/so/search?q=header&spm=1001.2101.3001.7020)，否则当设置了ContentType时也是直接下载图片
>
> [参考](https://blog.csdn.net/q289658763/article/details/125928198)

```java
/**
	 * 响应体加入文件流
	 * @param response	
	 * @param filePath	文件从盘符开始的完整路径
	 */
	private void responseFileStream(HttpServletResponse response, String filePath){
		logger.debug("responseFileStream imgPath:"+filePath);
		if(filePath.contains("%")){
			try {
				filePath = URLDecoder.decode(filePath,"UTF-8");
			} catch (UnsupportedEncodingException e) {
				logger.debug("responseFileStream decode error:"+e.toString());
			}
		}
		if(filePath.contains("%")){
			try {
				filePath = URLDecoder.decode(filePath,"UTF-8");
			} catch (UnsupportedEncodingException e) {
				logger.debug("responseFileStream decode error:"+e.toString());
			}
		}
		
		ServletOutputStream out = null;
		FileInputStream in = null;
		try {
			in = new FileInputStream(new File(filePath));
			String[] dir = filePath.split("/");
			String fileName = dir[dir.length-1];
			String[] array = fileName.split("[.]");
			String fileType = array[array.length-1].toLowerCase();
			//设置文件ContentType类型
			if("jpg,jepg,gif,png".contains(fileType)){//图片类型
				response.setContentType("image/"+fileType);
			}else if("pdf".contains(fileType)){//pdf类型
				response.setContentType("application/pdf");
			}else{//自动判断下载文件类型 
				response.setContentType("multipart/form-data");
			}
			//设置文件头：最后一个参数是设置下载文件名
	        //response.setHeader("Content-Disposition", "attachment;fileName="+fileName);   
			out = response.getOutputStream();
			// 读取文件流
			int len = 0;
			byte[] buffer = new byte[1024 * 10];
			while ((len = in.read(buffer)) != -1) {
				out.write(buffer, 0, len);
			}
			out.flush();
		} catch (FileNotFoundException e) {
			logger.error("responseFileStream error:FileNotFoundException" + e.toString());
		} catch (Exception e) {
			logger.error("responseFileStream error:" + e.toString());
		} finally {
			try {
				out.close();
				in.close();
			} catch (NullPointerException e) {
				logger.error("responseFileStream stream close() error:NullPointerException" + e.toString());
			} catch (Exception e) {
				logger.error("responseFileStream stream close() error:" + e.toString());
			}
		}
	}
```



### 前端图片预览

> 通过访问地址，在img 标签 src中 渲染

后端：

```java
public static String viewPicture(HttpServletResponse response, String filePath) {

    String format = filePath.substring(filePath.lastIndexOf(".") + 1);
    File file = new File( filePath);
    try {

        if (file.exists() && file.isFile()) {
            //读取图片文件流
            BufferedImage image = ImageIO.read(new FileInputStream(file));
            //将图片写到输出流
            ImageIO.write(image, format, response.getOutputStream());
            return null;
        } else {
            return "文件不存在";
        }

    } catch (IOException e) {
        return "预览失败";
    }

}
```

```java
// uploadAnnotateFile 是上传根路径
@RequestMapping(value="/viewPicture", method = RequestMethod.GET)
public ResponseBean viewPicture(HttpServletResponse res,String path) {
    String msg = FolderUtil.viewPicture(res, uploadAnnotateFile + path);
    return ResponseBean.success(msg);
}
```

前端：

```hmtl
<img src="http:xxx:xxx/api/viewPicture?path=/xxx.jpg" />
```



### 跨域cookie共享

> 主域 www.xxx.com， iframe 嵌入子页面 22.56.128.50:8888,子页面调用接口歇尔代主页面 的cookie。

```html
<iframe src="www.xxx.com/config/xxxLogin?target=page1">
    
</iframe>
```

iframe 中的地址是主页面的地址，利用 nginx  匹配 **/config** 转发 到 **22.56.128.50:8888** 这样一来 内嵌页面与主页面不跨域了。可以访问主页面的cookie。

但是有个问题，子页面跨域自动携带 cookie 失败了（暂时不大明白原因）。只能通过 js读取cookie 作为参数传递给后端



### Mysql 查询表字段结构注释

> 数据库名 是需要修改的

```sql
SELECT
a.table_name 表名,
a.table_comment 表说明,
b.COLUMN_NAME 字段名,
b.column_comment 字段说明,
b.column_type 字段类型,
b.column_key 约束
FROM
information_schema. TABLES a
LEFT JOIN information_schema. COLUMNS b ON a.table_name = b.TABLE_NAME
WHERE
a.table_schema = '数据库名'
ORDER BY
a.table_name
```

> 原文链接：https://blog.csdn.net/LQZ8888/article/details/127879597

### **查询某数据库下的所有表名 表注释**

```sql
SELECT
table_name 表名,
table_comment 表说明
FROM
information_schema.TABLES
WHERE
table_schema = '数据库名'
ORDER BY
table_name
```

