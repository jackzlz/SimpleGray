---
layout: post
title: 浅谈Javascript模块化开发
category: tech
---

***

* 目录
{:toc}

几种javascript模块开发的写法。自己接触过的几种javascript模块开发，由于水平有限，只能简单谈一谈。

***

### 立即执行匿名函数

> 由于javascript的函数作用域，将模块代码放入立即执行匿名函数中，防止污染全局变量。将需要对外提供的类或对象暴露到window对象中。

#### 需要实例化的模块

##### 定义


	// mod1.js
	(function(window) {
		var defaultOpts = {};

		// 外部传入一些配置选项，也可不传入以默认选项实现
		var Module1 = function(options) {
			this.options = $.extend(true, {}, defaultOpts, options);
			this.init();
		};

		// 将需要对外提供的方法加到原型链上
		Module1.prototype = {
			constructor : Module1,
			init : function() {
				this.initPage();
				this.initEvent();
				this.initOther();
			},
			initPage : function() {
			},
			initEvent : function() {
				method1();
			},
			initOther : function() {
			},
			getOne : function(){}
			// 其他需要对外提供的接口
		};

		// 不需要对外提供的方法
		function method1() {
		}

		// 暴露模块到window对象中
		window.Module1 = Module1;
	}(window));



##### 使用

	// mod1
	var mod1 = new Module1({opt1:"",opt2:""});
	var one = mod1.getOne();
	// mod2
	var mod2 = new Module1();
	var one = mod2.getOne();

####  不需要实例化的模块

##### 定义

	var mod1 = (function() {

		// !!!"全局"变量会共享
		var var1 = "abc";

		function getOne() {

		}

		function getTwo() {

		}

		var module1 = {
			m1 : getOne,
			m2 : getTwo
		};

		return module1;
	})();


##### 使用

	var one = mod1.m1();
	var two = mod1.m2();


#### 总结

> **优点：** 不依赖第三方框架；

> **缺点：** 使用前必须预先加载js文件；当模块存在依赖时，依赖js的加载顺序。

### seajs中的模块化
> seajs遵循CMD规范(RequireJS 遵循AMD规范) 。[两者不同之处](https://github.com/seajs/seajs/issues/277)。将匿名函数的写法替换成seajs的写法非常简单，只需要将声明和导出部分替换即可。

#### 需要实例化的模块

##### 定义

	define(function(require, exports, module) {
		var defaultOpts = {};

		// 外部传入一些配置选项，也可不传入以默认选项实现
		var Module1 = function(options) {
			this.options = $.extend(true, {}, defaultOpts, options);
			this.init();
		};

		// 其他代码与匿名函数一致
		// ...

		// 导出不再使用window对象
		module.exports = Module1;
	});

##### 使用

	define(function(require, exports, module) {
		// 相对于sea.js的目录。模块目录就是md/mod1.js
		// require时可以省略.js
		var Module1 = require("md/mod1");

		// mod1
		var mod1 = new Module1({opt1:"",opt2:""});
		var one = mod1.getOne();
		// mod2
		var mod2 = new Module1();
		var one = mod2.getOne();
	});

#### 不需要实例化的模块

##### 定义

	define(function(require, exports, module) {
		var var1 = "abc";

		function getOne() {
		}
		function getTwo() {
		}

		// 导出不再使用window对象
		module.exports = {
			m1 : getOne,
			m2 : getTwo
		};
	});

##### 使用

	define(function(require, exports, module) {
		var mod1 = require("md/mod1");

		var one = mod1.m1();
		var two = mod1.m2();
	});


#### 参考

[Sea.js官网](http://seajs.org/docs/)

[Why SeaJS](http://cyj.me/why-seajs/zh/#intro)

[seajs模块化jQuery与jQuery插件](http://julabs.com/blog/seajs-jquery-and-plugins/)

[如何改造现有文件为 CMD 模块](https://github.com/seajs/seajs/issues/971)

### ECMAScript6中的模块化

> 为了解决JavaScript中的模块化问题，ECMAScript6在语言层面上实现了**模块功能**；
> ECMAScript6中有了**块级作用域**，立即执行匿名函数就不再必要了;
>  ECMAScript6新增了**let**命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

#### 需要实例化的模块

##### 定义

	// mod1.js
	{
		class Module1 {
			let defaultOpts = {};
			constructor(options) {
				this.options = $.extend(true, {}, defaultOpts, options);
				this.init();
			}
			init() {
				this.initPage();
				this.initEvent();
				this.initOther();
			}
			initPage() {
			}
			initEvent() {
			}
			initOther() {
			}
			getOne() {
			}
		}
		// 自定义模块并导出
		export { Module1 }
	}

##### 使用

	// 导入
	import { Module1 } from 'md/mod1';
	// mod1
	let mod1 = new Module1({opt1:"",opt2:""});
	let one = mod1.getOne();
	// mod2
	let mod2 = new Module1();
	let one = mod2.getOne();

#### 不需要实例化的模块

##### 定义

	{
		let var1 = "abc";

		function getOne() {
		}
		function getTwo() {
		}

		// 导出
		export {getOne, getTwo}
	}

##### 使用

	// 导入
	import { getOne, getTwo} from 'md/mod1';
	let one = getOne();
	let two = getTwo();
	// 整体导入
	import * as mod1 from 'md/mod1';
	// 或者
	module mod1 from 'md/mod1';
	let one = mod1.m1();
	let two = mod1.m2();


#### 参考
[ECMAScript 6入门](http://es6.ruanyifeng.com/)
