---
layout: post
title: "读《高可用可伸缩微服务架构》感"
date: "2019-07-14 21:30"
author: "Danfei"
---
2019-07-14 21:30

读《高可用可伸缩微服务架构》感

第四章 Spring Boot / Spring Cloud 实践

Spring官方对Spring Boot，Spring Cloud，Spring Cloud Data Flow的定位如下：
	
	- Spring Boot：Build Anything
	- Spring Cloud：Coordinate Anything
	- Spring Cloud Data Flow：Connect Everything
	
Spring Boot设计之初就是为了以最少的配置，最快的速度来启动和运行Spring项目，Spring Boot使用特定的配置来构建生产就绪型的应用程序

Spring Cloud基于Spring Boot的开发便利性，通过构建一系列的框架集合来简化分布式微服务式架构，为微服务带来弹性，可靠性和协调性。

Spring Boot以约定大于配置的核心思想使用特定的方式进行配置，能以很低的成本去集成其他主流开源软件。

Dubbo Spring Boot Starter

Dubbo Spring Boot Starter 致力于简化Dubbo RPC框架在Spring Boot应用场景中的开发，同时整合了Spring的特性。

OPS端点

根据dubbo-spring-boot-actuator模块的结构初步分析执行器端点的实现原理；将端点（DubboEndpoint）适配委托给MVC策略端点（DubboMvcEndpoint），再通过端点MVC
适配器（EndpointMvcAdapter）将端点暴露为HTTP请求方式的MVC端点，然后使用端点自动配置（DubboEndpointAutoConfiguration）和MVC端点暴露的配置
（DubboMvcEndpointManagementContextConfiguration）来分别注入DubboMvcEndpoint MVC端点。

简单来说，服务化的核心就是将传统的一站式应用根据业务拆分成一个个服务，而微服务在这个基础上要更加彻底的去耦合（不再共享DB，KV，去掉重量级的ESB），
并且强调DevOps和快速演化。

DevOps是Development和Operations的合体，它要求开发，测试，运维进行一体化的合作，进行更小，更频繁，更自动化的应用发布，以及围绕应用架构来构建基础设施
的架构。这就要求应用充分的内聚，也方便运维和管理，这个理念和微服务不谋而合。

REST是Representational State Transfer的缩写，含义为表述性状态转移，REST是一种软件架构风格，REST并非标准，而是一种开发Web应用的设计和开发方式，可以降低
开发的复杂度，提高系统的可伸缩性。相比较于基于传统的SOAP协议，XML格式的WSDL的webService来说，REST模式提供了更简洁的实现方案

** Spring Cloud 技术栈总览 **

1. 服务治理，这是Spring的核心，目前Spring Cloud主要是通过整合Netflix的相关产品来实现这方面的功能，包括用于服务注册和发现的Rureka，调用断路器
Hystrix，调用端负载均衡Ribbon，REST客户端Feign，智能服务路由Zuul，用于监控数据收集和展示的Spectator, Servo, Atlas,用于配置读取的Archaius
和提供Controller层Reactive封装的RxJava

2. 配置中心，基于Spring Cloud Neflix和Spring Cloud Bus，Spring又提供了Spring Cloud Config，实现了配置集中管理，动态刷新的配置中心概念。
配置通过Git或简单文件来存储，支持加密解密

3. 消息组件，Spring Cloud Stream对分布式消息的各种需求做了抽象，包括发布订阅，分组消费，消息分片等功能，实现了微服务之间的异步通信。Spring
Cloud Stream也继承了第三方的RabbitMQ和Apache Kafka作为消息队列的实现。

4. 分布式链路监控，Spring Cloud Sleuth提供了全自动，可配置的数据埋点，以收集微服务调用链路上的性能数据，并发送给Zipkin进行存储，统计和展示

5. 安全控制，Spring Cloud Security基于OAuth2这个开发网络的安全标准，提供了微服务环境下的单点登录，资源授权，令牌管理等功能。

6. 数据处理：Spring Cloud Task， Spring Cloud Batch。

Swagger是一个规范和完整的框架，用于生成，描述，调用和可视化RESTful风格的Web服务。

** 服务注册与发现 **

在分布式系统领域有一个著名的CAP定理：C(数据一致性)，A(服务可用性)，P(服务对网络分区故障的容错性)这三个特性在任何分布式系统中不能同时满足，
最多同时满足两个。

Zookeeper在很多场景下作为服务发现的解决方案。Zookeeper保证的是CP，即任何时刻对Zookeeper的访问请求能得到一致的结果，同时系统对网络分区具备容错性，
但他不能保证每次服务请求的可用性。从实际情况来分析，在使用Zookeeper获取服务列表时，如果Zookeeper正在选主，或者Zookeeper集群中半数以上机器不可用，
那么久无法获取数据，所以说，Zookeeper不能保证服务可用性。

的确，对于大多数分布式环境，尤其是涉及数据存储的场景，数据一致性是应该首先被保证的，这也是Zookeeper被设计成CP的主要原因。但是对于服务发现场景而言，
针对同一个服务，即使注册中心的不同节点保存的服务提供者信息不一致，也不会造成灾难性的后果。因为对于服务消费者而言，能够消费才是最重要的。所以，对于
服务发现而言，可用性比数据一致性更加重要（AP优于CP），Spring Cloud Netflix在设计Eureka时就是遵守AP原则。

在Eureka的实现中，节点之间是互相平等的，部分注册中心的节点“挂掉“也不会对集群造成影响，即使集群只剩下一个节点存活，也可以正常提供服务。哪怕是所有的
服务注册节点都挂了，Eureka Clients也会缓存服务调用的信息。保证了微服务之间的互相调用时足够健壮的。

** 配置中心 **

配置中心功能：

1. 将配置进行集中管理，提供统一的配置服务，让开发，测试，生产环境均可直接从配置服务中读取配置信息
2. 能够追溯配置变更的历史，比如可以发现谁在什么时候修改了哪些内容，并能根据这些信息进行配置回滚操作
3. 配置中心还能够增加权限管理，保证不同角色不同权限的人只能访问有关权限的配置，保证线上配置信息的安全
4. 基于配置中心的微服务非常适合多环境的打包，不用多次打包，而是一次打包

