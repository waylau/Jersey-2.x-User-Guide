4.5. Java SE Deployment Environments 部署在 Java SE 环境
========================

##4.5.1. HTTP 服务器

基于 Java 的 HTTP 服务器展现了一种简约、灵活的部署 Jersey 应用程序的方式。HTTP 服务器通常是嵌入在应用程序中，并通过配置,以编程形式来启动。一般来说,Jersey 容器为特定的 HTTP 服务器提供了一个定制化的工厂方法，用来返回一个正确初始化的 HTTP 服务器实例。

###4.5.1.1. JDK Http Server

从 Java SE 6 开始,Java 运行时附带一个内置的轻量级的 HTTP 服务器。Jersey 通过  jersey-container-jdk-http 容器扩展模块，提供集成这个 Java SE HTTP 服务器。此时，不是直接创建 [HttpServer](http://docs.oracle.com/javase/6/docs/jre/api/net/httpserver/spec/com/sun/net/httpserver/HttpServer.html) 实例,而是使用 [JdkHttpServerFactory](https://jersey.java.net/apidocs/2.21/jersey/org/glassfish/jersey/jdkhttp/JdkHttpServerFactory.html) 的 createHttpServer()方法,它根据 Jersey 容器配置和 Application 子类提供的初始化来创建 HttpServer 实例 。

创建一个 内嵌 Jersey 的jdk http server 非常简单:

Example 4.5. 使用 Jersey 和 JDK HTTP Server

	URI baseUri = UriBuilder.fromUri("http://localhost/").port(9998).build();
	ResourceConfig config = new ResourceConfig(MyResource.class);
	HttpServer server = JdkHttpServerFactory.createHttpServer(baseUri, config);

JDK HTTP 容器依赖：

	<dependency>
	    <groupId>org.glassfish.jersey.containers</groupId>
	    <artifactId>jersey-container-jdk-http</artifactId>
	    <version>2.21</version>
	</dependency>

###4.5.1.2. Grizzly HTTP Server

[Grizzly](http://grizzly.java.net/) 是一个建立在 Java [NIO](http://docs.oracle.com/javase/6/docs/api/java/nio/package-summary.html) 之上的支持多协议的框架。Grizzly 旨在简化强大的和可扩展的服务器开发。Jersey 提供了一个容器的扩展模块，可以使用 Grizzly 作为运行 JAX-RS 应用普通的 HTTP 容器支持。从 Grizzly 服务器运行 JAX-RS 或 Jersey 的应用是一种最轻量和最容易的方法，用来展现 RESTful 服务。

Grizzly 容器支持 HTTP 注射 Grizzly 的特性 org.glassfish.grizzly.http.server.Request 和org.glassfish.grizzly.http.server.Response 实例到 JAX-RS 和Jersey 应用资源和供应者。然而，由于 Grizzly 的 Request 是非代理性的，Grizzly Request的注入到单例（默认）的JAX-RS /和Jersey 提供者只可能通过 javax.inject.Provider 实例。（Grizzly Response会遭受同样的限制。）

Example 4.6. 使用 Jersey 和 Grizzly HTTP Server

	URI baseUri = UriBuilder.fromUri("http://localhost/").port(9998).build();
	    ResourceConfig config = new ResourceConfig(MyResource.class);
	    HttpServer server = GrizzlyHttpServerFactory.createHttpServer(uri, config);

容器扩展模块依赖要加入:

	<dependency>
	    <groupId>org.glassfish.jersey.containers</groupId>
	    <artifactId>jersey-container-grizzly2-http</artifactId>
	    <version>2.21</version>
	</dependency>

**注意**：通过[测试框架](https://jersey.java.net/documentation/latest/test-framework.html)， Jersey 使用 Grizzly 已经广泛的在项目单元和端到端进行了测试。

###4.5.1.3. Simple 服务器

[Simple](http://www.simpleframework.org/) 是一个框架允许开发者创建 HTTP 服务器，并嵌入到应用中。同样的，通过从  jersey-container-simple-http 容器扩展模块调用工厂方法实现创建服务器实例。

Simple 的框架支持 HTTP 容器注入 Simple 框架特性 的org.simpleframework.http.Request 和 org.simpleframework.http.Response 实例到 JAX-RS 和 Jersey 应用资源和供应者。

Example 4.7. 使用 Jersey 和 Simple 框架

	URI baseUri = UriBuilder.fromUri("http://localhost/").port(9998).build();
	    ResourceConfig config = new ResourceConfig(MyResource.class);
	    SimpleContainer server = SimpleContainerFactory.create(baseUri, config);

容器扩展模块依赖要加入:
 
	<dependency>
	    <groupId>org.glassfish.jersey.containers</groupId>
	    <artifactId>jersey-container-simple-http</artifactId>
	    <version>2.21</version>
	</dependency>

**注意**：Simple HTTP 容器不支持部署在除了根路径是 ("/")以外的上下文路径。非根路径的上下文路径在部署中是被忽略的。

###4.5.1.4. Jetty HTTP Server

Jetty 是流行的 Servlet 容器和 HTTP 服务器。在此我们不深究 Jetty 作为 Servlet 容器的能力（尽管我们在我们的测试和实例使用它），因为作为基于 Servlet 部署模型并没有什么特别，具体会在 第4.7节，“[基于 Servlet 部署](https://jersey.java.net/documentation/latest/deployment.html#deployment.servlet)”部分进行描述。我们将在这里只重点描述如何使用 Jetty 的 HTTP 服务器。

Jetty HTTP 容器支持注入 Jetty 特性的org.eclipse.jetty.server.Request 和 org.eclipse.jetty.server.Response 实例到 JAX-RS 和 Jersey 应用资源和供应者。然而，由于 Jetty HTTP Request 是非代理性的，Jetty Request 的注入到单例（默认）的JAX-RS /和Jersey 提供者只可能通过 javax.inject.Provider 实例。（Jetty Response 会遭受同样的限制。）

Example 4.8. 使用 Jersey 和 Jetty HTTP Server


	URI baseUri = UriBuilder.fromUri("http://localhost/").port(9998).build();
	ResourceConfig config = new ResourceConfig(MyResource.class);
	Server server = JettyHttpContainerFactory.createServer(baseUri, config);

容器扩展模块依赖要加入（**译者注：**原文中依赖包有误，这里做了更正）:

	<dependency>
		<groupId>org.glassfish.jersey.containers</groupId>
		<artifactId>jersey-container-jetty-http</artifactId>
	    <version>2.21</version>
	</dependency>

**注意**：Jetty HTTP 容器不支持部署在除了根路径是 ("/")以外的上下文路径。非根路径的上下文路径在部署中是被忽略的。
