# <center>SpringBoot</center>



## 八股部分
### Q1 说一说 SpringBoot 的特点

#### Q1.1 首先介绍一下 Spring

Spring 是一个开源框架，它是 重量级企业开发框架 **EJB** 的替代品。Spring 为企业级 Java 开发提供了两个最主要的特点:

- **依赖注入**
- **面向切面编程**
- 通过 简单的 Java 对象 **(POJO)** 实现了 **EJB** 的功能，降低了开发的复杂性。

**值得注意的是，虽然 Spring 的组件代码是轻量级的，但是它的配置文件确是重量级的**，这也是 Spring 的缺点之一。

虽然，`Spring2.5` 引入了基于注解的组件扫描机制，这个消除了大量针对应用程序自身组件的显性XML配置。`Spring3.0` 引入了基于Java的配置，这个是一种类型安全的可重构配置方式，可以替代 `XML` 配置。

但是我们在开启某些 Spring框架特性的时候，或者引入第三方库的时候，依旧需要显式配置`xml`文件。所以就比较加大我们开发的负担。所以我们选择了 `SpringBoot`。

#### Q1.2 SpringBoot 的特点
`SpringBoot` 的特点其实也很很好理解，`boot` 就是挂起的意思。`SpringBoot` 就是让我们的应用程序更快的启动，更快的运行。

1. **提高生产力** ：因为是属于开箱即用的，我们开发项目可以利用 SpringBoot 的默认配置即可。等到开发过程中，如果有需要，我们再进行单独的修改相关的配置/或者是引入相关的依赖即可。

2. **与Spring生态无缝衔接** : SpringBoot 是基于 Spring 的，所以它与 Spring 生态无缝衔接，我们可以利用 SpringBoot 来快速的开发我们的项目。SpringBoot 可以快速的集成各个模块，比如 **Spring JDBC,Spring ORM,...** 简化了与这些工具的整合过程，增加了效率。

3. **内嵌服务器** : SpringBoot 内置了 Tomcat,Jetty,Undertow 等支持HTTP服务器，我们可以直接运行一个 jar 包，就可以启动一个 web 服务。 开发者可以像运行普通的 Java 程序那样运行 SpringBoot 项目。

4. **多种插件支持** : SpringBoot 提供了很多的插件支持，比如 `Maven` 插件，`Gradle` 插件，`SpringBoot CLI` 等等。可以使用内置工具开发和测试 SpringBoot 应用程序。】


### Q2 什么是 SpringBoot-Starters
> Spring Boot Starters are a set of convenient dependency descriptors that you can include in your application. You get a one-stop-shop for all the Spring and related technology that you need without having to hunt through sample code and copy paste loads of dependency descriptors. 

上面是 SpringBoot 官方对于 `SpringBoot-Starters` 的描述。简单来说，`SpringBoot-Starters` 是一组方便的依赖描述符，你可以在你的应用程序中包含它。你可以一站式获取所有你需要的 Spring 和相关技术，而不必在示例代码中搜索并复制粘贴大量的依赖描述符。


举个例子，我们通常会使用到 `RESTful` 服务或者`Web`应用程序，我们通常需要自己进行添加很多单独的依赖，但是有了 `SpringBoot-Web-Starters` 我们就可以直接引入这个依赖，然后就可以直接使用了。里面包含了所有 RESTful 服务所需要的依赖。**同时也可以帮我们避免版本依赖的问题**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```