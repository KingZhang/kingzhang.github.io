---
title: 整合jQuery和Prototype遇到的问题
date: 2017-11-10
comments: true
tags: 
	- jQuery
	- Prototype
---

由于项目要在旧的服务器上面运行，而旧的服务器底层用了Prototype，所以需要解决jQuery和Prototype冲突的问题。

## $符号冲突问题

这个还是很好解决的。

```javascript
jQuery.noConflict()
```

而如果不想将原来所有$符号换成jQuery，可以用即时函数包住原来的jQuery代码：

<!--more-->

```javascript
(function($){
})(jQuery);

jQuery(document).ready(function($){
});

jQuery(function($){
});
```

##  for in 数组循环问题

for in 语句本身并不是一个 "bad practice"，但是却容易被误用，比如用在数组上。

使用for in语句首先要注意，如果不能百分百确定，那么请使用hasOwnProperty：

```javascript
for (var prop in obj) {
   if (obj.hasOwnProperty(prop)) {
       // prop is not inherited
   }
}
```

其次，不要用for in对数组或类数组对象(array-like)。由于Prototype会自动为所有数组添加一推属性，即使用上面hasOwnProperty的方式也无法过滤。不过可以用下面这种方式：

```javascript
for (var prop in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, prop)) {
    // prop is not inherited
    }
}
```

##  IE弹框问题 “Stop running this script”

这是一个很奇怪的问题。原来的框架在IE没有问题，但是导入Prototype之后，IE弹框

```
Stop running this script?

A script on this page is causing your web browser to run slowly. 
If it continues to run, your computer might become unresponsive.
```

对于这个问题完全摸不着头脑，通过删除引入文件终于找到罪魁祸首，一个两万多行的文件。一开始还以为里面某个语句导致了这个问题，但是发现并不是。将文件分成两个文件后，就没有出现这个弹框了。