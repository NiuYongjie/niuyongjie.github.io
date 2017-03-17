---
layout: post
title: Exception:javax.persistence.Table.indexes()
tags: Exception,Apusic,金蝶
grammar_cjkRuby: true
---

[toc]

## 问题描述

由于项目需要,必须将项目部署到国产中间件Apusic应用服务器中.在启动容器时报
找不到方法: ==javax.persistence.Table.indexes()==

```java
Caused by: java.lang.NoSuchMethodError: javax.persistence.Table.indexes()[Ljavax/persistence/Index;
	at org.hibernate.cfg.annotations.EntityBinder.processComplementaryTableDefinitions(EntityBinder.java:936)
	at org.hibernate.cfg.AnnotationBinder.bindClass(AnnotationBinder.java:824)
	at org.hibernate.cfg.Configuration$MetadataSourceQueue.processAnnotatedClassesQueue(Configuration.java:3788)
	at org.hibernate.cfg.Configuration$MetadataSourceQueue.processMetadata(Configuration.java:3742)
	at org.hibernate.cfg.Configuration.secondPassCompile(Configuration.java:1410)
	at org.hibernate.cfg.Configuration.buildSessionFactory(Configuration.java:1844)
	at org.hibernate.cfg.Configuration.buildSessionFactory(Configuration.java:1928)
	at org.springframework.orm.hibernate4.LocalSessionFactoryBuilder.buildSessionFactory(LocalSessionFactoryBuilder.java:343)
	at org.springframework.orm.hibernate4.LocalSessionFactoryBean.buildSessionFactory(LocalSessionFactoryBean.java:431)
	at org.springframework.orm.hibernate4.LocalSessionFactoryBean.afterPropertiesSet(LocalSessionFactoryBean.java:416)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1612)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1549)
	... 49 more
```

项目之前在tomcat服务器中能够正常启动.从网上查找资料,很多人说是Hibernate的bug,经过不断的尝试与排查,最终确定这个异常不是Hibernate的bug.

## 解决过程

### 网上解决方案

源代码的编写形式如下所示:

```java 
  @Entity  
  @Table(name = "category")  
  public class Category  {  
  	 // Fields  
 	 private Integer id;  
  	 private String type;  
 	 private Boolean hot;  
  	 //省略  
  }
```

将 ==@Table(name="TABLENAME")== 改写为 ==@Entity(name="TABLENAME")== 这种形式.
这种修改不一定起作用

### 问题的原因

从提示的错误信息来看,问题的原因在 ==javax.persistence.Table== 这个类没有indexes()方法.

### 解决的过程

#### 统一项目工程中包版本

首先,搜索项目中有哪些包有 ==javax.persistence.Table== 这个类.
搜索结果发现hibernate-jpa和javax.persistence包中有相应的类.

[]

用反编译工具分别查看两个包中的Table类.

[]

可以看出 ==javax.persistence== 包中的Table类中没有indexes()方法.
由于容器启动过程有用到indexes()方法,所以,应该替换掉javax.persistence这个包.
查询Hibernate的官网,可以得到项目中使用的hibernate4.6中 ==hibernate-jpa-2.1-api== 是基于 ==javax.persistence.2.1== 开发的,所以可以通过替换为  ==javax.persistence.2.1== 的包或修改pom.xml文件中对应的版本来时项目工程中的包版本保持统一.

## 统一web容器中的包版本

在完成上述工作后,启动容器发现还是报同样的错误,既然项目中的版本都已经统一,那么只有一种可能,就是,容器中自带的包的版本和项目中包的版本有冲突.
通过排查Apusic的目录,发现Apusic使用的是 ==javax.persistence.2.0.5== 的包:

[%Apusic%/lib/ext/javax.persistence-2.0.5.jar]

将该包替换为 ==javax.persistence.2.0.5== 的包.

Apusic搜索包可以通过 ==Apusic应用服务器监控管理平台== 的环境概要面板进行搜索比较方便

## 检查javaee核心包

在统一web容器中的包版本后,容器启动依旧报相同错误,实在百思不得其解.项目中和web容器中能够搜索到的 ==javax.persistence== 包都已经替换为统一的版本.到底还有哪个地方引用了 ==javax.persistence.2.0== 

通过各种google和猜测最终定位到了超级大boss: ==javaee_src.jar== .在其他容器中可能是j2ee.jar

[]

果断删除之,重启容器后,一切正常



## 总结

总结下,首先根据报错提示信息,确定是 ==javax.persistence.Table== 中应为版本问题缺少indexes()方法.其次,在确定正确的包的版本的情况下统一工程项目中,web容器中和javaee.jar(j2ee.jar)等等中版本一致就可以解决这个问题.

## 参考资料

[Hibernate 社区讨论贴][1]

[csdn 博客解决方法][2]

[oschina 中的解决博文][3]


  [1]: https://forum.hibernate.org/viewtopic.php?f=1&t=1036261&view=next
  [2]: http://blog.csdn.net/eson_15/article/details/51273804
  [3]: https://my.oschina.net/JasonZhang/blog/539095