title: "a 标签中的 input 文本不能被选中"
date: 2015-04-30 10:14:14
categories: html
description: a 标签中的 input 文本不能被选中
---

若你写了这样一个 `html` 结构

```
<a href="http://iamtutu.com"> 
	<input type="text" value="something" />
</a>

```

会发现 `<input>` 中的文字不能选中，会触发 `<a>` 。

尝试把 `href = "javascript:void();"` 这样，但是还是不能选中。

最后，将 `<a>` 的attr属性移除` $().removeAttr()` 就能选中了。

虽然表面看起来就没有必要有 `<a>` 了，但是如果在需要编辑一个连接的文本时，这个结构还是不错的。编辑的时候，将 `href` 属性先储存在别的地方，移除 `<a>` 的 href 属性，编辑完成之后，再恢复就 OK 了。

另外，css 选中文本的样式
```
::selection { background:lightblue; }
```