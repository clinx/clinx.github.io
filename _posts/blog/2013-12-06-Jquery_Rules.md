---
layout: post
title: Jqurey API and Construction
description: Jquery的使用与设计思想
category: blog
---

Jquery全世界都在用，没有理由不学习一下。开个玩笑，逗你玩。买卖是双方的事，由不得谁单方面做主。哈哈
非常喜欢的一个[Jquery源码分析][1]讲解blog，牛逼讲的非常到位。
但自己还是要总结一下，毕竟人的思维模式是不同的，方便自己。

从使用上看$().是一个反设计思想的单一原则设计。一个初始化构造方法可以接受9中参数。

    $(htmlDomelement),$('<tag>'),$('tagName'),$(function(){}),$("input:radio",parentDom),
    $(),$($()),$('tag',overrideProperties);
    
可以看到多少是为了构造一个Jquery工厂包装的dom对象。得到dom对象的参数特点是遵循CSS
选择器

    
[1]: http://www.cnblogs.com/aaronjs/category/511281.html
