---
layout: post
title: JAVA中高级知识点
published: true
---
# 接口和抽象类区别？
1.语法层次
2.设计层次
接口是自顶向下的设计，抽象类是自底向上的抽象。
接口是对行为的抽象，抽象类是对整个类（除了行为之外的属性、逻辑）的抽象。
3.使用领域
抽象类用于is-a
接口用于like-a
<!--more-->
# java中的代理机制，AOP实现方式
cglib动态代理:MethodInterceptor
jdk动态代理:InvocationHandler

# 多线程 锁
1.Runnable\Thread
2.ExecutorService、Callable、Future、concurrent
3.synchronized\Lock\volatile

# volatile 轻量同步：有可见性、不具备原子性

## BIO NIO AIO （高效并发、阻塞）
BIO\NIO:BIO基于字节流和字符流进行操作，而NIO基于Channel和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。
同步、异步：
表现是否交付第三方处理

在并发时同步需等待前一任务执行完毕，异步可多路同时执行

BIO:
同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。

BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解。

NIO:
同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。（因为从流改为缓冲操作。但受缓冲区容量限制，因此对于重数据会因处理不过来爆掉）

AIO:
异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。

AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。