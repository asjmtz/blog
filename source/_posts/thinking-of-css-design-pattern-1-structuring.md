title:  CSS 设计模式的思考（一）之结构篇
date: 2016-12-06 18:53:14
categories: css
description: thinking of css design pattern , something about OOCSS, SMACSS, BEM and RSCSS, and their structuring ideologies
---

面对日渐庞大的 CSS 文件们，如何组织和分类他们呢？怎么样才能写出好维护又易扩展的 CSS 样式，真是头疼呢！
<!-- more -->

现在项目用上了 React，写起页面来 component + component，十分的愉快。但是看到旁边的样式，及时用上了 SCSS、CSS modules，还是一如既往的混乱，而且有病入膏肓的迹象。这几天稍微得了点闲，我决定研究看看有没有什么好的解决方案。没想到从此打开了新世界的大门。

loooong long ago，就已经有大牛们意识到这些问题了。对于如何写好 CSS，他们给出了一些结构模式。
我按照时间轴来介绍下。

## [OOCSS - Object Oriented CSS](https://www.smashingmagazine.com/2011/12/an-introduction-to-object-oriented-css-oocss/) (2011)

他的主要思想是下面这两个：

#### 1. Separate Structure and Skin (结构和主题)

将结构和主题的样式分开，然后在使用的时候可以按照需要，为不同的结构加上各种主题的 CSS 类。
如：

```css
.button {
  width: 200px;
  height: 50px;
}

.box {
  width: 400px;
  overflow: hidden;
}


.skin {
  border: solid 1px #ccc;
  background: linear-gradient(#ccc, #222);
  box-shadow: rgba(0, 0, 0, .5) 2px 2px 5px;
}
```
`.button` 和 `.box` 是结构性的类，而 `.skin` 是一个主题性的类

#### 2. Separate Container and Content （容器和内容)

提倡将有共性的功能，提取到一个独立的类中，作为一个 module 。
比如原来有个 header，可以定义为：

```css
.header-inside {
  width: 980px;
  height: 260px;
  padding: 20px;
  margin: 0 auto;
  position: relative;
  overflow: hidden;
}
```
但考虑到他的容器性也可以为其他元素所用，所以可以提取为下面这两个类：

```css
.global-width {
  width: 980px;
  margin: 0 auto;
  position: relative;
  padding-left: 20px;
  padding-right: 20px;
  overflow: hidden;
}

.header-inside {
  padding-top: 20px;
  padding-bottom: 20px;
  height: 260px;
}
```
`.global-width` 是一个容器类，定义了一个容器的宽度、设置了 `position` 的上下文、与子元素的距离等

#### 总结起来 OOCSS 推崇的是：
> 基于 CSS class 的模块构建，不推荐使用 ID、后代选择器（如：`.sidebar h2`）

接着又出来了一个新想法


## [SMACSS(Scalable and Modular Architecture for CSS)](https://smacss.com/book/categorizing) (2012)

SMACSS 的观点为，可以将项目中 CSS 的类型或功能分为这五个类型：

- Base  
	- 【定义】直接在 html 的元素 (html, body, h1, ul, etc) 上设定的样式。
	可以理解为我们为元素设置的默认样式，比如我们常用到了 `reset.css`


- Layout
	- 【定义】顾名思义，布局用的样式，比如我们用的 `grid system`
	- 【命名】加前缀 `l-` 或 `layout-` 

- Module
	- 【定义】模块的样式
	- 【命名】为 `modulename` 和 `modulename-`，比如：`alert` 和 `alert-body`

- State
	- 【定义】状态类型的样式用来定义在特定状态下的样式，如(hidden or expanded, active/inactive)
	- 【命名】加前缀 `is-`, 比如：`is-active`、`is-hidden`

- Theme 
	- 【定义】主题类型可以用来自定义一些颜色，字体，或者其他模块的特殊样式
	
如果你对他感兴趣，建议到他的官网仔细看看每个类型详细的规则

P.S:
我很是喜欢这个分类，和我前期脑补的理想分类差不多

然后又又出来一个新结构

## [BEM —— Block + Element + Modifier](https://en.bem.info/) (2013)

他的理念即 CSS 分类为 Block、Element、Modifier，和 SMACSS 也有些类似的地方。

- Block
	- 【定义】类似我们之前说的 module 或 component 或者像乐高积木
	- 【命名】`bloak-name`

- Element
	- 【定义】Block 的一部分组成，不能独立使用
	- 【命名】`block-name__element-name` ，如 `search-form__input`

- Modifier 
	- 【定义】定义 Block 或 Element 的状态
	- 【命名】`block-name_modifier-name` | `block-name__element-name_modifier-name`

```html
<!-- The `search-form` block has the `theme` modifier with the value `islands` -->
<form class="search-form search-form_theme_islands">
    <input class="search-form__input">

    <!-- The `button` element has the `size` modifier with the value `m` -->
    <button class="search-form__button search-form__button_size_m">Search</button>
</form>
```

BEM 官网上有更多详细的规则。BEM 有蛮多的推崇者，但是他这牵家带口的命名语法也让很多人敬而远之。

江湖纷争不断，后面又涌现了一些 [ACSS](http://patternlab.io/)、CCSS 等等。但是即使有了这么多门绝世武功，许多 CSS 码民仍旧水深火热的 coding 中。

为啥呢？是不是他们都还不够好？正在我苦恼的总结发散时，我又又又发现了他

## rscss.io

他的基本概念融合了 BEM 和 SMACSS 的精华，又在命名上有些独特的创新

- Component
	- 【定义】就是那个 component，你懂的
	- 【命名】`component-name` ，这里一定要两个字连个线

- Element
	- 【定义】类似 BEM 的 Element，
		定义这个 element 的类时，只能使用直接子选择器 `.component-name > .element`
	- 【命名】`elementname` ，此处一定要一个字，一个字 

- Variant
	- 【定义】类似 BEM 的 Modifier 或 SMACSS 的 State
	- 【命名】加前缀 `-` ，如：`-disabled`, `-active` （这是模仿了命令行的写法）

- Helper
	- 【定义】可以强制覆盖来修改样式的一些类，用来应急。非特殊情况不推荐使用
	- 【命名】加前缀 `_`，如：`._pull-left { float: left !important; }`

- Layout
	- 【定义】布局管理的类
	- 【命名】`component-name`，没有特殊化 


#### rscss.io 上的对比

| RSCSS | BEM | SMACSS |
| :-----: | :---: | :------: |
| Component | Block | Module |
| Element | Element | Sub-Component |
| Layout | ? | Layout |
| Variant | Modifier | Sub-Module & State |

## tips

- Grids control width
- Content control height 

## Tips for sass

=============================================

## Real World

situation:

- Bootstrap based: coupling with html decade
- module styled

ideal target：

- Base: reset + grid 
- Theme : color ( primary + secondary + ...  ) + typographic( font-size + font-family + 字体行间距 。。。)
- Module: UI ( buttons + Media + Label + ... ) + Form ( Form + Editors... )
	- structure + skin 
	- block + modifier

usage 


<p data-height="300" data-theme-id="6215" data-slug-hash="zoWVyX" data-default-tab="html,result" data-user="asjmtz" data-embed-version="2" data-pen-title="css design pattern -- button" data-preview="true" class="codepen">See the Pen <a href="http://codepen.io/asjmtz/pen/zoWVyX/">css design pattern -- button</a> by asjmtz (<a href="http://codepen.io/asjmtz">@asjmtz</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>


modifier(structure) + modifier(skin) => ''


realistic target:

- bootstrap
