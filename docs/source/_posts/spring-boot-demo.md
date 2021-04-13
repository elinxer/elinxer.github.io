---
title: spring-boot入门--基础使用
date: 2021-04-13 16:57:49
categories: Java
tags: 
    - java
    - spring-boot
    - mybatis

---

## java Spring-boot + 微服务


这里是我学习spring-boot的一个快速流程和文档记录，可以依据这个快速了解一下这个spring微服务的生态

java版本1.8，工具ideaintellj， mysql，Redis，maven3，git bash

组件：nacos，mybatis，com.alibaba.cloud等

<!-- more -->

### 跑起来 demo com.zhiteer.demo

1. 下载ideaintellj

2. 下载java8和配置好maven

3. 下载工程demo

```

git clone https://github.com/elinxer/spring-cloud-demo.git

```

4. 打开ideaintellj，点击open导入文件夹

如果弹出是否要信任项目，直接信任

5. 下载 nacos

网址 ：https://github.com/alibaba/nacos/releases

压缩一个压缩包：nacos-server-2.0.0.zip

解压在磁盘上一个位置，然后进入bin：D:\develop\nacos-server-2.0.0\nacos\bin

单机启动nacos，因为是本地，不支持多机启动
./startup.cmd -m standalone


6. 配置MySQL，配置Redis


mysql 数据库 test

测试用户表：

``` sql
CREATE TABLE `user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## spring boot 学习

1. 注意 spring boot 请求带上请求头json以及提交参数默认项 {} 采用raw方式

比如 代码里面使用了 @RequestBody 则必须不能为空，且要raw-json内容作为参数请求

## mybatis 学习

1.spring boot mybatis 

在配置xml的时候并不是自动匹配resource下面的xml的，你必须在项目配置里面指定一个目录：
并且还要指定一个model包地址
src/main/resources/application.properties
########################## mybatis begin ##########################
mybatis.mapper-locations=classpath*:mybatis/*.xml
mybatis.type-aliases-package=com.zhiteer.demo.**.model
########################## mybatis end ##########################

配置好后，xml文件的名称要与mapper映射的文件名保持一致，并且标识注解@Mapper，这样才能匹配上

注意mybatis的xml编写以及用处，尤其是resultType和resultMap区分

使用mybatis是可以同时使用注解和xml方式的

spring boot接口输出返回JSON格式要对象和Map格式，同时如要定义固定的返回结构，需要载入一个固定结构的对象


mybatis 底层是基于jdbc驱动的
https://www.zhihu.com/question/383388104



mybatis 学习文档：
http://c.biancheng.net/view/4313.html
是如何防注入：


### mybatis 生命周期

生命周期是组件的重要问题，尤其是在多线程的环境中，比如互联网应用、Socket 请求等，而 MyBatis 也常用于多线程的环境中，错误使用会造成严重的多线程并发问题，为了正确编写 MyBatis 的应用程序，我们需要掌握 MyBatis 组件的生命周期。

所谓生命周期就是每一个对象应该存活的时间，比如一些对象一次用完后就要关闭，使它们被 Java 虚拟机（JVM）销毁，以避免继续占用资源，所以我们会根据每一个组件的作用去确定其生命周期。

SqlSessionFactoryBuilder
SqlSessionFactoryBuilder 的作用在于创建 SqlSessionFactory，创建成功后，SqlSessionFactoryBuilder 就失去了作用，所以它只能存在于创建 SqlSessionFactory 的方法中，而不要让其长期存在。因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。
SqlSessionFactory
SqlSessionFactory 可以被认为是一个数据库连接池，它的作用是创建 SqlSession 接口对象。因为 MyBatis 的本质就是 Java 对数据库的操作，所以 SqlSessionFactory 的生命周期存在于整个 MyBatis 的应用之中，所以一旦创建了 SqlSessionFactory，就要长期保存它，直至不再使用 MyBatis 应用，所以可以认为 SqlSessionFactory 的生命周期就等同于 MyBatis 的应用周期。

由于 SqlSessionFactory 是一个对数据库的连接池，所以它占据着数据库的连接资源。如果创建多个 SqlSessionFactory，那么就存在多个数据库连接池，这样不利于对数据库资源的控制，也会导致数据库连接资源被消耗光，出现系统宕机等情况，所以尽量避免发生这样的情况。

因此在一般的应用中我们往往希望 SqlSessionFactory 作为一个单例，让它在应用中被共享。所以说 SqlSessionFactory 的最佳作用域是应用作用域。

SqlSession
如果说 SqlSessionFactory 相当于数据库连接池，那么 SqlSession 就相当于一个数据库连接（Connection 对象），你可以在一个事务里面执行多条 SQL，然后通过它的 commit、rollback 等方法，提交或者回滚事务。

所以它应该存活在一个业务请求中，处理完整个请求后，应该关闭这条连接，让它归还给 SqlSessionFactory，否则数据库资源就很快被耗费精光，系统就会瘫痪，所以用 try...catch...finally... 语句来保证其正确关闭。

所以 SqlSession 的最佳的作用域是请求或方法作用域。

Mapper
Mapper 是一个接口，它由 SqlSession 所创建，所以它的最大生命周期至多和 SqlSession 保持一致，尽管它很好用，但是由于 SqlSession 的关闭，它的数据库连接资源也会消失，所以它的生命周期应该小于等于 SqlSession 的生命周期。Mapper 代表的是一个请求中的业务处理，所以它应该在一个请求中，一旦处理完了相关的业务，就应该废弃它。

以上，我们讨论了 MyBatis 组件的生命周期。 



异常错误分析
java.lang.IllegalArgumentException: Result Maps collection does not contain value for com.xx.xx.map
这种错误直接将对应xml的resultMap改为resultType



resultMap和resultType区别

通常，定义一个resultMap的字段自定义映射才需要使用resultMap类型去匹配实体类，所以正常是要一个resultMap标签

resultType返回得系统类型，如果是实体类，那么默认约束的字段就是和实体类保持一致，字段格式采用驼峰形式定义下划线



### mybatis 缓存机制


https://tech.meituan.com/2018/01/19/mybatis-cache.html

mybatis缓存机制有一级缓存和二级缓存，当指定缓存后，查询会生成一级缓存，如果进行了数据的修改一级缓存会消失
个SqlSession中持有了Executor，每个Executor中有一个LocalCache。当用户发起查询时，MyBatis根据当前执行的语句生成MappedStatement，在Local Cache进行查询，如果缓存命中的话，直接返回结果给用户，如果缓存没有命中的话，查询数据库，结果写入Local Cache，最后返回结果给用户。具体实现类的类关系图如下图所示。  



### mybatis 事务

