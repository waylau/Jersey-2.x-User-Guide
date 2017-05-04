Chapter 20. MVC Templates 模板
==============

Jersey 提供了支持 Model-View-Controller (MVC) 设计模式的扩展。 

在 Jersey 组件的上下文中，在 MVC 模式中的 Controller 对应于一个资源类或方法，View 对应绑定到资源类或方法的模板，Model 对应从资源方法（Controller）返回的Java 对象（或 Java Bean）。

*注：从本章的一些段落/例子来自  Paul Sandoz 的 [MVCJ](https://blogs.oracle.com/sandoz/entry/mvcj) 博客。*

在 Jersey 2，基础 MVC API 由两个类组成（在 org.glassfish.jersey.server.mvc包），可用于绑定模型到视图（模板），分别是  [Viewable](https://jersey.java.net/apidocs/2.25.1/jersey/org/glassfish/jersey/server/mvc/Viewable.html) 和 [@Template](https://jersey.java.net/apidocs/2.25.1/jersey/org/glassfish/jersey/server/mvc/Template.html)。在使用 Jersey MVC 模板支持时，这些类确定了采用显示还是隐士式的处理方法。