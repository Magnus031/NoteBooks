# <center>概述</center>

## Introduce

`Maven` 中的 `pom.xml` 文件是 `Maven` 项目的核心文件，它是一个 XML 文件，包含了项目的配置信息，用于描述项目如何构建，声明项目的依赖等。


我们在 `pom.xml` 文件中可以配置项目的基本信息，如项目的 `groupId`、`artifactId`、`version` 等，还可以配置项目的依赖、插件、构建等信息。


- `groupId` : 表示项目的所属组织或团体，通常采用反向域名的命名规则，如 `com.example`。

- `artifactId` : 表示项目的模块名，通常是项目的名称。
两者组合在一起就构成了项目的 **唯一命名坐标**


我们下面举了个例子 
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

上面的就是指的是 `org.springframework.boot` 组织下的 `spring-boot-starter-web` 模块。适用于一些常见的 `Web` 开发时候的依赖starters.



