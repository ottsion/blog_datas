---
title: "如何强制更改Docker容器中的Mysql密码"
date: 2020-06-02T10:58:24+08:00
draft: false
author: "caicai"
tags: ["docker","mysql"]
categories: ["bugs"]
---



转自：https://blog.csdn.net/fastlearn/article/details/80511661

## 解决步骤

- 增加“忘记密码启动”模式

- 重新设置root密码

- 删掉“忘记密码启动”模式

- 重启容器镜像即可

## 具体操作

- 通过docker exec命令进入容器内

```
docker exec -it db002 /bin/bash
# 注：db002是我安装mysql数据库的容器名
```

- 编辑mysql配置文件，增加“忘记密码启动”模式：skip-grant-tables

```
vi /etc/mysql/conf.d/docker.cnf
# 添加这句
skip-grant-tables 
```

最终配置文件docker.cnf内容如下：

```
[mysqld]
skip-host-cache
skip-name-resolve
skip-grant-tables
```

- 退出容器，重启容器实例

 ```
exit
 ```

- 通过步骤1再次进入容器，输入myql命令，免密码直接登录mysql

```
mysql
```

- 设置root用户新密码：a123456。刷新权限，退出mysql

```
#切换实例，user表位于mysql实例下
mysql> use mysql;
#更改root密码
mysql> UPDATE user SET Password = password ( 'a123456' ) WHERE User = 'root';
# mysql5.7使用这句：
mysql> update mysql.user set authentication_string=password('a123456') where user='root';
```

# 刷新权限
```
mysql> flush privileges;
```

# 退出
```
mysql> quit
```

6. 重新编辑mysql配置文件docker.cnf，删掉“忘记密码启动”模式

```
vi /etc/mysql/conf.d/docker.cnf
```

7. 退出容器，重启容器即可

```
docker restart db002
```

