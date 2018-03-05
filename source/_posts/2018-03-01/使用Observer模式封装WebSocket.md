---
title: 使用Observer模式封装WebSocket
date: 2018-03-01
comments: true
tags: 
	- websocket
	- observer
	- npm
---

利用Observer模式封装WebSocket的插件，后端需遵从一定的数据结构。

[Github](https://github.com/kingzhang/ws-request)

## 安装
```bash
npm install --save ws-request
```

## 使用
<!--more-->
```javascript
import WsRequest from 'ws-request';

// 创建WsRequest对象，并未开启websocket
const wsRequest = new WsRequest('ws://localhost:8181', {
    monitorField: 'event',  // 可选字段，response监听字段，默认event
    dataField: 'data', // 可选字段，response实际数据字段，默认data
});

// 监听OPEN&CLOSE事件
wsRequest.subscribe('OPEN', () => {
    console.log('ws open!!');
});
wsRequest.subscribe('CLOSE', () => {
    console.log('ws close!!');
});

// 监听所有事件
wsRequest.subscribe('*', (data) => {
    console.log('all event', data);
});

// 监听特定事件
wsRequest.subscribe('event1', (data) => {
    console.log('event1 data', data);
});

// 取消监听
function callback1(data) {
    console.log('callback1', data);
}
wsRequest.subscribe('event1', callback1);
wsRequest.unsubscribe('event1', callback1);

// 连接websocket
wsRequest.open();
// 获取webscoket状态
wsRequest.isOpen();
// 发送消息
wsRequest.send(JSON.stringify({"getData": "field"}));
// 关闭websocket
wsRequest.close();
```

## WebScoket后台数据结构
```javascript
{
    "event": "sendData1",
    "data": "{'name': 'todo'}",
}
```