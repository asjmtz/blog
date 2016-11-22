title: Angular Template 模板 
date: 2014-09-26 11:06:59
categories: Angular
description: Angular phonecat 教程 part 3 —— 模版
---


为了说明Angular怎样加强标准HTML，这一节会将一个纯html转为模板，Angular用他来展示一些数据集合。我们加的数据是两个手机的基本信息。

## step 1

调整工作区到step1
```
git checkout -f step-1
```

然后别忘了 `npm start` ，在浏览器上查看新的页面展示。

这是个纯html的静态模板，下面看看Angular的动态模板是怎么弄的。马上就到了step 2。

## step 2

```
git checkout -f step-2
```

Angular比较提倡使用 **MVC 设计模式** 来结耦代码。由于这个理念，这个步骤使用了一些 JS 代码来增加app中的 model，view 和 controller 组件。

### View and Template

在 Angular 中，视图是模型通过 HTML 模板的映射。这意味着，无论何时数据模型的变化，都能刷新绑定点而更新视图。

` app/index.html: `

```
<html ng-app="phonecatApp">
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="js/controllers.js"></script>
</head>
<body ng-controller="PhoneListCtrl">

  <ul>
    <li ng-repeat="phone in phones">
      {{phone.name}}
      <p>{{phone.snippet}}</p>
    </li>
  </ul>

</body>
</html>
```

### ng-repeat

我们将硬代码写的phone  list 替换为 ` ngRepeat directive ` 和两个 Angular expression。

- 这个 `ng-repeat="phone in phones" ` 属性是一个Angular repeater指令。repeater让 Angular 为每个list里的phone建立 `<li>` 模板。总之就是模板中的 `for in` 循环。

### ng-controller

这个指令绑定个 `PhoneListCtrl` 的 controller 到 body 上。数据 ` {{phone.name}}` 和 `{{phone.snippet}}` 是他设置的，(⊙_⊙)。

### ng-app

前面出现的这个指令，现在给他赋值了之后，则表示 `phonecarApp` 是一个module模块名。这个模块包含着`PhoneListCtrl` 这个控制器。

他们的关系图：

![关系图](http://iamtutu.qiniudn.com/tutorial_02.png)

### Module and Controller

`app/js/controllers.js:`

```
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope) {
  $scope.phones = [
    {'name': 'Nexus S',
     'snippet': 'Fast just got faster with Nexus S.'},
    {'name': 'Motorola XOOM™ with Wi-Fi',
     'snippet': 'The Next, Next Generation tablet.'},
    {'name': 'MOTOROLA XOOM™',
     'snippet': 'The Next, Next Generation tablet.'}
  ];
});
```

1. 在html标签上定义的模块名字 `phonecatApp` ，在这里注册为 Angular 的 module。
2. 然后在这个module下注册了一个控制器——`PhoneListCtrl`

控制器让我们能够在 model 和 view 之间建立数据绑定。`PhoneListCtrl` 控制器绑定 phone data 到 `$scope` 。这个 scope 是 root scope 的 prototypical descendant（原型子孙）？？root scope在app 开始时被定义。这个控制器的scope可以使用到任何在控制器标签中的绑定。

### scope

在Angular中scope的概念十分的重要，他是将template、model和controller公共工作的粘合剂。
详细的Angular scope 介绍请翻墙看：[ angular scope documentation.](https://docs.angularjs.org/api/ng/type/$rootScope.Scope) ^_^

### 测试

#### 全局控制器

用Angular 的方法将控制器从视图中分离，我们可以方便的编写测试代码。如果控制器在全局作用域可用，我们可用一个虚拟的scope来实例化控制器：

```
describe('PhoneListCtrl', function(){

it('should create "phones" model with 3 phones', function() {
  var scope = {},
      ctrl = new PhoneListCtrl(scope);

  expect(scope.phones.length).toBe(3);
});

});
```

在Angular中写单元测试挺方便啊O(∩_∩)O哈哈~
因为在软件开发中，测试是一个关键的步骤，所以Angular简化了测试，以鼓励coder们多写测试啊。

#### 非全局控制器

Angular提供了一个service服务——`$controller` 。他可以通过名字获取你的控制器。

```
describe('PhoneListCtrl', function(){

beforeEach(module('phonecatApp'));

it('should create "phones" model with 3 phones', inject(function($controller) {
  var scope = {},
      ctrl = $controller('PhoneListCtrl', {$scope:scope});

  expect(scope.phones.length).toBe(3);
}));

});
```

解释：

- 告诉Angular 在每个测试之前加载 `phonecatApp` 模块
- 让Angular 注册 `$controller` 服务到我们的测试函数
- 用 `$controller` 来建立一个 `PhoneListCtrl` 的实例
- 在实例中，我们来证实phones数组拥有三个记录的属性

### Writing and Running Tests 写跑测试

angular-seed项目预先配置了使用 **Karma** 来跑单元测试，确保开跑前已经使用 `npm install` 安装了Karma相关的组件。

使用 `npm test` 开跑：

- Karma 会打开一个浏览器的页面来跑你的测试，可以无视
- 你会看到命令台输出（大概）：
```
 info: Karma server started at http://localhost:9876/
  info (launcher): Starting  browser "Chrome"
  info (Chrome 22.0): Connected on socket id tPUm9DXcLHtZTKbAEO-n
  Chrome 22.0: Executed 1 of 1 SUCCESS (0.093 secs / 0.004 secs)
```

 \(^o^)/ success！！

- 当你修改你的test.js的源文件时，Karma会默认帮你重跑一遍，so sweet O(∩_∩)O








