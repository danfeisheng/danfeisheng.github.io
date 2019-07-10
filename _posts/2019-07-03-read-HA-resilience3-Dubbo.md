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

可以看出，服务提供者Provider向服务注册中心Registry注册服务，而消费者Consumer从服务注册中心订阅所需要的服务，但不是所有服务，当有新的Provider出现，或者
现有Provider宕机时，注册中心Registry都会尽早发现，并将新的Provider列表推送给对应的Consumer。有了这样的机制，Dubbo才能做到Failover，而Failover的时效性，
由注册中心Registry的实现决定。

在配置初始化和服务注册与订阅完成后，剩下的工作就是对服务接口类进行包装，产生代理对象并返回。Dubbo实现代理对象的方式有两种，一种是使用JDK动态代理，
使用的是JDKProxyFactory，另一种是使用Javassit字节码来实现，使用JavassitProxyFactory来实现，Dubbo模式使用的是JavassitProxyFactory。

我们在调用服务接口的时候就会触发代理类，这个代理类是通过invokerInvocationHandler将服务接口包装成的一个代理类，通过代理类实现服务路由，服务选取，以及
与服务提供者Provider端的远程通信，这些过程服务调用者是无法感知的，就像在应用中调用本地方法一样。虽然使用简单，但是在性能上和调用本地方法却又很大的差别，
不仅要考虑服务提供者Provider的性能，还要考虑网络环境的健康状况。

每一个动态代理类都必须实现InvocationHandler接口，并且每个代理类的实例都关联了一个handler，当我们通过代理对象调用一个方法时，这个方法调用就会转为由
InvocationHandler接口的invoke方法来调用。

**功能降级：**

	功能降级一词最简单的解释就是“弃朱保帅”，而降级的目的就是停止一些非核心的系统以保证系统的核心功能能够正常使用。在Dubbo中，降级一词还有另一层含义，
	因网络，超时等异常长时间出现后，Dubbo通过正常的通信协议（比如Netty）无法正常工作，则可以考虑采用其他的通信方式，比如Hessian或HTTP的方式，一些非
	关键和实时的数据也可以调用本地缓存的数据返回。
	
** 容错负载：**
	
	容错负载是Dubbo的重要组成模块，该模块实现了多种集群特性，还实现了目录服务，负债均衡，路由策略，和服务治理配置等特性。
	
	- Invoker是服务提供者（Provider）的抽象，invoker封装了Provider地址及服务接口信息
	- Directory代表多个Invoker，可以把它看做一个List，但与List不同的是，它的值可能会动态变化，比如**注册中心推送变更**
	- Cluster将Directory中的多个Invoker伪装成一个Invoker，伪装过程包含了容错逻辑，调用失败后，重试另一个
	- Router可以从多个Invoker中通过路由规则进行过滤和筛选
	- LoadBalance可以从多个Invoker中选出一个
	
** 负载均衡：**
	
	- RoundRobin LoadBalance：权重轮询算法，按照公约后的权重设置轮询比例
	
	原理：把来自用户的请求轮流分配给内部的服务器，例如从1开始一直到N，N是服务器的总数，然后重新开始循环
	
	- LeastActive LoadBalance：最少活跃调用数均衡发
	
	原理：最少活跃调用数，活跃数指调用前后计数差，使慢的机器收到更少
	
	- ConsistentHash LoadBalance：一致性Hash算法
	
	原理：一致性Hash，相同参数的请求总是发到同一个提供者，一致性Hash算法可以解决服务提供者的增加，移除以及挂掉的情况，也可以通过构建虚拟节点，尽可能
	避免分配失衡，具有很好的平衡性
	
	- Random LoadBalance：随机均衡算法（** Dubbo默认的负载均衡策略 **）
	原理：按照权重设置随机几率，如果每个提供者的权重都相同，那么根据列表长度直接随机选取一个，如果权重不同，则累加权重值，从0~累加的权重值中选取一个
	随机数，然后判断该随机数落在哪个提供者上
	
** 集群策略：**

	- Failover Cluster：失败转移。当出现失败时，重试其他服务器，通常用于读操作，但重试会带来更长的延迟（** 默认集群策略 **）
	- Failfast Cluster：快速失败。只发起一次调用，失败立即报错，通常用于非幂等性操作
	- Failback Cluster：失败自动恢复。对于Invoker调用失败，后台记录失败请求，任务定时重发，通常用户通知。
	- Broadcast Cluster：广播调用。遍历所有Invokers，如果调用某个Invoker报错，则catch住异常，这样就不影响其他Invoker调用
	- Available Cluster：获取可用的调用。遍历所有Invokers并判断Invoker.isAvliable，只要有一个为true就直接调用返回，不管成不成功
	- Failsafe Cluster：失败安全。出现异常时直接忽略，通常用于写入审计日志等操作。
	- Forking Cluster：并行调用。** 只要一个成功即返回，通常用于实时性要求较高的操作，但需要浪费更多的服务资源 **
	- Mergeable Cluster：分组聚合。按组合并返回结果，比如某个服务接口有多种实现，可以用group分区，调用者调用多种实现并将得到的结果合并
	
默认的集群策略类FailoverClusterInvoker，通过doInvoke方法发起调用，FailoverClusterInvoker的重试次数默认是2次，所以最多执行3次。每一次重试都要新获取
可用的服务列表，然后根据选定的负载均衡策略选择出一个可用的服务调用，如果调用失败，则要判断当前异常是否是业务异常，如果是则不重试直接抛出异常。
	
** 在FailoverClusterInvoker实例中，通过select方法选择服务，在选择出一个可用的服务后，就正式进入调用环节了，也就是Result result = invoker.invoke(invocation)。
** 这行代码会经过一系列的Filter通过配置好的通信协议，远程调用响应的Provider，执行并返回结果，返回结果和异常信息全部封装到Result对象中，最终实现一次完整
** 的调用过程

Filter是一种递归的链式调用，用来在远程调用真正执行的前后加入一些逻辑，跟AOP的拦截器Servlet中Filter概念一样。