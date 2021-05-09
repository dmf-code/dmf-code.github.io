---
title: tarsweb平台搭建
date: 2021-05-09 18:17:48
tags: tars
categories: tars
cover: https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509185133.png
---

环境推荐使用：`Centos7`

`docker` 环境搭建可以自己在网上找教程，文章中就不再说明了。

#### docker-compose 安装

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose 

sudo chmod +x /usr/local/bin/docker-compose
```

#### DaoCloud 镜像配置

```bash
curl -L https://get.daocloud.io/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose 

chmod +x /usr/local/bin/docker-compose
```

#### tars 环境搭建 docker-compose 文件

```yaml
version: "3"


services:
    tars:
        image: tarscloud/tars:php
        # 自定义容器名称
        container_name: tars
        # 自定义宿主机与容器之间的端口映射
        ports:
            - "3000:3000"
            - "80:80"
            - "20000-20005:20000-20005"
        # 挂载宿主机与容器之间的文件路径
        volumes:
            - /c/Users/tars_data:/data
            - /c/Users:/root/TarsShare
        # 自定义所需环境变量
        environment:
            # 此选项用于将Tars的系统进程的数据目录挂载到 /data 目录之下
            - MOUNT_DATA=true
            # 数据库ip 可以使用容器名来获取动态分配的ip地址
            - DBIP=mysql
            # 数据库端口
            - DBPort=3306
            # 数据库使用者
            - DBUser=root
            # 数据库密码
            - DBPassword=tars2015
        # 依赖服务
        depends_on:
            - mysql
        # 所属网络
        networks:
            - kong-net
        # 设置容器访问宿主机权限
        privileged: true
        restart: always


    mysql:
        image: mysql:5.7.28
        container_name: mysql
        ports:
            - "3306:3306"
        volumes:
            # 挂载数据库目录
            - "/c/Users/mysql_data:/var/lib/mysql"
        environment:
            # 设置数据库密码
            - "MYSQL_ROOT_PASSWORD=tars2015"
        networks:
            - "kong-net"
        privileged: true
        restart: "always"
        command:
            # 设置支持全零的date字段值
            - --sql_mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
            # window下mysql不能使用aio
            - --innodb_use_native_aio=0


    nginx:
        image: "nginx:latest"
        container_name: "nginx"
        privileged: true
        ports:
            - "12226:12226"
            - "1234:1234"
            - "12345:80"
        volumes:
            - "/c/Users/nginx/web:/usr/share/nginx/html"
            - "/c/Users/nginx/vhost:/etc/nginx/conf.d"
            - "/c/Users/nginx/nginx.conf:/etc/nginx/nginx.conf"
        depends_on:
            - tars
        networks:
            - "kong-net"


    redis:
        image: "redis:latest"
        container_name: "redis"
        restart: "always"
        volumes:
            - "/c/Users/redis:/etc/redis"
        ports:
            - "6379:6379"
        command:
            # 数据本地写入应用
            - --appendonly yes
        networks:
            - "kong-net"


    kong:
        image: "kong:latest"
        container_name: "kong"
        ports:
            - "8000:8000"
            - "8001:8001"
            - "8443:8443"
            - "8444:8444"
        environment:
            # kong 信息输出设置
            - "KONG_ADMIN_ACCESS_LOG=/dev/stdout"
            - "KONG_ADMIN_ERROR_LOG=/dev/stderr"
            # 监听ip + port
            - "KONG_ADMIN_LISTEN=0.0.0.0:8001"
            - "KONG_CASSANDRA_CONTACT_POINTS=kong-postgres"
            # pg数据库名
            - "KONG_PG_DATABASE=kong"
            # pg 主机名
            - "KONG_PG_HOST=kong-postgres"
            # pg数据库
            - "KONG_DATABASE=postgres"
            # pg数据库密码
            - "KONG_PG_PASSWORD=tars2015"
            # 用户限定
            - "KONG_PG_USER=kong"
            - "KONG_PROXY_ACCESS_LOG=/dev/stdout"
            - "KONG_PROXY_ERROR_LOG=/dev/stderr"
        networks:
            - "kong-net"
        # 依赖服务
        depends_on:
            - "kong-postgres"
            - "kong-migrations"
        restart: "always"


    kong-postgres:
        image: "postgres:latest"
        container_name: "kong-postgres"
        ports:
            - "5432:5432"
        environment:
            - "POSTGRES_USER=kong"
            - "POSTGRES_DB=kong"
            - "POSTGRES_PASSWORD=tars2015"
            # 绑定pg数据目录
            - "PGDATA=/tmp/postgres_data"
        restart: "always"
        networks:
            - "kong-net"
        volumes:
            - "/c/Users/postgres_data:/tmp/postgres_data"


    kong-migrations:
        image: "kong:latest"
        container_name: "kong-migrations"
        # 这个服务就是用来执行这个命令的
        command: "kong migrations bootstrap"
        environment:
            - "KONG_CASSANDRA_CONTACT_POINTS=kong-postgres"
            - "KONG_PG_DATABASE=kong"
            - "KONG_PG_HOST=kong-postgres"
            - "KONG_DATABASE=postgres"
            - "KONG_PG_PASSWORD=tars2015"
            - "KONG_PG_USER=kong"
        depends_on:
            - "kong-postgres"
        links:
            - "kong-postgres:kong-postgres"
        networks:
            - "kong-net"
        restart: "on-failure"


    kong-dashboard:
        image: "hansonyao/kong-dashboard:latest"
        container_name: "kong-dashboard"
        restart: "always"
        ports:
            - "8084:8080"
        depends_on:
            - "kong-postgres"
            - "kong-migrations"
            - "kong"
        # 服务部署完成执行的命令，bboysoul用户名tars2015密码
        command: "start --kong-url http://kong:8001 --basic-auth bboysoul=tars2015"
        networks:
            - "kong-net"


networks:
    kong-net:
        driver: "bridge"
```

#### 启动，配置更新重启

```bash
docker-compose up -d
```

#### 关闭

```bash
docker-compose down
```

![image.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509182411.png)


看见服务状态全部为UP就说明启动正常了，如果是有启动不正常的情况出现，我们可以尝试

```bash
docker logs tars
```
来查看具体是报什么错误了，然后再具体情况具体解决


查看具体docker容器的ip可以使用如下命令

```bash
docker inspect tars | grep IPAddress
```
![image.png](https://raw.githubusercontent.com/dmf-code/picture/main/picGo/20210509182443.png)

要看具体的挂载，端口，网络的话也是使用上面的命令，不过要将grep去掉。
