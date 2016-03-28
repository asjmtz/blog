title: Qunit 单元测试学习笔记
date: 2014-06-24 11:06:59
categories: test
description: 介绍用 QUnit 做简单的单元测试
---

>参考文章
1. [如何用Qunit测试你的JavaScript代码][1]
推荐网站
1. http://testswarm.com/
##简介

>在计算机编程中，单元测试（又称为模块测试）是针对程序模块(软件设计的最小单位)来进行正确性检验的测试工作。程序单元是应用的最小可测试部件。在过程化编程中，一个单元就是单个程序、函数、过程等；对于面向对象编程，最小单元就是方法，包括基类（超类）、抽象类、或者派生类（子类）中的方法。

来自wiki，大概。。。简单地说，你为你的代码的每个功能写测试，如果所有这些测试都通过了，那么你可以肯定的是，代码没有缺陷（通常，还是由你的测试有多彻底而定）。

##为什么你要测试你的代码

1. 首先，点击测试法很烦。点击事实上并不是一件轻松的工作，因为你不得不确保每样东西都被点到而且很有可能你错过了一个或两个。

2. 其次，你为测试做的每件事情是不能复用的，这意味着它很难回归。原来的代码测试并发布之后，修改了新的需求，接下来发现旧的问题又出现了，叫做“回归”。

3. 它使跨浏览器兼容性测试很容易。仅仅在不同浏览器中运行你的测试案例就行，如果一个浏览器出现问题，你修复它并重新运行这些测试案例，确保不会在别的浏览器引起回归，一旦全部通过测试，你可以肯定的说，所有的目标浏览器都支持。

##如何用QUnit写单元测试

在要测试的项目里面添加一个测试的test.js和Qunit.js和Qunit.css。
嗯，我来试一试！！我的测试代码如下
```
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>test html</title>
	<link rel="stylesheet" href="http://code.jquery.com/qunit/qunit-1.14.0.css">
	<script type="text/javascript" src="http://code.jquery.com/qunit/qunit-1.14.0.js"></script>
	<script >
		// Let's test this function
		function isEven(val) {
			return val % 2 === 0;
		}
		function test(){
			console.log("test rewrite");
		}
		QUnit.test('isEven', function(assert) {
			assert.equal(isEven(0), true,'Zero is an even number');
			assert.equal(isEven(2), true, 'So is two');
			assert.equal(isEven(-4), true, 'So is negative four');
			assert.equal(!isEven(1), true, 'One is not an even number');
			assert.equal(!isEven(-7), true, 'Neither is negative seven');
		});
		QUnit.test( "equal test", function( assert ) {
		    assert.equal( 0, 0, "Zero, Zero; equal succeeds" );
		    assert.equal( "", 0, "Empty, Zero; equal succeeds" );
		    assert.equal( "", "", "Empty, Empty; equal succeeds" );
		 
		    assert.equal( "three", 3, "Three, 3; equal fails" );
		    assert.equal( null, false, "null, false; equal fails" );
		});
	</script>
</head>
<body>
	<div id="qunit"></div>
</body>
</html>
```
这是些基本的用法，就是对某个要测试的方法，输入测试值、预测结果，然后跑测试
一些高级用法待续。。。

  [1]: http://www.woiweb.net/how-to-test-your-javascript-code-with-qunit.html