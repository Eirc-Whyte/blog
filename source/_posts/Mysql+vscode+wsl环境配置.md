---
title: Mysql+vscode+wsl环境配置
date: 2019-08-17 23:12:11
tags: Mysql wsl vscode 踩坑记
---

##  0# 

安装vscode

安装wsl Ubuntu18.04（换源aliyun

安装mysql-server

## 1# 

先获得root权限

``` shell
sudo su
```

wsl中输入

```shell
service mysql start
```

若出现

```shell
MySQL 5.7 No directory, logging in with HOME=/
```

then

```shell
service mysql stop
usermod -d /var/lib/mysql/ mysql
service mysql start
```

其中 /var/lib/mysql/ 处存储的是mysql的日志文件&数据库文件

usermod修改mysql 的登入目录为上述目录

然后可能会显示：

```shell
'Access denied for user 'root'@'localhost'
```

编辑 /etc/mysql/mysql.conf.d/mysqld.cnf 或者对应的mysql配置文件，

在[mysqld]下面添加：

```
skip-grant-tables
```

再次重启mysql，

```
mysql -uroot -p
```

直接回车跳过密码

```` sql
ALTER USER 'root'@'localhost' IDENTIFIED BY mysql_native_password 'password';
flush privileges;
````

重启

```
service mysql restart
```

## 2# 

vscode安装mysql插件

![1568863461052](C:\Users\Eric\AppData\Roaming\Typora\typora-user-images\1568863461052.png)

选择

![1568863484764](C:\Users\Eric\AppData\Roaming\Typora\typora-user-images\1568863484764.png)

添加connection

![1568863508590](C:\Users\Eric\AppData\Roaming\Typora\typora-user-images\1568863508590.png)

输入password之后connect

即可成功

## 3#  

常用的mssql插件无法连接wsl 的mysql不知道为啥

反正就是连不上233