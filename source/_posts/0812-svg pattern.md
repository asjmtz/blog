title: SVG Patterns
date: 2014-08-25 11:38:38
categories: svg
description: svg pattern 的用法
---

参考文章：[《How to Use SVG Patterns》][1]
SVG的基础语法和属性选项：[Tips for Creating Accessible SVG][3]

SVG Patterns是用图案填充SVG图形的一种方式，一些简单的组合就可以做出非常炫的效果。


## SVG基础

本文集中讨论的浏览器和设备对象为：nternet Explorer 11, Firefox 28 and Chrome 30 on Windows, with Jaws 15 and NVDA 2014.1. Safari 6 on iOS/OSX with VoiceOver, and Chrome 30 on Android with TalkBack。

在HTML5 doctype声明下，SVG标签使用时不用包括命名空间的声明。像下面这样
```
<svg version="1.1" width="300" height="200">
    <title>Green rectangle</title>
    <rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg>
```

### ARIA 属性

讲ARIA的属性加入到HTML元素中，可以让浏览器更新信息。他对各种浏览器和屏幕阅读器都有很好的支持，所以他可以帮助有阅读障碍的人士更好的浏览网页。

在SVG里加上aria-labelledby属性的话，可以加强`<SVG>`和他的子元素的联系。

```
<svg version="1.1" width="300" height="200" aria-labelledby="title">
    <title id="title">Green rectangle</title>
    <rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
</svg>
```
其他的还有一些属性集合起来，可以写成下面这样
```
<svg version="1.1" width="300" height="200" aria-labelledby="title desc">
    <title id="title">Green rectangle</title>
    <desc id="desc">A light green rectangle with rounded corners and a dark green border.</desc>
    <a xlink:href="http://example.com">
    <rect width="75" height="50" rx="20" ry="20" fill="#90ee90" stroke="#228b22" stroke-fill="1" />
    <text x="35" y="30" font-size="1em" text-anchor="middle" fill="#000000">Website</text>
    </a>
</svg>
```

### Make it focusable

如果你的SVG是可交互的，必须确保他有一个元素可以让键盘focus。所以这里在SVG里面加上了`<a>`元素，用户可以使用Tab键通过link来focus到SVG上。

##Fill Patterns

先看看简单的SVG patterns的语法。

<p data-height="268" data-theme-id="6215" data-slug-hash="bBxLe" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/asjmtz/pen/bBxLe/'>SVG pattern -- 1 </a> by asjmtz (<a href='http://codepen.io/asjmtz'>@asjmtz</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//codepen.io/assets/embed/ei.js"></script>

`<svg>` 元素可以包含一个 `<defs>` 元素，`<defs>` 定义的图案不会立即显示，直到当我们用他的ID引用到 `stroke` 或 `fill` 属性时，才会显示。

###patterns attributes

下面是使用patterns时的一些重要的属性介绍。

####ID

 `<pattern>` 中有唯一的 `id` 属性。可以让svg的shapes或text引用到他们的fill或stroke属性中。

####x,y, width, height

`<pattern>` 中的 x 和 y 属性定义了 pattern 图案在 svg 中开始填充的坐标距离。width 和 height 则是填充图案的外框的大小。

这里坐标系的选择取决于 `patternUnit` 和 `patternContentUnits` 两个属性的值。

#### patternUnits

`patternUnits = "userSpaceOnUse | objectBoundingBox"`。

他们是应用于x, y, width和 height 的两种坐标系。默认采用objectBoundingBox。引用W3C的原话。

> If patternUnits="userSpaceOnUse", ‘x’, ‘y’, ‘width’ and ‘height’ represent values in the coordinate system that results from taking the current user coordinate system in place at the time when the ‘pattern’ element is referenced (i.e., the user coordinate system for the element referencing the ‘pattern’ element via a ‘fill’ or ‘stroke’ property) and then applying the transform specified by attribute ‘patternTransform’.

 > If patternUnits="objectBoundingBox", the user coordinate system for attributes ‘x’, ‘y’, ‘width’ and ‘height’ is established using the bounding box of the element to which the pattern is applied (see Object bounding box units) and then applying the transform specified by attribute ‘patternTransform’.
If attribute ‘patternUnits’ is not specified, then the effect is as if a value of 'objectBoundingBox' were specified.

从现象看，`userSpaceOnUse` 使用的width和height是按绝对坐标系计算的像素值，他参考的坐标系和svg图形使用的是同一个，即他的坐标原点在svg父元素的左上角。

而 `objectBoundingBox` 的width和height是百分比值，填充图案的大小会随着svg的伸缩而伸缩。

上面还提到一个`patternTransform` 的属性，可以做坐标变换用的？？

<p data-height="268" data-theme-id="6215" data-slug-hash="wpekH" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/asjmtz/pen/wpekH/'>wpekH</a> by asjmtz (<a href='http://codepen.io/asjmtz'>@asjmtz</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//codepen.io/assets/embed/ei.js"></script>


#### patternContentUnits

和 `patternUnits` 一样可以选两个值，但是默认值为 `userSpaceOnUse` 。

###Nested Patterns

可以用一个填充填充另一个填充。来看看这个填充填充的填充的栗子。

###Paths as Patterns

<p data-height="268" data-theme-id="6215" data-slug-hash="uvBaA" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/asjmtz/pen/uvBaA/'>SVG patterns</a> by asjmtz (<a href='http://codepen.io/asjmtz'>@asjmtz</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//codepen.io/assets/embed/ei.js"></script>


  [1]: http://designmodo.com/svg-patterns/
  [2]: http://designmodo.com/svg-infographic/
  [3]: http://www.sitepoint.com/tips-accessible-svg/