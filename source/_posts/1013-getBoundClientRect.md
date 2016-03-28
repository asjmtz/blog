title: getBoundClientRect 函数详解
date: 2014-10-26 11:06:59
categories: js
description: getBoundClientRect 函数介绍
---


##getBoundClientRect函数详解
[转载自这里](http://www.cnblogs.com/2050/archive/2012/02/01/2335211.html)

###一、定义
 
getBoundingClientRect 函数是W3C组织在第一版本的W3C CSSOM View specification草案中确定的一个标准方法，在此之前，只有IE浏览器是支持该方法的，W3C在这次草案中把它扶正成为标准，足可以看出它并不简单。getBoundingClientRect 方法返回的是调用该方法的元素的TextRectangle对象，该对象具有top、left、right、bottom四个属性，分别代表该元素上、左、右、下四条边界相对于浏览器窗口左上角（注意，不是文档区域的左上角）的偏移像素值。

###二、兼容性

getBoundingClientRect方法最先在IE5中出现，后来被W3C接纳成为标准。目前IE5.5+、Firefox 3.5+、Chrome 4+、Safari 4.0+、Opara 10.10+等浏览器均支持该方法，兼容性几乎完美。

在具体表现方面，Firefox6以前的版本使用getBoundingClientRect时不能获取到top和bottom这两个属性值，Firefox6及以后的版本和其他支持getBoundingClientRect方法的浏览器则top、left、right、bottom四个属性值均能获取到。需要说明的是由于某些版本的IE浏览器的<html>文档根元素默认是有2px边框的,所以这里需要特别处理一下，微软MSDN上说在IE5中会存在这样的情况，但据我实际测试，xp系统中IE6也存在这样的问题，奇怪的是，我在win7环境下用IETester测试的IE各版本都是正常的。但这并不是什么大问题，我们只要把得到的值减去html根元素（body也可考虑进来）的clientLeft或clientTop就能保持各浏览器一致啦。

###三、用途

当getBoundingClientRect刚被w3c列为标准的时候，PPK还曾质疑过它是不是多此一举，因为已经存在类似的方法来获取元素的偏移位置，比如offsetLeft和offsetTop。但Jquery的作者John Resig马上阐明了getBoundingClientRect的用处。用传统的方法固然可以实现getBoundingClientRect同样的功能，但兼容各种浏览器以及各种不同的元素就会把你弄死，而且效率还非常低下。所以获取页面上某个元素相对于浏览器窗口的偏移量就成了getBoundingClientRect的用武之地了。而获取元素的偏移量能有什么用，我相信，你懂的~ ^_^
