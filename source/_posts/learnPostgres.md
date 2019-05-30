---
title: learnPostgres
date: 2019-05-30 13:26:15
tags: SQL
---
# 学习使用PostgresSql数据库
## 安装
我使用的阿里云的服务器，系统是Centos7
现在到Postgres的官网，选择相应的系统版本，获得相应的安装链接和命令。
![psql_install](/images/pginstall1.png)
1. 首先添加rpm源
2. 查看可以安装的postgresql版本 
> yum list | grep postgresql
![查看可安装版本](/images/psql2.png)
3. 安装 
> yum install postgresql11-contrib postgresql11-server -y
![安装过程](/images/psql3.png)
4. 查看是否安装完成 
> rpm -aq| grep postgres
![查看完成](images/psql4.png)
安装完成后Postgresql安装目录是/usr/pgsql-11 文件目录是/var/lib/pgsql/11/data
5. 初始化数据库 
> /usr/pgsql-11/bin/postgresql-11-setup initdb
![初始化](/images/psql5.png)
6. 设置自启动，并查看启动状态 
> systemctl enable postgresql-11
> systemctl start postgresql-11
> systemctl status postgresql-11
![启动状态](/images/psql6.png)
## psql入门使用
数据库安装后，会在自动创建一个Linux用户，名为postgres,自动创建一个Postgresql数据库的root用户，用户名就是postgres。
这两个是不同的，前一个是Linux用户，后一个是PostgreSQL数据库的用户。
现在切换到Linux用户->postgres中
![切换Linux用户](/images/psql7.png)
现在使用psql指令登录到PostgreSQL控制台中，在这里就可以进行数据库的操作啦，建立角色，授予权限，修改密码，创建数据库，表等等。
![进入数据库](/images/psql8.png)
好了，进入的第一件事，就是为了我们的root用户postgres设置一个密码，使用\password user给用户修改密码
> password postgres
当本机Linux用户名和PostgresSQL用户名相同时，可以使用psql命令直接进入到数据库中。
创建一个Linux用户，一个同名的PostgresSQL用户。
> 创建Linux用户
> sudo adduser canghai
> sudo passwd canghai
进入PostgresSQL用户，创建名为canghai的postgreSQL用户
> su - postgres
> psql 
> create user canghai with password '******';
创建一个测试数据库并授予全部权限给canghai这个用户
> create database test ower canghai;
> grant all privileges on database test to canghai;
切换为Linux用户，并且登录到psql中
![使用新用户登录](/images/psql9.png)
psql可以直接登录同名数据库，没有同名数据库需要psql test进入test数据库中。
也可以在任意用户下，通过用户名，密码，数据库，来登录。
> psql -U canghai -d test -h 127.0.0.1 -p 5432
![遇到了一些错误](/images/psql10.png)
在这里遇到了一些错误，是因为默认情况下，PostgresSQL使用基于IDENT的身份验证，现在我们要修改配置文件，允许通过网络提供基于用户名和密码的身份验证。
> vim /var/lib/pgsql/11/data/pg_hba.conf
![修改配置，允许本地和ipv4通过用户名密码登录](/images/psql15.png)
修改配置文件，允许远程ip访问，在这里也可以调整端口
![修改ip](/iamges/psql12.png)
重启postgresql-11
> systemctl restart postgresql-11
在阿里云控制台中开启防火墙，允许5432端口通过
![修改防火墙](/images/psql13.png)
检查防火墙是否开启&是否允许通过
> firewall-cmd --permanent --add-port=5432/tcp
> systemctl restart firewalld
现在已经可以在控制台上通过账号密码登录
![账号密码登录](/images/psql14.png)
也可以在本机上使用可视化工具通过网络连接数据库，我这里使用的是DataGrip
![可视化工具网络连接](/images/psql16.png)