---
title: webpack问题
date: 2017-11-11
comments: true
tags: 
	- webpack
---

## webpack jsx  Unexpected token:

配置loader

```json
{
  test: /\.js[x]?$/,
  loaders: ['react-hot', 'babel?presets[]=es2015&presets[]=react'],
  exclude: path.join(rootPath, 'node_modules')
}
```

或者添加到 .babelrc文件

## Failed at the node-sass@3.13.1 postinstall script

缺少python的编译环境，安装node-gyp和gym：

```shell
npm install -g node-gyp 
npm install –global –production windows-build-tools
```



## Found bindings for the following environments: - Windws 64-bit with Node.js

Nodejs版本不兼容



## 添加async的时候webpack打包报错

安装async的解析插件

```shell
npm install --save-dev babel-plugin-transform-async-to-generator
```

添加到 .babelrc文件

```json
{"plugins": ["transform-async-to-generator"]}
```

安装后运行可能再次报错：regeneratorRuntime is not defined

安装babel-polyfill

```shell
npm install --save-dev babel-polyfill
```

在webpack配置文件添加

```js
require("babel-polyfill");
module.exports = {
  entry: ['babel-polyfill', './test.js'],

  ......
};
```



