# MYSQL

## 安装与连接

### 安装

系统：Ubuntu16.04

```
apt-get update
aptget install mysql-server
```

### 连接

需要注意的是，在安装mysql 后是不可以使用navicat等远程工具进行连接的

默认安装后，3306端口只绑定给了localhost，所以外网是无法访问的

解决方法：

```sh
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

将bind-address   = 127.0.0.1注释掉

![image-20210721175850771](Untitled.assets/image-20210721175850771.png)

重启即可

```
systemctl restart mysql 
service mysql restart
```

 接着授予权限：

登陆mysql

```sh
mysql -h ip -P 3306 -uroot -p([密码)
```



```sql
use mysql
select user,host from user
```

看root用户的host是否为localhost