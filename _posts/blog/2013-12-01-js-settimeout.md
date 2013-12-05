---
layout: post
title: js setTimeout认清API
category: blog
description: setTimeout用的时候请主要第一个参数，是函数对象，不是字符串。
---

##凡事把先看自己的原因。

加了好多群，经常看见有人问setTimeout出错，唉。亲先看懂API.如果没看错第一个参数
setTimeout(function,milliseconds,lang) 是 function。
    
    定义一个方法：
    var i = 0;
    var callMethod = function(){
    	console.log("setTimeoutMetod");
    	i = i+1;
    	return function(){};
    }

    错误演示：
    1).  setTimeout(callMethod(),1000);
         console.log(i);
         输出顺序setTimeoutMetod，然后1
    2).  setTimeout("callMethod()",1000);//虽然输出正确，但不建议这样使用。
         console.log(i);
         输出0，然后setTimeoutMetod。
    一般性正确使用：
        setTimeout(callMethod,1000); //注意这个参数是一个Function object.相当于C或C++中的函数指针，C#的委托。
        console.log(i);
        输出0，然后setTimeoutMetod。

很显然想要的结果是 输出0，然后setTimeoutMetod。为什么是0，然后是setTimeout.好厉害，js不是说的单线程处理么。
单线程不是就一个单行道，干嘛会先输出0，然后输出setTimeout.
单线程的如果一条到跑完不就结束了，为什么当我们点击页面控件的时候，js处理线程还是在执行。

为了解决执行完就结束，想必大家都各自的解决方案。但一般实现是以让整个应用的代码处于一个循环当中。

