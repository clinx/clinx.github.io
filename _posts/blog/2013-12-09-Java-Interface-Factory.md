---
layout: post
title: Java使用代理和interface完成初级架构
description: 公司Freamwork的提供的lib封装的精髓，怎样把事件业务与java api底层实现相关联。
category: blog
---

吐槽之公司Freamwork Team 对Java端封装，是使用jar的编程人员对Java API底层的透明，同时提供对公司业务
interface的迎合。<br/>
这一切的基本点就是使用代理模式和对各种业务的抽象。在代理对象中