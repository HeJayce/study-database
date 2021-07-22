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

如果为localhost，则只能本地登陆，所以要将localhost改为可以远程的ip

如果是指定的IP，可以用：

```sql
grant all privileges on *.* to 'root'@'[允许的ip]' identified by '[密码]' with grant option;
flush privileges; 
```

通过所有IP

```sql
grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
flush privileges;
```



## 语法

### 查看数据库

```sql
show databases;
```

### 选择数据库

```sql
use database_name;
set names utf8;
```

#### 查看表

```sql
show tables;
```





## SELECT 选取

语法：

```sql
SELECT [字段],[字段] FROM [表名];
SELECT * FROM student;
```



### SELECT DISTINST

返回唯一不同的值，可理解为不重复

与select使用方法一样



### SELECT LIMIT

用于返回规定要返回记录的数目

**注意:。MySQL 支持 LIMIT 语句来选取指定的条数数据， Oracle 可以使用 ROWNUM 来选取。**

**SQL Server / MS Access 语法**

```sql
SELECT TOP number|percent column_name(s) FROM table_name;
```

**MySQL 语法**

```sql
SELECT [column_name] FROM [table_name] LIMIT [number];
SELECT * FROM websites LIMIT 2;输出前两个
```



## SQL 通配符

* `%` 类似于`*`
* `_` 下划线表示一个字符；
* `M%` : 为能配符，正则表达式，表示的意思为模糊查询信息为 M 开头的。
* `%M%` : 表示查询包含M的所有内容。
* `%M_` : 表示查询以M在倒数第二位的所有内容。

### 正则表达式

SQL [charlist]

MySQL中使用 **REGEXP** 或 **NOT REGEXP** 运算符 (或 **RLIKE** 和 **NOT RLIKE**) 来操作正则表达式

```sql
SELECT * FROM Websites WHERE name REGEXP '^[GFs]';
```





## WHERE

用于提取满足条件的记录

```sql
SELECT [字段],[字段] FROM [表名] WHERE [字段] operator value;
```

其中文本字段需要使用单引号

### where运算符

| 运算符  |                            描述                            | 语法                                    |
| :------ | :--------------------------------------------------------: | --------------------------------------- |
| =       |                            等于                            |                                         |
| <>      | 不等于。**注释：**在 SQL 的一些版本中，该操作符可被写成 != |                                         |
| >       |                            大于                            |                                         |
| <       |                            小于                            |                                         |
| >=      |                          大于等于                          |                                         |
| <=      |                          小于等于                          |                                         |
| BETWEEN |                        在某个范围内                        | where [字段] between value1 and value2; |
| IN      |                 指定针对某个列的多个可能值                 | where sal in (5000,3000,1500);          |
| AND     |                    与，同时满足两个条件                    | where sal > 2000 and sal < 3000;        |
| OR      |                  或，满足其中一个条件的值                  |                                         |
| NOT     |                  非 满足不包含该条件的值                   | where not sal > 1500;  ==  sal>=1500    |
| is null |                          查询空值                          |                                         |
| LIKE    |                        搜索某种模式                        | where ename like 'M%';内容有M的         |

* **%** 表示多个字值，**_** 下划线表示一个字符；
*  **M%** : 为能配符，正则表达式，表示的意思为模糊查询信息为 M 开头的。
*  **%M%** : 表示查询包含M的所有内容。
*  **%M_** : 表示查询以M在倒数第二位的所有内容。



逻辑运算可以集合起来

```sql
SELECT * FROM Websites WHERE alexa > 15 AND (country='CN' OR country='USA');
```

逻辑运算的优先级：


> ()    not        and         or




但where不一定非要运算符

`where 0` 会返回一个空值

`where 1` 相当于没有这句，因为每一行记录都返回true



## ORDER BY 排序

语法：

```sql
SELECT * FROM Websites ORDER BY alexa;
```

默认按照升序排列



降序：

```sql
SELECT * FROM Websites ORDER BY alexa DESC;
```



## INSERT INTO 插入

第一种形式，无需指定列名，只需提供被插值

```sql
INSERT INTO Websites (name, url, alexa, country) VALUES ('百度','https://www.baidu.com/','4','CN');
```

**如果不指定列名，需要将values全部按顺序列出**



insert into select 和select into from 的区别

```
insert into scorebak select * from socre where neza='neza'   --插入一行,要求表scorebak 必须存在
select *  into scorebak from score  where neza='neza'  --也是插入一行,要求表scorebak 不存在
```



## UPDATE 更新

```sql
UPDATE [table_name] SET column1=value1,column2=value2,... WHERE some_column=some_value;
```



### Update 警告！

在更新记录时要格外小心！在上面的实例中，如果我们省略了 WHERE 子句，如下所示：

```
UPDATE Websites
SET alexa='5000', country='USA'
```

执行以上代码会将 Websites 表中所有数据的 alexa 改为 5000，country 改为 USA。

**！！！！执行没有 WHERE 子句的 UPDATE 要慎重，再慎重。！！！！**



## DELETE 删除

```sql
DELETE FROM [table_name] WHERE [some_column=some_value];
```

删除行


