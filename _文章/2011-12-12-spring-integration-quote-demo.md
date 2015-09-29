---
ID: 635
post_title: 'Spring Integration &#8211; Quote Example理解'
author: riv
post_date: 2011-12-12 22:58:22
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/12/12/spring-integration-quote-demo/
published: true
---
前两天看了一个关于Spring Integation的Webnar。感觉相当不错。Spring从最初的反转控制到现在做的如此之大确实让人感叹。Spring的那些人既能写代码，又能做这么好的演讲，另人佩服。

准备花段时间看一下，也把自己的理解记一下。
大致翻了一下<a href="http://static.springsource.org/spring-integration/reference/pdf/spring-integration-reference.pdf">Spring 参考文档</a>
下载了并安装了<a href="http://www.springsource.com/downloads/sts">Spring Tools Suite</a>
下载了<a href="https://github.com/SpringSource/spring-integration-samples/zipball/master">示例代码</a>
解压示例代码，并在STS里以导入Maven工程方式导入。

Spring的依赖注入之后各个模块之间的关系不是特别直观了。完全依赖于对Spring配置文件的理解。Quote是一个非常简单的例子。
<pre class="brush:xml">
// Spring 依赖这些namespace来决定需要干什么
<beans:beans xmlns="http://www.springframework.org/schema/integration"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:stream="http://www.springframework.org/schema/integration/stream"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
			http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/integration
			http://www.springframework.org/schema/integration/spring-integration.xsd
			http://www.springframework.org/schema/integration/stream
			http://www.springframework.org/schema/integration/stream/spring-integration-stream.xsd">

	// 这是一个输入的通道，名字叫tickers。每300ms调用一次tickerStream的nextTicker函数
	<inbound-channel-adapter ref="tickerStream" method="nextTicker" channel="tickers">
		<poller fixed-delay="300" max-messages-per-poll="1"/>
	</inbound-channel-adapter>

	<channel id="tickers"></channel>


	// 这是一个输出通道，名字叫quotes。
	<stream:stdout-channel-adapter id="quotes" append-newline="true"></stream:stdout-channel-adapter>


	// 上面tickerStream bean
	<beans:bean id="tickerStream" class="org.springframework.integration.samples.quote.TickerStream"/></beans:bean>


	// 一个quoteService bean。在上面的例子中只有输入和输出通道。QuoteService里定义的另外一组输入和输出。
	<beans:bean id="quoteService" class="org.springframework.integration.samples.quote.QuoteService"></beans:bean>

</beans:beans>
</pre>
QuoteService定义。
<pre class="brush:java">
@MessageEndpoint
public class QuoteService {
	// 输入通道是tickers，输出通道是quotes
	@ServiceActivator(inputChannel="tickers", outputChannel="quotes")
	public Quote lookupQuote(String ticker) {
		BigDecimal price = new BigDecimal(new Random().nextDouble() * 100);		
		return new Quote(ticker, price.setScale(2, RoundingMode.HALF_EVEN));	
	}
}
</pre>

Spring根据上面几个通道定义，将每一个输入和输出一相连，就形成了一个有机的链条。