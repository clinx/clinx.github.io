---
layout: post
title: js setTimeout认清API
description: setTimeout用的时候请主要第一个参数，是函数对象，不是字符串。
category: blog
---

##凡事把先看自己的原因。

加了好多群，经常看见有人问setTimeout出错，唉。亲先看懂API [setTimeout][1].如果没看错第一个参数
setTimeout(function,milliseconds,lang) 是 function。
    <pre>
    定义一个方法：
    var i = 0;d
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
  </pre>
很显然想要的结果是 输出0，然后setTimeoutMetod。为什么是0，然后是setTimeout.好厉害，js不是说的单线程处理么。
单线程不是就一个单行道，干嘛会先输出0，然后输出setTimeout.
单线程的如果一条到跑完不就结束了，为什么当我们点击页面控件的时候，js处理线程还是在执行。

为了解决执行完就结束，想必大家都各自的解决方案。但一般实现是以让整个应用的代码处于一个循环当中。

    比如这样(以下是我YY的想的通的实现形式): 
    while(1){
        if(anyStatusChange){  //任何改变。就是其他进程|线程对js单线程状态改变 （windows 
                              //API捕获用户行为进程，由操作系统建立管道，改变js进程状态）
          var  queue = [主流程回调queue,event回调queue,setTimeout回调queue,Ajax回调queue等];//2微数组
          js主线程重复处理。模块
          for( i=0; i < queue.length;i++){
             var currentQueue = queue[i];
             for( j=0; j < currentQueue.length, j++){
                执行回调//回调的就是js的一级对象function.
             }
          }
        }else{
            sleep or wait to be aware.睡眠等待被唤醒。
        } 
    }

根据上面的代码现在来实现一下setTimeout,该怎么做到让其可以等待呢。
<pre>
   全熟假设,就当进行流程控制的一种想法：
   当程序调用setTimeout的时候。
   主程序记录下2个状态，和push一个方法回调。
   生成一个对象进行相互绑定。
   var setTimeoutItem = {};
   setTimeoutItem.startRecodeTime = +new Date();
   setTimeoutItem.delayTime = secondeArg;
   setTimeoutItem.callBack = (function(){return callMethod;})();
   queue[setTimeout回调queue].push(setTimeoutItem);

   然后是主流程对其的处理：//回调的就是js的一级对象function.
   var len = queue[setTimeout回调queue].length;
   if(len !==0 ){
      var currentTime = +new Date();
      for(i=0; i < len; i++){
         var currentSetTimeout = queue[setTimeout回调queue] [i];
         if(currentSetTimeout.delayTime+setTimeoutItem.startRecodeTime < currentTime){
            currentSetTimeout.callBack();
         }
      }
   }
</pre>
这样实现的话也能解释为什么js alert("block")阻塞，会影响delay的time和下面语句执行的输出<br/>
   1386253640566 - 1386253639811 < 1000.
<pre>
      setTimeout(function(){
             console.log(+new Date());//1386253640566
            }, 1000);
      var fibo = function(n){
         return n>1?fibo(n-1)+fibo(n-2):1; 
      }
      console.log(fibo(25));//121393

      console.log(+new Date());//1386253639811
</pre>


###最后给上Refernces:
- [Events and timing in-depth][TIMING]
- [timing-and-synchronization][SYNC]

[TIMING]: http://javascript.info/tutorial/events-and-timing-depth
[SYNC]: http://dev.opera.com/articles/view/timing-and-synchronization-in-javascript/
[1]: http://www.w3schools.com/jsref/met_win_settimeout.asp
