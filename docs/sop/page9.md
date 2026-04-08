---
description: chrome解决http自动跳转https问题
title: chrome解决http自动跳转https问题
readingTime: false
tag:
 - 前端基础
recommend: 2
---

1. 地址栏输入： chrome://net-internals/#hsts

2. 找到底部Delete domain security policies一栏，输入想处理的域名，点击delete。

    ![chrome截图](./imgs/image.png)

3. 搞定了，再次访问http域名不再自动跳转https了。

