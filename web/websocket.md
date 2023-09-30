# WebSocket

## 一. 介绍

### 1. 本质

`WebSocket`是HTML5实现的**单TCP连接**的**全双工 应用层协议**

### 2. 解决的问题

最重要的问题即 数据的实时刷新 过去有几种方式:

* **客户端轮询:** 即客户端每隔一段时间发一次轮询
* __长轮询__: 即服务端不立即回复,等到有数据再返回
* __WebSocket__

## 二. 连接流程

### 1. 前置Http请求

首先是TCP三次握手,握手完毕后客户端发送第一请求,是一个HTTP请求

请求头中含有以下内容:

```http
Connection:Upgrade
Upgrade:websocket
```

用于通知Http服务器及时切换协议

服务器返回__101__表示理解请求

### 2. 数据传输

本质就是TCP的传输, ACK确认加上数据长度,想要结束就添加一个FIN完成四次挥手

## 三. js API

### 1. 构造器

```js
new WebSocket(url, [protocol] );
//主要是url,要和服务器的websocket Endpoint对上
```

### 2. 属性

`readyState`:

- 0 - 表示连接尚未建立。
- 1 - 表示连接已建立，可以进行通信。
- 2 - 表示连接正在进行关闭。
- 3 - 表示连接已经关闭或者连接不能打开。

`bufferedAmount`:表示send放进缓冲区没发出去的字节数

### 3. 事件

`onopen`: 连接时触发,参数event(readystate 0->1)

`onmessage`: 接收消息时触发,参数event,数据为event.data

`onerror`: 发生错误触发

`onclose`: 关闭时触发

### 4. 方法

`send(data)`向另一端发送数据

data: 必须是 文本字符串,ArrayBuffer,blob,ArrayBufferView 之一

`close()`

## 四. javax api

javax提供的websocket API符合__JSR 356标准__

我们主要介绍其作为WebSocket服务器的行为

### 1. 依赖

```xml
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
    <version>1.1</version>
</dependency>
```

### 2. 端点配置

[官方对EndPoint的注释](./websocket_end.md)

配置类似于三大组件,有注解形式和继承形式两种

#### 2.1 继承形式(理解即可)

```java
public abstract class Endpoint {
    public abstract void onOpen(Session session, EndpointConfig config);
    public void onClose(Session session, CloseReason closeReason) {
    }
    public void onError(Session session, Throwable thr) {
    }
}
```

注意: 这个Session是webSocket的内容,包含websocket的各种信息,可以添加`MessageHandler`

```java
public class MyEndPoint extends Endpoint{
    @Override
    public void onOpen(Session session, EndpointConfig config) {
        System.out.println("opending");
        //信息处理,在onOpen中声明
        session.addMessageHandler(new MessageHandler.Whole<String>() {
            @Override
            public void onMessage(String message) {
                System.out.println(message);
            }
        });
    }

    @Override
    public void onClose(Session session, CloseReason closeReason) {
        System.out.println("closing");
    }

    @Override
    public void onError(Session session, Throwable thr) {
        System.out.println("erroring");
    }
}
```

注册信息,在`ServerApplicationConfig`实现类中: 该实现类在war包/jar包中会被tomcat自动扫描到

```java

public Set<ServerEndpointConfig> getEndpointConfigs(Set<Class<? extends Endpoint>> endpointClasses);

Set<Class<?>> getAnnotatedEndpointClasses(Set<Class<?>> scanned);
```

利用ServerEndpointConfig.Builder的静态方法创建ServerEndpointConfig的构建器并构建

```java
@Override
public Set<ServerEndpointConfig> getEndpointConfigs(Set<Class<? extends Endpoint>> endpointClasses) {
    HashSet<ServerEndpointConfig> set = new HashSet<>();
    for (Class<? extends Endpoint> endpointClass : endpointClasses) {
        //这里create中指定url,路径从contextPath后开始
        ServerEndpointConfig res = ServerEndpointConfig.Builder.create(endpointClass, "/wuziqi/wsocket").build();
        set.add(res);
    }
    return set;
}
```

完全看不懂怎么放进Spring容器,可能借助initializer吧

**发消息:** (对于两种方法通用时)

1. 从连接中获取`Session`

    如果你需要发送信息, 需要在OnOpen阶段将Session存储下来,以便其他方法使用

2. 使用 `Session` 获取 `RemoteEndpoint` 

   `Session.getBasicRemote` 方法 `Session.getAsyncRemote` 方法分别返回`RemoteEndpoint.Basic` 和 `RemoteEndpoint.Async`  .`RemoteEndpoint.Basic` 会阻塞消息和方法 `RemoteEndpoint.Async` 是异步的;

3. 使用 `RemoteEndpoint` 发消息

   以下几个方法对于Basic和Async通用

   - `void RemoteEndpoint.Basic.sendText(String text)`

   - `void RemoteEndpoint.Basic.sendBinary(ByteBuffer data)`

   - `void RemoteEndpoint.sendPing(ByteBuffer appData)`

   - `void RemoteEndpoint.sendPong(ByteBuffer appData)`

示例: 

```java
@ServerEndpoint("/echoall")
public class EchoAllEndpoint {
   @OnMessage
   public void onMessage(Session session, String msg) {
      try {
         for (Session sess : session.getOpenSessions()) {
            if (sess.isOpen())
               sess.getBasicRemote().sendText(msg);
         }
      } catch (IOException e) { ... }
   }
}
```

### 2.2 注解形式

注解形式更容易编写,也更容易整合spring

```java

@ServerEndpoint(value="/wuziqi/wsocket")
public class MyEndPoint2 {
    @OnOpen
    public void onOpen(Session s){
        System.out.println("opening");
    }
    @OnMessage
    public void onMessage(String message){
        System.out.println(message);
    }
}
```

## 五. 注解javax API

### 1. ServerEndpoint

注解,加在所实现的`Endpoint`上

* 参数:

  * `value`: 即url,从contextPath下开始,支持路径参数

  * ```java
    ServerEndpoint("/chat")
    ServerEndpoint("/chat/{user}")
    ServerEndpoint("/booking/{privilege-level}")
    ```

  * `subprotocols`: 子协议

  * `encoder/decoder`: 编码解码器,后面细说

  * `configurator`: ServerEndpointConfig.Configurator的子类,可以自己定制,

#### 1.1 路径参数

ServerEndPoint的value支持类似Spring路径变量的路径参数(本质是RFC6570语法)

声明: `xxx/xxx/{变量名}/xxx`

使用: 在所有监听函数中都可以以参数传入,参数增加注解`@PathParam(变量名)`即可,可以使用多个

### 1.2 消息的类型/转换

Java能接受和发送以下类型的消息:

`String`: 字符串

`ByteBuffer`: 二进制数据

`Object`: 需要经过编码/解码转换

`Ping/Pong`: 健康检查帧

其中只有Object需要进行转换

* __Encoder__:

Encoder有四个子接口供使用: 

`Encoder.Text<T>`: 将T转换为字符串

`Encoder.Binary<T>`: 将T转换为ByteBuffer

`Encoder.TextStream<T>`: 将 T转换为Writer

`Encoder.BinaryStream<T>`: 将T转换为OutputStream

我们可以自己编写它们的实现类并注册进ServerEndpoint

* __Decoder__: 其实同理

#### 1.4 监听方法

所有方法都可以加任意路径参数,以下是它们的其他参数:

`@OnOpen`:  可选Session,可选EndpointConfig

`@OnClose`: 可选Session,可选CloseReason

`@OnError`: 可选Session,必须Throwable

`@OnMessage`: 一个消息参数,可选Session

消息参数: 对于文本有`String`,`Reader(有Decoder.TextStream的情况下)`; 对于二进制有`ByteBuffer`,`InputStream`,对于pong有`PongMessage`

注意: 在方法结束后流将被清除

#### 1.5 消息发送

和上面的那个完全一致,会用到Decoder/Encoder

## 六. 整合Spring

先前都是由Servlet容器对ServerApplicationConfig进行扫描获取,和Spring关系不大

### 1. 基本配置

1. 设置`ServerEndpoint`的`configurator`为__SpringConfigurator__

2. 关闭servlet扫描: 将Endpoint实现和`ServerEndpointExporter`放进容器; 另外可以不把endpoin放进容器,可以通过 `EndpointRegistration` 部署

   ```java
   @Bean
   public EndpointRegistration echoEndpoint() {
       return new EndpointRegistration("/echo", EchoEndpoint.class);
   }
   ```

### 2. 注意点

Spring的组件普遍是单例,但是WebSocket并不是这样,所以我们不能期待着从Spring中Autowired一个Endpoint;

如果需要发送消息,我们应该维护一个静态的Endpoint列表并提供发送(send)方法

如果需要注入组件,我们最好用ApplicationContext自己注入

~~所以为什么要整合spring呢~~

## 七. **Spring WebSocket API**

//TODO