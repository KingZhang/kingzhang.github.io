---
title: HTTP 缓存机制
date: 2018-03-24
comments: true
tags: 
	- HTTP
	- cache
---

HTTP协议中定义了一系列请求头和响应头，第一次请求的时候，浏览器向服务器发送请求，后续浏览器根据请求头和响应头控制缓存。

<!--more-->

## 缓存的处理流程

```flow
start=>start: 开始
end=>end: 结束
retrieve=>inputoutput: 接受请求报文
analysis=>operation: 解析报文
search=>condition: 是否有缓存可用？
check=>condition: 新鲜度检查
create=>operation: 由缓存生成响应报文
return=>inputoutput: 由缓存返回响应报文
log=>operation: 记录日志

isChache=>condition: 使用缓存？
toServer=>operation: 发送请求到服务器
serverReturn=>inputoutput: 服务端返回，协商缓存

send2=>operation: 与服务器再验证

start->retrieve->analysis->search(yes)->check(yes)->create->return->log->end
search(no)->toServer->serverReturn->end
check(no)->send2->isChache(yes)->create->return->log->end
isChache(no)->toServer->serverReturn->end

```





## 缓存相关的请求头和响应头
### 请求头
* Cache-Control

* Expires

* If-Match

* If-Not-Match

* If-Modified-Since



### 响应头
* Cache-Control
* Etag
* Expires
* Last-Modified


## 缓存策略

服务器通过HTTP的缓存头部来控制缓存。Cache-Control优先级高于Expires，按优先级递减为：

* Cache-Control: no-store

  禁止缓存

* Cache-Control: no-cache

  资源被缓存，但是每次使用前都要与服务器进行新鲜度检查
  > HTTP1.0中的Pragma:no-cache与Cache- Control:no-cache同作用

* Cache-Control: must-revalidate
  ​可缓存，但是缓存过期后，必须到服务器进行再验证，否则不能提供缓存资源。

* Cache-Control: max-age

  缓存最大使用时长，为相对时长（从缓存生成时间开始计算，以秒为单位）

* Expires（HTTP1.0）

  指定绝对的过期日期，绝对日期依赖客户端的日期，如果服务端和客户端日期不一致，则会出现偏差。

  > Cache-Control: max-age和Expires本质上是一致的，由于Cache-Control使用相对时长，因此更倾向使用Cache-Control。

* 无缓存头部






## 缓存再验证

当文档过期的时候，需要到服务器询问资源是否发生了变化。对于缓存再验证，使用最多的两个头部为 If-Modified-Since 和 If-Not-Match。
* If-Modified-Since: <date>
* If-Not-Match: <tags>


#### If-Modified-Since + Last-Modified

> 配合Cache-Control使用

If-Modified-Since与Last-Modified配合使用，当缓存进行再验证的时候，If-Modified-Since携带最后修改缓存副本的日期。
* 如果文档没被修改或重写，服务器返回304 Not Modified。此时，服务器只会返回需要更新的头部，不会返回资源内容，同时返回新的修改日期；
* 如果文档被修改或重写了，服务器返回新的资源给缓存，并同时返回新的修改日期，浏览器更新缓存




#### If-Not-Match + Etag

> 配合Cache-Control使用
>
> Last-Modified和Etag一起的情况下，服务器先验证Etag，Etag一致时才会继续验证Last-Modified
>
> 带"W/"前缀表示弱验证器

Etag是资源在服务器端的唯一标识符，可能包含资源的序列号或版本号，或其他指纹信息。相比Last-Modified，Etag可以更加准确的控制缓存。当开发者修改资源的时候，可以修改Etag来标志新版本，从而防止以下情况缓存失效：

* 资源被周期性重写，但内容没有变化
* 服务器无法获取文件修改时间
* Last-Modified只能以秒为力度，资源可能在一秒以内被修改多次

## HTML配置缓存
HTML2.0 定义了 <META HTTP-EQUIV>标签（最初为服务器使用），部分浏览器会解析HTML中的HTTP-EQUIV标签，但是HTTP-EQUIV并是不控制缓存的好方法。







