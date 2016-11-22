title: 我就是记不住 callee 与 caller 的区别
date: 2015-04-22 16:53:14
categories: js
description: js 中的 callee 与 caller 的区别
---
# 魔法阵在召唤~~~~

### caller 与 callee

```

function fun1(){

  // callee [kɔ:'li:] 被召者 当前函数--我
  console.log('callee=====',arguments.callee)
  
  // caller ['kɔ:lə] 召唤者 调用当前函数的函数--调用我的函数
  console.log('caller=====',arguments.callee.caller)
 //or console.log(fun1.caller)	
}

function fun2(){
  fun1();
}

fun2();
```
[demo](http://jsbin.com/miqok/5/edit)