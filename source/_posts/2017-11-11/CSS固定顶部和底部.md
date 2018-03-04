---
title: CSS固定顶部和底部
date: 2017-11-10
comments: true
tags: 
	- CSS
---

首先body添加内边距：

```css
body {
    padding-top: 50px; /*顶部导航栏高度*/
    padding-bottom: 40px; /*底部高度*/
}
```

设置header和footer为fixed：

```css
header {
    position: fixed;
    z-index: 1024;
    left: 0;
    right: 0;
    top: 0;
}

footer {
    position: fixed;
    z-index: 1024;
    left: 0;
    right: 0;
    bottom: 0;
}
```

