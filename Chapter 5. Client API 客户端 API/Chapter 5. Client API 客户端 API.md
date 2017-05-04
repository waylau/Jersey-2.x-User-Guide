Chapter 5. Client API 客户端 API
========================

本章提供了一个如何开始使用Jersey构建RESTful服务的快速介绍。这里描述的示例使用轻量级的Grizzly HTTP服务器。在本章的最后你将看到如何实现相同的功能的JavaEE的Web应用程序，该程序可以部署在任何支持Servlet 2.5和更高版本的servlet容器里面。

*译者注*：本章所有例子的源码，可以在[https://github.com/waylau/Jersey-2.x-User-Guide-Demos](https://github.com/waylau/Jersey-2.x-User-Guide-Demos) 获取到。

本节介绍了  JAX-RS 客户端 API,这是一个基于Java API 流利沟通的RESTful Web 服务。这个标准的 API,也属于Java EE 7 目的是使它很容易通过 HTTP 协议消费 Web 服务,使开发人员能够简明、高效地实现移动客户端解决方案,利用现有的 HTTP 连接器和完善客户端实现

JAX-RS 客户端 API 可以用来消费任何暴露的 HTTP 协议或它的扩展(例如WebDAV),和并不局限于服务使用 JAX-RS 实现。然而,熟悉 JAX-RS 应该找到客户端 API 的开发人员服务的补充,特别是如果客户端 API 是利用这些服务,这些服务或测试。JAX-RS 客户端API 专有Jersey.x 客户机API和开发人员熟悉 Jersey.x 客户机API 应该发现它容易理解所有的概念引入新的 JAX-RS 客户端API。

客户端 API 的目标是3个:

1.封装的关键约束 REST 架构风格,即统一接口约束和相关数据元素,如客户端 Java 工件;

2.使它容易通过暴露 HTTP 来消费 RESTful Web 服务,一样 JAX-RS 服务器端 API 很容易开发 RESTful Web 服务;

3.有共同的概念和 JAX-RS API的扩展点之间的服务器和客户端编程模型。

作为标准 JAX-RS 客户端 API 扩展,Jersey 客户端 API 支持可插拔的体系结构允许使用不同的底层实现 HTTP 客户端[连接器](https://jersey.java.net/apidocs/2.13/jersey/org/glassfish/jersey/client/spi/Connector.html)。几个这样的实现目前 都由 Jersey 提供。我们有一个默认客户端连接器使用Http(s)URLConnection提供JDK以及连接器实现基于Apache Http客户机,Jetty Http 客户端和 Grizzly  异步客户端。