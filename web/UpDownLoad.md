# 上传文件

### 1. 用处

上传用户头像,之类的图片

### 2. 放在哪

放在数据库(不好)或者服务器

放数据库的缺点:

1. 占空间
2. 存取慢

所以一般文件都上传到__静态文件服务器__

我们没有条件,就放在项目中的地方,比如web-content下建立一个files目录

### 3. 写

-----

```html
<form method="post">
<!--必须post-->
		用户名:<input name="name"><br/>
		头像: <input name="photo" type="file"><br>
		<input name="submit" type="submit">
	</form>
```
<form>
		用户名:<input name="name"><br/>
		头像: <input name="photo" type="file"><br>
		<input name="submit" type="submit">
	</form>
------

但是想要获取文件,还得在表单的属性`enctype`改一下

```html
<form action="UploadServlet" method="POST" enctype="multipart/form-data">
```

enctype用来设置或返回用于编码表单内容的mime类型

使用包含文件上传控件的表单时必须用这个值

### 4. 接收

在servlet中使用request获得输入流

```java
ServletInputStream inputStream = request.getInputStream();
String string = IOUtils.toString(inputStream);
```

但是注意这个流是表单的所有内容,提取文件还在后面

* __使用commons-fileupload__工具
  * (依赖commons-io)

`DiskFileItemFactory` : 磁盘文件项工厂

`FileItem`: 文件项

`ServletFileUpload`: Servlet文件上传

```javA
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		DiskFileItemFactory factory = new DiskFileItemFactory();
		ServletFileUpload upload = new ServletFileUpload(factory);
		try {
			FileItemIterator itemIterator = upload.getItemIterator(request);
            //fileItemItertor版本
			while(itemIterator.hasNext()) {
				FileItemStream next = itemIterator.next();
                //这个stream才是核心
				User user = new User();
				if(next.isFormField()) {
					String fieldName = next.getFieldName();
					if("username".equals(fieldName)) {
						user.setUsername(IOUtils.toString(next.openStream()));
					}
				}else {
					String name = UUID.randomUUID().toString().replace("-", "") + "_" + next.getName();
					String realPath = getServletContext().getRealPath("/imgs");
					FileOutputStream os = new FileOutputStream(realPath + "/" + name);
					IOUtils.copy(next.openStream(),os);
				}
			}
			
		} catch (FileUploadException | IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	
	}
```
```java
import org.apache.tomcat.util.http.fileupload.disk.DiskFileItemFactory;
import org.apache.tomcat.util.http.fileupload.servlet.ServletFileUpload;
{
//不能提前自己获取流
DiskFileItemFactory factory = new DiskFileItemFactory();
ServletFileUpload fileUpload = new ServletFileUpload(factory);
//获取fileitem的准备工作
try {
	List<FileItem> parseRequest = fileUpload.parseRequest((RequestContext) request);
    //从请求里拿一个一个的item
	for (FileItem fileItem : parseRequest) {
		//主要就是操作FileItem
	}
} catch (FileUploadException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
}
		
```

`FileItem`:

* **isFormField:** 返回false表示有文件返回,true代表是简单的表单
* **getFieldName:** 表单项目的name值
* **getName:** 文件名,只对文件项有效
* 



#  文件下载

## 1. 前提

浏览器对"认识"的文件格式会显示,对"不认识"的文件格式会下载

```jsp
<a href="1.jsp">浏览器选择显示</a>
<a href="1.rar">浏览器选择下载</a>
```

## 2. 设置

有这三项需要在<u>响应头</u>中设置

* `Content-Type`

  * 传文件的MIME类型:

    * 如何获取MIME类型:

      ```java
      String mimeType = this.getServletContext().getMimeType("/1.jpg");
      //传入String路径
      System.out.println(mimeType);//image/jpeg
      ```

  ```java
  //设置ContentType
  response.setContentType(mimeType);
  ```

* `Content-Disposition`

  * 设置资源的处理方式

  * 设置值为`"attachment;filename=文件名"`

  * 文件名指的是下载后的文件名,不是资源的名字

    ```java
    response.setHeader("Content-Disposition", "attachment;filename=1.jpg");
    ```

* `Content-Length`

  * 可选设置,设置文件大小
  * 一般不设置

## 3. 中文文件

下载中文文件还是会出现问题

```java
string = new String("你好.jpg".getBytes(),"iso8859-1");
```

然后亲测把这段变成filename的值好用