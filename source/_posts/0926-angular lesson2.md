title: Angular Bootstrapping 引导
date: 2014-09-26 11:06:59
categories: Angular
description: Angular phonecat 教程 part 2 —— bootstrapping 
---

##angular Bootstrapping—— 引导

##step 0

进入 `angular-phonecat` 的目录，运行命令：

```
git checkout -f step-0
```

这个命令会重置我们的工作区到教程的初始步骤step 0。
跑 `npm start` ，进可以通过 http://localhost:8000/app/ 浏览app页面。

## 这些code是要闹哪样呢？

`app/index.html` 的code

```
<!doctype html>
<html lang="en" ng-app>
<head>
  <meta charset="utf-8">
  <title>My HTML File</title>
  <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.css">
  <link rel="stylesheet" href="css/app.css">
  <script src="bower_components/angular/angular.js"></script>
</head>
<body>

  <p>Nothing here { {'yet' + '!'} }</p>

</body>
</html>

```

### `ng-app` 

`ng-app` 代表Angular指令 `ngApp`,  用来标记我们的HTML元素，会让Angular 认定为 app 的 root element。大家可以自由的决定app的入口元素在哪里，html或其他元素。

## ` { { } }`

数据绑定的code

```
Nothing here { {'yet' + '!'} }
```

这行代码展示了两个Angular模板的核心特点：

-  绑定使用双括号—— `{ { } }`
-  在绑定中使用简单的表达式—— `'yet' + '!'`

这个绑定告诉了Angular去计算表单式，然后把结果插入DOM里绑定数据的地方。在下一步我们会看到，与单向绑定不同的是，当表达式计算的结果改变时，数据会保持更新到DOM上。

[ Angular expression ](https://docs.angularjs.org/guide/expression) 是类JS的代码缩写，他的 context 上下文是当前的module scope，而不是全局域 `(window)` 。

## Bootstrapping AngularJS apps

使用 `ngApp` 指令引导Angular app 很方便也适用于大部分的应用。 但是，在一些其他的使用了scripts loader的栗子中，可以使用这个来引导app——[ imperative / manual](https://docs.angularjs.org/guide/bootstrap)。

当APP引导的时候完成了三个重要的事情：

 1. 注册器注册依赖？？
 2. 注册器建立根作用域，他将作为我们应用的上下文context
 3. Angular从 `ngApp` 根元素开始编译DOM，处理根下面的指令和绑定们

一旦一个app被引导完成，他会等待浏览器事件的触发（例如鼠标点击，键盘事件，或者HTTP应答）。一旦事件被触发，Angular就去侦测是否他会改变任何Model，然后通过更新所有相关的绑定，将改变的地方映射到View去。

示意图：
![angular ng-app](http://iamtutu.qiniudn.com/tutorial_00.png)






 
  





