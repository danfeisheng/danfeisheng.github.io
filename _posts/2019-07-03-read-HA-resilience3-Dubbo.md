---
layout: post
title: "读《高可用可伸缩微服务架构》感"
date: "2019-07-04 21:45"
author: "Danfei"
---
2019-07-03 21:45

读《高可用可伸缩微服务架构》感

第三章 Dubbo框架的原理与实现

作为Dubbo使用者，最先接触的是Dubbo的配置文件，在配置文件中配置注册中心，服务提供者，服务消费者等。而我们所使用的Spring标签则是Dubbo自定义的标签，
比如dubbo:service/等。Spring解析这些自定义的标签并将信息封装在Dubbo的Config中。

Spring自定义标签的使用，在Spring中完成一个自定义标签需要如下步骤：

	- 设计配置属性和JavaBean
	- 编写XSD文件
	- 编写BeanDefinitionParser标签解析类
	- 编写调用标签解析类的NamespaceHandler类
	- 编写spring.handlers和spring.schemas以供Spring读取
	- 在Spring中使用
	
Dubbo在启动的时候会从dubbo-container/dubbo-container-spring包中的SpringContainer类开始，这个类主要负责启动Spring的上下文，加载解析Spring的配置文件

所有的Dubbo标签都统一用DubboBeanDefinitionParser进行解析，基于一对一属性映射，将XML标签解析为Bean对象。在DubboBeanDefinitionParser解析类中最关键的是
parse方法。

Dubbo的架构体系采用的是“微核+插件”，这样做使整个架构的扩展性更强，可以在不修改核心代码的情况下进行新增插件的添加，而这个体系中最核心的机制是采用SPI，
为接口寻找服务实现的机制，这个机制与Spring中的IoC思想有些类似，将程序中接口与实现的强关联关系变成可插拔关系。

1. Java SPI
	
SPI全称为Service Provider Interface，是JDK内置的一种服务提供发现功能，一种动态替换发现的例子。

在程序执行前我们并不知道Protocol接口要加载的是哪个实现类，Dubbo通过getAdaptiveExtension方法利用默认的JavassistCompiler生成上述ProtocolAdapative类，
可以看到在Protocol接口类中所有被加了@Adaptive注解的方法都有了具体的实现，整个过程类似于Java的动态代理或Spring的AOP实现。当我们使用refprotocol对象
调用方法时，其实是调用Protocol Adaptive类中对应的代理方法，根据URL的参数找到具体实现类名称，然后通过ExtensionLoader对象的getExtension方法找到具体
实现类进行调用。

Dubbo与注册中心的结构图：

![avatar](https://www.processon.com/view/link/5d234535e4b0aad4c929b27c)