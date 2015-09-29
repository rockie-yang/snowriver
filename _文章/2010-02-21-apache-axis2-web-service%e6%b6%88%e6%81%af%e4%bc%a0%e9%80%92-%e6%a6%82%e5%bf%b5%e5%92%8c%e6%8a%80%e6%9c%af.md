---
ID: 93
post_title: 'Apache Axis2 Web Service消息传递: 概念和技术'
author: riv
post_date: 2010-02-21 22:08:36
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2010/02/21/apache-axis2-web-service%e6%b6%88%e6%81%af%e4%bc%a0%e9%80%92-%e6%a6%82%e5%bf%b5%e5%92%8c%e6%8a%80%e6%9c%af/
published: true
---
本文意译自<a href="http://onjava.com/pub/a/onjava/2005/07/27/axis2.html">http://onjava.com/pub/a/onjava/2005/07/27/axis2.html</a>
Web Services Messaging with Apache Axis2: Concepts and Techniques
by Srinath Perera, Ajith Ranabahu
07/27/2005
这段时间在看WebService的东西。看到这篇文章还没有中文翻译，就翻译一下，锻炼一下自己的英语。本文可以被任意转载，如有转载请注明出处。<!--more-->

直到最近，Web Service最自然的交互方式一直是基于同步的请求响应模式。很快人们发现同步的请求响应模式只是消息传递场景中很小的一个子集。消息传递在构造松耦合系统起着至关重要的作用，同步的请求响应模式的局限性是显而易见的。和消息传递相关的Web service标准如WS-addressing和WSDL，已经为发现更多的消息传递模型打下了基础。Apache Axis2架构不假定一种特定消息交换模型，同步或者是异步行为。本文解释消息传递概念以及如何使用Axis2实现几种常用的消息传递场景.

消息传递简介

在分布式计算中的最大挑战是：当资源是分布之后如何解决进程间通信问题。研究人员还在试图找更好的解决方案。有趣的是，几乎所有的解决方案都基于：远程过程调用（RPC）和消息传递。

毫无疑问，使用远程过程调用在开发人员中更受欢迎。这部分由于它和本地过程调用类似。由于开发人员熟悉本地过程调用，所以在开发分布式系统时自然的倾向就是使用RPC风格。而另一方面，消息传递却没有那么受欢迎。提及消息传递时，只有少部分开发人员愿意抬眼看看。虽然如此，消息传递在某些应用场合比RPC提供更大的优势。

RPC和消息传递的基本区别如下：

* 消息传递没有客户端和服务器感念。因为消息传递框架（也称为面向消息中间件MOM）更专注于投递消息。各个节点在接收和发送消息时都是等价的。RPC有服务请求者（也叫客户端）和服务提供者（也叫服务器）的概念。
* 消息传递是时间独立的。没有节点期待实时接收消息。面向消息中间件负责将消息投递到相关节点。RPC在一方不可用时无法完成。
    * 消息传递可以简单的多次传递或投递到多个节点。而RPC从本质上来说是一对一通信。消息传递更加灵活，多次传递或投递到多个节点对发送者来说不需要做什么事。

Web Service消息传递

Web services定义基于XML消息传递。下面三个参量描述一个Web Service消息交互。

   1. 消息交换模式（Message exchange pattern MEP）
   2. 同步和异步客户端API
   3. 单向（One-way）/双向（two-way）传输行为


Web service消息传递基于发送和接收消息。一个消息由一方发送，由另一方接收。消息可能相互关联，这样的消息组定义为消息交换模式（MEP）。

客户端API定义消息请求者的同步/异步行为。同步请求中，客户端API调用时将阻塞并等待消息到达目的地。异步请求中，客户端API调用时不阻塞，当相关消息到达时，它将与前面消息关联。

传输可以归类为单向传输（单工）或双向传输（双工）。SMTP和JMS是单向传输，它们不阻塞。HTTP和TCP是双向传输，响应消息可以通过返回通道传回。在Web service消息传递中，双向传输可以用作单向传输，在这种情况下也可以直接当做单向传输。

消息交换模式Message Exchange Patterns

按照W3C建议，MEP用于建立通信双方交换信息的模式。MEP确定相关消息的通用分组。MEP定义基于服务请求者和服务提供者。需要指出的是，MEP命名基于服务提供者端消息特性。所有的名字都可以这么理解，in就是请求，out就是响应。

让我们使用最常见的两个MEP来举例子。

   1. 单向In-only/"触发并忘记fire and forget:"。服务请求者发送一个消息到服务提供者而不期待任何相关的响应消息。
   2. 双向In-out/"请求响应request response:"。服务请求者发送一个消息到服务提供者并期待响应消息。


MEP的概念还在不停的发展。模式的数量是无限的，而消息中间件只可能实现几个MEP。其中"触发并忘记"和"请求响应"是最通用的，其它的模式可以使用这两种来构建。
客户端API同步/异步行为
同步（阻塞）/异步（不阻塞）行为基于处理web service调用的线程。同步服务会阻塞并等待响应消息返回。异步服务不会阻塞直接返回，响应消息的传输会由后台运行的另一个线程处理。

两种行为都有重要的应用场合。例如一笔银行交易需要一系列的信息来回交互。银行交易是顺序的，在执行下一步之前必须拿到前一步的结果，所以同步等待结果是必要的。例如一个机票预订程序需要从多个地方获取数据并根据结果匹配。这时异步方式可以工作，预订程序可以递交请求并在数据返回后继续工作。考虑到网络延迟，异步方式常常可以得到更好的效果。
同步调用相当简单。调用者等待响应消息返回，编码时和本地调用没什么两样。但是异步消息要相对复杂些，客户端必须要处理这些复杂性。但是在某些场合下处理这种额外的复杂性是还是有必要的。
传输层行为
传输层行为对web service如何传递消息至关重要。传输按照行为可以归类为单向传输或双向传输。
单向传输简化了web service消息传递的复杂性，响应消息必须通过另一个通道返回。如果传输是双向的，消息传递可以选择使用单向还是双向方式。如当传输是HTTP时，响应消息可以通过HTTP连接的返回路径返回；或者web service提供者可以写HTTP 200 表示在该连接没有响应返回，在这种情况下响应需要通过另一个独立的HTTP连接发送。
Web Service Addressing角色
Web service addressing （也叫WS-addressing） 奠定了web service 交互双方信息交互框架。下面五个参数定义了一个交互。
1.	信息交互模式Message exchange pattern
2.	通过可用服务传输
3.	传输相关消息
4.	传输的行为
5.	客户端同步/异步行为
服务提供者声明前两个，客户端可以随意定义剩下的几个。客户端的同步/异步行为对服务提供者来说是完全透明的，客户端使用WS-addressing来解释web service传递。
在许多概念中， WS-addressing定义了四个：To、ReplyTo、RelatesTo和FaultTo以及一个特别的地址Anonymous。当服务提供者接收到一个SOAP消息，找到TO地址并调用对应服务。如果有返回结果则发送到ReplyTo地址；如果有错误发生则发送到FaultTo地址。如果ReplyTo或FaultTo没有指定或者指定为Anonymous ，响应消息通过双向传输的返回路径返回（因为Anonymous始终解析为双向传输的默认返回路径）
传输和WS-addressing一起定义了消息关联机制。同步传输时消息通过共享同一个传输通道关联。异步传输时消息通过共享公用信息关联，WS-Addressing的RelateTo头用于定义这种关系。
下表为几种常见消息交换定义的值。
MEP	Sync/Async	Transport Behavior	Message	To	ReplyTo	RelatesTo
In-Only	NA	One-way	IN	Optional	Optional	NA
In-Out	Sync	One	IN	Optional	Anonymous	NA
			OUT	Anonymous	Optional	IN-Message
In-Out	Async	One	IN	Optional	Anonymous	NA
			OUT	Anonymous	Optional	IN-Message
In-Out	Async	two	IN	Required	Required	NA
			OUT	Required	Optional	IN-Message
注：由于原文写于2005年，Axis2从0.94之后改变了Client端的实现，在后面章节中的例子描述和目前的Axis2 1.5.1版本不太一致。所以后面的章节没有翻译，而是从Axis2 1.5.1的示例和源码中摘取。
Axis2客户端
Axis2的客户端，无论是单向还是双向都由类ServiceClient实现。下面对各个例子分别说明。英文请参考http://ws.apache.org/axis2/1_5_1/userguide.html。

单向
Axis2客户端的单向传输由类ServiceClient的sendRobust函数实现，示例代码如下。
public class MailClient {

    private static String toEpr = "http://localhost:8080/axis2/services/myservice";

    public static void main(String[] args) throws AxisFault {
        Options options = new Options();
        options.setTo(
                new EndpointReference(toEpr));
        options.setAction("urn:echo");
        ServiceClient servicClient = new ServiceClient();

        servicClient.setOptions(options);

        servicClient.sendRobust(getPayload());
    }

    private static OMElement getPayload() {
        OMFactory fac = OMAbstractFactory.getOMFactory();
        OMNamespace omNs = fac.createOMNamespace(
                "http://example1.org/example1", "example1");
        OMElement method = fac.createOMElement("echo", omNs);
        OMElement value = fac.createOMElement("Text", omNs);
        value.addChild(fac.createOMText(value, "Axis2 Echo String "));
        method.addChild(value);

        return method;
    }
}

双向同步

Axis2客户端的双向同步传输由类ServiceClient的sendReceive函数实现，示例代码如下。
public class EchoBlockingClient {
    private static EndpointReference targetEPR = new EndpointReference("http://localhost:8080/axis2/services/MyService");

    public static void main(String[] args) {
        try {
            OMElement payload = ClientUtil.getEchoOMElement();
            Options options = new Options();
            options.setTo(targetEPR);
            options.setAction("urn:echo");

            //Blocking invocation
            ServiceClient sender = new ServiceClient();
            sender.setOptions(options);
            OMElement result = sender.sendReceive(payload);

            System.out.println(result);

        } catch (AxisFault axisFault) {
            axisFault.printStackTrace();
        }
    }
}
如果希望发送和接收使用不同的通道可以为option指定setUseSeparateListener为True。个人感觉这种方式从底层实现来说和双向异步应该是一样的。


            options.setTransportInProtocol(Constants.TRANSPORT_HTTP);
            options.setUseSeparateListener(true);

双向异步
Axis2客户端的双向同步传输由类ServiceClient的sendReceiveNonBlocking函数实现，示例代码如下。
public class EchoNonBlockingClient {
    private static EndpointReference targetEPR = new EndpointReference("http://127.0.0.1:8080/axis2/services/MyService");

    public static void main(String[] args) {
        ServiceClient sender = null;
        try {
            OMElement payload = ClientUtil.getEchoOMElement();
            Options options = new Options();
            options.setTo(targetEPR);
            options.setAction("urn:echo");

            //Callback to handle the response
            AxisCallback callback = new AxisCallback() {
            	public void onComplete() {
				}

				public void onFault(MessageContext arg0) {
				}

				public void onMessage(MessageContext arg0) {
				}

				public void onError(Exception arg0) {
				}
            };

            //Non-Blocking Invocation
            sender = new ServiceClient();
            sender.setOptions(options);
            sender.sendReceiveNonBlocking(payload, callback);

            Thread.sleep(1000);

        } catch (AxisFault axisFault) {
            axisFault.printStackTrace();
        } catch (Exception ex) {
            ex.printStackTrace();
        } finally {
            try {
                sender.cleanup();
            } catch (AxisFault axisFault) {
                //
            }
        }
    }
}
Resources
•	Official website of Apache Axis2
•	W3C draft, "WSDL Version 2.0 Part 2: Message Exchange Patterns"
•	"Why Messaging? The Mountain of Worthless Information," Ted Neward's blog, Wednesday May 9, 2003
•	"Introduction to WS-Addressing," by Beth Linker, dev2dev, January 31, 2005
•	"Async," Dave Orchard's blog, May 05, 2005
•	"Programming Patterns to Build Asynchronous Web Services," Holt Adams, IBM Developer Works, June 1, 2002
Srinath Perera is a principal architect for Apache Axis2. 
Ajith Ranabahu is a committer for the Apache Axis2 project and has been working on web-service-based projects for the past three years.

