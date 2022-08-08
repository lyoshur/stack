---
title: linux使用tar.gz安装mysql
description: linux使用tar.gz安装mysql
slug: linux使用tar.gz安装mysql
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---


* 使用压缩包安装mysql

安装前置依赖  
shell> sudo apt-get install libaio-dev    
添加用户以及用户组  
shell> sudo groupadd mysql    
shell> sudo useradd -r -g mysql mysql   
切换目录  
shell> sudo cd /usr/local   
解压压缩包，设置软链接  
shell> tar zxvf /home/niumd/mysql-5.5.13-linux2.6-i686.tar.gz   
shell> sudo ln -s /usr/local/mysql-5.5.13-linux2.6-i686 mysql      
更改用户权限  
shell> sudo chown -R mysql .   
shell> sudo chgrp -R mysql .   
执行安装  
shell> cd mysql
shell> sudo scripts/mysql_install_db --user=mysql      
修改用户权限  
shell> sudo chown -R root .
shell> sudo chown -R mysql data   
放置service和配置文件
shell> sudo cp support-files/mysql.server /etc/init.d/mysql.server   
shell> sudo cp support-files/my-medium.cnf /etc/my.cnf   
这里回车后会停留，直接再按回车即可  
shell> sudo bin/mysqld_safe --user=mysql &    
设置密码  
shell> sudo pwd /usr/local/mysql
shell> sudo bin/mysqladmin -u root password 'new-password'
测试  
shell> sudo bin/mysql -u root -p

* 其他命令

让mysql开机启动  
$ sudo update-rc.d -f mysql.server defaults  
取消开机启动  
$ sudo update-rc.d -f mysql.server remove  
将mysql加入软链接  
$ sudo ln -s /usr/local/mysql/bin/mysql /usr/local/bin/mysql  

设置外网访问  
use mysql;  
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'  IDENTIFIED BY 'you_password'  WITH GRANT OPTION;  
flush privileges;  
