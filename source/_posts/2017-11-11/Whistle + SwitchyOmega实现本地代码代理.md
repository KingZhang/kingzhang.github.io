---
title: Whistle + SwitchyOmega实现本地代码代理
date: 2017-11-11
comments: true
tags: 
	- Whistle
	- SwitchOmmega
---

## Whistle

[whistle](https://github.com/avwo/whistle)是一款用[Node](https://nodejs.org/)实现的跨平台的Web调试代理工具，支持查看修改http(s)、Websocket连接的请求和响应内容。

类似Fiddler，之前也是因为Fiddler无法使用，才找到替代方案。

详细的使用文档参考官网 https://avwo.github.io/whistle

需要注意的是：

<!--more-->

* 代理HTTPS请求需要安装证书并开启Https拦截功能：

https://avwo.github.io/whistle/webui/https.html

- 如果遇到无法代理HTTPS的情况，尝试升级到最新版本

```powershell
npm update -g whistle
```

* 每次需要命令行启动whistle：

```powershell
w2 start
```



## SwitchyOmega

SwitchyOmega主要是用来管理和切换多个代理，对于公司内部网络代理。

配置教程 https://www.switchyomega.com/settings.html

可以按照以下配置方式：

* proxy情景模式设置公司代理地址
* whistle情景模式设计whistle代理地址
* auto switch情景模式对不同的地址适配不同的情景模式