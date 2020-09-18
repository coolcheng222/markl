# JDBC

> [TOC]

## 一. 绪论

### 1. 数据的持久化

数据的持久化(persistence): 把**数据保存到可掉电式存储设备以供使用**,大多数情况下,数据持久化意味着将内存中的数据保存到硬盘上加以固化.而持久化的实现过**程大多通过关系型数据库**完成

### 2. Java中的数据库存储技术

* 数据库存取技术在Java中:
  * JDBC直接访问
  * JDO技术(java data object)
  * 第三方OR工具,比如Hibernate,Mybatis

__Jdbc__是数据库访问数据库的**基石**

### 3. JDBC

`JDBC(Java Database Connectivity)`是一个**独立于特定数据库管理系统**,通过Sql数据库存取和操作的**公共接口**(API),定义了用来访问数据库的标准Java类库(java.sql,javax.sql)

* 其为不同数据库提供了统一的途径,为开发者屏蔽了一些细节问题
* JDBC的__目标__是使JAva程序员能使用JDBC连接任何提供了JDBC驱动程序的数据库系统

<u>现有的JDBC驱动:(可以看做JDBC接口的实现类):</u>

* `JDBCMysqllmpl` : mysql
* `JDBCOraclelmpl`: Oracle
* `JDBCSQLServerlmpl`: SQL server
* `JDBCDB2lmpl`: DB2

### 4. JDBC体系结构

JDBC接口包含两个层次:

* 面向应用的API: JavaAPI,抽象接口,供应用程序开发人员使用
* 面向数据库的API: Java Driver API,供开发商开发数据库驱动使用

因为开发商和程序员直到针对接口,所以可以称为__面向接口编程__

### 5. jdbc整体步骤

![image-20200619174757841](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200619174757841.png)

> ODBC叫__开放式数据库连接__,是微软推出的(SQL server),使用者可以使用ODBC API
>
> 使用JDBC的时候可以用JDBC访问ODBC弄成__JO桥__

## 二. 数据库连接

### 1. 获取数据库连接

**三种方式是递进关系,不是并列关系**

#### 1.1 方式一: Driver(驱动)接口

```java
//核心内容:
//Driver的静态方法 connect(String url,Properties info)
Connection conn = driver.connect(url,info);
```

* 为核心内容提供各种对象参数:

  * driver:Driver是一个接口,需要它的实现类(驱动)来实例化,

    ```java
    //我们先导入了驱动的jar包,让我们看看能不能成功吧
    Driver driver = new com.mysql.jdbc.Driver();
    ```

  * `url`: 在驱动文档docs文件夹的readme中查找到

    * ```xml
      <name>url</name>
      <value>jdbc:mysql://localhost:3306/test1</value>
      <!--   主协议:子协议//主机名:端口/数据库名 -->
      ```

    * ```java
      String url = "jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8";
      //后面这串不加判错
      ```

  * `info`: Properties是map的实现类,应当传入`user`和`password`

    ```java
    
    Properties info = new Properties();
    info.setProperty("user", "root");
    info.setProperty("password", "123456")
    ```

* 整体代码:

#### 1.2 方式二: 更好的可移植性

```java
Driver driver = new com.mysql.jdbc.Driver();
//考虑到这段代码用到了第三方的东西,不具有可移植性
```

希望优化,可以考虑用反射获取想要连接的数据库种类

```java
Class<?> clazz = Class.forName("com.mysql.jdbc.Driver");
Driver driver = (Driver)clazz.getDeclaredConstructor().newInstance();
```



#### 1.3 方式三: 使用DriverManager替换driver

`java.sql.DriverManager`是一个实现类,拥有提供Connection类的静态方法

步骤:

1. 调用registerDriver静态方法注册驱动

   ```java
   Class<?> clazz = Class.forName("com.mysql.jdbc.Driver");
   Driver driver = (Driver)clazz.getDeclaredConstructor().newInstance();
   //前两步和上面一样
   DriverManager.registerDriver(driver);
   ```

2. 调用`getConnection`获取连接

   ```java
   Connection getConnection(String url,String user,String password);
   ```

   

#### 1.4 上述方法优化

在加载Driver驱动类的时候调用了**静态代码块**注册了驱动,此时**只要加载**它就行了,**不需要额外行动注册**

其实甚至不需要加载类,导包的时候就帮你弄好了,但是不建议

```java
public void test3() throws SQLException, ClassNotFoundException, InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException, NoSuchMethodException, SecurityException {
		Class.forName("com.mysql.jdbc.Driver");
//		Driver driver = (Driver)clazz.getDeclaredConstructor().newInstance();
//		DriverManager.registerDriver(driver);
    ....
}
```

#### 1.5 将基本信息放在配置文件中(最终版本)

在src下建立一个.properties文件

```properties
#properties文件内容,四项基本信息
user=root
password=123456
url=jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8
driverClass=com.jdbc.mysql.Driver
```

* 加载配置

  ```java
  Properties pro = new Properties();
  InputStream ras = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
  		
  pro.load(ras);
  ```

* 获取信息

  ```java
  String user = pro.getProperty("user");
  String passward = pro.getProperty("passward");
  String url = pro.getProperty("url");
  String driverClass = pro.getProperty("driverClass");
  ```

* 剩下的一样

  ```java
  Class.forName(driverClass);
  		
  Connection connection = DriverManager.getConnection(url,user,password);
  System.out.println(connection);
  ```

## 三. 使用PreparedStatement实现CRUD

`CRUD`就是"增删改查"

### 1. 操作和连接数据库

数据库连接被用于向数据库发送命令和SQL语句,并接受数据库服务器返回的结果.其实一个数据库连接就是一个Socket连接.

* 在java.sql有3个__接口__定义了对数据库的不同调用方式:
  * `Statement`:用于执行静态SQL语句并返回生成的结果对象
  * `PreparedStatement`: SQL语句被**预编译**并存储在此对象中,可以高效多次的执行该语句
  * `CallableStatement`: 用于执行SQL存储过程

### 2. Statement的弊端

> 1. 需要拼接sql语句 "select ..." + user + "and..."
> 2. 存在SQL注入的问题,把一定字符串传入会导致查询错误的通过
>    * __sql注入__是利用某些系统没有对用户输入的数据充分检查,而在用户输入数据中心注入非法SQL语句段或命令,从而利用SQL殷勤完成恶意行为的做法

### 3. PreparedStatement增删改

PreparedStatement是预编译的sql语句对象

#### 3.1 获取+增加数据

增删改基本一样

* 先按上一节创建连接conn

* 获取一个PreparedStatement实例

  ```java
  //声明样式
  PreparedStatement conn.prepareStatement(String sql);
  ```

  * 具体使用:

    * 预编译sql语句,sql语句中会用到占位符`?`

    ```java
    String sql = "insert into customers (name,email,birth) values (?,?,?)";
    //?:占位符
    PreparedStatement ps = conn.prepareStatement(sql);
    //获取实例
    ```

  * 填充占位符

    ```java
    //使用ps的setxx类型方法
    //字符型可用字符串String,日期型可用java.sql.Date
    //new java.sql.Date(long);传入时间戳,或这date.getTime()
    
    ps.setString(1,"哪吒"); 
    // 第一个参数代表占位符的序号,从1开始.第二个参数代表值
    ps.setString(2, "nezha@gmail.com");
    
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    java.util.Date parse = sdf.parse("2000-01-01");
    ps.setDate(3, new java.sql.Date(parse.getTime()));
    ```

* 执行sql操作

  ```java
  ps.execute();
  ```

* __关闭资源__

  * 因为要关闭资源,所以__不能用throws__,必须使用和try catch finally使用
  * 跟关流一样,该关就关,声明的位置要在try外
  * 连接和statement都要关,调用close方法,需要局部trycatch和验证空指针

```java
finally {// 资源关闭
	if(ps != null)
		try {
			ps.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	if(conn != null)
		try {
			conn.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
}
```

#### 3.2 获取反馈

只用`execute`返回的布尔类型并不是反馈.

想要获取增删改的反馈可用`executeUpdate()`代替之,返回的是__影响的行数__

### 4. 封装工具类

为了方便进行连接和一系列操作,我们封装了工具类`JDBCUtils`在jdbc1工程的`com.at.util`包下

### 5. PreparedStatement查询

跟增删改不同的地方在于,需要获得**结果集**

```java

ps = conn.prepareStatement("select id,name,email,birth from customers where id = ?");
//获得ps
//填充占位符
```

* 执行:

  并非调用execute而是使用`executeQuery()`,返回一个ResultSet对象

  ```java
  ResultSet res = ps.executeQuery();
  ```

### 6. ORM思想(对象关系映射)/类型映射

* ORM编程思想:
  * 一个数据表对应一个Java类
    * 应该是一个**Java bean**
  * 一个记录对应一个Java类的对象
  * 一个字段对应Java类的一个属性

| Java类             | SQL类                                |
| ------------------ | ------------------------------------ |
| boolean            | bit                                  |
| byte               | tinyint                              |
| short              | smallint                             |
| int                | Integer                              |
| long               | bigint                               |
| string             | char,varchar,longvarchar             |
| byte array         | binary , var binary                  |
| java.sql.Date      | Date(可以用string,然后让mysql自动转) |
| java.sql.Time      | Time                                 |
| java.sql.Timestamp | Timestamp                            |
|                    |                                      |

### 7. __处理结果集(重点)__

#### 7.1 获取值

  * 类似于迭代器iterator,每个元组类似集合中一个元素

    `res.next()`: 返回一个__布尔值__,表示有没有元素,并__指针下移__

    然后使用`res.getXxx(column/列别名)`(xxx用int,String,DAte之类的类型替换),column用字段出现的顺序值(从1开始)替换

  * ```java
    		if(res.next()) {//判断结果集下一条是否有数据,如果有就true指针下移,不然false直接结束
      			
      			int id = res.getInt(1); //获取第一个字段
      			String name = res.getString(2);//第二个字段
      			String email = res.getString("email");
      			Date birth = res.getDate(4);
    }
    ```

#### 7.2 结果集元数据

结果集细节都存放在__结果集元数据__中,调用getMetaData

```java
ResultSetMetaData rsmd = res.getMetaData();
```

* 可以获取结果集的列数

```java
int cc = rsmd.getColumnCount();
```

* 可以获取指定Column的列名--不推荐

  ```java
  String columnName = rsmd.getColumnName(i + 1);
  ```

* 可以获取Column的别名--推荐

  ```java
  String columnLabel = rsmd.getColumnLabel(i + 1);
  ```

  

> 以此可以把bean的属性(反射)和字段名关联.在**属性和字段名相同**时进行这样的操作:
>
> ```java
> String columnName = rsmd.getColumnName(i + 1);//这个操作叫获取列名
> 
> Field field = Customer.class.getDeclaredField(columnName);
> field.setAccessible(true);
> field.set(cust, object);
> ```
>
> <br>在**字段名和属性名不同时**这样操作: 在编写SQL语句时用__别名__给字段赋上__属性名__,并用元数据__获取别名__
>
> ```java
> String columnName = rsmd.getColumnLabel(i + 1);//这个操作叫获取列的别名
> 
> Field field = Order.class.getDeclaredField(columnName);
> field.setAccessible(true);
> field.set(order, value);
> ```
>
> 



#### 7.3 关闭结果集

* 关闭资源: **结果集也要关**

  ```java
  
  if(res != null){
  			try {
  				res.close();
  			} catch (SQLException e) {
  				// TODO Auto-generated catch block
  				e.printStackTrace();
  			}
  		}
  ```

  

### 8. 通用表查询方法实例

使用泛型,传入结果类的class.

如果返回多条使用List<T\>泛型

```java
public <T> T getInstance(Class<T> clazz, String sql, Object... args) {
	//使用泛型方法
		Connection conn = null;
		PreparedStatement ps = null;
		ResultSet rs = null;
		try {
			conn = JDBCUtils.getConnection();
			ps = conn.prepareStatement(sql);

			for (int i = 0; i < args.length; i++) {
				ps.setObject(i + 1, args[i]);
			}
			rs = ps.executeQuery();
			ResultSetMetaData rsmd = rs.getMetaData();
			int column = rsmd.getColumnCount();

			if (rs.next()) {
				T t = clazz.newInstance();//使用泛型和反射获取对象
				for (int i = 0; i < column; i++) {
					String ll = rsmd.getColumnLabel(i + 1);
					Field df = clazz.getDeclaredField(ll);
					df.setAccessible(true);
					Object value = rs.getObject(i + 1);
					df.set(t, value);
				}
				return t;
			}
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (InstantiationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (NoSuchFieldException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SecurityException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {

			JDBCUtils.closeResource(conn, ps, rs);
		}
		return null;
	}

```

### 9. 它是怎么解决注入问题?

它是预编译sql语句,占位符不会改变句体关系.

### 10. 还有什么好处?

* 易于操作blob
* 易于批量操作

## 四. blob数据问题

只有PreparedStatement做得到

### 1. mysql中的blob

* tinyblob: 255字节
* blob: 最大65k
* mediumblob: 最大16mb
* longblob: 最大4G

### 2. blob填充占位符

传入一个文件字节流,然后用setBlob

```java
fis = new FileInputStream("jaaa.png");
ps.setBlob(4, fis);
```

### 3. 查询blob

首先我们拿到了结果集rs,需要获取结果集中的blob

先getBlob,然后用对象获取输入流

```java
//java.sql.blob
Blob blob = rs.getBlob("photo");
InputStream bs = blob.getBinaryStream();//这个流要关
```

然后用输入流输入数据

```java
fos = new FileOutputStream(new File("zhuyin.jpg"));//这个流也要关
byte[] cbuf = new byte[2014];

int len;
while ((len = bs.read(cbuf)) != -1) {
	fos.write(cbuf, 0, len);
}
```

## 五. 批量插入数据

方式一:

就硬execute,**要好久**

```java
String sql = "insert into goods (name) values (?)";
	ps = conn.prepareStatement(sql);
	for (int i = 0; i < 20000; i++) {
		ps.setObject(1, "name_" + i);
		ps.execute();
    }
```



方式二:

用ps伟大的`addBatch()`,`executeBatch()`,`clearBatch()`

mysql默认关闭批处理,需要在url后面加上`rewriteBatchedStatements=true`

太快了!

```java
for (int i = 0; i < 20000; i++) {
ps.addBatch();
		
		if(i % 500 == 0) {
			//攒够500执行
			ps.executeBatch();
			ps.clearBatch();
		}
		if(i == 19999) {
			ps.executeBatch();
			ps.clearBatch();
		}
		


```

优化: 用事务优化,插完了再提交

```java
conn.setAutoCommit(false);
//......
//....
conn.commit();
```

## 六. 数据库事务

### 1. 隔壁mysql的事务笔记,来复习下

__事务__(transaction)是一个或一组sql语句组成一个执行单元,特点是_要么全部执行,要么全部不执行(语句相互依赖)_.在mysql中,仅有存储引擎`Innodb`支持事务

```sql
update user_table set balance = balance - 100 where user = 'AA';
update user_table set balance = balance + 100 where user = 'BB';
```

>
>
>___ACID___四大属性
>
>1. __原子性(Atomicity)__
>
>   即一个transaction不可再分,必须以整体进行
>
>2. __一致性(Consistency)__
>
>   数据库应从一个一致状态转换到另一个一致状态
>
>3. __隔离性(Isolation)__
>
>   一个事务的执行不能被其他事务干扰
>
>4. __持久性(Durability)__
>
>   事务一旦提交,对数据库的改变是永久的

### 2. jdbc下事务的提交条件

- 数据一旦提交，就不可回滚。
- 数据什么时候意味着提交？
  - **当一个连接对象被创建时，默认情况下是自动提交事务**：每次执行一个 SQL 语句时，如果执行成功，就会向数据库自动提交，而不能回滚。
  - **关闭数据库连接，数据就会自动的提交。**如果多个操作，每个操作使用的是自己单独的连接，则无法保证事务。即同一个事务的多个操作必须在同一个连接下。

### 3. 使用jdbc事务

* 首先,我们在执行多条语句的时候应该用**同一个连接,**并且**不能关**

* 然后关掉自动提交

  ```java
  conn.setAutoCommit(false);
  ```

* 然后在try到异常时,在catch里回滚

  ```java
  conn.rollback();
  conn.commit();
  ```

  

* 在关闭资源前把连接恢复默认(指自动提交开启)



### 3. 隔壁mysql记的隔离机制

#### 并发会产生的问题

> __脏读__:事务T1读取了T2已被更新但_没有提交_的字段,若T2回滚,T1读取错误(临时而无效)
>
> __不可重复读__:T1读取了一个字段,T2_更新_了字段,则T1只能读取值不同的字段
>
> __幻读__:若T1读了几个字段,T2_插入_新的行,T1再读就会多出几行
#### mysql隔离级别

* 查看隔离级别语句:

  ```sql
  select @@tx_isolation;
  ```
* 隔离级别
  

| 关键词          |                                                 |
| --------------- | ----------------------------------------------- |
| read-uncommited | 最低隔离,三种问题都会产生                       |
| read-commited   | 避免脏读,不避免幻读和不可重复读                 |
| repeatable-read | 避免脏读和不可重复读,不解决幻读_(默认隔离级别)_ |
| serializable    | 全部避免,性能低下                               |

* 设置隔离级别

  ```sql
  set session transaction isolation level 关键词; #设定当前连接
  set global transaction isolation level 关键词;#设置全局隔离级别,重启有效
  ```

### 4. JDBC设置和查看隔离级别

```java
conn.getTransactionIsolation();
//对应以下几个非静态变量
int TRANSACTION_NONE             = 0;
int TRANSACTION_READ_UNCOMMITTED = 1;
int TRANSACTION_READ_COMMITTED   = 2;
int TRANSACTION_REPEATABLE_READ  = 4;
int TRANSACTION_SERIALIZABLE     = 8;
conn.setTransactionIsolation(常量/值);
```

## 七. DAO

*DAO*(Data Access Object) 数据访问对象是一个面向对象的数据库接口，封装了数据库通用的操作

就把之前通用的操作放进(抽象方法BaseDAO)去就行了



* 在实例化时提供__接口__规范对表的提取方法
* 然后提供实现类同时继承抽象类和实现接口

在书城项目中有完整的DAO体系

## 八. 数据库连接池

### 1. 必要性

* 在开发基于数据库的web程序时,传统模式:
  * 在主程序(如servlet,b	eans)中建立数据库连接
  * 进行sql
  * 断开数据库连接

* 古老的方式存在的问题:
  * 普通JDBC用DriverManager获取,每次都要讲Connection加载到内存中验证用户名密码,就向数据库要求一件事就断开,数据库连接资源__没有得到充分利用__
  * 每次数据库连接后都得断开,不然就内存泄漏了,可能会要重启数据库
  * 不能控制被创建的连接对象数

* 提供了连接池接口: `javax.sql.DataSource`
  * 选讲三个实现
  * `DBCP`: 速度快,不稳定
  * `C3P0`: 慢,稳定
  * `Druid`

### 2. C3P0

先导入jar包

```java
//高耦合做法
ComboPooledDataSource cpds = new ComboPooledDataSource();
cpds.setDriverClass( "com.mysql.cj.jdbc.Driver" ); //loads the jdbc driver          
cpds.setJdbcUrl( "jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&serverTimezone=UTC" );
cpds.setUser("root");                                  
cpds.setPassword("123456");   

//建议从配置文件获取,下个小标题详述

```

设置基本属性:

```java

cpds.setInitialPoolSize(10);//初始化池大小
```

获取连接

```java
Connection connection = cpds.getConnection();
```

销毁整个连接池

```java
DataSource.destroy(cbps);
```

#### 2.1 配置文件获取连接

配置c3p0.properties太low了,我们快用xml文件来配置!

固定文件名`c3p0-config.xml`,写在src下

以下文本修改自官方doc

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>

  <named-config name="intergalactoApp"> 
  <!-- 提供获取连接的4个基本信息 -->
    <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&serverTimezone=UTC</property>
    <property name="user">root</property>
    <property name="password">123456</property>
    
    <!-- 数据库连接池管理信息 -->
      <!--连接池的连接数不够时一次性申请的连接数 -->
    <property name="acquireIncrement">5</property>
      <!-- 初始化默认连接数-->
    <property name="initialPoolSize">10</property>
      <!--维护的最小的连接数-->
    <property name="minPoolSize">10</property>
      <!--维护上限-->
    <property name="maxPoolSize">100</property>
      <!--连接池中维护的最多的statement的个数-->
    <property name="maxStatements">50</property>
      <!--每个连接最多使用statement的个数-->
    <property name="maxStatementsPerConnection">2</property>


  </named-config>
</c3p0-config>
```

java连接数据库操作

```java
ComboPooledDataSource cpds = new ComboPooledDataSource("intergalactoApp");//这个参数对应xml中<named-config name="intergalactoApp">的名字
cpds.getConnection();
```

### 3 DBCP

导入jar包,dbcp和pool

* 实例化DataSource接口

  ```java
  BasicDataSource source = new BasicDataSource();
  ```

* 查看index文档

* 设置基本信息

  ```java
  //不推荐
  source.setUsername("root");
  source.setUrl("jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&serverTimezone=UTC");
  source.setPassword("123456");
  source.setDriverClassName("com.mysql.cj.jdbc.Driver");
  ```

* 设置数据库管理属性

* ```java
  source.setInitialSize(10);
  		source.setMaxActive(10);
  ```

* ```txt
  dbcp连接池常用基本配置属性
  
  1.initialSize ：连接池启动时创建的初始化连接数量（默认值为0）
  
  2.maxActive ：连接池中可同时连接的最大的连接数（默认值为8，调整为20，高峰单机器在20并发左右，自己根据应用场景定）
  
  3.maxIdle：连接池中最大的空闲的连接数，超过的空闲连接将被释放，如果设置为负数表示不限制（默认为8个，maxIdle不能设置太小，因为假如在高负载的情况下，连接的打开时间比关闭的时间快，会引起连接池中idle的个数 上升超过maxIdle，而造成频繁的连接销毁和创建，类似于jvm参数中的Xmx设置)
  
  4.minIdle：连接池中最小的空闲的连接数，低于这个数量会被创建新的连接（默认为0，调整为5，该参数越接近maxIdle，性能越好，因为连接的创建和销毁，都是需要消耗资源的；但是不能太大，因为在机器很空闲的时候，也会创建低于minidle个数的连接，类似于jvm参数中的Xmn设置）
  
  5.maxWait  ：最大等待时间，当没有可用连接时，连接池等待连接释放的最大时间，超过该时间限制会抛出异常，如果设置-1表示无限等待（默认为无限，调整为60000ms，避免因线程池不够用，而导致请求被无限制挂起）
  
  6.poolPreparedStatements：开启池的prepared（默认是false，未调整，经过测试，开启后的性能没有关闭的好。）
  
  7.maxOpenPreparedStatements：开启池的prepared 后的同时最大连接数（默认无限制，同上，未配置）
  
  8.minEvictableIdleTimeMillis  ：连接池中连接，在时间段内一直空闲， 被逐出连接池的时间
  
  9.removeAbandonedTimeout  ：超过时间限制，回收没有用(废弃)的连接（默认为 300秒，调整为180）
  
  10.removeAbandoned  ：超过removeAbandonedTimeout时间后，是否进 行没用连接（废弃）的回收（默认为false，调整为true)
  ```

#### 3.1 使用配置文件输入基本信息

使用`BasicDataSourceFactory`工厂类

```java
static DataSource createDataProperties(Properties properties);
//传入一个properties,返回一个配好的实例
```

```properties
# 配置文件内容
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/test1?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&serverTimezone=UTC
username=root
password=123456

initialSize=10
```

### 4. Druid(推荐)

Druid是阿里巴巴开源平台上一个数据库连接池实现，它结合了C3P0、DBCP、Proxool等DB池的优点，同时加入了日志监控，可以很好的监控DB池连接和SQL的执行情况，可以说是针对监控而生的DB连接池，**可以说是目前最好的连接池之一。**

```java
package com.atguigu.druid;

import java.sql.Connection;
import java.util.Properties;

import javax.sql.DataSource;

import com.alibaba.druid.pool.DruidDataSourceFactory;

public class TestDruid {
	public static void main(String[] args) throws Exception {
		Properties pro = new Properties();		 	pro.load(TestDruid.class.getClassLoader().getResourceAsStream("druid.properties"));
		DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        //和dbcp一样
		Connection conn = ds.getConnection();
		System.out.println(conn);
	}
}

```

其中，src下的配置文件为：【druid.properties】

```java
url=jdbc:mysql://localhost:3306/test?rewriteBatchedStatements=true
username=root
password=123456
driverClassName=com.mysql.jdbc.Driver

initialSize=10
maxActive=20
maxWait=1000
filters=wall
```

- 详细配置参数：

| **配置**        | **缺省** | **说明**                                                     |
| --------------- | -------- | ------------------------------------------------------------ |
| name            |          | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。   如果没有配置，将会生成一个名字，格式是：”DataSource-” +   System.identityHashCode(this) |
| url             |          | 连接数据库的url，不同数据库不一样。例如：mysql :   jdbc:mysql://10.20.153.104:3306/druid2      oracle :   jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username        |          | 连接数据库的用户名                                           |
| password        |          | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：<https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter> |
| driverClassName |          | 根据url自动识别   这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName(建议配置下) |
| initialSize     | 0        | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive       | 8        | 最大连接池数量                                               |
| maxIdle         | 8        | 已经不再使用，配置了也没效果                                 |
| minIdle         |          | 最小连接池数量                                               |

## 九. DBUtils

commons-dbutils是Apache组织提供的一个开源JDBC工具类库,封装了针对于数据库的增删改查操作

以`QueryRunner类为例`使用dbutils

```java
 QueryRunner runner = new QueryRunner();
 Connection conn = JDBCUtils.getConnection();
 String sql  ="insert into customers (name,email,birth) values (?,?,?)"
 runner.update(conn,sql,"蔡徐坤","caixukun@","1999-12-01");

//记得关connection
```
* 查询
  * 查询时需要用到**ResultSetHandler<T>**的实现类

```java
//查询时需要用到ResultSetHandler<T>的实现类
QueryRunner runner = new QueryRunner();
  Connection conn = JDBCUtils.getConnection();
  String sql = "select * from customers where id = ?";
  
BeanHandler<Customer> handler = new BeanHandler<>(Customer.class);
BeanListHandler<Customer> hand2 = new BeanListHandler<>(Customer.class);
ScalarHandler handler3 = new ScalarHandler();//获取一个特殊值

  Customer cust = runner.query(conn,sql,handler,23);
List<Customer> ll = runner.query(conn,sql,hand2,23);
Lont l = (Long) runner.query(conn,sql,handler3);//会返回object ,可以强转
```

* 关闭资源
* 使用DBUtils工具类实现

```java
DBUtils.closeQuietly(conn);
DBUtils.closeQuietly(ps);
DBUtils.closeQuietly(rs);
//不用处理异常的关闭
```

## 拓展: BeanUtils

第三方工具类,拥有各种花式操作bean的方法

`commons-beanutils-1.8.0.jar`

`commons-logging-1.1.1.jar`

### 1. 方法

* `setProperty` 设置属性值

  ```java
  BeanUtils.setProperty(bean, name, value);
  //bean: 哪个对象 Object
  //name: 属性名 String
  //value: 属性值 Object
  ```

  * 拥有智能化,可以把字符串转成数字,如果不能转就回归默认值
  * 针对的是__getter,setter__而不是属性本身

  > bean的属性真正的名字是set,get后面的字符串

* `populate`

  * 将map中的键值对直接映射到bean中

    ```java
    BeanUtils.populate(bean, properties);
    //bean: 对象,Object
    //properties: Map
    ```

  * 实例: request获取map直接封装成bean

    ```java
    Map<String, String[]> parameterMap = request.getParameterMap();
    BeanUtils.populate(t, parameterMap);
    ```

    