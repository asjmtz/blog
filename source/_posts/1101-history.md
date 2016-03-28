title: window.history 复习
date: 2014-11-02 11:06:59
categories: js
description: 使用 window.history 来控制浏览器前进和后退
---


## 复习

模拟用户点击浏览器后退按钮
```
window.history.back();
```

模拟用户点击浏览器前进按钮
```
window.history.forward();
```

可以用go()方法加载在session历史记录中的某个页面，参数是一个相对于当前页面的index值（当前页面是index0）

后退一页
```
window.history.go(-1);
```
前进一页
```
window.history.go(1);
```

history栈的页面数量可以由length属性得到：
```
var numberOfEntries = window.history.length;
```

**Note:**  Internet Explorer 支持将URL字符串作为参数传给go()，这种用法不是标准的用法，且Gecko不支持。

## HTML5 History

HTML5 引入了`history.pushState( )` 和 `history.replaceState( )` 方法，他们分别允许你添加和修改history实体。他们可以结合`window.onpopstate` 事件使用。

