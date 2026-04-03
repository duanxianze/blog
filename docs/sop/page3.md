---
description: docker
title: docker-compose使用方法
readingTime: false
tag:
 - 前端基础
recommend: 3
---

docker-compose使用真的很方便，用来部署开源项目十分简单，以halo为例:

创建一个docker-compose.yaml文件

从官网复制以下代码

```yaml
version: "3"

services:

  halo:

    image: halohub/halo:2.6

    container_name: halo

    restart: on-failure:3

    network_mode: "host"

    volumes:

      - ./:/root/.halo2

    command:

      # 修改为自己已有的 MySQL 配置

      - --spring.r2dbc.url=r2dbc:pool:mysql://localhost:3306/halo

      - --spring.r2dbc.username=halo

      - --spring.r2dbc.password=

      - --spring.sql.init.platform=mysql

      # 外部访问地址，请根据实际需要修改

      - --halo.external-url=http://localhost:8090/

      # 初始化的超级管理员用户名

      - --halo.security.initializer.superadminusername=admin

      # 初始化的超级管理员密码

      - --halo.security.initializer.superadminpassword=P@88w0rd

      # 端口号 默认8090

      - --server.port=8090
```
自己创建一个halo的数据库，修改好mysql的账号密码

运行docker-compose up -d 即可，项目已经正常使用

其他的docker-compose命令记录

# 查看web容器的实时日志

`$ docker-compose logs -f web`

# 停止所有up命令启动的容器

`$ docker-compose down`

# 停止所有up命令启动的容器,并移除数据卷

`$ docker-compose down -v`

# 重新启动停止服务的容器

`$ docker-compose restart web`

# 暂停web容器

`$ docker-compose pause web`

# 恢复web容器

`$ docker-compose unpause web`

# 删除web容器，删除前必需停止stop web容器服务

`$ docker-compose rm web `

# 查看各个服务容器内运行的进程

`$ docker-compose top     `

