# maven

> [TOC]

## 一. 绪论

### 1. 目前的问题

1. 一个项目就是一个工程
2. 项目中jar必须手动赋值粘贴到web-inf/lib
3. jar包需要别人准备好或官网下载
4. jar包依赖的jar包需要手动加入项目

### 2. Maven是什么

Maven是一款**服务于JAva平台**的**自动化构建工具**.

"构建"的发展:

* Make->ant->Maven->gradle

### 3. 构建的概念

* 以"Java源文件","框架配置文件","JSP","HTML","图片"等资源为原材料,去生产一个可以运行的项目的过程,叫做__构建__
  * 有编译,搭建,部署的含义
* 编译: JAva源文件[User.java]->class字节码->虚拟机解释
* 部署: 一个BS项目运行的不是动态Web工程本身,而是Web工程编译的结果
  * 在![image-20200621144858415](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200621144858415.png)这种东西就叫**运行时环境(rt)**,是**不在文件目录下**的,只是对某一组jar包的引用

> Java代码: java需要编译才能执行
>
> web工程: 当浏览器访问JAva程序就需要将包含java的web工程编译结果拿到服务器指定目录下.
>
> 实际项目: 实际项目整合第三方框架,需要正确的部署项目才可以运行

#### 3.1 构建环节

1. **清理**: 将以前编译饿到的class字节码文件删除
2. **编译**: 将JAva源程序编译成class字节码文件
3. **测试** :自动测试,自动调用junit程序
4. **报告** : 测试程序执行的结果
5. **打包** :动态web工程打war包,java工程打jar包
6. **安装** :Maven特定概念--将打包得到的文件复制到仓库的指定位置
7. **部署** :将动态web工程生成的war包复制到servlet容器指定目录下使其运行

### 4. 安装maven

先配置javahome,然后解压maven,运行mvn -v查看版本

配置环境变量__MAVEN_HOME__或者__M2_HOME__

## 二. 约定目录结构

```
Hello(根目录,为工程名)
|--------src(源码)
|--------pom.xml(现成配置文件)
|---------|--------main(在主程序)
|---------|--------|---------java(JAva源文件)
|---------|--------|---------resources(存放框架或其他工具的配置文件)
|---------|--------test(放测试程序)
|---------|--------|---------java
|---------|--------|---------resources
```



* 重要性:
  * maven负责自动化构建,编译为例,maven想要自动编译必须知道Java源文件在哪里
  * 如果自定义的东西想让框架或工具知道,有两种办法
    * 配置文件
    * 遵守框架的约定
      * log4j.properties / log4j.xml
      * 约定>配置>编码

## 三. 常用maven命令

> 注意: 执行与构建相关(编译,测试,打包)的maven命令必须进入pom.xml所在目录

```bash
mvn clean #清理
mvn compile #编译主程序
mvn test-compile #编译测试程序
mvn test # 执行测试
mvn package #打包1

```

## 四. pom

`project OBject Mode`

pom.xml是核心配置文件,与构建过程相关的一切相关设置都在这个文件中进行配置

### 1.坐标gav

 Maven中坐标使用`三个向量`在仓库中唯一定位一个mavaen工程

* `groupid`: 公司或组织域名倒序 + 项目名

  ```xml
  <groupid>com.at.maven</groupid>
  ```

  

* `artifactid`: 模块名

  ```xml
  <artifactId>Hello</artifactId>
  ```

* `version`: 版本

  ```xml
  <version>0.0.1-SNAPSHOT</version>
  ```

  

## 五. 仓库

> 本地仓库: 当前电脑的Maven仓库
>
> 远程仓库: 
>
> * 私服(Nexus): 搭建在局域网,为局域网内maven服务
> * 中央仓库(Central):
> * 中央仓库镜像

保存的内容:

* maven自身需要的插件
* 第三方框架工具的jar
* 我们自己开发的maven工程

## 六. 依赖

maven解析依赖信息时会到本地仓库搞

```xml
    <dependency>
      <groupId>com.at.maven</groupId>
      <artifactId>Hello</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <scope>compile</scope>
    </dependency>
```

* 对于我们自己开发的maven工程,使用install命令就可以让他进仓库

```bash
mvn install
```

* `scope`标签的理解
  * 称为__依赖的范围__,一般有`compile`,`provided`,`test`
    * compile: 对主程序有效,对test也可见
    * test: 对主程序无效,对测试程序有效
    * provided: 不参与打包
  * compile会一只照顾开发部署运行到tomcat,但provided在部署时就舍弃,由servlet提供