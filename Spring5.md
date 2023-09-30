# Spring

>[TOC]

## 一.绪论

### 1. 介绍

Spring是一个**开源J2EE框架**. 

Spring是一个`IOC(DI)`和`AOP`容器框架

*  具体介绍:
  * **轻量级**: Spring是非侵入性的的,基于Spring开发的应用中的对象可以不依赖于
  * __依赖注入__:(DI)把创建对象的过程交给Spring管理
  * __面向切面编程__:(AOP)不修改源代码的情况下进行功能增强
  * __容器__: 是一个容器,包含并且管理应用对象的生命周期.
  * __框架__: Spring中可以使用XML和Java注解组合这些对象.
  * __一站式__

* 特点:
  * 方便解耦,简化开发
  * AOP编程支持
  * 方便程序的测试
  * 方便集成优秀框架,如mybaits
  * 降低J2EE API使用难度
  * 方便事务管理
  * 源码设计巧妙

* 模块
* ![image-20200617142839685](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200617142839685.png)

### 2. 初使用

先把jar复制到项目的目录下,

然后创建Spring配置文件,在配置文件配置创建的对象(.xml)