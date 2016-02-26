---
layout: post
title: jQuery Validate使用bootstrap错误提示样式
category: tech
---

~~~ javascript
$.validator.setDefaults({
    // 对隐藏域也进行校验
    ignore : [],
        // 手动触发校验
    onsubmit : false,
    highlight : function(element) {
        $(element).closest('.form-group').addClass('has-error');
    },
    unhighlight : function(element) {
        $(element).closest('.form-group').removeClass('has-error');
    },
    errorElement : 'span',
    errorClass : 'help-block',
    errorPlacement : function(error, element) {
        if (element.parent('.input-group').length) {
            error.insertAfter(element.parent());
        } else {
            error.insertAfter(element);
        }
    }
});
// 手动触发校验代码
$this.validator.form();
// OR
$("#myFormId").valid();
~~~
<!--more-->

![效果](http://7xqzc5.com1.z0.glb.clouddn.com/13230801_TQRI.gif)

[Bootstrap 3 with jQuery Validation Plugin](http://stackoverflow.com/questions/18754020/bootstrap-3-with-jquery-validation-plugin)
