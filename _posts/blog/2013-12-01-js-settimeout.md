---
layout: post
title: js setTimeout认清API
category: blog
description: setTimeout用的时候请主要第一个参数，是函数对象，不是字符串。
---
##凡事把先看自己的原因。

    加了好多群，经常看见有人问setTimeout出错，唉。亲先看懂API[setTimeout][1]好不好。如果没看错第一个参数
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

[1]:  http://www.w3schools.com/jsref/met_win_settimeout.asp "w3cSchool setTimeout"