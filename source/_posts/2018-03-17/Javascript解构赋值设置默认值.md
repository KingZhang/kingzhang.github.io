---
title: Javascript解构赋值设置默认值
date: 2018-03-17
comments: true
tags: 
	- Javascript
	- Destructuring
---
## 基本语法

ES6解构赋值设置默认值语法如下：

```javascript
const people = {
    firseName: 'test'
};
const { firseName = 'firseName', secondName = 'secondName' } = people;
console.log(firseName); // 'test'
console.log(secondName); // 'secondName'
```

PS. 方法参数设置默认值类似

```javascript
function test(name, age = 100) {
    console.log(name, age);
}
test(); // undefined, 100
```

<!--more-->

## 默认值只过滤undefined

ES6之前，很多人喜欢利用短路表达式设置默认值，因此会有下面这种问题

```javascript
const age = 0;
const myAge = age || 100;
console.log(myAge); // 100

const testFlag = false;
const flag = testFlag || true;
console.log(flag); // true
```

但是对于解构赋值的默认值来说，只过滤undefined

```javascript
const people = {
    age: 0
};
const { age = 100 } = people;
console.log(age); // 0

const { flag = 'value' } = { flag: undefined };
console.log(flag); // 'value'

const { flag = 'value' } = { flag: null };
console.log(flag); // null

const { flag = true } = { flag: false };
console.log(flag); // false
```



## 与重命名结合

```javascript
const people = {
    firseName: 'test'
};
const { firseName: name = 'firseName', secondName = 'secondName' } = people;
console.log(name); // 'test'
// console.log(firseName); // firseName is not defined
console.log(secondName); // 'secondName'
```

