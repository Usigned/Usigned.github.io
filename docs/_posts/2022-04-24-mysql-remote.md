---
title: mysql配置远程连接
tages: [数据库]
---

mysql配置远程访问

⚠️不要在公网内配置，以防被攻击（亲身教训）。

## 内容

1. 创建可以远程访问的账号，并赋予权限
2. 修改mysql启动配置

3. (tips)mysql安装后无法获取root密码解决方案

## 准备远程账号

mysql中账号包含用户名和host两部分，用户只能从指定的host访问。为了能够远程访问mysql，首先需要创建一个能够远程访问的账号。

### 创建

使用create user创建用户，使用@符号指定host，不指定默认从任何地方访问。

```sql
# 只能从127.0.0.1(localhost)访问
create user qing@127.0.0.1 = "your password";

# 能从任何host访问
create user qing = "123456";
```

### 赋予权限

给创建的user赋予权限，使用`on`指定生效的database.table，\*\.*来表示所有数据库中的所有表。

```sql
# 给予qing对于所有数据库、所有表的所有权限
grant all on *.* to qing;
```

## 修改mysql启动配置

Mysql8.0版本下默认只监听本地端口，需要修改mysqld配置文件。

```mysql
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

这是一个只读文件，修改需要使用root权限。进入文件后，将下面两行注释掉，或者修改为自己需要的host（注释掉默认监听0.0.0.0）。

```
# bind-address		= 127.0.0.1
# mysqlx-bind-address	= 127.0.0.1
```

修改完成后重启mysql服务

```shell
service mysql restart
```

## mysql安装后无法获取root密码解决方案

问题：使用apt-get安装mysql-server时，没有提示设置root密码。安装完后无法使用root登陆。

解决方法：

1. 去`/etc/mysql/debian.conf`中获取用户名和密码

   ```sh
   > sudo cat /etc/mysql/debian.conf
   # Automatically generated for Debian scripts. DO NOT TOUCH!
   [client]
   host     = localhost
   user     = debian-sys-maint
   password = 4DUNmomQLH1UCp37
   socket   = /var/run/mysqld/mysqld.sock
   [mysql_upgrade]
   host     = localhost
   user     = debian-sys-maint
   password = 4DUNmomQLH1UCp37
   socket   = /var/run/mysqld/mysqld.sock
   ```

2. 使用该用户名和密码登陆

   ```shell
   mysql -u debian-sys-maint -p
   ```

3. 修改root的密码

   - 使用下面的方法修改root的密码会报warning且无法成功修改（可能是由于root初始密码为空导致的，在修改完密码后再次使用下面命令修改密码则无异常）

   ```sql
   set password for root = '1234';
   ```

   - 使用下面方法修改可以成功

   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY '1234';
   ```

   

