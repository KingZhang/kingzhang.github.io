---
title: Nodejs复制文件
date: 2017-11-11
comments: true
tags: 
	- Nodejs
---

## 新版本

Node.js 8.5.0版本之后有api方法fs.copyFile 和fs.copyFileSync对文件复制。



## 旧版本

通过stream进行文件复制：

```javascript
var fs = require('fs');

fs.createReadStream('test.log').pipe(fs.createWriteStream('newLog.log'));
```

如果你必须对`createReadStream` 和 createWriteStream 做错误处理：

```javascript
function copyFile(source, target, cb) {
  var cbCalled = false;

  var rd = fs.createReadStream(source);
  rd.on("error", function(err) {
    done(err);
  });
  var wr = fs.createWriteStream(target);
  wr.on("error", function(err) {
    done(err);
  });
  wr.on("close", function(ex) {
    done();
  });
  rd.pipe(wr);

  function done(err) {
    if (!cbCalled) {
      cb(err);
      cbCalled = true;
    }
  }
}
```



