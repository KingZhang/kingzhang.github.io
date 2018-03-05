---
title: Javascript命名空间模式
date: 2017-11-10
comments: true
tags: 
	- Javascript
---

命名空间是通过为项目或库创建一个全局对象，然后将所有功能添加到该全局变量中。通过减少程序中全局变量的数量，实现单全局变量，从而在具有大量函数、对象和其他变量的情况下不会造成全局污染，同时也避免了命名冲突等问题。

然而，在不同的文件中给一个命名空间添加属性的时候，首先要保证这个命名空间是已经存在的，同时不对已有的命名空间造成任何破坏。可以通过非破坏性的命名空间函数实现：

<!--more-->

```javascript
var KUI = KUI || {};
KUI.utils = KUI.utils || {};

KUI.utils.namespace = function(ns){
    var parts = ns.split("."),
        object = KUI,
        i, len;

    if(parts[0] === "KUI"){
        parts = parts.slice(1);
    }
    for(i = 0, len = parts.length; i < len; i+=1){
        if(!object[parts[i]]){
            object[parts[i]] = {};
        }
        object = object[parts[i]];
    }
    return object;

};
```

用法：

```javascript
KUI.utils.namespace("KUI.common");

KUI.utils.namespace("KUI.common.testing");

KUI.utils.namespace("KUI.modules.function.plugins");

KUI.utils.namespace("format");
```

看一下经过上述后KUI都有什么：

```javascript
{

    "utils": {},

    "common": {

        "testing": {}

    },

    "modules": {

        "function": {

            "plugins": {}

        }

    },

    "format": {}

}
```

命名空间模式的缺点

1. 需要输入更长的字符，并且需要更长的解析时间；
2. 对单全局变量的依赖性，即任何代码都可以修改该全局实例，其他代码将获得修改后的实例。
