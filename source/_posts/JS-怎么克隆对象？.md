title: "JS 怎么克隆对象？"
date: 2015-04-27 17:12:03
categories: js
description: JS 克隆对象, JSON, $.extend, 性能
---

## 对象克隆

不能影响到原来的对象，并且需要深层拷贝的情况，比如原始对象为：

```
var oldObj = {a:2123, b:2312, c:[2,3,45,2]};

```

考虑以下方法：

1、使用 JSON API

```
var newObj = JSON.parse( JSON.stringify(oldObj));
```

2、考虑 jquery extend

```
var newObj = $.extend(  )

```

3、 new Object() 

balabalabala....再研究。。。

## demo

[In JS Bin](http://jsbin.com/hudoke/2/edit)

## 速度测试

[In JS performance](http://jsperf.com/clone-object-ways)

> Written with [StackEdit](https://stackedit.io/).
