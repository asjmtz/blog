title: Protractor 学习笔记
date: 2014-09-30 11:06:59
categories: test
description: 安装 protractor 和他的基本用法
---

## 准备活动

### 简介

protractor 是个 [Node.js](http://nodejs.org/) 程序。所以首先得安装 Node.js。然后就可以使用npm 安装 protractor 了。

通常 protractor 使用 [Jasmine](http://jasmine.github.io/1.3/introduction.html) 测试框架作为他的测试接口。

下面的教程的测试将使用一个独立的 Selenium Server 来控制浏览器。需要安装 JDK 来运行这个服务器。可以用 `java -version` 命令来测试是否安装成功 JDK。

### 安装
 
全局安装protractor
```
npm install -g protractor
```

这个命令会安装两个命令行工具，protractor 和 webdriver-manager。尝试跑 `protractor --version` 确认安装成功。webdriver-manager 是一个辅助工具，他可以简单的让Selenium Server 的实例跑起来？？用这个命令可以让他来下载必要的 binaries：
```
webdriver-manager update
```

现在可以打开一个服务器
```
webdriver-manager start
```

这个命令会开启一个Selenium Server ，然后输出一串的 log 信息。你的 Protractor 测试将会把请求发到服务器来控制本地的浏览器。在整个教程中，请让服务器在后面撒欢的跑。你可以到这里看服务器的状态信息 http://localhost:4444/wd/hub 。


## step 0 - 写一测试

新开个命令行或终端窗口，并选个干净整洁的文件夹来做测试目录。

Protractor 运行需要两个文件，一个是 **spec file** ，一个是 **configuration file**。

我们先从一个简单的测试开始，内容是引导一个Angular app 例子和检验他的title。我们将会用到这里的 Super Calculator application —— http://juliemr.github.io/protractor-demo/

将下面的代码 copy 到 spec.js 中：
```
// spec.js
describe('angularjs homepage', function() {
  it('should have a title', function() {
    browser.get('http://juliemr.github.io/protractor-demo/');

    expect(browser.getTitle()).toEqual('Super Calculator');
  });
});
```
 
 这里 `describe` 和 `it` 语法来自 Jasmine 框架。`browser` 由 Protractor 全局创建的，他可以用来执行浏览器级别的命令，例如用 `browser` 导航。

现在来创建配置文件，将下面的代码 copy 到 conf.js：
```
// conf.js
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['spec.js']
}
```

这个配置文件通知 Protractor 你的测试文件（specs）的位置，还有Selenium Server 的对话地址（seleniumAddress）。其他配置项使用默认的，Chrome是默认浏览器。

现在运行测试
```
protractor conf.js
```

OK! 我们会看到 Chrome 打开一个指向 Calculator 的窗口，然后又闪关了。测试结果输出应为 `1 tests, 1 assertion, 0 failures`。真是可喜可贺，我们第一个 Protractor 测试已经成功了 \(^o^)/~

## step 1 - 元素交互

现在来修改下测试文件，让他与页面上的文件进行交互吧！变 ~ 身 ~
```
// spec.js
describe('angularjs homepage', function() {
  it('should add one and two', function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
    element(by.model('first')).sendKeys(1);
    element(by.model('second')).sendKeys(2);

    element(by.id('gobutton')).click();

    expect(element(by.binding('latest')).getText()).
        toEqual('5'); // This is wrong!
  });
});
```

这里也使用了两个 Protractor 创建的全局 `elemenet` 和 `by`。`element` 函数用来寻找页面 HTML 元素，他会返回一个 ElementFinder 对象，这个对象可与元素交互或者获取他们的信息（狗仔队~~）。在这个测试中，我们使用 `sendKeys` 来给 `<input>` 输入值，`click` 来点击按钮，`getText` 来返回元素的内容。

`element` 需要一个定位仪参数。`by` 元素创建了定位仪，我们使用三个类型的定位仪。
- `by.model('first')` 来查找元素 `ng-model="first"`。
-  `by.id('gobutton')` 来根据给定的id查找。
-  `by.binding('latest')` 来发现绑定了latest变量的元素，即 `{ {latest} }`

[关于定位仪 (by) 和元素查找器 (element) 的更多信息](http://angular.github.io/protractor/#/locators)

运行测试
```
protractor conf.js
```

1 + 2 == 5肯定会 failed 啦~\(≧▽≦)/~啦啦啦 

## step 2 - 多个测试

稍微美化点，将两个测试结合起来，提出重复的部分

```
// spec.js
describe('angularjs homepage', function() {
  var firstNumber = element(by.model('first'));
  var secondNumber = element(by.model('second'));
  var goButton = element(by.id('gobutton'));
  var latestResult = element(by.binding('latest'));

  beforeEach(function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
  });

  it('should have a title', function() {
    expect(browser.getTitle()).toEqual('Super Calculator');
  });

  it('should add one and two', function() {
    firstNumber.sendKeys(1);
    secondNumber.sendKeys(2);

    goButton.click();

    expect(latestResult.getText()).toEqual('3');
  });

  it('should add four and six', function() {
    // Fill this in.
    expect(latestResult.getText()).toEqual('10');
  });
});
```

这里，我们将导航拉到一个 `beforeEach` 函数里，他会运行在每个 `it` 块前。

## step 3 - 改变配置

变 ~ 身 ~ \(^o^)/~
```
// conf.js
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['spec.js'],
  capabilities: {
    browserName: 'firefox'
  }
}
```

尝试重新运行测试。我们可以看到 Firefox 代替 chrome 跑了出来。`capabilities` 对象描述了测试使用的浏览器。所有可用的选项，看这里 [the reference config file](https://github.com/angular/protractor/blob/master/docs/referenceConf.js)

还可以一次在多个浏览器中跑测试，像这样
```
// conf.js
exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['spec.js'],
  multiCapabilities: [{
    browserName: 'firefox'
  }, {
    browserName: 'chrome'
  }]
}
```
大家可以试试看效果。Chrome 和 Firefox 会同步进行测试，命令行中的结果是独立显示的。

我的 Firefox 没有安装在默认 C 盘的路径上，所以有报错 `Cannot find firefox binary in PATH. Make sure firefox is installed.` ，把 `firefox.exe` 的路径加在了系统变量 PATH ，也没有解决。

这个问题修改的[参考文章](http://sariyalee.iteye.com/blog/1688271)，万万没想到，我最后还是重装了Firefox。不知道以后还能不能愉快地自定义安装地址了呢╮(╯▽╰)╭

## step 4 - 元素列表

重新回到测试文件，有时会遇到多个元素的列表需要处理的问题。可以用 `element.all` 处理，这个方法会返回一个 ElementArrayFinder。在我们的这个 Calculator app 中，每个操作都记录在一个历史的表格中，使用的是 Angular 的 `ng-repeater`。下面让我们来测试这些操作是否记录在表格中。

变 ~ 身 ~ \(^o^)/~
```
// spec.js
describe('angularjs homepage', function() {
  var firstNumber = element(by.model('first'));
  var secondNumber = element(by.model('second'));
  var goButton = element(by.id('gobutton'));
  var latestResult = element(by.binding('latest'));
  var history = element.all(by.repeater('result in memory'));

  function add(a, b) {
    firstNumber.sendKeys(a);
    secondNumber.sendKeys(b);
    goButton.click();
  }

  beforeEach(function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
  });

  it('should have a history', function() {
    add(1, 2);
    add(3, 4);

    expect(history.count()).toEqual(2);

    add(5, 6);

    expect(history.count()).toEqual(0); // This is wrong!
  });
});
```

这里我们做了两件事：
- 首先，我们创建了一个辅助函数`add`，并且增加了变量 `history`。
- 我们用了 `element.all` 结合 `by.repeater` 定位仪来获得一个 ElementArrayFinder。在 spec 文件中，我们用了count函数计算了历史记录的应有 length 。

`ElementArrayFinder` 处理 `add` 外还有许多方法。用 `last` 来获得一个匹配定位仪获得元素集中的最后一个元素。

```
it('should have a history', function() {
    add(1, 2);
    add(3, 4);

    expect(history.last().getText()).toContain('1 + 2');
    expect(history.first().getText()).toContain('foo'); // This is wrong!
  });
``` 
因为 Calculator 展示了最老的结果在底部，我们用 `toContain` Jasmine 匹配来假设元素的文字包含有“1+2”。

ElementArrayFinder 还有 `each`、`map`、 `filter` 和 `reduce` 这些方法和 JS 的数组方法很相似。[更多 API 细节看这里](http://angular.github.io/protractor/#/api?view=ElementArrayFinder)

> Written with [StackEdit](https://stackedit.io/).