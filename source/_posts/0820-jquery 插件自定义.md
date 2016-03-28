title: jQuery 插件自定义
date: 2014-08-20 11:06:59
categories: js
description: 如何自定义一个 jQuery 的插件
---

## jquery 插件

```

jQuery.myPlugin = {
	foo: function() {
		alert('This is a test. This is only a test.');
	},
	bar: function(param) {
		alert('This function takes a parameter, which is "' + param + '".');
	}
};
//采用命名空间的函数仍然是全局函数，调用时采用的方法：
$.myPlugin.foo();
$.myPlugin.bar('baz');



(function($) {
	$.fn.extend({
		pluginName: function(opt, callback) {
			// Our plugin implementation code goes here.
		}
	})
})(jQuery);


//闭包
(function($) {
    //插件定义
	$.fn.swipe = function(options) {

		//extend default options with those provided
		//if deep extend is true,the merge becomes recursive(递归)
		var opts = $.extend(true, $.fn.swipe.defaults, options);
	};

	//将默认的参数写在外外面，对于插件的使用者而言，更容易用较少的代码覆盖和修改插件的配置
	$.fn.swipe.defaults = {
		background: "red"
	}

})(jQuery);



//这个只需要调用一次，且不一定要在ready块中调用
$.fn.swipe.defaults.foreground = 'blue';

$("#swipe").swipe();

```