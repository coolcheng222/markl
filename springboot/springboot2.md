# SpringBoot2

## 一. 相对于springboot1

### 1. @Configuration

该注解增加了`proxyBeanMethods`属性,布尔类型

若为true,则配置类为代理对象,每次调用其方法都会先去容器找对应bean,如果有就不调用该方法直接返回

若为false,则不用配置类代理,每次调用方法返回的都不是相同对象,也不是容器中对象,就算用

设置为true则为"full全量",为false则为`lite轻量级`,除非有组件依赖,不然推荐轻量级

### 2. @EnableConfigurationProperties

* 加在配置类上
* 参数为:
  * value: Class类,指明需要绑定属性的类型,并且会将其加入容器
* 然后在指明的类上用@ConfigurationProperties(),加入容器的对应组件就可以被赋值了

### 3. debug=true

开启debug模式, 会输出各种自动配置类生效情况等信息

### 4. @MatrixVariable矩阵变量

**是RequestMapping中url的新写法**

以前我们将参数放在query字符串中,现在我们可以用<u>矩阵变量</u>(即*URL重写*)的格式写在url路径中

## 二. 新工具

### 1. lombok

其实1.5版本springboot就有lombok,但是没用罢了

1. 导入依赖: 版本已经被版本控制中心指定了

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

2. 给idea装插件(搜索lombok),高版本idea自带

3. 在bean中定义属性再加上`@Data`就可以编译时自动添加getter和setter

   * 添加`@ToSrting`可以自动生成toString
   * `@NoArgsConstructor`,`@AllArgsConstructor`增加构造器

### 



