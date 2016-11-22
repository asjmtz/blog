title: "JSON 是否为空"
date: 2015-04-30 10:08:28
categories: js
description: js 判断 JSON对象是否为空的写法
---

jquery 中的写法

```
$.isEmptyObject = function ( obj ) {
		var name;
		for ( name in obj ) {
			return false;
		}
		return true;
}

```