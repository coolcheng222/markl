# MybatisPlus

[TOC]

## 一. 介绍

MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

![framework](https://mybatis.plus/img/mybatis-plus-framework.jpg)

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 二. 快速开始(紧随官网)

### 1. 引入Springboot以及plus依赖

对于springboot可以使用以下,对于非Springboot引入mybatis-plus即可

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.5</version>
</dependency>
```

### 2. 按mybatis一样配置数据源

隐私问题,不粘贴代码

### 3. 主启动类添加MapperScan

```java
@SpringBootApplication
@MapperScan("com.sealll.mybatisp.mapper")
public class MybatispApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatispApplication.class, args);
    }

}
```

### 3. 创建实体和接口

```java
public interface UserMapper extends BaseMapper<User> {
}
```

### 4. 直接使用接口

```java
@SpringBootTest
class MybatispApplicationTests {
    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        System.out.println(("----- selectAll method test ------"));
        List<User> userList = userMapper.selectList(null);
        userList.forEach(System.out::println);
    }

}
```

## 三. 简单的配置

### 1. 非Springboot项目

对于Spring/MVC项目,

* 一方面@MapperScan可用,也可以换成MapperScannerConfigurer

* 另一方面,配置的SqlSessionFactory(Bean)需要改成<u>mybatis-plus的SqlSessionFactory</u>

```xml
<bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

### 2. 注解

以下注释基本都添加在bean的属性上.如果不是的话会额外说明

> 该版本为3.0.4,内容有时候跟官方文档的3.0.0完全不一样

#### 2.1 @TableName

加bean类上

指明该实体对应的表名及相关信息

属性:

* `value`: 表名
* `resultMap`: 字符串.对应xml中resultMap的id

#### 2.2 @TableId

加在属性上,注明主键; <u>相当于配置了ResultMap的id标签</u>

* `value`: 主键的列名(驼峰命名)

* `IdType`: 该配置让java在增加时填入主键而不是数据库填入主键

  |     值      |                             描述                             |
  | :---------: | :----------------------------------------------------------: |
  |    AUTO     |                         数据库ID自增                         |
  |    NONE     | 无状态,该类型为未设置主键类型(注解里等于跟随全局,全局里约等于 INPUT) |
  |    INPUT    |                    insert前自行set主键值                     |
  |  ASSIGN_ID  | 分配ID(主键类型为Number(Long和Integer)或String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法) |
  | ASSIGN_UUID | 分配UUID,主键类型为String(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextUUID`(默认default方法) |

#### 2.3 @TableField

属性映射以及操作

* `value`: 列名,驼峰命名

* `el`: 映射情况,类似于我们会在配置时使用#{role, jdbcType=BIGINT}这样的语法,我们在el中可以配置"role, jdbcType=BIGINT"

* `exist`: 数据库里是否存在该字段映射,默认为true

* `condition`: where策略,字符串按照SqlCondition的枚举

* `update`: 看注释来的更容易懂

  ```java
  /**
   * <p>
   * 字段 update set 部分注入, 该注解优于 el 注解使用
   * </p>
   * <p>
   * 例如：@TableField(.. , update="%s+1") 其中 %s 会填充为字段
   * 输出 SQL 为：update 表 set 字段=字段+1 where ...
   * </p>
   * <p>
   * 例如：@TableField(.. , update="now()") 使用数据库时间
   * 输出 SQL 为：update 表 set 字段=now() where ...
   * </p>
   */
  ```

* `strategy`: 字段验证策略,FieldStrategy类型,有NOT_NULL,NOT_EMPTY,IGNORED,DEFAULT四种
  
  * 摸不着头脑
* `fill`: 字段填充策略,FieldFill类型,有DEFAULT(不处理),INSERT(插入时填充),UPDATE(更新时填充).INSERT_UPDATE(都填充)四种策略
* `select`: boolean 是否加入查询

#### 2.4 @Version

乐观锁,加了以后操作字段会加乐观锁

#### 2.5 @EnumValue

属性是枚举类型的注解

看不懂!!!

#### 2.6 @OrderBy

isDesc: 是否倒序

sort: 数字越小越靠前

## 四. 代码生成

### 1. 引入依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.4.1</version>
</dependency>
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.0</version>
</dependency>

```

### 2. 编写代码生成

大致脉络:

```java
    public static void main(String[] args) {
        // new一个代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        //...设置各种全局配置并放进mpg
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        //...设置数据源并配置
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        //...
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };
        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        //....
        mpg.setStrategy(strategy);
        mpg.execute();
    }
```

* 配置的大体内容:
  * 数据源datasource
  * 生成表和排除表的策略strategy
  * 生成代码的包的路径等信息packageInfo
  * 模板template
  * 全局配置策略globalConfig
  * 注入配置injectionConfig

#### 2.1 数据源

最重要的是设置数据源基本信息,其他的可以去官网文档了解一下

```java
DataSourceConfig dataSource = new DataSourceConfig();
dataSource.setDriverName("com.mysql.jdbc.Driver");
dataSource.setUrl("");
dataSource.setUsername("");
dataSource.setPassword("");
mbg.setDataSource(dataSource);
```

#### 2.2 策略

`include`: 包含的表名,和exclue二选一,当enableSqlFilter(3.3.0+)为false时，允许正则表达式

`exclude`: 不包含的表名,和inclue二选一,当enableSqlFilter为false时，允许正则表达式

`restControllerStyle`: 是否生成Rest风格Controller

其他的内容比如service名,controller名,mapper名等需要的时候自己查文档

```java
StrategyConfig strategyConfig = new StrategyConfig();
strategyConfig.setRestControllerStyle(true);
strategyConfig.setExclude("");
```

#### 2.3 包

`parent`: 下面所有包的父包,相当于公共父包,帮助少写点东西

 `moduleName`:父包模块名

`entity`:Entity包名

类似的还有service,serviceImpl,mapper,xml,controller

` pathInfo`: 路径配置信息

```java
PackageConfig packageConfig = new PackageConfig();
packageConfig.setParent("com.sealll.mybatisp");
packageConfig.setMapper("mapper");
packageConfig.setEntity("bean");
packageConfig.setController("controller");
packageConfig.setService("service");
packageConfig.setServiceImpl("service.impl");
packageConfig.setXml("xml");
mbg.setPackageInfo(packageConfig);
```

#### 2.4 全局配置

`OutputDir`: 输出目录,一般我们从maven目录结构的java下开始(默认D盘根目录)

`fileOverride`: 默认false,是否覆盖已有文件

`open`: 是否在资源管理器打开输出目录

其他比如文件命名方式可以看文档

