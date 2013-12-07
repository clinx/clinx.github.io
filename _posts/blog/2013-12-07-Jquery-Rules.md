---
layout: post
title: Jqurey API and Construction
description: Jquery的使用与设计思想
category: blog
---
Jquery全世界都在用，没有理由不学习一下。开个玩笑，逗你玩。买卖是双方的事，由不得谁单方面做主。哈哈
非常喜欢的一个[Jquery源码分析][1]讲解blog，牛逼讲的非常到位。
但自己还是要总结一下，毕竟人的思维模式是不同的，方便自己。

(1).从使用上看$().是一个反单一原则的设计。一个初始化构造方法可以接受9中参数。

    $(htmlDomelement),$('<tag>'),$('tagName'),$(function(){}),$("input:radio",parentDom),
    $(),$($()),$('tag',overrideProperties);
    
可以看到多半是为了构造一个Jquery工厂包装的dom对象。得到dom对象的参数特点是遵循CSS选择器
比如div, classname, identification等token进行组合使用。 而有一些自定义有比较常用的规则
比如odd,visiable像这样的$('li:odd'),$('div:visiable')如果不知道可以看看CSS3的API。
这里传入多种参数，在实现的时候肯定会通过if进行区分判断，而Jquery用了一种token的思想，先把
传入进来的参数用词法分析的套路进行取词，然后得到有顺序的取词结构进行语义分析。语义分析的时候
是从右到左的顺序进行解析。从有到左的好处是儿子一定只有一个父亲，而父亲不一定就只有一个儿子。
比如：
    div .clzName #idname   
    如果从左向右找首先要找到所有的div 然后遍历所有后代节点包含 .className的节点 然后才是Id
    而如果从右向左就不会有这种情况了，首先找id=#idname的节点,然后是自己的祖先节点，一直到div.
    这和一棵倒树，一样从叶子（明确对象）到根（明确）的路径一般很好找，而从根（明确）到叶子（模糊）不好找一样。
    所以这写CSS的时候 如果最右边是*号的话sublineLinter会提示修改选择器。因为这样会影响浏览器渲染性能。
    
用Jquery生成的对象，就可以对编程人员，比较简单的使用，因为我们操作的对象是经过Jquery构造的，而不是原生的
DOM对象，大家都知道原生的对象如果要对一下属性的增该，就要实现浏览器的兼容，而如果用Jquery的话这些问题，他
都用一种hook的思想进行代理处理。

(2.)Jquery Hooks使用的地方很多。val(),attr(),css(),.prop().可以看到这里的读取值，读取属性，读取css,读取原型值，
都是一个方法，set 和 get的结合，让我们操作这些值的时候都用一个入口。 而且在兼容的时候，Jquery使用适配器的模式进
行对具体的流量器进行处理。如果调用莫个属性就用具体的适配器进行处理。
比如有一个

     var mapHooks = ｛
        tabIndex : function(){兼容处理}，
        selected ：function(){兼容处理};
     ｝；
     jquery = function(){
       prop : function(key,value){
          if(!!key&&!value){
            return key in mapHooks?mapHooks(key):this[key];
          }
       }
     }
    $('dom').porp("tablIndex");

可以看到兼容都到特定的Hooks集合去处理了。这样可以统一处理，方便扩展，而且不用写太多的if分支判断。

(3.)js和dom之间的操作需要建立一座桥进行连接，代价大，为什么不是一起的呢，我们要想到最开始，浏览器是没有JS的，
是web发展了一定时间才发现需要，用户需要跟流量器进行客服端的交互行为，这才有了内存结构DOM结构书供JS调用的接口。
所以需要一个中间管理对象负责连接JS和DOM之间的操作，但又要保证良好的性能尽量少直接操作DOM.而有些对象可以不直
接和DOM绑定所以Jquery帮我们实现了数据事件缓存的机制，让我们不用自己去关心浏览器内存泄漏的一些问题。




[1]: http://www.cnblogs.com/aaronjs/category/511281.html
