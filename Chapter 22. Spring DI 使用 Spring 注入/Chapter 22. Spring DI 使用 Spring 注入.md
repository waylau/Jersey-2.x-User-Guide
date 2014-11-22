Chapter 22. Spring DI 使用 Spring 注入
========================

Jersey 提供对 Spring DI 的扩展。使得 Jersey 在使用 Spring bean 时 就像是 JAX-RS 的组件 (比如 资源和提供者) 并且允许  Spring 注入 Jersey 管理的组件中。

这个 Spring 扩展模块配置是基于注解的。当 Spring bean 注入，就会产生的 受 Spring 管理的 JAX-RS 类。 注入的 Spring bean 不只是 通过 Spring XML 来配置，也支持用 Spring 的 单例和 请求域。

*局限性：
Spring bean 不能被直接注射到 JAX-RS 类 ，通过 Spring XML 配置*