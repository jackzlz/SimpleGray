---
layout: post
title: 判断javascript中的类型
category: 技术
---

判断javascript中的类型（摘自《编写可维护的JavaScript》）

<!--more-->

## 检测原始值

> 你希望一个值是字符串、数字、布尔值或`undefined`，最佳选择是使用`typeof`运算符

~~~ javascript
// 检测字符串
if (typeof name === "string") {
    antherName = name.sbustring(3);
}
// 检测数字
if (typeof count ==== "number") {
    updateCount(count);
}
// 检测布尔值
if (typeof found === "boolean" && found) {
    message("Found!");
}
// 检测undefined
if (typeof MyApp === "undefined") {
    MyApp = {};
}
~~~

`typeof`运算符的独特之处在于，将其用于一个未声明的变量也不会报错。未定义的变量和值为`undefined`的变量通过`typeof`都将返回`undefined`

## 检测引用值

> `javascript`中除了原始值之外的值都是引用。几种类置的引用类型：`Object`、`Array`、`Date`和`Error`。`typeof`运算符在判断这些引用类型时则显得力不从心。因为所有对象都会返回`Object`。`typeof`运算符用于`null`时也会返回`object`

~~~ javascript
// 检测日期
if (value instanceof Date) {
    console.log(value.getFullYear());
}
// 检测正则表达式
if (value instanceof RegExp) {
    if (value.test(antherValue)) {
        console.log("Matched");
    }
}
// 检测Error
if (value instanceof Error) {
    throw value;
}
// 检测自定义对象(唯一的方法)
if (me instanceof Person) {

}
// 所有对象都被认为是Object的实例
if (me instanceof Object) {

}
~~~

## 检测函数

~~~ javascript
function myFunc(){}
console.log(typeof myFunc === "function");
~~~

## 检测数组

~~~ javascript
// 采用鸭式辨型的方法
function isArray(value) {
    return typeof value.sort === "function";
}
// 优雅的解决方案
function isArray(value) {
    return Object.prototype.toString.call(value) === "[Object Array]";
}
function isArray(value) {
    if (typeof Array.isArray === "function") {
        return Array.isArray(value);
    }
    return Object.prototype.toString.call(value) === "[Object Array]";
}
~~~

## 检测属性

~~~ javascript
var object = {
    count: 0,
    related: null
};
if ("count" in object) {

}
if ("related" in object) {
}
// IE8以及小于IE8版本中，DOM对象并非继承自Object，因此不包含hasOwnProperty方法
// 如果对象是DOM时需要判断
if ("hasOwnProperty" in Object && Object.hasOwnProperty("related")) {

}
~~~
