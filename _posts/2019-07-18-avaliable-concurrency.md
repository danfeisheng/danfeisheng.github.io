---
layout: post
title: "读《高可用可伸缩微服务架构》感"
date: "2019-07-18 23:23"
author: "Danfei"
---
2019-07-18 23:23

读《高可用可伸缩微服务架构》感

微服务稳定性保证的常用手段

一般从两个维度来评估微服务的稳定性：技术实践，流程管理和支持，这里我们侧重于前者，我们一般从以下两点来考察系统的稳定性。

	* 高可用，当前服务依赖的校友系统性能降低或失败时，该服务应该怎么响应，是快速失败还是增加重试？大促时如何应对瞬间涌入的流量？
	*高并发：底层服务如何保证服务的吞吐量？如何提高消费者的处理速度？
	
高可用：

限流

断路器

超时与重试

高并发：

异步

缓存

