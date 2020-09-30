# 架构

## 一. 软件架构

```
|-软件
|-用户界面交互(前端)
|-----表示层(直接与用户交互)
|-业务逻辑(代码)
|-----业务逻辑层
|-----数据库访问层
```

分层的目的->分模块->解耦

* 表示层: 和用户直接交互

  * 有HTML页面
  * Servlet

* 业务逻辑层: 处理网站的业务功能

  * 编写的业务逻辑代码

    也可以称作`Service层`

* 数据库访问层:(`持久化层`)

  * `DAO层`: 所有操作数据库的类放在DAO层
  * `模型/bean层`: 创建与数据库数据对应的对象

## 二. 项目架构

### 1. 架构

基于软件架构如何构建项目

![image-20200907143956129](../pics/Project/image-20200907143956129.png) 

1. 用户查看html页面,发送请求
2. Servlet层某个Servlet收到用户请求
3. 调用Service层相关方法处理
4. Service层直接调用DAO和数据库沟通
5. DAO把查询到的封装成Bean,系统用bean就行
6. ![image-20200908121649085](../pics/Project/image-20200908121649085.png)

### 2. 分层开发

1. **需求分析,建立模型**

   1. 建立登录注册相关模型(Bean层)

      1. 建立用户表

         > 当数据表很多的时候一般会按照命名规则分层
         >
         > sys_user : 系统-用户

      2. 建立用户类

   2. 建立DAO层,操作数据库

      1. JDBCUtils,获取数据库连接
      2. 一个表一个DAO类,x先是BaseDAO然后扩展子类比如账户UserDAO

   3. 建立Service层

      1. 比如UserService管理用户,BookService管书

   4. 建立Servlet层: 处理用户请求

   5. 建立界面

### 3, BaseDAO难点

```java
public class BaseDAO<T> {
    private QueryRunner qr = new QueryRunner();
    private Class<T> type; //等待获取泛型的class

    public BaseDAO(){
        //获取父类类型
        ParameterizedType superclass = (ParameterizedType) this.getClass().getGenericSuperclass(); //经过测试,当前类的type为有参数type
        type = (Class<T>) superclass.getActualTypeArguments()[0];//所以可以直接获取泛型参数给type
    }
}
```

## 三. MVC模式

### 1. 从Web应用的层次结构说起

1. 显示层: 用户交互
2. 业务逻辑层: 逻辑运算
3. 持久化层: 数据库交互,增删改查

![image-20200923134410750](../pics/Project/image-20200923134410750.png)

* 软件分层的好处:
  * 简化开发
  * 易于维护
  * 易于分工

### 2. MVC

`Model View Controller`

MVC最初是显示层模型,现在已经可以应用于整个软件架构

* `View视图`: 只负责数据和界面的显示,不接受与显示数据无关的代码,便于程序员的分工合作
  * JSP,HTML,少量接触java代码
* `Model模型`: 把页面要显示的数据抽取成模型
* `Controller控制层`: 控制页面转发,跳转逻辑
  * servlet层

MVC显示层逻辑

![image-20200923135023408](../pics/Project/image-20200923135023408.png)

MVC的另一种逻辑

![image-20200923135126740](../pics/Project/image-20200923135126740.png)

* 从红线看:
  * Model: 把页面要显示的数据抽取成模型JavaBean
* 从蓝线看:
  * Model: 是业务模型service层,可以访问数据库Dao层

### 3. MVC框架

Struts2(过时),SpringMVC

## 二. 分页

为了减轻数据库负担, 我们需要将显示分页,就像百度那样.

### 1. 分层准备

* Servlet控制层: BookManagerServlet -->list
* Service层: 查出部分图书(分页要求的第一页,第二页...)
* Dao层: 查出部分图书(分页查询 limit index,size)
* Bean层: 图书bean,分页模型bean
  * 分页模型: 封装当前模型信息
  * ![image-20200925130402878](../pics/Project/image-20200925130402878.png)

### 2. Bean层

分页模型Page内容分析:

* 封装的东西可以多种多样,他们都可以分页

```
//当前第几页 int pageNo           用户请求的
//一共几页 int totalPage	      计算出来的,根据总记录数以及每页记录数
//总记录数 int totalCount         数据库select count(1)
//页面记录数 int pageSize          人工指定
//数据库查询的起始索引 int index;    根据请求第几页计算(pageNo - 1) * pageSize
//是否有下一页 boolean hasNext;
//是否有上一页 boolean hasPrev;     
//分页的实际数据: List<T> pageData

//分页请求的url: String url
```

### 3. Dao层

写一些分页有关的Dao

```java
//List<Book> getPageList(int index,int size) //在Dao层返回原始数据
//
```

### 4. Service层

写分页有关的方法,传入页码,页面大小,创建bean并设置值,返回

### 5. Servlet层 + 视图层

第一次请求显示图书列表,应该只显示第一页数据

```java
//弄个page方法
```

* 页面跳转绑定事件受base标签影响

```javascript
$("#gotopage").click(function(){
	var pn = $("#pn_input").val();
	location.href = "manager/BookManagerServlet?method=page&pn=" + pn;
})
```

* 完整分页jsp出版

  ```jsp
  <div id="page_nav">
  <a href="manager/BookManagerServlet?method=page">首页</a>
  <c:if test="${page.hasPrev}">
  <a href="manager/BookManagerServlet?method=page&pn=${page.pageNo - 1 }">上一页</a>
  </c:if>
  
  <c:forEach begin="1" end="${page.totalPage }" var="pnum">
  	<c:if test="${page.pageNo == pnum}">
  		【${page.pageNo }】
  	</c:if>
  	<c:if test="${ page.pageNo != pnum}">
  	<a href="manager/BookManagerServlet?method=page&pn=${pnum}">${pnum }</a>
  	</c:if>
  </c:forEach>
  
  <c:if test="${page.hasNext }">
  	<a href="manager/BookManagerServlet?method=page&pn=${page.pageNo + 1 }">下一页</a>
  </c:if>
  <a href="manager/BookManagerServlet?method=page&pn=${page.totalPage }">末页</a>
  共${page.totalPage }页，${page.totalCount }条记录 到第
      <input value="${page.pageNo }" name="pn" id="pn_input" />页 
      <input type="button" value="确定" id="gotopage">
  </div>
  ```

  

页码缩略显示分析:

> 当前1: [1],2,3,4,5
>
> 当前2: 1,[2],3,4,5
>
> 当前4 2,3,[4],5,6  相当于只显示当前页码的前两页,后两页

* 五页以内全部显示

  begin=1,end=totalPage

* 五页以上

  * 当前页码小于3,显示1-5 begin=1,end=5

```jsp

<c:if test="${page.totalPage > 5 }">
	<c:if test="${page.pageNo <= 3 }">
		<c:set scope="page" var="begin" value="1"></c:set>
		<c:set scope="page" var="end" value="5"></c:set>
	</c:if>
	<c:if test="${page.pageNo > 3 }">
		<c:set scope="page" var="begin" value="${page.pageNo - 2 }"></c:set>
		<c:set scope="page" var="end" value="${page.pageNo + 2 }"></c:set>
	</c:if>
	<c:if test="${page.pageNo + 2 > page.totalPage }">
		<c:set scope="page" var="begin" value="${page.totalPage - 4 }"></c:set>
		<c:set scope="page" var="end" value="${page.totalPage }"></c:set>
	</c:if>
</c:if>
	<c:forEach begin="${begin}" end="${end }" var="pnum">
        ...
```

## 五. 首页过Servlet

首先修改默认welcome-file是行不通的,因为各种文件都会过`/`目录,会调用多次servlet

不妨在首页直接转发到Servlet

```jsp
<jsp:forward page="..."></jsp:forward>
```

