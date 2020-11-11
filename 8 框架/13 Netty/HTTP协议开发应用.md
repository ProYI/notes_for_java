### HTTP协议开发应用

<h4> HTTP协议介绍</h4>

HTTP（超文本传输）协议是建立在TCP传输协议之上的应用层协议

HTTP是一个属于应用层的面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统

HTTP协议是目前Web开发的主流协议，基于HTTP的应用非常广泛。Netty的HTTP协议栈是基于Netty的NIO通信框架开发的，因此Netty的HTTP协议也是异步非阻塞的

<h5>HTTP协议的主要特点</h5>

1、支持Client/Server模式

2、简单----客户端向服务器请求服务时，只需指定服务URL，携带必要的请求参数或者消息体

3、灵活----HTTP允许传输任意类型的数据对象，传输的内容类型与HTTP消息头中的Content-Type加以标记

4、无状态----HTTP协议是无状态协议，无状态是指协议对于事务处理没有记忆能力

```
缺失状态意味着如果后续处理需要之前的信息，它必须重传，可能导致每次连接传送的数据量增大；
另一方面，在服务器不需要先前信息时，它的应答就较快，负载较轻
```

<h5>HTTP协议的URL</h5>

HTTP URL（URL是一种特殊类型的URI，包含了用于查找某个资源的足够的信息）的格式

```html
http://host:[":"port][abs_path]
```

http表示通过HTTP协议来定位网络资源

host表示合法的Internet主机域名或者IP地址

port指定一个端口号，为空则使用默认端口80

abs_path指定请求资源的URI，如果URL中没有给出abs_path，那么当它作为请求URI时，必须以`/`的形式给出，通常浏览器会自动帮忙完成

<h5>HTTP请求消息（HttpRequest）</h5>

HTTP请求由三部分组成：

* HTTP请求行
* HTTP消息头
* HTTP请求正文

请求行以一个方法符开头，以空格分开，后面跟着请求的URI和协议的版本，格式为：

```
Method Request-URI Http-Version CRLF
```

Method表示请求方法

Request-URI是一个统一资源标识符

HTTP-Version表示请求的HTTP协议版本

CRLF表示回车和换行

<h5>HTTP响应消息（HttpResponse）</h5>

HTTP响应由三部分组成：

* 状态行
* 消息报头
* 响应正文