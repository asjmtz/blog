title:  CSS 设计模式的思考
date: 2016-12-06 18:53:14
categories: css
description: thinking-of-css-design-pattern
---


由于目前项目中的 CSS 文件零散，各个 CSS 文件没有特别明确的责任分工，造成样式的管理混乱。迫使我思考如何有效地界定样式文件的职责，
一个元素的样式到底如何划分最高效可维护？

- 目标：易维护、好扩展
- 分析：this one ，that one，then I
- 实行：coding。。。

### OOCSS - Object Oriented CSS

- Separate Structure and Skin (结构和外形？样式？)
- Separate Container and Content （容器和内容）

===================================================

- component library : Headings/List/Module headers and footers/Grids/Buttons/Media
- Separate Container and Content 
- Separate Structure and Skin （Block + ）




### SMACSS

- Base — 
	- These are your defaults (html, body, h1, ul, etc)
	-  Nothing needed ( element selectors )

- Layout — 
	- These divide the page into major sections
	- l- or layout- prefixes

- Module — 
	- These are the reusable modular components of a design
	- modulename-

- State — 
	- These describe how things look when in a particular state (hidden or expanded, active/inactive)
	- is- prefix as in is-active or is-hidden

- Theme — 
	- These define things like a color scheme or typographic treatment across a sit
	- 



### BEM —— Block + Element + Modifier

Use JSON structure to comprehensive

```
{
	block: 'page',
	content: [
		{
			block: 'header',
			content: [
				{ 
					elem: 'input', 
					content: 'search',
					mods: { 
						state : 'current',
						size: 'big',
						type: 'buttons'
					},
				},
			],
		},
		{
			block: 'sidebar',
			content: [],
		},
	]
}
```


- Block: independent entity
- Element: part of block, are context-dependent
- Modifier: an additional CSS class for a block or an element



Naming:

.Block__Element-Modifiers


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

```
// .module 
<button class="btn"></button>

// .module + .module-modifier
<button class="btn btn-xl"></button>

// .module + .module-modifier + .module-theme
<button class="btn btn-xl btn-primary"></button>

// .module + .module-modifier + ... + .module-modifier + .module-theme
<button class="btn btn-xl btn-active btn-primary"></button>

```





realistic target:

- bootstrap
