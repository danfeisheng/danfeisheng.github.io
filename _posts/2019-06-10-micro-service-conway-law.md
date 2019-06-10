---
layout: post
title: "Conway's Law"
date: "2019-06-10 22:14"
author: "Danfei"
---
2019-06-10 22:14

Micro Service - Conway's Law

参考自：https://www.cnblogs.com/gudi/p/6685474.html

微服务的很多核心理念其实在半个世纪前就被提出了，并且在软件飞速发展的这半个世纪不断被验证，它就是康威定律(Conway's Law).

在康威的这篇文章中最著名的一句话是：
	
	Organizations which design systems are constrained to produce which are copied of the communication structures of these organizations. 
	- Melvin Conway(1967)
	
	系统设计团队的组织构成，团队内外的沟通结构，很大程度上决定了其设计出来的产品，更加简明的，组织形式等于系统设计

第一定律：Communication dictates design（组织沟通方式会通过系统设计表达出来）

组织的沟通和系统设计间的紧密联系，解决好人与人之间的沟通，是一个好的系统设计很重要的点。《人月神话》中有一句著名的话：

	Adding manpower to a late software project make it later - Fred Brooks(1975)

沟通成本 = n(n-1)/2,算法复杂度为O(n^2)。沟通成本随着项目人数的增加呈指数级增长

第二定律：There is never enough time to do something right, but there is always enough time to do it over
（时间再多一件事情也不可能做的完美，但总有时间做完一件事情）

	Problem too complicated? Ignore details. 
	
	Not enough resources?Give up features.
	
	-- 《Efficiency-Effectiveness Trade Offs》Eric Hollnagel (2009)

其实我们在日常开发中也经常碰到。产品经理的需求太复杂了？适当忽略一些细节，先抓主线。产品经理的需求太多了？放弃一些功能。

做到安全有两种方式：

	常规的安全指的是尽可能多的发现并消除错误的部分，达到绝对安全，这是理想。

	另一种则是弹性安全，即使发生错误，只要及时恢复，也能正常工作，这是现实。

建议放弃打造完美系统的想法，而是通过不断的尝试，发现问题，确保问题发生时，系统能自动复原即可，而不追求系统的绝对正确和安全。

这其实和持续集成，敏捷开发理念异曲同工。

这和互联网公司维护的分布式系统的弹性设计也是一个道理。对于一个分布式系统，我们几乎永远不可能找到并修复所有的bug，单元测试覆盖1000%也没有用，
错误流淌在分布式系统的血液里。解决方法不是消灭这些问题，而是容忍这些问题，在问题发生时，能自动回复，微服务组成的系统，
每一个微服务都可能挂掉，这是常态，我们只有有足够的冗余和备份即可。即所谓的 弹性设计（Resilience） 或者叫高可用设计（High Availability）。

第三定律：There is a homomorphism from the linear graph of a system to the linear graph of its design organization
（线型系统和线型组织架构间有潜在的异质同态特性）        

组织和设计间内在关系。更直白的说，你想要什么样的系统，就搭建什么样的团队。

如果你的系统是按照业务边界划分的，按照一个业务目标去把自己的模块做出小系统，小产品的话，即微服务的架构

  微服务的理念团队间应该是 inter-operate, not integrate 。inter-operate是定义好系统的边界和接口，
  在一个团队内全栈，让团队自治，原因就是因为如果团队按照这样的方式组建，将沟通的成本维持在系统内部，每个子系统就会更加内聚，
  彼此的依赖耦合能变弱，跨系统的沟通成本也就能降低。
  
第四定律： The structures of large systems tend to disintegrate during development, qualitatively more so than with small systems
（大的系统组织总是比小系统更倾向于分解）

我们的组织一般是如何解决这个沟通问题的呢？Divide and conquer,分而治之。

所以，一个大的组织因为沟通成本/管理问题，总为被拆分成一个个小团队。

最后， 康威定律 告诉我们组织沟通的方式会在系统设计上有所表达，每个经理都被赋予一定的职责去做大系统的某一小部分，
他们和大系统便有了沟通的边界，所以大的系统也会因此被拆分成一个个小团队负责的小系统（微服务是一种好的模式）

**康威定律如何解释微服务的合理性**

了解了康威定律是什么，再来看看他如何在半个世纪前就奠定了微服务架构的理论基础。

1. 人与人的沟通是非常复杂的，一个人的沟通精力是有限的，所以当问题太复杂需要很多人解决的时候，我们需要做拆分组织来达成对沟通效率的管理
2. 组织内人与人的沟通方式决定了他们参与的系统设计，管理者可以通过不同的拆分方式带来不同的团队间沟通方式，从而影响系统设计
3. 如果子系统是内聚的，和外部的沟通边界是明确的，能降低沟通成本，对应的设计也会更合理高效
4. 复杂的系统需要通过容错弹性的方式持续优化，不要指望一个大而全的设计或架构，好的架构和设计都是慢慢迭代出来的
      
带来的具体的实践建议是：

1. 我们要用一切手段提升沟通效率，比如slack，github，wiki。能2个人讲清楚的事情，就不要拉更多人，每个人每个系统都有明确的分工，
出了问题知道马上找谁，避免踢皮球的问题。
2. 通过MVP的方式来设计系统，通过不断的迭代来验证优化，系统应该是弹性设计的。
3. 你想要什么样的系统设计，就架构什么样的团队，能扁平化就扁平化。最好按业务来划分团队，这样能让团队自然的自治内聚，
明确的业务边界会减少和外部的沟通成本，每个小团队都对自己的模块的整个生命周期负责，没有边界不清，没有无效的扯皮，inter-operate, not integrate。
4. 做小而美的团队，人多会带来沟通的成本，让效率下降。亚马逊的Bezos有个逗趣的比喻，如果2个披萨不够一个团队吃的，那么这个团队就太大了。
事实上一般一个互联网公司小产品的团队差不多就是7，8人左右（包含前后端测试交互用研等，可能身兼数职）。
      
再对应下衡量微服务的标准，我们很容易会发现他们之间的密切关系：

1. 分布式服务组成的系统
2. 按照业务而不是技术来划分组织
3. 做有生命的产品而不是项目
4. Smart endpoints and dumb pipes（我的理解是强服务个体和弱通信）
5. 自动化运维（DevOps）
6. 容错
7. 快速演化