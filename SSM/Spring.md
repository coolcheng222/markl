# Spring

学的是Spring4

## 一. 绪论

### 1. 框架

**框架**是高度抽取可重用代码的一种设计,具有高度的通用性;

> 比如书城里的WebUtils,BaseServlet,之类的东西都挺不错的,打个包还能用

是一些半成品软件

### 2. Spring

Spring是一个<span style="color:red">IOC和AOP</span>__容器框架__

* Spring之前有一项技术叫做`EJB`(企业级JavaBean),拥有容器技术,但是以前的配置实在是太麻烦了,还只能JBoss提供容器环境

Sping的优良特性:

* __非侵入式__: 基于Spring开发的应用对象可以不依赖于Spirng的API
* __依赖注入__: 即IOC(后续展开)
* __面向切面编程__: 即AOP(后续展开)
* __容器__: Spring是一个容器,因为它包含并且管理应用对象的生命周期
* __组件化__: 用简单的组件配置组合复杂的应用
* __一站式__: 在IOC和AOP基础上可以整合各种企业应用的开源框架和第三方库

### 3. 官网及下载

官网: [Spring.io](https://spring.io/),也可以去apache官网找

在官网找到projects查看overview,找到Spring framework

然后自己摸索maven依赖在哪找

### 4. 模块划分

![image-20201020194541475](../pics/Spring/image-20201020194541475.png)

每个绿色矩形就是一个模块,从Test自底向上

黑色代表组成模块的jar包,要使用模块的完整功能,这些jar包都要导入

如果模块没有黑色,导它自身就行

> __Test__: Spring的单元测试模块
>
> __Core Container__ : 核心容器(IOC)
>
> * beans + core + context + SpEL(Spring表达式语言,expression包)
>
> __AOP+Aspects__: 面向切面编程模块
>
> __Data Access__/Integration: 
>
> * <u>数据访问</u> JDBC+ORM+Transactions(事务,tx包) +
> *  <u>集成</u> OX(xml)M+JMS
>
> __Web__: web应用 
>
> * WebSocket,Servlet(在web包),Web(webmvc包),Portlet

### 5. IOC和AOP简述

Spring可以利用IOC容器整合Structs2,Hibernate,Mybatis之类的框架

可以根据AOP面向切面编程学习__声明式事务__

### 6. 框架编写流程

**导包----写配置-----测试**

# IOC

## 一. IOC和DI

### 1. *概念

`IOC(Inversion Of Control)`: __控制反转__

将主动式控制反转为被动式控制

> 相关概念:
>
> __控制__: 指资源的获取方式
>
> * 主动式: 想要资源,自己创建,对于复杂对象的创建比较庞大
> * 被动式: 资源的获取不是程序员自己创建,而是交给__容器__来创建和设置
>
> __容器__: 能管理所有的组件(即有功能的类),容器可以自动探查出哪些组件需要用到另一些组件. 容器帮我们创建对象,并把对象赋值过去.
>
> 使主动的new资源变成被动的接收资源

`DI(Dependency Injection)`: __依赖注入__

是对IOC思想的一种落地

容器能够知道哪个组件运行的时候,需要另外一个组件; 然后容器通过**反射**形式,将容器中准备好的对象注入到组件中

只要是容器管理的组件都能使用容器提供的强大功能

### 2. IOC导包

在此处导入容器模块的<u>4个jar包(beans,core,context,expression)</u>和<u>日志包(commons-logging)</u>

## 二. *IOC配置

在eclipse中创建__Spring Bean Configuration File(一个xml)__,可以看见根标签就叫bean

### 1. 注册bean组件

1. 先创建一个bean名字叫Person,差不多就长这样:(简略版)

```java
public class Person {
	private String lastName;
	private Integer age;
	private String gender;
	private String email;
	.....
}
```

2. **在xml里注册bean:**

* `bean标签`: 一个bean标签可以注册一个组件(对象/类)
  * 属性:
    * **class:** 要注册的组件的全类名
    * **id**: 该组件(对象)的唯一标识
  * 标签体:
    * 使用`property`标签为属性赋值,标签的属性有name和value,是kv对
    * name用的是bean中getter的规律

```xml

<bean class="com.at.bean.Person" id="person01">
		<property name="lastName" value="张三"></property>
		<property name="age" value="18"></property>
		<property name="email" value="zhangsan@gui"></property>
		<property name="gender" value="f"></property>
	</bean>

```

