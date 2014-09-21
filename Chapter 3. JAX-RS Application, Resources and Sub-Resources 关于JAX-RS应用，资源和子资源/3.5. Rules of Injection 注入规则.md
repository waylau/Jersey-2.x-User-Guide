3.5. Rules of Injection 注入规则
========================

前面的章节中已经给出注释类型的例子，主要用标注方法参数，也可以通过对类的域进行注解将值注入到这些类型。

本节介绍了在注释类型上注入值的规则。注入可以用在属性，构造函数参数，资源/子资源/子资源定位方法的参数和 bean setter方法。以下介绍的这些注入的情况下：

Example 3.23. 注入
	
	@Path("{id:\\d+}")
	public class InjectedResource {
	    // 注入到属性
	    @DefaultValue("q") @QueryParam("p")
	    private String p;
	 
	    // 注入到构造函数参数
	    public InjectedResource(@PathParam("id") int id) { ... }
	 
	    // 注入到资源参数
	    @GET
	    public String get(@Context UriInfo ui) { ... }
	 
	    // 注入子资源方法参数
	    @Path("sub-id")
	    @GET
	    public String get(@PathParam("sub-id") String id) { ... }
	 
	    // 注入子资源方法参数定位器方法参数
	    @Path("sub-id")
	    public SubResource getSubResource(@PathParam("sub-id") String id) { ... }
	 
	    // 注入 bean setter 方法
	    @HeaderParam("X-header")
	    public void setHeader(String header) { ... }
	}

有一些限制，当注射到一个生命周期为单域的资源类。在这种情况下，类的属性或构造函数的参数不能被注入请求特定的参数。所以，例如，以下是不允许的。

Example 3.24. 错误!注入单域

	@Path("resource")
	@Singleton
	public static class MySingletonResource {
	 
	    @QueryParam("query")
	    String param; //错误：不能将特定参数注入单例资源，
					  //会使程序初始化失败
	 
	    @GET
	    public String get() {
	        return "query param: " + param;
	    }
	}

上面的例子验证了应用程序不能为单资源注入请求特定的参数，否则验证失败的。同样的例子，如果查询的参数将被注入到一个单例构造函数参数则失败。换句话说，如果你希望一个资源实例的服务很多请求，则资源实例不能绑定到一个特定的请求参数。

存在例外，特定请求对象可以注入到构造函数或类属性。这些对象的运行时注入的代理可以同时服务多个请求。这些请求的对象是HttpHeaders, Request, UriInfo, SecurityContex。这些代理可以使用 [@Context](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Context.html) 注释进行注入。下面的示例展示将代理注入单资源类。

Example 3.25. Injection of proxies into singleton

	@Path("resource")
	@Singleton
	public static class MySingletonResource {
	    @Context
	    Request request; // 这个是允许的: 
						 //请求的代理将会被注入进单例
	 
	    public MySingletonResource(@Context SecurityContext securityContext) {
						 // 这个也是允许的: 
						 // SecurityContext的代理将会被注入进单例
	    }
	 
	    @GET
	    public String get() {
	        return "query param: " + param;
	    }
	}

总结,可以为以下结构注入：

<table>
<tr>
<th>Java 构造</th>
<th>描述</th>
</tr>

<tr>
<td>Class fields</td>
<td>将值直接注入类属性。这属性
可以是 private 但一定不能是 final 。除了上面提到的代理类型方法外，不能用在单例范围。</td>
</tr>
<tr>
<td>Constructor parameters</td>
<td>构造函数会调用注入值。如果多个构造函数其中存在一个最可注的射参数则将被调用。除了上面提到的代理类型方法外，不能用在单例范围。
</td>
</tr>
<tr>
<td>Resource methods</td>
<td>资源的方法（带有 @GET, @POST, ...注解）包含的参数可以在执行时注射。可以在任何范围使用。
</td>
</tr>
<tr>
<td>Sub resource locators</td>
<td>子资源的方法（带有 @GET, @POST, ...注解）包含的参数可以在执行时注射。可以在任何范围使用。
</td>
</tr>
<tr>
<td>Setter methods</td>
<td>值可以被注入 setter 方法将初始化属性，而不是直接将值注入属性的。注射只能用于 @Context 注释。这意味着它不能使用，例如将查询参数注入，但可以用在请求注入。setter 方法将会在对象创建后执行，且只有一次。该方法的名称不必要有一个 setter 模式。除了上面提到的代理类型，不能在单例范围内使用。
</td>
</tr>
</table>

下面的示例显示所有可能的值可以被注入的 Java 构建函数。

Example 3.26. 可能注入的例子

	@Path("resource")
	public static class SummaryOfInjectionsResource {
	    @QueryParam("query")
	    String param; // injection into a class field 注入类的属性
	 
	 
	    @GET
	    public String get(@QueryParam("query") String methodQueryParam) {
	        // injection into a resource method parameter 注入资源的方法参数
	        return "query param: " + param;
	    }
	 
	    @Path("sub-resource-locator")
	    public Class<SubResource> subResourceLocator(@QueryParam("query") String subResourceQueryParam) {
	        // injection into a sub resource locator parameter注入子资源定位器参数
	        return SubResource.class;
	    }
	 
	    public SummaryOfInjectionsResource(@QueryParam("query") String constructorQueryParam) {
	        // injection into a constructor parameter注入构造器的参数
	    }
	 
	 
	    @Context
	    public void setRequest(Request request) {
	        // injection into a setter method注入setter方法
	        System.out.println(request != null);
	    }
	}
	 
	public static class SubResource {
	    @GET
	    public String get() {
	        return "sub resource";
	    }
	}

[@FormParam](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/FormParam.html) 注释是特别的，仅可利用资源和子资源的方法。这是因为它从请求实体中提取信息
