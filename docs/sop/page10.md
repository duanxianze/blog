---
description: fpr
title: fpr配置文件写法
readingTime: false
tag:
 - 前端基础
recommend: 3
---

## 客户端  frpc.toml

```toml
serverAddr = "***"  //服务器域名
serverPort = 7*** //服务器监听端口
auth.method = "token"  // 校验方式
auth.token = "*****" // token
#不能删除, 否则连接不上会闪退
loginFailExit=false
[[proxies]]
name = "fno"
type = "http" //代理类型
localIP = "127.0.0.1"
localPort = 5222
customDomains = ["*****"] //重要！打开时的域名，类似于nginx的serve_name
```

## 服务端 frps.toml

```toml
bindAddr = "0.0.0.0"
bindPort = **** //监听端口
vhostHTTPPort = 7*** //  对外服务端口
vhostHTTPSPort = 7*** // 对外服务端口https
auth.method = "token"  // 校验方式
auth.token = "*****" // token
webServer.addr = "0.0.0.0"
webServer.port = 7***  // web控制台端口
webServer.user = "frps_user" // web控制台用户
webServer.password = "****" // web控制台用户密码
```