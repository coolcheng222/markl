# Netty

## 一. 介绍

Netty是由JBOSS提供的JAVA开源框架,是一个**异步基于事件驱动**的网络应用框架,主要针对在TCP协议下,面向Clients端的高并发应用,用以快速开发高性能,高可靠性的IO程序

基于NIO,NIO包含JDK原生WebClient,原生WebClient操作TCP/IP层

## 二. IO模型

### 1. 网络编程IO模型

`BIO`即**同步阻塞**IO,java原生的IO模型

客户端有连接请求就启动一个线程进行处理,如果这个链接不做任何事情就会造成不必要的线程开销

`NIO`是**同步非阻塞**的,实现模式是一个线程处理多个请求,线程维护一个多路复用选择器轮询,<u>多路复用</u>

`AIO`: 异步非阻塞

### 2. BIO编码

```java
package com.at.bio;


import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * @author sealll
 * @time 2021/8/13 14:39
 */
public class BioServer {
    public static void main(String[] args) throws IOException {
        //创建一个线程池
        ExecutorService executorService = Executors.newCachedThreadPool();

        //创建ServerSocket
        ServerSocket serverSocket = new ServerSocket(6666);

        System.out.println("服务器启动");

        while(true){
            //监听
            final Socket socket = serverSocket.accept();
            System.out.println("连接到一个客户端");

            executorService.execute(()->{
                handler(socket);
            });
        }
    }
    public static void handler(Socket socket){
        byte[] bytes = new byte[1024];
        //通过socket获取输入流
        try {
            InputStream inputStream = socket.getInputStream();
            while(true){
                int read = inputStream.read(bytes);
                if(read == -1){
                    break;
                }else{
                    System.out.println(new String(bytes,0,read));
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            System.out.println("关闭连接");
            try{
                socket.close();
            }catch (IOException e) {
                e.printStackTrace();
            }
        }
    }


}
```

## 三. NIO

面向缓冲区编程

### 1. 组件

`Channel`: 通道负责读写,从buffer读入数据给server,或者写入buffer发给客户端

`Buffer`: 客户端与Buffer交互

`Selector`: 服务器每个线程维护多个channel,由选择器选择通道; 

非阻塞: 线程不会被阻塞,其可以一次处理一个通道,若所有通道挂起,线程可以做其他事情