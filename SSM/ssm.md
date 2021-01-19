# SSM整合

## 一. 导包

Spring:

* Spring核心

  spring-beans-4.0.0.RELEASE.jar
  spring-context-4.0.0.RELEASE.jar
  spring-core-4.0.0.RELEASE.jar
  spring-expression-4.0.0.RELEASE.jar

* Springjdbc

  spring-jdbc-4.0.0.RELEASE.jar
  spring-orm-4.0.0.RELEASE.jar
  spring-tx-4.0.0.RELEASE.jar

* Spring测试

  spring-test-4.0.0.RELEASE.jar

* aop

  spring-aop-4.0.0.RELEASE.jar
  spring-aspects-4.0.0.RELEASE.jar



SpringMVC:

spring-web-4.0.0.RELEASE.jar
spring-webmvc-4.0.0.RELEASE.jar

commons-fileupload-1.2.1.jar
commons-io-2.0.jar
commons-logging-1.1.3.jar
jstl.jar
standard.jar

jackson-annotations-2.1.5.jar
jackson-core-2.1.5.jar
jackson-databind-2.1.5.jar

Mybatis:

## 二. SS整合

一个用Listener引入,一个用DispatcherServlet引入

不同的配置文件,MVC能用Spring的组件

```xml
<!-- needed for ContextLoaderListener -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/applicationContext.xml</param-value>
	</context-param>

	<!-- Bootstraps the root web application context before servlet initialization -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- needed for ContextLoaderListener -->
	<!-- The front controller of this Spring Web application, responsible for handling all application requests -->
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring/applicationMVC.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<!-- Map all requests to the DispatcherServlet for handling -->
	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
```

## 三. SSM整合

全局配置文件中:

* 数据源和事务管理放进Spring
* 映射文件的mapper也交给Spring
* 全局配置中只保留settings

> 这样一来就不需要放在同名包下了

### 1. 导入整合包

整合包能将dao的实现加入到容器中

`mybatis-spring-x.x.x.jar`

### 2. 配置SqlSessionFactoryBean

```xml
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="configLocation" value="classpath:mybatis/mybatis-config2.xml"></property>
	<property name="dataSource" value="ds"></property>
	<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"></property>
</bean>
```

### 3. 将dao实现加入到容器

使用MapperScannerConfigurer并配置包,包下的接口实现类就会自动加入容器

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="com.at.dao"></property>
</bean>
```

