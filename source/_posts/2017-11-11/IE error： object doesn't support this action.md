---
title: IE error： object doesn't support this action
date: 2017-11-10
comments: true
tags: 
	- IE
	- Javascript
---

​	IE总是有各种稀奇古怪的错误，IE8之后还好点，至少可以dubug。IE6 IE7就是灾难，报错就算了，所谓的error line总是指向莫名奇妙的地方，让人一头雾水。最近在IE7上碰到一个error：“Object doesn’t support this action”，指向的error line竟然是空行，而代码在其他浏览器没有问题。

​      仔细检查，发现下面这行代码出错了：

```javascript
for(i = 0, length = currencyMap.length; i < length; i++)
```

发现没有用var定义变量，先加上：

```javascript
for(var i = 0, length = currencyMap.length; i < length; i++)
```

再运行，不再报错了。

​       一般来说，如果声明的时候没有使用var关键字，就相当于window.length。但是IE为什么却抛出“Object doesn’t support this action”？

​       网上搜索了一下，基本都说由于使用了IE的保留关键字（reserved keyword），但是并没有搜到IE相关的保留关键字。测试了一下， 如“item”也有相同问题：

```javascript
items=[];

for(item in items){   // would throw the same error.

}
```