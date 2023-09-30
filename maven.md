

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
mvn install #安装
mvn site # 生成站点

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

## 七. 生命周期

生命周期就是各个构建环节的执行顺序

maven的核心程序中定义了抽象的生命周期,生命周期的各个阶段具体任务由插件完成

* Maven有3个相互独立生命周期
  * clean lifecycle: 清理
    * pre-clean
    * clean
    * post-clean
  * default lifecycle: 核心部分,编译,测试,打包,安装,部署等
    * 
  * site lifecycle: 生成报告,站点,发布站点
    * pre-site
    * site
    * post-site
    * site-deploy

* 生命周期特点:
  * 无论要执行生命周期中的那个阶段,都会从整个生命周期最初的位置执行##

### 2. 插件和目标

* 声明周期的各个阶段仅仅定义了要执行的是什么
* 各个阶段和插件的目标是对应的
* 相似的目标由特定插件完成
* 可以将目标看做"调用插件功能的命令"

| 生命周期阶段 | 插件目标    | 插件                  |
| ------------ | ----------- | --------------------- |
| compile      | compile     | maven-compiler-plugin |
| test-compile | testCompile | maven-compiler-plugin |
|              |             |                       |

## 八 .依赖(plus)

### 1. 传递性

compile的依赖会传递到依赖你的工程里.

### 2. 原则

解决jar冲突问题

* 情况1: 我项目依赖了一个log4j版本,依赖的项目传递了另一个log4j版本
  * 解决: **就近原则**,传递性上越近的就选择
* 情况2:在依赖路径长度相同时,有版本冲突抉择
  * 解决:__先声明者优先(dependency的顺序在前)__

### 3. 统一管理依赖的版本号

Spring依赖的jar包都是4.0.0,想要统一升级为4.1.1,怎么办

* 建议:

  * 使用properties标签内统一声明版本号

  * 然后使用变量

    ```xml
    <properties>
        <com.at.spring>4.1.1</com.at.spring>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId></groupId>
            <artifactId></artifactId>
            <version>${com.at.spring}</version>
                     <!--相当于用一个变量统一制定版本号-->
        </dependency>
    </dependencies>
    ```

    

## 九. 继承

### 1. 举例

不能传递的依赖有junit4.0,4.9,4.12 会对团队协同开发造成问题

思路: 将junit依赖版本统一提取到父工程中,在子工程声明junit依赖时不指定版本.以父工程统一设定为准

配置继承后要先安装父工程

* 操作步骤

  * 创建一个maven工程做个父工程,打包方式pom

  * 子工程中声明对父工程引用

    ```xml
    <parent>
        <groupId>父工程</groupId>
        <artifictId>父工程</artifictId>
        <version>父工程版本</version>
        <relativePath>父工程pom.xml相对路径</relativePath>
    </parent>
    ```

    

  * 将子工程坐标中与父工程重复的内容删除

  * 在父工程中管理junit依赖

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
            	<!--...-->
            </dependency>
        </dependencies>
    </dependencyManagement>
    ```

    

  * 删除子工程中的版本号

## 十. 聚合(一键安装)

在一个总的聚合工程中配置各个参与模块

```>
<modules>
	<module>子工程路径</moudule>
	<module>子工程路径</moudule>
	<module>子工程路径</moudule>
</modules>
```

