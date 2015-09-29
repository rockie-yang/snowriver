---
ID: 715
post_title: 多语言系统设计
author: riv
post_date: 2012-07-22 04:53:37
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2012/07/22/%e5%a4%9a%e8%af%ad%e8%a8%80%e7%b3%bb%e7%bb%9f%e8%ae%be%e8%ae%a1/
published: true
---
<!--:en--><img alt="" src="http://www.zcars.com.au/images/ford-powershift-gearbox12.jpg" title="GearBox" class="alignright" width="320" height="200" />Every employee is expected to be a universal screw by managers, from designing system, trouble shooting, to holding customer presentation. No matter what problems happened, any employee should be able to solve it.

Why there are so many types of screws in hardware shop? Why so many different gear types in a gear box?

When come to software design world, one programming language also can not fit all scenarios. Of course, programming languages need be minimized in a product.

<ul>
	<li>Some parts are very stable. Some parts need be changed very often</li>
	<li>Some components only need be modified by few gurus. Most component need be created by new comers after few days training</li>
	<li>Some paths are performance critical. Some path are not</li>
</ul>

When come to programming language selection, following criteria are applicable to me.
<ul>
	<li>What is the target platform? Does the product need cross different platforms? If the product only need to be ran on Windows, .Net can be good choice</li>
	<li>How is the execution performance? Like assemble is very good in execution performance, it is still very useful in critical path even the development efficiency is very low</li>
	<li>How is the development efficiency? Like Matlab can be very efficient when goes to modelling</li>
	<li>How good is the development tool? Visual Basic already very developer friendly in last century</li>
	<li>Are there good libraries. Like Java, there are always libraries which you can think of</li>
	<li>Is it easy to find programmers? <a href="http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html">TIOBE Language Index</a> can be a good reference</li>
	<li>Is it difficult to learn? Python is quite easy to get in. While Scala is more difficult. Most developers can not master it in short time</li>
	<li>Is it easy to debugable? Like C++ meta template, it is only debugable in brain. Like XSLT, almost no way to debug (Although XMLSpy has debug feature, but the product normally not executed by XMLSpy which will lead result different)</li>
	<li>Is it easy to understand? Like workflow is very easy to understand</li>
	<li>Is it fun to work with? Java for me is just a work language, no fun at all. There are much more fun programming in C/C++/Python/Scala</li>
</ul>

From TIOBE programming language index, just select few popular languages（If the product needs be ran on different platform）
<ul>
	<li>C/C++/Java can be good alternatives as system language</li>
	<li>Python/Ruby/JavaScript can be good alternatives as business logic language</li>
</ul>

Due to some practical reason, I wanna make a prototype based on Jython. System design in Java and business logic design in Python. Following use case need be fulfilled and have real hands on by myself.
<ul>
	<li>IP protection. libraries need be protected in certain ways. Jython->Java Class</li>
	<li>Data model. JSON as data model</li>
	<li>Debug. log, unit test, remote debug</li>
	<li>External system communication. Spring Integration</li>
	<li>Monitoring and Visualize execution. HypericQ</li>
	<li>Data persistency. NoSQL</li>
	<li>Continuous Integration. ?</li>
	<li>Package and distribution. Jython->Java Class->Jar and Execute Jython directly</li>
</ul>
<!--:--><!--:zh--><img alt="" src="http://www.zcars.com.au/images/ford-powershift-gearbox12.jpg" title="GearBox" class="alignright" width="320" height="200" />似乎大部分老板都希望每个员工都是一个万能螺丝钉。哪里有需要就拧到哪里去！要写的了代码，查得出异常，还要能忽悠得了客户。哪个模块出了问题，任何人上去都要能改的了。

那五金店里为什么有那么多种型号的螺丝钉呢？

在一个系统里面往往也比较适合用多种语言开发，特别是那些企业系统。良好的分层设计往往是一个系统成功的关键。
<ul>
	<li>有比较稳定的部分，有经常需要变化的部分</li>
	<li>有少部分东西只有牛人才能改的动，有大部分东西新人经过两天培训也要能改的动</li>
	<li>有性能非常关键的模块，也有无所谓的模块</li>
</ul>

对于语言或者一种技术的选择，下面的一些指标可以考虑。
<ul>
	<li>目标平台。需不需要跨平台，还是只在某一个平台运行就可以了。如果只在Windows使用，那.Net就很适合了</li>
	<li>执行性能好不好。像汇编执行性能就很好，如果某些需要极限速度的时候就可以考虑用汇编来写。还能使用最新CPU的指令来使性能最大化</li>
	<li>开发效率是否高。像Matlab对数学建模开发效率就非常高</li>
	<li>开发工具是否好用。像VB,VC在6.0的时候就相当好用了</li>
	<li>库是否丰富。像Java基本上能想到的库都能找到</li>
	<li>开发人员多不多。可以参考<a href="http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html">TIOBE 软件语言排名</a>。C语言有排到第一名了，C一切尽在掌握的感觉还是很爽的</li>
	<li>学习曲线。像Python很容易入门。Scala就难多了，没有一定的数学功底还真不容易</li>
	<li>调试能力强不强。像C++的模板，只能在大脑里调试。像XSLT基本没法调试(虽然XMPSpy有单步功能，但是和用其它库写出来的结果不一定一致)</li>
	<li>是否直观。像工作流就很直观，根本不需要是程序员</li>
	<li>有没有乐趣。Java纯粹就是一个工作语言，没有任何乐趣可言。C/C++/Python/Scala每种语言都比Java有乐趣</li>
</ul>

从TIOBE的前几名语言来看（如果需要跨平台的话）
<ul>
	<li>C/C++/Java作为系统设计语言比较适合。</li>
	<li>Python/Ruby/JavaScript作为业务逻辑设计比较适合。</li>
</ul>

基于某些客观原因。打算搞一个基于Jython的原型玩玩。Java写系统框架，Python写逻辑。<!--:-->