---
layout: post
title: 选择除第一个元素外的其他子元素
category: tech
---

选择div下的除了第一个p外的所有子元素p

<!--more-->

~~~ css
/*选择div下的除了第一个p外的所有子元素p*/
div>p:not(:first-child)
~~~
