4.7. Servlet-based Deployment 基于 Servlet 的部署
========================

在一个 Servlet 容器，JAX-RS 定义了多个部署选项，针对不同的Servlet 容器所支持 Servlet API 的版本。下面的部分详细描述了这些选项。

##4.7.1. Servlet 2.x Container

Jersey 集成 Servlet 容器支持至少 Servlet 2.5 规范。运行在 Servlet API 3.0 或者更高版本的容器，会带给你运行更多的功能特性（特别是异步请求处理支持）和更方便、更灵活的部署选项。在这节内容，我们注意力集中在基本部署模型 Servlet 2.5 或之上版本的容器。

在 Servlet 2.5 环境，你要明确声明 Jersey 容器 Servlet 在你的 Web 应用的 web.xml 部署描述符文件中。

Example 4.9. 将 Jersey 当做 Servlet

	<web-app>
	    <servlet>
	        <servlet-name>MyApplication</servlet-name>
	        <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
	        <init-param>
	            ...
	        </init-param>
	    </servlet>
	    ...
	    <servlet-mapping>
	        <servlet-name>MyApplication</servlet-name>
	        <url-pattern>/myApp/*</url-pattern>
	    </servlet-mapping>
	    ...
	</web-app>

或者，你可以注册 Jersey 容器过滤器

Example 4.10. 将 Jersey 当做 Servlet Filter

	<web-app>
	    <filter>
	        <filter-name>MyApplication</filter-name>
	        <filter-class>org.glassfish.jersey.servlet.ServletContainer</filter-class>
	        <init-param>
	            ...
	        </init-param>
	    </filter>
	    ...
	    <filter-mapping>
	        <filter-name>MyApplication</filter-name>
	        <url-pattern>/myApp/*</url-pattern>
	    </filter-mapping>
	    ...
	</web-app>

`<init-param>` 元素内容将取决于你如何决定资源配置不同的 Jersey 资源。

###4.7.1.1. 自定义 Application 子类

如果你的继承 [Application](http://jax-rs-spec.java.net/nonav/2.0/apidocs/javax/ws/rs/core/Application.html) 类来提供有关根资源类的列表（getresources()）和单身（getsingletons()），即你的 JAX-RS 应用模型，然后你需要注册一个 javax.ws.rs.Application [原名]  名称的 Servlet 或 Servlet 过滤器作为 web 应用程序的初始化参数，在 web.xml 中进行部署描述：

Example 4.11.  配置 Jersey 容器 Servlet 或者 过滤器来自定义 Application 子类

	<init-param>
	    <param-name>javax.ws.rs.Application</param-name>
	    <param-value>org.foo.MyApplication</param-value>
	</init-param>

Jersey 将考虑所有 Application 实现的  getClasses() 和 getSingletons() 方法的返回。

**注意**：JAX-RS 规范定义的配置名称确实是 javax.ws.rs.Application 和不是 javax.ws.rs.core.Application可想而知。

###4.7.1.2. Jersey 扫描包

如果配置属性无需设置，要部署应用程序只包括存储在特定的包的资源和提供者，那么你可以指示 Jersey 自动扫描这些包，这样就能自动注册找到的任何资源和提供者：

Example 4.12. 配置 Jersey 的 Servlet 或者 Filter 来扫描包

	<init-param>
	    <param-name>jersey.config.server.provider.packages</param-name>
	    <param-value>
	        org.foo.myresources,org.bar.otherresources
	    </param-value>
	</init-param>
	<init-param>
	    <param-name>jersey.config.server.provider.scanning.recursive</param-name>
	    <param-value>false</param-value>
	</init-param>

Jersey 将会自动发现被选中的资源和提供者。你可以通过设置 jersey.config.server.provider.scanning.recursive 属性来决定 Jersey 是否扫描子包。默认值是 true , 即启用递归扫描子包。

###4.7.1.3. 选择具体的资源和提供者类

上述包扫描是开发和测试是非常有用，而在生产部署环境中，你可能想要有更多一点的控制枚举特定资源和提供者类。在 Jersey 就有可能达到甚至不需要实现一个自定义 Application 子类。特定的资源和提供者的完全限定类名可以是一个逗号分隔的 jersey.config.server.provider.classnames 初始化参数提供的值。

Example 4.13. 配置 Jersey 的 Servlet 或者 Filter 来使用类的列表

	<init-param>
	    <param-name>jersey.config.server.provider.classnames</param-name>
	    <param-value>
	        org.foo.myresources.MyDogResource,
	        org.bar.otherresources.MyCatResource
	    </param-value>
	</init-param>

**注意**：所有已在本部分也适用于支持 Servlet API 3 和以后的技术规范 Servlet 容器。新的 Servlet 规范只给你额外的功能，以及更灵活的部署选项。

##4.7.2. Servlet 3.x Container

###4.7.2.1. 无描述的部署

一个实现了 [Application](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Application.html) 子类的  JAX-RS 应用在 Servlet 3.0 容器中将会有很多部署选项。对于简单的部署，web.xml不是必须的。相反，一个 [@ApplicationPath](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/ApplicationPath.html) 注释可用于自定义  [Application](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Application.html) 子类和给给所有配置中的 JAX-RS 资源定义基础应用程序 URI：

Example 4.14. 在 JAX-RS 应用部署中使用 @ApplicationPath 和 Servlet 3.0

	@ApplicationPath("resources")
	public class MyApplication extends ResourceConfig {
	    public MyApplication() {
	        packages("org.foo.rest;org.bar.rest");
	    }
	}

**注意**：在 ResourceConfig 很多其他的便利方法可用于您的自定义类的构造函数来配置您的JAX-RS应用，详见 [ResourceConfig](https://jersey.java.net/apidocs/2.21/jersey/org/glassfish/jersey/server/ResourceConfig.html) API文档。

如果你不为你的基于 Maven 的Web 应用项目提供的 web.xml 部署描述文件，你需要配置你的 maven-war-plugin 插件来忽略缺失的 web.xml 文件，通过设置 failOnMissingWebXml 配置属性为 false 在你的项目的pom.xml 文件中：

Example 4.15. 配置你的 maven-war-plugin 插件来忽略缺失的 web.xml

	<plugins>
	    ...
	    <plugin>
	        <groupId>org.apache.maven.plugins</groupId>
	        <artifactId>maven-war-plugin</artifactId>
	        <version>2.3</version>
	        <configuration>
	            <failOnMissingWebXml>false</failOnMissingWebXml>
	        </configuration>
	    </plugin>
	    ...
	</plugins>

###4.7.2.2. 通过 web.xml 部署

另一种 Servlet 3.x 容器的部署模型是在 web.xml 声明 JAX-RS 应用细节。这通常是适用于更复杂的部署，例如当安全模型需要适当的定义或额外的初始化参数被传递到 Jersey 的运行时。JAX-RS 1.1 和以后的指定完全限定名称，实现[Application](http://jax-rs-spec.java.net/nonav/2.0/apidocs/javax/ws/rs/core/Application.html)，可用于在 web.xml 部署描述符一个  <servlet-name> 元素定义为你的应用的一类。

Example 4.16. 在 Servlet 3.0 使用 web.xml 使用 JAX-RS 应用部署
	
	<web-app>
	    <servlet>
	        <servlet-name>org.foo.rest.MyApplication</servlet-name>
	    </servlet>
	    ...
	    <servlet-mapping>
	        <servlet-name>org.foo.rest.MyApplication</servlet-name>
	        <url-pattern>/resources</url-pattern>
	    </servlet-mapping>
	    ...
	</web-app>

注意到， <servlet-class> 是在 Servlet 描述中省略了。这是一个正确的声明使用详细描述了 Servlet 3.0 扩展机制，在[第4.7.2.3“ Servlet 程序机制”](https://jersey.java.net/documentation/latest/deployment.html#deployment.servlet.3.pluggability)部分。还注意到，<servlet-mapping> 在使用的示例中是定义基础资源的URI。

**提示**：在一个 Servlet 2.x 运行时，它会声明 Jersey 容器的 Servlet 或者 Filter并且将通过应用程序的实现类名称为 init-param 实体中的一个，详见[第4.7.1，“Servlet 2。x容器”](https://jersey.java.net/documentation/latest/deployment.html#deployment.servlet.2)。

###4.7.2.3. Servlet 插件机制

Servlet 的框架插件机制是 Servlet 3 规范的一个特性。它简化了各种建立在 Servlet 框架的配置。 web.xml 文件不再是所有的配置选项中心点，它是可能的模块化部署描述符利用所谓的 Web 片段的几个具体和集中的 web.xml 文件的概念。一组 Web 片段基本上建立了最终的部署描述符。该机构还提供 SPI 钩，使 Web 框架注册自己的 Servlet 容器或定制 Servlet 容器部署过程中的一些其他的方式。本节描述如何 JAX-RS 和 Jersey Servlet 插件机制。

####4.7.2.3.1. 没有 Application 子类的 JAX-RS 应用

如果没有 Application （或者 ResourceConfig ）子类的存在，Jersey 会动态添加 Jersey 容器 Servlet 并且设置它的名称 到 javax.ws.rs.core.Application 。这个应用的路径将会被扫描并且所有的根资源类（[@Path](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/Path.html) 注解的类）跟[@Provider](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/ext/Provider.html)注解的提供者一样在应用注解包中将会包自定的注册进 JAX-RS 应用中。 应用程序已经根据添加了 javax.ws.rs.core.Application Servlet 映射的部署描述符被打包：

Example 4.17.  没用 Application 子类的 JAX-RS  应用的 web.xml

	<web-app version="3.0"
	    xmlns="http://java.sun.com/xml/ns/javaee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	 
	    <!-- Servlet declaration can be omitted in which case
	         it would be automatically added by Jersey -->
	    <servlet>
	        <servlet-name>javax.ws.rs.core.Application</servlet-name>
	    </servlet>
	 
	    <servlet-mapping>
	        <servlet-name>javax.ws.rs.core.Application</servlet-name>
	        <url-pattern>/myresources/*</url-pattern>
	    </servlet-mapping>
	</web-app>

####4.7.2.3.2. 自定义 Application 子类的 JAX-RS 应用

当存在一个自定义的应用程序类，在这种情况下 Jersey 的服务器运行时行为取决于是否有一个定义来处理应用程序类的 Servlet。

如果 web.xml 包含了 Servlet 定义,初始化参数 javax.ws.rs.Application 的值是 Application 子类的完全限定名称，Jersey 无需其他步骤。

如果没有定义自定义  Application 子类的 Servlet，那么  Jersey 会动态添加一个  Application 子类的完全限定名称的 Servlet。为了定义添加的 Servlet 的映射，你可以用 [@ApplicationPath](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/ApplicationPath.html) 来注解自定义  Application 子类(Jersey 将使用注释值添加 /* 自动定义 Servlet 映射),或者直接在 web.xml 指定 Servlet 映射。

在下面的例子中，我们假设 JAX-RS 应用程序使用自定义 Application 的子类定义的命名为 org.example.MyApplication ，那么 web.xml 文件可以有以下结构：

Example 4.18. 

	<web-app version="3.0"
	    xmlns="http://java.sun.com/xml/ns/javaee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	 
	    <!-- Servlet declaration can be omitted in which case
	         it would be automatically added by Jersey -->
	    <servlet>
	        <servlet-name>org.example.MyApplication</servlet-name>
	    </servlet>
	 
	    <!-- Servlet mapping can be omitted in case the Application subclass
	         is annotated with @ApplicationPath annotation; in such case
	         the mapping would be automatically added by Jersey -->
	    <servlet-mapping>
	        <servlet-name>org.example.MyApplication</servlet-name>
	        <url-pattern>/myresources/*</url-pattern>
	    </servlet-mapping>
	</web-app>

**注意**：如果您的自定义 Application 子类打包成了 war,它定义了哪些资源将被考虑。

* 如果 getClasses() 和 getSingletons() 方法返回一个空集合，然后所有的根资源类和提供者封装在 Web 应用程序档案将被使用，Jersey 会自动发现他们通过扫描 .war 文件。
* 如果上述两种方法  getClasses() 和 getSingletons() 返回一个非空集合，这些类和/或单例会发布在 JAX-RS 应用中。

Table 4.1. Servlet 3 Pluggability Overview 插件机制总览

<thead>
<tr>
<th align="left">Condition</th>
<th align="left">Jersey action</th>
<th align="left">Servlet Name</th>
<th align="left">web.xml</th>
</tr></thead>
<tbody>
<tr><td align="left">没有 Application 子类 </td><td align="left">添加 Servlet</td><td align="left"> javax.ws.rs.core.Application </code></td><td align="left">需要 Servlet 映射</td>
</tr><tr><td align="left"> Application 子类被已经存在的Servlet 控制</td><td align="left">无 action</td><td align="left">已经定义</td><td align="left">不需要</td></tr><tr><td align="left"> Application子类没有被已经存在的Servlet 控制</td><td align="left">添加 Servlet</td><td align="left">Application 子类的全限定名</td><td align="left">
若没有 @ApplicationPath 注解在 Application 子类,则 Servlet 映射是必须的
</td></tr></tbody>

##4.7.3. Jersey Servlet container modules 容器模块

Jersey 使用它自己的实现了 Servlet 的 ServletContainer 和 Filter API 去整合 Servlet 容器。任何 JAX-RS 运行时，Jersey 提供对 Servlet 容器的支持使得能够支持 Servlet 2.5 版本以上规范。支持在一个 Servlet 容器顶层的 JAX-RS 2.0 异步资源，Servlet 规范版本必需是 3 或更高版本的支持。

当部署进 Servlet 容器，Jersey 应用一般会打包成 .war 文件。与任何其他 Servlet 应用程序一样, JAX-RS 应用程序类打包在 WEB-INF/classes 或者 WEB-INF/lib ，所需的应用程序库位于 WEB-INF/lib。有关详细信息,请参阅 Servlet 规范([JSR 315](http://jcp.org/en/jsr/detail?id=315))。

Jersey 提供了两个 Servlet 模块。第一个模块是 Jersey 核心 Servlet 模块,提供核心 Servlet 需要集成的支持,需要 Servlet 2.5 或更高的容器:

	<dependency>
	    <groupId>org.glassfish.jersey.containers</groupId>
	    <artifactId>jersey-container-servlet-core</artifactId>
	</dependency>

为了支持额外的 Servlet 3.x 部署模式和异步 JAX-RS 资源的编程模型,另外一个 Jersey 模块为:

	<dependency>
	    <groupId>org.glassfish.jersey.containers</groupId>
	    <artifactId>jersey-container-servlet</artifactId>
	</dependency>

jersey-container-servlet 模块取决于 jersey-container-servlet-core 模块,因此当使用它时,它不需要显式地声明 jersey-container-servlet-core 依赖性。

注意,在简单的情况下,您不需要提供部署描述((web.xml)而是使用 @ApplicationPath 注释,如[4.7.2.3.1节“无 Application 子类的 JAX-RS 应用”](https://jersey.java.net/documentation/latest/user-guide.html#deployment.servlet.3.pluggability.noapp)所述。
