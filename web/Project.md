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

