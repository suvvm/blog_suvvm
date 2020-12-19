---
title: Maven依赖冲突
date: 2020-04-30 20:55:44
categories:
- Java
tags:
- Maven
---

# Maven依赖冲突

## Maven依赖引入规则

### 最短依赖路径原则

最短依赖路径原则是maven依赖冲突的自动解决规则，若产生依赖冲突则会采用依赖深度最浅的版本

例

在springBoot项目中主动引入spring-core的依赖，我使用了springboot2.2.1

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

sping-boot-starter-test-2.2.1.RELEASE.pom中指明了使用spring-core的版本为5.2.1

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.2.1.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

这时主动在pom.xml中引入spring-core 4.3.27

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.3.27.RELEASE</version>
</dependency>
```

之后尝试运行项目。

报错

```
Exception in thread "main" java.lang.IllegalArgumentException: Cannot instantiate interface org.springframework.contex
```

### 最先声明原则

若产生冲突的依赖深度相同则会采用最先声明的版本。

## 依赖冲突的处理

可以使用mvn dependency:tree来查看依赖树

若使用idea开发则可以直接在pom.xml中右键选择Maven->Show Dependencies，之后可以看到如下图所示，冲突部份依赖将由红色虚线标识

![img](Maven依赖冲突\mavendependenciesdemo.svg)

这时只需要找到冲突的依赖，通过exclusions在对应依赖项中将不需要的版本排除，idea则可以在上述操作打开的目标图中直接右键选择Exclude即可自动为目标依赖添加exclusions

