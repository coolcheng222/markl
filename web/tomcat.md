# TOMCAT

<img src="../pics/tomcat/image-20200826144903973.png" alt="image-20200826144903973" style="zoom:33%;" />

## 一. 绪论

### 1. Web服务器

Web服务器主要用来接收客户端发送的请求和相应客户端请求

作为JavaWeb来说还需要有**Servlet容器,**主要作用是调用java程序处理用户发送的请求,并相应指定资源

开发JavaWeb项目Web服务器和Servlet容器都是必须的

* 常见JAvaWeb服务器:
  * `Tomcat(Apache)`
  * `JBOSS(Red Hat)`
  * `GlassFish(Oracle)`
  * `Resin(Caucho)`
  * `Weblogic(Oracle)`
  * `Websphere(IBM)`

### 2. TOMCAT概述

Tomcat是Apache软件基金会Jakata项目中的一个核心项目,有Sun公司的支持

### 3. Tomcat安装

在官网下载压缩包

它是Java编写的,依赖于`JAVA_HOME`环境变量

开始后就呈现了一个`port:8080`的服务器,可以使用IP:8080访问,局域网的大家都可以看见

启动: 用startup.bat或者`catalina run`

### 4. tomcat目录下的结构

> **bin**: 包含tomcat启动命令和停止tomcat的批处理或者可执行文件
>
> **conf**: 包含tomcat的配置文件
>
> **lib**: tomcat运行依赖的jar包
>
> **logs**: tomcat运行日志
>
> **temp**: 临时文件夹
>
> **webapps**: web应用程序们,集合了所有web项目,一个文件夹代表一个
>
> * 默认访问root项目
> * 写好的应用应该放在这
> * 想访问别的项目,应该这样用:`localhost:8080/项目名`
>
> __work__: 保存tomcat运行时编译好的一些文件