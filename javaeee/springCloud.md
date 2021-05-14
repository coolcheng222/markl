# SpringCloud(Hoxton)介绍

[TOC]

## 一. SpringCloud和SpringBoot版本适配/微服务技术栈

![image-20210428161244387](../pics/springCloud/image-20210428161244387.png)

版本选择: 

Springboot: 2.2.2

SpringCloud: Hoxton.SR1

Cloud Alibaba: 2.1.0

### 1. 组件停更

SpringCloud老组件: 已经停更, 只修复bug,不再接受PullRequest,不在发布新版本![img](https://img2020.cnblogs.com/blog/966359/202007/966359-20200709215532979-1975170559.png)

### 2. 微服务维度

1. 服务注册与发现
2. 服务负载和调用
3. 服务熔断降级
4. 服务网关
5. 服务分布式配置
6. 服务开发

## 二. 构建父工程

### 1. 创建/依赖版本选择

1. 创建一个maven工程

2. 添加依赖

* **Springboot依赖 **2.2.2

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
```

* __SpringCloud依赖__ H.SR1

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-dependencies</artifactId>
  <version>Hoxton.SR1</version>
  <type>pom</type>
  <scope>import</scope>
</dependency>
```

* **Spring-cloud-阿里巴巴** 2.1.0

```xml
<dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
```

### 2. 其他依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>${druid.version}</version>
</dependency>
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>${mybatis.spring.boot.version}</version>
</dependency>
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>${lombok.version}</version>
</dependency>
```

## 三. 支付模块构建

### 1. 依赖实现

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
</dependencies>
```
### 2. web构建

一层层构建就行了

### 3. dev热部署

* 引入依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <scope>runtime</scope>
      <optional>true</optional>
  </dependency>
  ```

* 给父工程引入插件

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <fork>true</fork>
        <addResources>true</addResources>
      </configuration>
    </plugin>
  </plugins>
</build>
```

* 给idea添加选项: setting-compiler

  ![image-20210509213058826](../pics/springCloud/image-20210509213058826.png)

  按下ctrl+alt+shift+/ 选择registry,打钩

![image-20210509213241881](../pics/springCloud/image-20210509213241881.png)

## 四. 订单模块**调用**支付模块

### 1. RestTemplate

最简单的方法就是用httpClient直接调用

`RestTemplate`来自spring-web,对httpclient进行封装

```java
@RestController
@Slf4j
public class OrderController {
    public static final String PAYMENT_URL = "http://localhost:8001";
    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create",payment,CommonResult.class);
    }
}
```

restTemplate默认发送json,所以接收方应该加上ResponseBody

## 技巧. 提取服务共享内容

先前两个工程共有entities包,造成冗余

提取一个模块来保存公有部分

### 1. 公用依赖:

提取进公有模块的pom

### 2. 共用代码:

提取进共用的模块

### 3. install并依赖

# 注册中心Eureka

## 一. 概念

### 1. 服务治理

在传统RPC远程调用框架中,管理每个服务和服务之间的依赖关系比较复杂,管理复杂,所以需要使用**服务治理**,管理服务与服务之间的依赖关系.可以实现<u>服务调用,负载均衡,容错</u>等,实现<u>服务发现和注册</u>

### 2. 服务注册与发现

* **服务注册:** 将服务信息注册进注册中心
* **服务发现:** 从注册中心上获取服务信息
* 本质: key-value存取服务
* **调用**: 底层就是httpClient

Eureka采用**CS的设计架构**,Server作为服务注册功能的服务器,作为<u>服务注册中心</u>

系统中的其他微服务使用<u>Eureka客户端</u>链接到<u>服务器</u>并维持心跳连接,这样维护人员就可以通过Duraka Server来监控系统中的微服务是否正常运行

![image-20210511212955263](../pics/springCloud/image-20210511212955263.png)

* **EurekaServer:** 各个微服务节点通过配置启动后在Server中进行注册,这样EurakaServer中的服务注册表中将存储所有可用服务节点的信息,可以在界面中直观看到
* __EurekaClient__: 是JAva客户端,用于简化和EurekaServer的交互; 客户端内置一个轮询负载算法的负载均衡器. 在应用启动后,将会向EurekaServer 发送心跳(默认30秒). 如果EurekaServer在多个周期内没收到心跳,会将该节点从服务注册中心移除(默认90s)

## 二. 服务端安装

### 1. 步骤

1. 建maven模块

2. 引入eureka Server依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

3. 配置yml

   ```yml
   server:
     port: 7001
   eureka:
     instance:
       hostname: localhost
     client:
       # 不注册自己
       register-with-eureka: false
   #     自己是注册中心,维护服务,不检索
       fetch-registry: false
       service-url:
         # 设置服务端地址, 处理包括查询和注册服务
         defaultZone: http://${eureka.instance.hostname}:${server.port}/euraka
   ```

4. 给主启动类加`@EnableEurekaServer`

   ```java
   @SpringBootTest
   @EnableEurekaServer
   public class EurekaMain7001 {
       public static void main(String[] args) {
           SpringApplication.run(EurekaMain7001.class);
       }
   }
   ```

### 2. 启动

启动并访问7001页面观察是否成功

### 3. 注册支付模块(Provider)

* 引入pom

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

* 添加yml配置

  ```js
  eureka:
    client:
  	# 注册自己
      register-with-eureka: true
  	# 查找服务
      fetch-registry: true
      service-url:
        defaultZone: http://localhost:7001/eureka
  ```

* 给主启动类添加`@EnableEurekaClient`注解

* 注册的服务名即为配置的

  ```yml
  spring:
    application:
      name: cloud-payment-service
  ```

### 4. 注册消费模块(consumer)

* 引入客户端pom

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

* 修改yml

```yml
eureka:
  client:
    # 自己是否注册进eureka
    register-with-eureka: true
    # 抓取已有的注册信息,以便实现负载均衡
    fetch-registry: true
    service-url: http://localhost:7001/eureka
```

* 给主启动类添加`@EnableEurekaClient`注解
* 服务名为spring.application.name配置

### 5. 检查服务注册

进入localhost:7001查看

![image-20210513220859255](../pics/springCloud/image-20210513220859255.png)

## 三. Eureka集群原理

### 1. 集群

微服务RPC远程服务调用最依赖__高可用__, 而Eureka最核心的部分是服务器,所以

首先要保证Server没有宕机,再保证Provider不会宕机

* 原理: 搭建Eureka服务器集群的原理, 即服务器之间__互相注册__,对外暴露出一个整体

![image-20210513221641487](../pics/springCloud/image-20210513221641487.png)