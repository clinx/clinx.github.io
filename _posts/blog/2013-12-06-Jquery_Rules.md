---
layout: post
title: Jqurey API and Construction
description: Jquery的使用与设计思想
category : blog
---


Jquery全世界都在用，没有理由不学习一下。开个玩笑，逗你玩。买卖是双方的事，由不得谁单方面做主。哈哈
非常喜欢的一个[Jquery源码分析][1]讲解blog，牛逼讲的非常到位。
但自己还是要总结一下，毕竟人的思维模式是不同的，方便自己。

从使用上看$().是一个反设计思想的单一原则设计。一个初始化构造方法可以接受9中参数。

    $(htmlDomelement),$('<tag>'),$('tagName'),$(function(){}),$("input:radio",parentDom),
    $(),$($()),$('tag',overrideProperties);
    
可以看到多半是为了构造一个Jquery工厂包装的dom对象。得到dom对象的参数特点是遵循CSS选择器
比如div, classname, identification等token进行组合使用。 而有一些自定义有比较常用的规则
比如odd,visiable像这样的$('li:odd'),$('div:visiable')就模仿CSS伪类一样定义。就不用需要记
多种选择器结构。
这里传入多种参数，在实现的时候肯定会通过if进行区分判断，而Jquery用了一种token的思想，先把
传入进来的参数用词法分析的套路进行取词，然后得到有顺序的取词结构进行语义分析。语义分析的时候
是从右到左的顺序进行解析。从有到左的好处是儿子一定只有一个父亲，而父亲不一定就只有一个儿子。
比如：<pre>
    div .clzName #idname   
    如果从左向右找首先要找到所有的div 然后遍历所有后代节点包含 .className的节点 然后才是Id
    而如果从右向左就不会有这种情况了，首先找id=#idname的节点,然后是自己的祖先节点，一直到div.
    这和一棵倒树，一样从叶子（明确对象）到根（明确）的路径一般很好找，而从根（明确）到叶子（模糊）不好找一样。
    </pre>


[1]: http://www.cnblogs.com/aaronjs/category/511281.html
