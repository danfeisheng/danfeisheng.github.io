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

简单来说，服务化的核心就是将吃醋安通的一站式应用根据业务拆分成一个个服务，而微服务在这个基础上要更加彻底的去耦合（不再共享DB，KV，去掉重量级的ESB），
并且强调DevOps和快速演化。

DevOps是Development和Operations的合体，它要求开发，测试，运维进行一体化的合作，进行更小，更频繁，更自动化的应用发布，以及围绕应用架构来构建基础设施
的架构。这就要求应用充分的内聚，也方便运维和管理，这个理念和微服务不谋而合。

REST是Representational State Transfer的缩写，含义为表述性状态转移，REST是一种软件架构风格，REST并非标准，而是一种开发Web应用的设计和开发方式，可以降低
开发的复杂度，提高系统的可伸缩性。相比较于基于传统的SOAP协议，XML格式的WSDL的webService来说，REST模式提供了更简洁的实现方案