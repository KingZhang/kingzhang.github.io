---
title: Javascript闭包
date: 2017-11-10
comments: true
tags: 
	- Javascript
---

闭包是ECMAScript一个很重要的特征，但是却很难用合适的定义来描述它。虽然闭包很难清晰地描述，但是，却很容易创建，或者说，不小心创建。然而，闭包的存在其实是有一定的潜在问题的。为了避免“不小心”地创建闭包，以及更好地利用闭包的优点，有必要理解闭包的机制。

## 闭包的定义

关于闭包，有太多的定义，特别是有一些定义非常抽象，象这个：

A "closure" is an expression (typically a function) that can have free variables together with an environment that binds those variables.


大致是说闭包是一个表达式，拥有一些自由变量及绑定这些变量的执行环境。这种定义太书面化，反而难以理解。

 <!--more-->

还有另一个定义：

**所有函数都是闭包。**这个定义给我很大的迷惑，换句话说，由于Javascript没有块级作用域，因此闭包一般指的是函数（想不出除了函数以外还有哪些方式可以构成闭包）。

 

这里不想太多讨论函数与闭包的关系，下面给出我认为比较容易理解的定义吧。


首先，**闭包的存在是基于作用域链。**由于作用域链的机制，所有函数（即使全局函数）都能引用上下文执行环境中的变量（即free variables）。

其次，闭包内部必须有**free variables**。顺便说下两种变量1. Local variables (bound variables) 2. Non-local variables (free variables)

最后，**在其上下文环境结束后仍然存在**。即内部函数拥有比它的外部函数更长的生命周期。


## 关于闭包定义的解析

 

关于闭包定义的两点，一直在考虑是不是必须同时满足。

首先，如果闭包内部没有free variables，即是说它没有访问外部的变量，那么就失去了闭包的意义。（除非通过其他闭包改变了行为）因此，我认为free variables是必要条件。

其次，如果函数内部存在free variables，但是当其上下文环境销毁后，它也跟着销毁。可以想象内部函数，虽然访问了其外部函数变量，但是当外部函数执行完后也随之回收。这种情况下，闭包的讨论也没有意义。

来看两个例子：

```javascript
var objectA = (function() {
        var localA = "localA";
        innerFn();

        // 单纯的内部函数调用
        function innerFn() {
            localA = "innerChange";
        }
        return {
            getLocalA : function() {
                return "empty";
            }
        };
    })();

    objectA.getLocalA();
    objectA.getLocalA = function() {
        return localA;
    };
    //console.log(objectA.getLocalA()); //error: localA is not defined

    var objectB = (function() {
        var localB = "localB";
        return {
            getLocalB : function() {
                return "empty";
            },
            updateGetLocalB : function() {
                this.getLocalB = function() {
                    return localB;
                };
            },
            updateLocalB : function() {
                localB = "changeLocalB";
            }
        };
    })();

    console.log(objectB.getLocalB()); // empty
    // 通过其他闭包改变
    objectB.updateGetLocalB();
    console.log(objectB.getLocalB()); // localB
    objectB.updateLocalB();
    console.log(objectB.getLocalB()); // changeLocalB
```

 

## 闭包的优点和缺点

闭包的优点是闭包内部可以访问到定义它们的外部函数的参数和变量（除了this和arguments）。

闭包主要的问题便是它会保存包含它的函数的作用域，因此比一般函数占用更多的内存空间，因此不宜过度使用闭包。

 

## 闭包的应用 

闭包最基本的应用场景便是通过保护内部变量从而实现私有，比如模块模式。