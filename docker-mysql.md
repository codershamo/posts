# 使用docker运行Mysql实例

## 获取Mysql镜像

```shell
docker pull mysql
```

## 查看镜像

```shell
{17:04}~ ➭ docker images;
REPOSITORY                                          TAG                 IMAGE ID            CREATED             SIZE
mysql                                               latest              6a834f03bd02        9 days ago          484MB
```

## 运行Mysql容器

```shell
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=11 -d mysql
```

上述命令参数含义

```shell

run            运行一个容器
--name         后面是这个镜像的名称
-p 3306:3306   表示在这个容器中使用3306端口(第二个)映射到本机的端口号也为3306(第一个)
-d             表示使用守护进程运行，即服务挂在后
```

查看当前运行的容器状态：

```shell
{17:22}~ ➭ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
7cef688c5fb2        mysql               "docker-entrypoint.s…"   9 seconds ago       Up 8 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
```

## 登录Mysql实例

本地机器需要安装mysql-client

```shell
sudo apt-get install mysql-client-core-5.6
```

使用mysql命令访问服务器，密码如刚才所示为11,100.80.181.92为我这台机器的ip, 3306为刚才所示的占用本物理机的端口（不是在docker内部的端口）

```shell
{17:33}~ ➭ mysql -h100.80.181.92 -P3306 -uroot  -p11
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.12 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```

## 参考

https://blog.csdn.net/qq_39781497/article/details/78060551