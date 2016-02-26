---
layout: post
title: 使用grunt构建seajs项目遇到的各种坑
category: tech
---

----

* 目录
{:toc}

## 使用grunt-cmd-transport转换为具名模块遇到的问题

### 1. 最新版本依赖不能完全提取

`grunt-cmd-transport` 最新版本0.5.1不能完全提取依赖，需要降级到0.4.1。

参考：
[https://github.com/spmjs/grunt-cmd-transport/issues/103](https://github.com/spmjs/grunt-cmd-transport/issues/103)

### 2. 转换后出新双反斜杠

同样需要降级到0.4.1才行。

参考：

[https://github.com/spmjs/grunt-cmd-transport/issues/96](https://github.com/spmjs/grunt-cmd-transport/issues/96)

[http://blog.csdn.net/weizengxun/article/details/45153519](http://blog.csdn.net/weizengxun/article/details/45153519)

<!--more-->

### 3. 会将深层次的依赖也提取到deps中

网上解决方法，不要配置`paths`，然后用`--force`强制执行。

但是如果所有require都用相对路径，也就是都以`.`开头，就不需要强制执行了。 `transport`后所有依赖包括间接依赖都会提取到`deps`中。这样在`concat`的时候就不会出现模块重复或者漏掉模块的情况。

所以为了避免`concat`出现问题，`require`一定要用相对路径，`concat`设置`include`为`relative`

参考：

[http://blog.csdn.net/kyfxbl/article/details/13998371](http://blog.csdn.net/kyfxbl/article/details/13998371)

[https://github.com/spmjs/grunt-cmd-transport/issues/56](https://github.com/spmjs/grunt-cmd-transport/issues/56)


### 4. 模板文件的问题

不管是`html`还是`tpl`，不要做成`js文件`。模板文件就放`html`代码即可。如果是`.js`文件，那么生成的`debug`文件与`require`会不一致(`debug`一个在前一个在后)。因为`.js`文件是以`script.jsParser`处理的而不是`text.html2jsParser`或者`template.tplParser`处理。

~~~ javascript
var transport = require('grunt-cmd-transport');
var style = transport.style.init(grunt);
var text = transport.text.init(grunt);
var script = transport.script.init(grunt);

transport: {
	options: {
		debug : true,
	    parsers : {
	         '.js' : [script.jsParser],
	         '.css' : [style.css2jsParser],
	         '.html' : [text.html2jsParser]
	     }
    },
}
~~~

### 5.  css文件的问题

css文件在transport的时候可以用style.css2jsParser或者style.cssParser。前者转换为.js文件或者还是css文件只是会带上`/*! define 模块id */`一行这样的注释。

但是如果在`require`用的是相对路径，那么`concat`的时候就会去找`js文件`，而`require`用的是绝对路径，那么`concat`的时候就会去找`css文件`。

生成的js文件是`seajs.importStyle`格式的，所以入口页面还需要引入`seajs-style.js`

参考：
[https://github.com/spmjs/grunt-cmd-concat/issues/37](https://github.com/spmjs/grunt-cmd-concat/issues/37)

----

## 使用grunt-cmd-concat合并模块

- `concat`设置`include`为`relative`，设置`all`时合并会出现重复
- 如果css文件还没有`transport`则需要设置`css2js: require('grunt-cmd-transport').style.css2js`，这时候`require`的时候就不能用相对路径

参考：
[https://github.com/spmjs/grunt-cmd-concat/issues/37](https://github.com/spmjs/grunt-cmd-concat/issues/37)
