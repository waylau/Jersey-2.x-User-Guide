4.8. Java EE Platform 在 Java EE 平台
========================

本节描述,如何发布 Jersey JAX-RS 资源的各种 Java EE 平台元素。JAX-RS 和 Jersey 给你更多选择的可能性,符合你的口味(和设计),取决您决定使用 Java EE 技术管理你的资源。

##4.8.1. Managed Beans 管理 Beans

Jersey 支持使用Java EE 托管 bean 作为根资源类、子类提供者以及Application 子类。

在下面的代码中,您可以找到一个 bean 的一个例子,使用受管 bean 拦截器定义为一个 JAX-RS bean 。bean 用于拦截方法调用资源 getIt():

	@ManagedBean
	@Path("/managedbean")
	public class ManagedBeanResource {
	 
	    public static class MyInterceptor {
	        @AroundInvoke
	        public String around(InvocationContext ctx) throws Exception {
	            System.out.println("around() called");
	            return (String) ctx.proceed();
	        }
	    }
	 
	    @GET
	    @Produces("text/plain")
	    @Interceptors(MyInterceptor.class)
	    public String getIt() {
	        return "Hi managed bean!";
	    }
	}

##4.8.2. Context and Dependency Injection (CDI) 上下文和依赖注入

CDI bean 可以作为 Jersey 根资源类、子类提供者以及 Application 子类。提供者以及 Application 子类必是单例或应用程序作用域。

下一个例子显示了一个使用 CDI bean 作为 JAX-RS 资源类。我们假设,CDI已经启用。该例子通过使用另一个 bean (MyOtherCdiBean)提供类型安全的依赖注入分开使用 CDI :

	@Path("/cdibean")
	public class CdiBeanResource {
	    @Inject MyOtherCdiBean bean;  // CDI injected bean
	 
	    @GET
	    @Produces("text/plain")
	    public String getIt() {
	        return bean.getIt();
	    }
	}

##4.8.3. Enterprise Java Beans (EJB)

无状态和单例会话 bean 可以作为Jersey 根资源类、子类提供者和/或Application 子类。你可以选择从 EJB 本地接口的注释方法或直接的在一个无接口的 EJB POJO 方法。. JAX-RS 规范要求其实现者发现 EJB 通过检查注释类(或本地接口),而不是在部署描述符(ejb-jar.xml)。因此,保持您的  JAX-RS 应用程序移植,不覆盖 EJB 注解 或提供任何在部署描述符文件附加的元数据。

以下示例包含一个无状态EJB本地接口用于 Jersey :

	@Local
	public interface LocalEjb {
	    @GET
	    @Produces("text/plain")
	   public String getIt();
	}
	 
	@Stateless
	@Path("/stateless")
	public class StatelessEjbResource implements LocalEjb {
	    @Override
	    public String getIt() {
	        return "Hi Stateless!";
	    }
	}

**注意**

请注意,Jersey 目前不支持 JAX-RS 应用程序打包成单独的 EJB 模块的部署(ejb-jars)。使用 EJB 作为 JAX-RS 资源, EJB 需要打包直接在 WAR 或 EAR 中包含至少一个 WAR 。这是确保 Servlet 容器初始化,对于 Jersey 运行时的引导是必要的.

##4.8.4. Java EE Servers 在 Java EE 服务器中

###4.8.4.1. GlassFish Application Server

[2.3.1](https://jersey.java.net/documentation/latest/user-guide.html#servlet-app-glassfish)中解释说,在 GlassFish 中你不需要添加任何特定的依赖 ,Jersey 已经打包在 GlassFish 。你只需要将 provided-scoped 依赖项添加到您的项目使能够编译它。在运行时,GlassFish 将确保您的应用程序能够访问 Jersey 库。

从2.7版本开始, Jersey 允许使用  @javax.inject.Inject 注入注解注入 Jersey 特定类型进 CDI 来启用 JAX-RS 组件。这也包括自定义HK2 绑定,被配置为 Jersey 应用程序的一部分。该功能特别允许使用Jersey 监测统计(统计特性)提供在CDI的环境中,在注射是唯一获得监测数据的意思。

因为 CDI 和 HK2 都使用相同的注入注释,Jersey 在某些情况下可以发生歧义,这可能导致严重的运行时的问题。获得更好的控制 Jersey 评估 HK2注入,终端用户可以利用新引进的 [Hk2CustomBoundTypesProvider](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/gf/cdi/spi/Hk2CustomBoundTypesProvider.html),SPI。请参阅有关 javadoc 得到详细信息在应用程序中如何使用 SPI 。

###4.8.4.2. Oracle WebLogic Server

WebLogic 12.1.2及早期版本,只支持 JAX-RS 1.1 ([JSR 311](http://jcp.org/en/jsr/detail?id=311)) 的 Jersey 1.x (WebLogic 12.1.2附带 Jersey 1.13)。更新  Jersey 1.x 的版本 在早些时候这些 WebLogic 服务器版本,请参阅 [Updating the Version of Jersey JAX-RS RI](http://docs.oracle.com/middleware/1212/wls/RESTF/version-restful-service.htm)在WebLogic RESTful Web服务开发指南。

在WebLogic 12.1.3,附带 Jersey 1.18 作为  JAX-RS 1.1 默认的提供者。在这个版本的WebLogic,JAX-RS 2.0 (使用 Jersey 2.5.1)为一个支持可选安装共享库。请通读 [WebLogic 12.1.3 RESTful Web Services Development Guide](http://docs.oracle.com/middleware/1213/wls/RESTF/use-jersey20-ri.htm#RESTF290),里面详细说明了在 WebLogic 12.1.3 如何启用 JAX-RS 2.0  

###4.8.4.3. Other Application Servers

第三方 Java EE 应用服务器通常附带一个 JAX-RS 实现。如果您想要使用Jersey 而不是默认的 JAX-RS 提供者,您需要将J ersey 库添加到您的类路径和禁用默认的 JAX-RS 提供者的容器。

一般来说,Jersey 将被部署为一个 Servlet 并且资源可以部署在不同的方式,如本节所述。然而,确切的步骤将因供应商不同而不同。
