---
title: "Ubuntu_20.04 Startup"
date: 2022-04-21T17:29:54+08:00
draft:  false
tags: ["ubuntu"]
categories: ["chtwork"]
---

# NAT設定(in virtualbox)
```
map 127.0.0.1:22 to 10.0.2.15:22   
map 127.0.0.1:3306 to 10.0.2.15:3306   
map 127.0.0.1:8080 to 10.0.2.15:8080
```

# sshd
* 確認sshd是否正常   
systemctl status ssh.service
* 查看是否有啟用sshd   
systemctl list-units | grep ssh
* 可以到下列位置查看services的寫法   
/etc/systemd/system/multi-user.target.wants

# 安裝openjdk8 (下載完copy到/opt底下解開)
* 下載openjdk並解壓縮至/opt底下
```
update-alternatives --install /usr/bin/java java /opt/jdk8u302-b08/bin/java 100
update-alternatives --install /usr/bin/javac javac /opt/jdk8u302-b08/bin/javac 100
update-alternatives --install /usr/bin/jar jar /opt/jdk8u302-b08/bin/jar 100 
```

# 安裝mysql 8.0 on ubuntu
```
sudo apt install mysql-server
sudo mysql_secure_installation #設定password policy及root密碼(但好像不會生效，root在本機仍可以blank password登入)

登入並將root權限提高
mysql -u root -p
mysql> grant all privileges on *.* to 'root'@'localhost';
mysql> flush privileges;

修改root密碼(需要密碼才能登入)
mysql>  alter user 'root'@'localhost' identified with mysql_native_password by '<password>';

修改user的host權限(%=任意IP位址)
mysql> update mysql.user set host='%' where user='root';

要能夠remote access(透過mysql workbench)
編輯 sudo vi /etc/mysql/mysql.conf.d/mysqld.conf下面這行
  bind-address            = 0.0.0.0

把db資料倒進mysql
如果dump來源是5.7版，須個別dump每個database
編輯sql檔在最上方加上下列指令再執行匯入
CREATE DATABASE IF NOT EXISTS <database name>;
USE <database name>;

匯入dumped sql files
mysql -u root -p<password> < airkids.sql mysql -u root -p<password> < curation.sql

創建遠端super user帳號
mysql> create user 'hddadmin'@'%' identified by '<password>'; 
mysql> grant all privileges on *.* to 'hddadmin'@'%';
 mysql> flush privileges;

創建airkids帳號
mysql> create user 'airkids'@'%' identified by '<password>'; 
mysql> grant all privileges on airkids.* to 'airkids'@'%';
mysql> flush privileges;
```

# 安裝Glassfish4
```
1. 直接把備份解tar開來，並把下列資料夾清空
glassfish4/glassfish/domain/sekoan/application
glassfish4/glassfish/domain/sekoan/autodeploy
執行sudo glassfish4/glassfish/bin/asadmin start-domain  sekoan指令

2. 遇到第一個錯誤訊息如下：
"Client does not support authentication protocol requested by server; consider upgrading MySQL client"
上stackoverflow查完 發現是mysql帳號的plugin不支援
執行下面指令即可以解決
mysql> alter user 'airkids'@'%' identified with mysql_native_password by '<password>';

3. 重啟glassfish後，遇到第二個錯誤訊息：
"java.sql.SQLException: Unknown initial character set index '255' received from server. Initial client character set can be forced via the 'characterEncoding' property."
只要在/etc/mysql/mysql.conf.d/mysqld.conf裡加上 character_set_server = utf8
再重啟mysqld 就可以解決了(utf8 = utf8mb3)
因為mysql8.0之後預設改成utf8mb4了
  
4. 再重啟後，發現連接到127.0.0.1:8080/airkids-backend出現timeout
一直被導到172.16.74.81:8080/security/login/去
才發現glassfish4/glassfish/domain/sekoan/config/domain.xml裡
寫死了172.16.74.81為host IP => 修改成127.0.0.1就好囉!
```

# systemctl service for Glassfish
sudo vim /etc/systemd/system/glassfish.service

```
[Unit]
Description=GlassFish Server v4.1
After=syslog.target network.target

[Service]
User=root
Type=forking
ExecStart=/home/chiakie/glassfish4/bin/asadmin start-domain sekoan
ExecStop=/home/chiakie/glassfish4/bin/asadmin stop-domain sekoan

[Install]
WantedBy=multi-user.target
```
sudo systemctl daemon-reload   
sudo systemctl start glassfish.service   
sudo systemctl enable glassfish.service   
sudo systemctl status glassfish.service   
sudo systemctl stop glassfish.service   

