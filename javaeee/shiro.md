# Shiro

## 一. 介绍

Java权限框架

### 1. 概念:

![img](https://atts.w3cschool.cn/attachments/image/wk/shiro/1.png)

* 核心功能: 

**Authentication**：身份认证 / 登录，验证用户是不是拥有相应的身份；

**Authorization**：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；

**Session** **Management**：会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通 JavaSE 环境的，也可以是如 Web 环境的；

**Cryptography**：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

![img](https://atts.w3cschool.cn/attachments/image/wk/shiro/2.png)

**Subject**：主体，代表了当前 “用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；即一个抽象概念；所有 Subject 都绑定到 SecurityManager，与 Subject 的所有交互都会委托给 SecurityManager；可以把 Subject 认为是一个门面；SecurityManager 才是实际的执行者；

**SecurityManager**：安全管理器；即所有与安全有关的操作都会与 SecurityManager 交互；且它管理着所有 Subject；可以看出它是 Shiro 的核心，它负责与后边介绍的其他组件进行交互，如果学习过 SpringMVC，你可以把它看成 DispatcherServlet 前端控制器；

**Realm**：域，Shiro 从从 Realm 获取安全数据（如用户、角色、权限），就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色 / 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource，即安全数据源。

## 二. 身份验证

身份验证由两个要素构成

`principals(身份)`: 表示主体的属性,比如用户名

`credentials`(凭证):只有主体知道的安全值 

* ini配置操作

```ini
#ini配置
[users]
zhang=123
san=123
```

```java
public class ShiroTest {
    @Test
    public void test1(){
        // 获取Ini初始化的Security Manager工厂
        IniSecurityManagerFactory factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        //获取manager并注册给utils
        SecurityManager instance = factory.createInstance();
        SecurityUtils.setSecurityManager(instance);

        //获得subject
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
        try{
            //登录!
            subject.login(token);
        } catch (AuthenticationException e) {
            //身份验证失败
            System.out.println("不认识!");
        }
        if(subject.isAuthenticated()){
            System.out.println("我要登出");
            subject.logout();
        }
    }
```

![img](https://atts.w3cschool.cn/attachments/image/wk/shiro/4.png)

```java
// manager底层,使用authenticator来验证
return this.authenticator.authenticate(token);
// authenticator底层,使用realm验证
 AuthenticationInfo info = realm.getAuthenticationInfo(token);
```

### 2. 指定realm

Realm是shiro获取有关安全数据的地方,上一节使用的是IniRealm,显然不够开发需求

```java
public interface Realm {
    String getName(); // 获取realm名字

    boolean supports(AuthenticationToken var1);// 支不支持解析这个token

    AuthenticationInfo getAuthenticationInfo(AuthenticationToken var1) throws AuthenticationException;// 解析token
}
```

* 自定义realm

  * 实现Realm后在ini中配置,逗号分隔,有先后顺序

    ```ini
    #声明一个realm
    myRealm1=com.github.zhangkaitao.shiro.chapter2.realm.MyRealm1
    myRealm2=com.github.zhangkaitao.shiro.chapter2.realm.MyRealm2
    #指定securityManager的realms实现
    securityManager.realms=$myRealm1,$myRealm2
    ```

### 3. 指定验证策略

当拥有多个Realm时,底层会调用`AuthenticationStrategy`来验证

* 三个实现类:

  * **`FirstSuccessfulStrategy`**：只要有一个 `Realm` 验证成功即可，只返回第一个 `Realm` 身份验证成功的认证信息，其他的忽略；

    `AtLeastOneSuccessfulStrategy`：只要有一个 `Realm` 验证成功即可,返回所有 `Realm` 身份验证成功的认证信息(默认)

    `AllSuccessfulStrategy`：所有 `Realm` 验证成功才算成功，且返回所有 `Realm` 身份验证成功的认证信息，如果有一个失败就失败了。

* ```ini
  allSuccessfulStrategy=org.apache.shiro.authc.pam.AllSuccessfulStrategy
  securityManager.authenticator.authenticationStrategy=$allSuccessfulStrategy
  ```


## 三. 授权

### 1. 关键词

`Subject`: 主体, 可以狭义的理解成用户

`Resource`: 资源, 要访问的资源,比如页面

`Permission`: 权限

`Role`: 角色 , 权限的集合

* 隐式角色: 不明显区分角色,只按照主体分配权限
* 显示角色: 区分角色,将主体置于角色中

### 2. 判断权限

Shiro 支持三种方式的授权：

编程式：通过写 if/else 授权代码块完成：

```java
Subject subject = SecurityUtils.getSubject();
if(subject.hasRole(“admin”)) {
    //有权限
} else {
    //无权限
}
```

注解式：通过在执行的 Java 方法上放置相应的注解完成：

```java
@RequiresRoles("admin")
public void hello() {
    //有权限
}
```

### 3. 授予/判断角色

> 本节根据角色名来判断权限,属于隐式角色

iniRealm: 

```ini
[user]
zhang=123,role1,admin
用户名=密码,角色1,...
```

判断是否拥有角色:

```java
subject.hasRole("role1");//返回一个布尔值
subject.hasRoles(Arrays.asList("role1","role2"));//返回一个布尔数组
subject.hasRoles(Arrays.asList("role1","role2"));//返回一个布尔值
```

断言角色权限(抛出UnauthorizedException异常)

```java
subject.checkRole();
subject.checkRoles();
```



### 4. ini定义角色

> 本节起属于显示角色

```ini
[roles]
role1=user:create,user:update
role2="user:create",user:delete
角色名=权限1,权限2...
# 如果操作中有逗号,那权限就用引号括起
```

权限: 一般为`资源标识:操作`格式

### 5. 权限判断

采用`subject.isPermitted("权限字符串")`,`subject.isPermittedAll(Collection)`来判断是否符合权限

或者断言(抛出UnauthorizedException异常)

```java
//断言拥有权限：user:create
subject().checkPermission("user:create");
//断言拥有权限：user:delete and user:update
subject().checkPermissions("user:delete", "user:update");
//断言拥有权限：user:view 失败抛出异常
subject().checkPermissions("user:view");
```

### 6. 权限定义

#### 6.1 通配符

`资源:操作:实例`

通配符:(对于配置和验证都有效)

`:` 表示资源,操作,实例之间的分割

`,`表示操作之间的分割

`*` 表示任意资源/实例/操作

#### 6.2 通配符的例子

在判断中也一样,注意 system:user是资源,实例在前四个例子中没有表示出来

* 单个资源单个权限

  ```ini
  role1=system:user:update
  ```

* 单个资源多个权限(逗号分隔版本)

  ```ini
  role1=system:user:update,delete
  ; 就算是写了很多权限,对system:user:*也不能通过判断
  ```

* 单个资源所有权限

  ```ini
  role1=system:user:*
  ```

* 所有资源的n个权限

  ```ini
  role1=*:view,update
  ```

* 实例

  ```ini
  role=user:view:1
  role=*:view:1
  role=user:*:*
  ```

* 缺省

  默认没有写的就是`*`

### 7. 流程/原理/进阶

![img](https://atts.w3cschool.cn/attachments/day_210114/202101141719562904.png)

核心组件: `Authorizer`

组件: 

`Permission`: WildcardPermission 权限接口,定义了implies(Permission)来判断是否符合权限

`PermissionResolver`: 把字符串转换成Permission,默认使用 WildcardPermissionResolver

是**AuthorizingRealm**的属性,由ModularRealmAuthorizer调用来解析字符串

```java
public interface PermissionResolver {
    Permission resolvePermission(String permissionString);
}
```

`RolePermissionResolver`: 解析角色拥有的权限

通过身份信息获取AuthorizationInfo后,获取不到用户的ObjectPermission,StringPermission,就会调用RolePermissionResolver来解析角色权限(然后我嗯是找不到它的实现类)

```java
public interface RolePermissionResolver {
    Collection<Permission> resolvePermissionsInRole(String var1);
}
```

```ini
[main]
\#自定义authorizer
authorizer=org.apache.shiro.authz.ModularRealmAuthorizer
\#自定义permissionResolver
\#permissionResolver=org.apache.shiro.authz.permission.WildcardPermissionResolver
permissionResolver=com.github.zhangkaitao.shiro.chapter3.permission.BitAndWildPermissionResolver
authorizer.permissionResolver=$permissionResolver
\#自定义rolePermissionResolver
rolePermissionResolver=com.github.zhangkaitao.shiro.chapter3.permission.MyRolePermissionResolver
authorizer.rolePermissionResolver=$rolePermissionResolver
securityManager.authorizer=$authorizer
\#自定义realm 一定要放在securityManager.authorizer赋值之后（因为调用setRealms会将realms设置给authorizer，并给各个Realm设置permissionResolver和rolePermissionResolver）
realm=com.github.zhangkaitao.shiro.chapter3.realm.MyRealm
securityManager.realms=$realm
```

## 四. 配置的本质

到现在为止,我们用配置做这些事:

IniRealm,还有给SecurityManager组件及其子组件注入; **本质就是调用他们的setter来赋值POJO**,和Spring的IOC十分相似;

## 五. 加密



# Shiro源码观察(ini)

## 一. ini读取

```java
new IniSecurityManagerFactory("classpath:shiro.ini");
//这一步其实只是创建了一个工厂,然后将Ini载入到工厂的属性中
```

所以我们可以看看Ini的读取: 

### 1. 调用栈

```java
Ini.fromResourcePath(iniResourcePath);// 利用静态方法读取
ini.loadFromPath(resourcePath); // 调用非静态方法给ini加载内容
this.load(is); // ini加载InputStream
this.load((Reader)isr); //ini包装InputStream为Reader读取
this.load(scanner); // 包装为Scanner
```

### 3. ini数据结构

```java
private final Map<String, Ini.Section> sections;
```

`Section`: ini中会有以`[]`分隔的区域,而[]中的内容就是后面几行的section

`sections`: 即存储`section名-内容`的映射,是`LinkedHashMap`

* `Ini.Section`: Map<String,String\>实现类,维护了一个LinkedHashMap<String,String\>

  

### 4. ini读取核心: 添加Section

```java
while(scanner.hasNextLine()) {
	//...
    //读取一行并clean处理
    if (line != null && !line.startsWith("#") && !line.startsWith(";")) {
        // 判断是不是[]型的一行,如果是就迎接新的section
        String newSectionName = getSectionName(line); 
        if (newSectionName != null) {
            //遇到新的section,就把旧section放进map中,后面详解
            this.addSection(sectionName, sectionContent);
            // 清空名字和内容
            sectionContent = new StringBuilder();
            sectionName = newSectionName;
            //..日志,省略
        } else {
            //不是section头就嗯加内容
            sectionContent.append(rawLine).append("\n");
        }
    }
}
```

### 5. ini读取核心: 解析section内容

```java
Ini.Section section = new Ini.Section(name, contentString);
// 对Section内容解析由构造器完成
```

核心代码:

```java
private static Map<String, String> toMapProps(String content) {
    Map<String, String> props = new LinkedHashMap();
    StringBuilder lineBuffer = new StringBuilder();
    // 注意,它把字符串放进Scanner来一行一行读
    Scanner scanner = new Scanner(content);

    while(scanner.hasNextLine()) {
        String line = StringUtils.clean(scanner.nextLine());
        if (isContinued(line)) { // 判断这一行是否和下一行连续(即判断末尾反斜杠是否为奇数)
            line = line.substring(0, line.length() - 1);
            lineBuffer.append(line); // 连接
        } else {
            lineBuffer.append(line);
            line = lineBuffer.toString();
            lineBuffer = new StringBuilder();
            // 简单地说就是以:或者=分隔key和value
            String[] kvPair = splitKeyValue(line);
            props.put(kvPair[0], kvPair[1]);
        }
    }

    return props;
}
```

## 一+. 创建SecurityManager

### 1. 

如果ini读入为空,则返回一个`DefaultSecurityManager`实例

如果ini有内容,

**调用栈:**

```java
instance = this.createInstance(ini);
SecurityManager securityManager = this.createSecurityManager(ini);
return this.createSecurityManager(ini, mainSection);
// mainSection: 如果ini有[main],即为main,不然为空串

```

```java
private SecurityManager createSecurityManager(Ini ini, Section mainSection) {
    //这里返回了一个map,里面首先有DefaultSecurityManager
    //然后若ini里有roles或者users section,则会再放一个IniRealm
    Map<String, ?> defaults = this.createDefaults(ini, mainSection);
    // 把他们和EventBus放进ReflectionBuilder中,然后处理一些配置?(里面看不懂了)
    Map<String, ?> objects = this.buildInstances(mainSection, defaults);
    // 把刚刚处理好的DefaultSecurityManager拿出来
    SecurityManager securityManager = this.getSecurityManagerBean();
    //判断有没有realm了,没有的话就把之前配置的加进来
    boolean autoApplyRealms = this.isAutoApplyRealms(securityManager);
    if (autoApplyRealms) {
        Collection<Realm> realms = this.getRealms(objects);
        if (!CollectionUtils.isEmpty(realms)) {
            this.applyRealmsToSecurityManager(realms, securityManager);
        }
    }

    return securityManager;
}
```

### 2. DefaultSecurityManager构造栈

这个SecurityManager虽然叫默认,但是不简单

<img src="../pics/shiro/image-20210329205244229.png" alt="image-20210329205244229" style="zoom:67%;" />

#### 2.1 SecurityManager接口

```java
public interface SecurityManager extends Authenticator, Authorizer, SessionManager {
    //这三个是SecurityManager带的方法
    Subject login(Subject var1, AuthenticationToken var2) throws AuthenticationException;

    void logout(Subject var1);

    Subject createSubject(SubjectContext var1);
    
    //这是sessionManager的方法
    Session start(SessionContext var1);

    Session getSession(SessionKey var1) throws SessionException;
    
    // Authenticator验证器的方法
    AuthenticationInfo authenticate(AuthenticationToken var1) throws AuthenticationException;
    
    //Authorisor方法
    //...一堆hasRole,checkRole,isPermitted方法
}
```

#### 2.2 CachingSecurityManager

空参构造器设置一个`DefaultEventBus`,此时CacheManager被声明了却没有被赋值

#### 2.3 RealmSecurityManager

声明了一个Realm集合,什么也没做

#### 2.4 AuthenticatingSecurityManager

给类中加入了` Authenticator`

```java
private Authenticator authenticator = new ModularRealmAuthenticator();
```

#### 2.5 AuthorizingSecurityManager

给类中加入了`Authorizer`

```java
private Authorizer authorizer = new ModularRealmAuthorizer();
```

#### 2.6 SessionsSecurityManager

给类中增加了`SessionManager`

```
private SessionManager sessionManager = new DefaultSessionManager();
```

## 一++. 创建Subject

就是用SecurityManager的方法创建一个,传入一个平平无奇的SecurityContext

在DefaultSecurityManager中会给它做包装,

返回一个`DelegatingSubject`,并给他设置默认状态

并将subject保存在SubjectDao中

## 二. 登录

```java
public void login(AuthenticationToken token) throws AuthenticationException {
    //清除身份信息
    this.clearRunAsIdentitiesInternal();
    //使用securityManager来登录
    Subject subject = this.securityManager.login(this, token);
    String host = null;
    PrincipalCollection principals;
    if (subject instanceof DelegatingSubject) {
        DelegatingSubject delegating = (DelegatingSubject)subject;
        principals = delegating.principals;
        host = delegating.host;
    } else {
        principals = subject.getPrincipals();
    }

    if (principals != null && !principals.isEmpty()) {
        this.principals = principals;
        this.authenticated = true;
        if (token instanceof HostAuthenticationToken) {
            host = ((HostAuthenticationToken)token).getHost();
        }

        if (host != null) {
            this.host = host;
        }

        Session session = subject.getSession(false);
        if (session != null) {
            this.session = this.decorate(session);
        } else {
            this.session = null;
        }

    } else {
        String msg = "Principals returned from securityManager.login( token ) returned a null or empty value.  This value must be non null and populated with one or more elements.";
        throw new IllegalStateException(msg);
    }
}
```

