4.3. Auto-Discoverable Features 自动发现功能
========================

默认情况下 Jersey 2.x 不隐式注册在 classpath 上可用的模块中的任何扩展功能，除非明确在扩展文档中进行说明。用户将明确注册的扩展[功能](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Feature.html)来使用他们的 Application 子类。一小部分 Jersey 提供模块不需要显式注册他们的扩展功能，因为这些在[配置](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Configuration.html)（客户端/服务器）中将会被Jersey 运行时发现和注册，这些功能模块实现的这些特性将呈现在 JAX-RS 应用部署的 classpath 上。这些自动发现模块包括：

* 来自 jersey-media-moxy 的 JSON 绑定特性
* jersey-media-json-processing
* jersey-bean-validation

除了这些模块也有几个特性/提供者 出现在 jersey-server 模块，被过这一机制发现并且受到 Jersey 自动发现的配置的影响（见[第4.3.1节，“配置自动发现机制”](https://jersey.java.net/documentation/latest/deployment.html#deployment.autodiscovery.config)），即：

* [WadlFeature](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/server/wadl/WadlFeature.html) - 支持 wadl 处理。
* [UriConnegFilter](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/server/filter/UriConnegFilter.html) - 在基于 URI 内容协商的过滤器。

几乎所有的 Jersey 自动发现的实现拥有  AutoDiscoverable.DEFAULT_PRIORITY @Priority 的设置。

**注意**：自动发现功能是通过实现内部 AutoDiscoverable Jersey SPI 这个接口目前是不公开的，以及考虑到未来的变化，当试图使用它要小心。

##4.3.1. Configuring Feature Auto-discovery Mechanism 配置自动发现机制

在 Jersey，以上所描述的自动发现机制特性是默认启用的。它可以通过使用特殊（普通/服务器/客户端）属性来关闭：常见自动发现属性：

* [CommonProperties.FEATURE_AUTO_DISCOVERY_DISABLE](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/CommonProperties.html#FEATURE_AUTO_DISCOVERY_DISABLE)

当设置时，自动发现机制在客户端/服务端全局的被关闭

* [CommonProperties.JSON_PROCESSING_FEATURE_DISABLE](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/CommonProperties.html#JSON_PROCESSING_FEATURE_DISABLE)

当设置，禁用JSON处理配置（JSR-353）特征。

* [CommonProperties.MOXY_JSON_FEATURE_DISABLE](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/CommonProperties.html#MOXY_JSON_FEATURE_DISABLE)

当设置，禁用 MOXy Json 配置特征。

为每个这些属性,有一个客户机/服务器计数器部分分别只在 Jersey 客户端或服务器运行时呈现(见 [ClientProperties](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/client/ClientProperties.html) / [ServerProperties](https://jersey.java.net/apidocs/2.16/jersey/org/glassfish/jersey/server/ServerProperties.html))。设置时,每一个客户机/服务器相关的特定自动发现属性覆盖相关的公共属性值。

**注意**：如果一个自动发现机制（一般或特定功能）被禁用，然后所有的功能，组件和/或性能，采用自动发现机制的默认注册必须手动注册。
