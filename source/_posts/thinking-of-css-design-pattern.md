title:  CSS 设计模式的思考之一——结构篇
date: 2016-12-06 18:53:14
categories: css
description: thinking of css design pattern , something about OOCSS, SMACSS, BEM and RSCSS, and their structuring ideologies
---

面对日渐庞大的 CSS 文件们，如何组织和分类他们呢？怎么样才能写出好维护又易扩展的 CSS 样式，真是头疼呢！
<!-- more -->

现在项目用上了 React，写起页面来 component + component，十分的愉快。但是看到旁边的样式，及时用上了 SCSS、CSS modules，还是一如既往的混乱，而且有病入膏肓的迹象。这几天稍微得了点闲，我决定研究看看有没有什么好的解决方案。没想到从此打开了新世界的大门。

loooong long ago，就已经有大牛们意识到这些问题了。对于如何写好 CSS，他们给出了一些结构想法。

### OOCSS - Object Oriented CSS (2011)
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
	
	



### BEM —— Block + Element + Modifier

Use JSON structure to comprehensive

```javascript
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

```html
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
