---
layout: post
title: Java使用代理和interface完成初级架构
description: 公司Freamwork的提供的lib封装的精髓，怎样把事件业务与java api底层实现相关联。
category: blog
---

吐槽之公司Freamwork Team 对Java端封装，是使用jar的编程人员对Java API底层的透明，同时提供对公司业务interface的迎合。<br/>
这一切的基本点就是使用代理模式和对各种业务的抽象。在代理类继承业务抽象interface,具体被代理对象为底层实现Object。

下面就来说有Jackson,Restful,SOAP,TibCoEMS,JPA.的封装与实现。
Jackson,一个Json转Java object,Java object转Json的jar包，相信大家都比较熟悉。而且项目中可能都在用。因为JSON数据AJAX和
webservice传输的时候都可能会用，传输数据量相对xml较小。
