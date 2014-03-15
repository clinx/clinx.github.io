---
layout: post
title: 实现简单SOAP webservice，应该如何入手
description: SOAP简单对象访问协议，使用WSDL对服务进行描述。SOAP是用来最终完成Web服务调用的，而WSDL则是用于描述如何使用SOAP来调用Web服务的。
category: java
---

SOAP(Simple Object Access Protocol)一种webservice具体实现的代名词,基于应用层的协议同HTTP，也可以说比HTTP更上一层。而webserice一般是作用是与不同系统不同平台相互调用而产生的。那么不同系统和平台对数据
的实现是不同的，如果要对各自系统中的对象进行读写操作，那么就需要一个共用消息载体XML，因为各系统都有相当多的lib进行支持.唉扯远了。有了消息当然是不够的，还需要对消息格式的限制，SOAP则是一个基于xml的协议，大家都共同认可这种格式的协议，就是说你发什么request和
response的格式。这只是跟协议本身的request的和response有关。而我们一般想知道的这个webservice具体提供了什么样的服务，这个时候就有WSDL(webservice description language)来进行对你要提供的具体服务进行描述。

现在考虑一下如果你是服务提供商，相对外部提供内部的resource。比如你是证券公司的开放人员，你需要向别人提供接口访问每只股票具体数据。

需求有了，看一看技术，会点Java，OK。现在主流的soap webserice实现JAX-WS。相对于JAX-RPC，直接对SAAJ和JAXB的封装。省去对具体SOAP协议的实现和POJO转XML的过程。好像还不错。
实现过程一般分WSDL契约优先和JAVA CODE优先。  

  - WSDL契约优先就是先定义好wsdl文件，然后根据wsdl去写代码，而且要求对xml schema,有比较完善的了解。  
  - 代码优先的话就是先从code入手，然后根据annotation去生成wsdl和xsd。

看来还是代码优先方便点。不然就要自己定义wsdl的以下元素.  

  - Types:webserice中的参数和返回值类型。这里就是清晰莫只股票的ID.和这只股票的具体信息。  
  - Message：对webserice中的type进行分类，那些属于input,那些属于output.  
  - Operation: 把input和output再分组，归纳到不同的函数中。  
  - PortType:具体java类，可能包含多个operation(函数)  
  - Binding：端口类型的具体协议和数据格式规范的绑定。(soap/http)  
  - Port:定义为协议/数据格式绑定与具体Web访问地址组合的单个服务访问点。客服端用到的qname. 
  - Service:对应整个wsdl

<pre>
@WebService(serviceName = "StockQuoteService",
		portName = "StockQuotePort",	
		targetNamespace = "http://localhost:8080/stockquote")
@SOAPBinding
public class StockQuotePortType {

	@WebMethod
	public TradePriceResult GetLastTradePrice(TradePriceRequest tradeRequest) {

		TradePriceResult priceResult =new PriceResult(); 

		return priceResult;
	}
}</pre>

然后通过wsgen命令进行生成wsdl。客服断通过wsimport命令：http://localhost:8080/stockquote?wsdl进行生成客服端。实践开发中可能通过soapui继续测试。当然也可能用RESTful(一种框架，非标准)进行代替以上的实现。
