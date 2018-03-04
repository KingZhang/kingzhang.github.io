---
title: 如何发布新的npm包
date: 2017-11-11
comments: true
tags: 
	- npm
---

## 初始化 npm init

按照需求填写项目信息即可，注意git repository要跟github上或者其他git仓库匹配。

初始化完成后可以进行开发，开发完成后，开始发布。



## 登录npm：npm login

通过npm login命令登录，如果未注册请先到网页注册帐号~

必须完成邮箱验证，否则publish的时候会报错。



## 发布 npm publish

发布npm包之前，记得写好README.md，发布后npm的展示页面上将会使用的你README文件。

后续每次更新都需要修改package.json中的version。

注意：如果你使用的是国内npm的地址，会报错：

```shell
no_perms Private mode enable, only admin can publish this module
```

设置回原本的就可以了

```shell
npm config set registry http://registry.npmjs.org 
```



## 下载你的npm包： npm install XXXX

发布后就可以通过npm install安装你的npm了~