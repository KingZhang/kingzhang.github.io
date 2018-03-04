---
title: Javascript this 解析
date: 2017-11-10
comments: true
tags: 
	- Javascript
---

Javascript中，this是一个非常有用的关键字, this是在运行时基于函数的运行环境绑定的，但是，如果使用的时候不注意，很容易就出错了。

ECMAScript Standard对this的定义看起来非常简单： The this keyword evaluates to the value of the **ThisBinding** of the current **execution context**.

其中，对于ThisBinding和execution context, ECMAScript Standard 有另外的详细说明：

## 执行环境(引用http://ecmascript.cn/)

当控制器转入 ECMA 脚本的可执行代码时，控制器会进入一个执行环境。**当前活动的多个执行环境在逻辑上形成一个栈结构。**该逻辑栈的最顶层的执行环境称为当前运行的执行环境。**任何时候，当控制器从当前运行的执行环境相关的可执行代码转入与该执行环境无关的可执行代码时，会创建一个新的执行环境。新建的这个执行环境会推入栈中，成为当前运行的执行环境。**

执行环境包含所有用于追踪与其相关的代码的执行进度的状态。精确地说，每个执行环境包含如下表列出的组件。

执行环境的状态组件

| **组件**      | **作用目的**                                 |
| ----------- | ---------------------------------------- |
| 词法环境        | 指定一个词法环境对象，用于解析该执行环境内的代码创建的标识符引用。        |
| 变量环境        | 指定一个词法环境对象，其环境数据用于保存由该执行环境内的代码通过 变量表达式 和 函数表达式 创建的绑定。 |
| ThisBinding | 指定该执行环境内的 ECMA 脚本代码中 this 关键字所关联的值。      |

其中执行环境的词法环境和变量环境组件始终为 词法环境 对象。当创建一个执行环境时，其词法环境组件和变量环境组件最初是同一个值。在该执行环境相关联的代码的执行过程中，变量环境组件永远不变，而词法环境组件有可能改变。

在本标准中，通常情况下，只有正在运行的执行环境（执行环境栈里的最顶层对象）会被算法直接修改。**因此当遇到“词法环境”，“变量环境”和“ThisBinding”这三个术语时，指的是正在运行的执行环境的对应组件。

执行环境是一个纯粹的标准机制，并不代表任何 ECMA 脚本实现的工件。在 ECMA 脚本程序中是不可能访问到执行环境的。

## ThisBinding

对于ThisBinding的指定，则分为几个情况，一般主要关注在函数中的绑定情况：

1. 初始化全局执行环境时，将 thisBinding设置为 全局对象。
2. 进入函数代码的执行环境时，控制流根据一个函数对象 F、调用者提供的**thisArg** 以及调用者提供的 argumentList，执行以下步骤：

A．如果 函数代码 是 严格模式下的代码 ，设 ThisBinding为 thisArg。

B. 否则如果 thisArg 是 null 或 undefined，则设 ThisBinding为 全局对象 。

C. 否则如果 Type(thisArg) 的结果不为 Object，则设 tThisBinding为 ToObject(thisArg)。

D．否则设 this 绑定为 thisArg。

 

因此，最终决定ThisBinding的是调用者提供的 thisArg。那么，如何知道thisArg的值呢？这就要看函数的调用方式了。

 

## 通过call或者apply调用

通过call或者apply调用函数时，thisArg的值比较明显，为传入的第一个参数。

```javascript
Function.prototype.apply (thisArg, argArray)

Function.prototype.call (thisArg [ , arg1 [ , arg2, … ] ] )
```

 

如下：

```javascript
function a(){

    console.log(this);

}

function b(){ }


a.apply(a); // function a

a.apply(b); // function b
```


## 作为对象方法调用

当函数作为对象方法调用时，this指向对象。

```javascript
var a = {};

a.b = b;

a.b(); // object a


function b(){

    console.log(this);

}
```


## 直接调用函数

直接调用函数时，this为全局变量Window（thisArg为null）。

```javascript
function a(){

console.log(this);

}

a(); // Window

```



要注意，直接调用函数时，无论嵌套多少层，无论是在哪个对象里面调用，this始终指向Window。


```javascript
function a(){

    console.log(this); // Window

    b(); // Window

    function b(){

        console.log(this);

        c(); // Window

        function c(){

            console.log(this);

        }

    }

}

a();
```

再看另一个例子：

```javascript
var a = {

    b : function(){

        console.log(this); // Object a

        c(); // Window

        function c(){

            console.log(this);

        }

        d(); // Window

    }

};

function d(){

    console.log(this);

}

a.b();
```

## 作为构造函数调用

当函数作为构造函数调用时，this指向新建的对象。

```javascript
var A = function(){

    console.log(this);

    this.c = 1;

    this.d = 2;

    this.b = function(){

        console.log(this);

        console.log(this.c);

        console.log(this.d);

    };

};

var a = new A(); // A{}

a.d = 3;

a.b(); // A {c: 1, b: function}

// 1

// 3
```

以上几种便是基本函数调用方式了，后面的几种调用方式中，只要识别函数基于上面哪种方式调用，便可以辨别this对象。

## 作为回调函数调用

当函数通过回调函数调用时，其调用方式可能会让人感到疑惑，先来看一个例子：

```javascript
var a = {

    b : b

};

function b(){

    console.log(this);

}

setTimeout(b, 500); // window

setTimeout(a.b, 1000); // window
```


第一处为this为window应该很好理解，因为看起来是直接调用函数，因此this指向window对象。但是第二处看起来似乎是作为对象的方法调用？事实上，回调函数往往是作为一个参数传入，无论它本身结构如何，最终它将赋给一个新的变量。因此，上面第二处如果写成这样应该更好理解：

```javascript
var f = a.b;

f();// window

setTimeout(f, 1000); //window
```

 

另外一种回调函数的方式是用一个匿名函数：

```javascript
setTimeout(function(){

    a.b(); // Object a

}, 1000);
```

 

## DOM节点相关的函数调用

在HTML代码中直接使用onclick事件处理时，分为几种情况：

1. 直接在事件处理中使用this，则this指向当前元素。
2. 间接在事件处理中使用this，则this指向全局变量Window。
3. 通过方法调用间接使用this，则this取决于使用上面何种方法调用。

```html
<button id="testBtn1" onclick="console.log(this);" >testBtn1</button> <!-- testBtn1 -->

<button id="testBtn2" onclick="(function(){console.log(this);})()" >testBtn2</button> <!-- Window -->

<button id="testBtn3" onclick="b()" >testBtn3</button> <!-- Window -->

<button id="testBtn4" onclick="a.b()" >testBtn4</button> <!-- Object a -->
```


在Javascript代码中通过事件绑定DOM元素时，也分为几种情况：

1. 通过onclick、addEventListener等函数绑定事件，调用函数中的this指向当前DOM元素。
2. IE中，通过attachEvent函数绑定事件时，调用函数中的this指向全局对象Window。
3. 大部分Javascript框架都对事件绑定进行了封装，以兼容IE与其他浏览器的区别。


```html
<button id="testBtn5" >testBtn5</button>

<button id="testBtn6" >testBtn6</button>

<button id="testBtn7" >testBtn7(except IE)</button>

<button id="testBtn8" >testBtn8(Only for IE)</button>

<button id="testBtn9" >testBtn9(jQuery)</button>
```


```javascript
var a = {

    b : b

};

function b(){

    console.log(this);

}

document.getElementById("testBtn5").onclick = b; // testBtn5

document.getElementById("testBtn6").onclick = a.b; // testBtn6

document.getElementById("testBtn7").addEventListener("click", b, false); // testBtn7

document.getElementById("testBtn8").attachEvent('onclick', b); // Window

jQuery("#testBtn9").click(b); // testBtn9
```

## 闭包中的this

由于this对象是在运行时基于函数的运行环境绑定的。在匿名函数中，其运行环境具有全局性，因此this对象通常指向window。然而，由于闭包的特殊性，这种差异可能在一定程度上引起意想不到的结果。

```javascript
var student = {

    name : "Ken",

    id : "2020",

    getId : function(){

        return function(){

            if(this.id == "2020"){

                this.id = "1010";

           }

           return this.id;

        };

    }

}

console.log(student.getId()());//undefined
```

如上代码，getId()方法返回了一个匿名函数，匿名函数对this.id处理后并返回。但是由于调用student.getId()()的时候会立刻执行并调用它返回的匿名函数，而此时匿名函数的this对象指向window，因此无法取得其外部作用域的this对象。

如果想要在闭包中访问到外部作用域的变量，可以将其外部作用域的this对象保存到一个闭包变量中。

```javascript
var student = {

    name : "Ken",

    id : "2020",

    getId : function(){

    var that = this;

    return function(){

            if(that.id == "2020"){

                that.id = "1010";

            }

            return that.id;

        };

    }

}

console.log(student.getId()());//1010
```


## 严格模式下的this

在ECMAScript的严格模式下，thisArg不会强制转化为一个对象。 因此this的值为null或undefined时不会转化为全局对象，并且基本类型的值不会转化为包装类型对象。

```javascript
function b(){

    "use strict";

    console.log(this);

}

b(); //undefined
```

## ES6箭头函数中的this

箭头函数并没有绑定this，而是通过作用域链往上查找。

```javascript
const obj = {
	test: 2,
	getTest() {
    	return () => {
    		console.log('test', this.test)
    	}
	}
}

var getTest = obj.getTest();
getTest(); // 2
```



