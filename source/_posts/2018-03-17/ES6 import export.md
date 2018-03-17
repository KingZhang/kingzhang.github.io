---
title: ES6 import export
date: 2018-03-17
comments: true
tags: 
	- Javascript
	- import
	- export
---
ES6 import & export 有下面几种用法：

## 默认导出（default export）

```javascript
// People.js
const People = function() {
    // ......
}
export default People;

// index.js
import People from 'People';
const people = new People();
```

一个文件中只能存在一个默认导出模块。

<!-- more -->

## 命名导出（named export）

```javascript
// Utils.js
export const appName = 'appName';
export function getName() {
    console.log('getName');
}
// index.js
// 用法一
import { appName, getName } from 'Utils';
console.log('appName', appName);
getName();
// 用法二
import * as Utils from 'Utils';
console.log('appName', Utils.appName);
Utils.getName();
```

## 混合导出 （mixed export）

```javascript
// Utils.js
export default function getDefault() {
    console.log('default export');
};
export function getName() {
    console.log('getName');
}
export { each as forEach };

// index.js
import getDefault, { getName } from 'Utils';

```

