# Mysql

[TOC]



## 安装与连接

### 安装

#### ubuntu

系统：Ubuntu16.04

```
apt-get update
apt-get install mysql-server
```

需要注意的是，在安装mysql 后是不可以使用navicat等远程工具进行连接的

默认安装后，3306端口只绑定给了localhost，所以外网是无法访问的

解决方法：

```sh
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

将bind-address   = 127.0.0.1注释掉

![image-20210721175850771](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525629.png)

重启即可

```
systemctl restart mysql 
service mysql restart
```

 接着授予权限：

#### centos

系统：centos7

下载mysql5.7的本地仓库文件

```sh
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

将仓库信息添加至yum

```shell
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

接着在通过yum进行安装

```shell
yum -y install mysql-community-server --nogpgcheck
```

此时mysql会取代mariadb

启动mysql服务

```sh
systemctl start mysqld
systemctl status mysqld
```

此时的登陆密码存放在文件里，查看密码，用该初始化密码登录

```
grep "A temporary password is generated" /var/log/mysqld.log|awk '{print $11}'
```

![image-20220309150733851](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202203091507458.png)

使用命令登录数据库后，修改该默认密码，否则无法使用

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '$Password';
```

其中，新密码必须使用大写，小写，数字和特殊字符组成

开启远程登录

```sql
grant all privileges on *.* to 'root'@'%' identified by 'password' with grant option;
flush privileges; 
```

其中%代表所有ip，password替换为数据库密码。



### 连接

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



## 用户管理

### 新建用户

```sql
creat user 'jayce'@'host' identified by 'password'
```

### 修改密码

```sql
alter user 'jayce'@'host' identified by 'password'
```

### 权限管理

#### 查看用户权限

```
show grants for 'root'@'localhost';
```

#### 授予权限

```sql
grant privileges on databasename.table to 'username'@'host';
```

```sql
revoke privileges on databasename.table to 'username'@'host';
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



### CREATE TABLE 创建表

```sql
CREATE TABLE table_name
(
column_name1 data_type(size),
column_name2 data_type(size),
column_name3 data_type(size)
);

CREATE TABLE Persons
(
PersonID int,
LastName varchar(255),
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
```

可使用 INSERT INTO 语句向空表写入数据。

### 约束

在创建表的时候用于规定数据规则，比如NOT NULL

在 SQL 中，我们有如下约束：

#### NOT NULL

* 指示某列不能存储 NULL 值。在默认的情况下，表的列接受 NULL 值。

#### UNIQUE 

* 保证某列的每行必须有唯一的值。比如ID等值需要唯一性

* 语法：
```sql
 /*MySQL*/
 CREATE TABLE Persons
 (
 P_Id int NOT NULL,
 Name varchar(255) NOT NULL,
 UNIQUE (P_Id)
 );
 /*SQL Server / Oracle / MS Access*/
 CREATE TABLE Persons
 (
 P_Id int NOT NULL UNIQUE,
 Name varchar(255) NOT NULL,
 );
```

* 定义多个列的 UNIQUE 约束：

```sql
 /*MySQL / SQL Server / Oracle / MS Access：*/
 CREATE TABLE Persons
 (
 P_Id int NOT NULL,
 Name varchar(255) NOT NULL,
 CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName)
 );
```

* 当表已被创建时，如需在 "P_Id" 列创建 UNIQUE 约束，请使用下面的 SQL：

```sql
  ALTER TABLE Persons ADD UNIQUE (P_Id);
```
* 定义多个列的 UNIQUE 约束:

```sql
ALTER TABLE Persons ADD CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName);
```

* **撤销UNIQUE约束**


```sql
/*MySQL*/
ALTER TABLE Persons
DROP INDEX uc_PersonID
/*SQL Server / Oracle / MS Access*/
ALTER TABLE Persons
DROP CONSTRAINT uc_PersonID
```

  

#### PRIMARY KEY

* NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。

* **主键必须包含唯一的值。**

    **主键列不能包含 NULL 值。**

    **每个表都应该有一个主键**，**并且每个表只能有一个主键**。

    ```sql
    /*MySQL*/
    CREATE TABLE Persons
    (
    P_Id int NOT NULL,
    Name varchar(255) NOT NULL,
    PRIMARY KEY (P_Id)
    )
    
    /*SQL Server / Oracle / MS Access：*/
    CREATE TABLE Persons
    (
    P_Id int NOT NULL PRIMARY KEY,
    Name varchar(255) NOT NULL,
    )
    ```

* **ALTER TABLE 时的 SQL PRIMARY KEY 约束**

    当表已被创建时，如需在 "P_Id" 列创建 PRIMARY KEY 约束，请使用下面的 SQL：

    ```sql
    /*MySQL / SQL Server / Oracle / MS Access*/
    ALTER TABLE Persons ADD PRIMARY KEY (P_Id)
    ```

    如需命名 PRIMARY KEY 约束，并定义多个列的 PRIMARY KEY 约束，请使用下面的 SQL 语法：

    ```sql
    /*MySQL / SQL Server / Oracle / MS Access*/
    ALTER TABLE Persons ADD CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
    ```

    **注释：**如果您使用 ALTER TABLE 语句添加主键，必须把主键列声明为不包含 NULL 值（在表首次创建时）。

    ------

    **撤销 PRIMARY KEY 约束**

    如需撤销 PRIMARY KEY 约束，请使用下面的 SQL：

    ```sql
    /*MySQL*/
    ALTER TABLE Persons DROP PRIMARY KEY
    /*SQL Server / Oracle / MS Access*/
    ALTER TABLE Persons DROP CONSTRAINT pk_PersonID
    ALTER TABLE Persons DROP CONSTRAINT P_Id
    ```
    
    

#### FOREIGN KEY

* 一个表中的 FOREIGN KEY 指向另一个表中的 UNIQUE KEY(唯一约束的键)。保证一个表中的数据匹配另一个表中的值的参照完整性。

* 外键的解释：一个表中的某一列为外键，与另一个表的唯一约束键（UNIQUE KEY）

* FOREIGN KEY 约束用于预防破坏表之间连接的行为。

    FOREIGN KEY 约束也能防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一

    

    ```sql
    /*MySQL*/
    CREATE TABLE Orders
    (
    O_Id int NOT NULL,
    OrderNo int NOT NULL,
    P_Id int,
    PRIMARY KEY (O_Id),
    FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
    )
    
    /*SQL Server / Oracle / MS Access*/
    CREATE TABLE Orders
    (
    O_Id int NOT NULL PRIMARY KEY,
    OrderNo int NOT NULL,
    P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
    )
    ```

    

#### CHECK

保证列中的值符合指定的条件。

#### DEFAULT

规定没有给列赋值时的默认值。





### SELECT 选取

当数据量大的时候，数据量会刷屏，使用pager less 可以切换less模式，除了pager less，还有pager more，pager awk、pager wc -l（统计行数）等，直接使用查询，可生效

语法：

```sql
SELECT [字段],[字段] FROM [表名];
SELECT * FROM student;
```



#### SELECT DISTINST

返回唯一不同的值，可理解为不重复

与select使用方法一样





#### SELECT LIMIT

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



### SQL 通配符

* `%` 类似于`*`
* `_` 下划线表示一个字符；
* `M%` : 为能配符，正则表达式，表示的意思为模糊查询信息为 M 开头的。
* `%M%` : 表示查询包含M的所有内容。
* `%M_` : 表示查询以M在倒数第二位的所有内容。

#### 正则表达式

SQL [charlist]

MySQL中使用 **REGEXP** 或 **NOT REGEXP** 运算符 (或 **RLIKE** 和 **NOT RLIKE**) 来操作正则表达式

```sql
SELECT * FROM Websites WHERE name REGEXP '^[GFs]';
```





### WHERE

用于提取满足条件的记录

```sql
SELECT [字段],[字段] FROM [表名] WHERE [字段] operator value;
```

其中文本字段需要使用单引号

#### where运算符

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



### ORDER BY 排序

语法：

```sql
SELECT * FROM Websites ORDER BY alexa;
```

默认按照升序排列



降序：

```sql
SELECT * FROM Websites ORDER BY alexa DESC;
```



### INSERT INTO 插入

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



### UPDATE 更新

```sql
UPDATE [table_name] SET column1=value1,column2=value2,... WHERE some_column=some_value;
```



#### Update 警告！

在更新记录时要格外小心！在上面的实例中，如果我们省略了 WHERE 子句，如下所示：

```
UPDATE Websites
SET alexa='5000', country='USA'
```

执行以上代码会将 Websites 表中所有数据的 alexa 改为 5000，country 改为 USA。

**！！！！执行没有 WHERE 子句的 UPDATE 要慎重，再慎重。！！！！**



### DELETE 删除

```sql
DELETE FROM [table_name] WHERE [some_column=some_value];
```

删除行



### SQL 别名

将表名称或列名称指定别名，创建别名让列名称可读性更强

```SQL
SELECT column_name AS alias_name FROM table_name;
SELECT column_name FROM table_name AS alias_name;
```



将多列结合一起：

```sql
SELECT name, CONCAT(url, ', ', alexa, ', ', country) AS site_info FROM websites;
```

对表使用别名：

```sql
SELECT w.name FROM websites as w WHERE w.name='Google';
```

```sql
SELECT w.name, w.url, a.count, a.date FROM Websites AS w, access_log AS a WHERE a.site_id=w.id and w.name="菜鸟教程";
```

在下面的情况下，使用别名很有用：

* 在查询中涉及超过一个表
* 在查询中使用了函数
* 列名称很长或者可读性差
* 需要把两个列或者多个列结合在一起



### SQL 连接（JOIN）

sql join用于将两个表结合起来

下图展示了 LEFT JOIN、RIGHT JOIN、INNER JOIN、OUTER JOIN 相关的 7 种用法。

[![img](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525432.png)](https://www.runoob.com/wp-content/uploads/2019/01/sql-join.png)



#### INNER JOIN

在表中存在至少一个匹配

```sql
SELECT column_name FROM table1 INNER JOIN table2 ON table1.column_name=table2.column_name;
SELECT column_name FROM table1 JOIN table2 ON table1.column_name=table2.column_name;
```

INNER JOIN 与JOIN相同

![image-20210723094810785](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525690.png)





#### LEFT JOIN

从左表返回所有的行，如果右表没有匹配，结果为NULL

![image-20210725182757047](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525903.png)

#### RIGHT JOIN

从右表返回所有的行，如果左表没有匹配，结果为NULL

#### FULL OUTER JOIN

只要左右表中存在一个表匹配，则返回行

FULL OUTER JOIN 关键字结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

```sql
SELECT column_name(s) FROM table1 FULL OUTER JOIN table2 ON table1.column_name=table2.column_name
```



#### SQL UNION

`UNION`操作符**合并**两个以上`SELECT`语句

请注意，UNION 内部的每个 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每个 SELECT语句中的列的顺序必须相同。



### NULL值处理

- **IS NULL:** 当列的值是 NULL,此运算符返回 true。
- **IS NOT NULL:** 当列的值不为 NULL, 运算符返回 true
- **<=>:** 比较操作符（不同于 = 运算符），当比较的的两个值相等或者都为 NULL 时返回 true。**只比较NULL**

**不能使用 = NULL 或 != NULL 在列中查找 NULL 值 **

**注意：**

```
select * , columnName1+ifnull(columnName2,0) from tableName;
```

columnName1，columnName2 为 int 型，当 columnName2 中，有值为 null 时，columnName1+columnName2=null，

 **ifnull(columnName2,0) 把 columnName2 中 null 值转为 0。**





### INSERT INTO SELECT

INSERT INTO SELECT 语句从一个表复制数据，然后把数据插入到一个已存在的表中。目标表中任何已存在的行都不会受影响。

复制希望的列插入到另一个已存在的表中

```sql
INSERT INTO table2 (column_name(s)) SELECT column_name(s) FROM table1;
INSERT INTO Websites (name, country) SELECT app_name, country FROM apps;
```



## 数据导入导出

### mysqldump

| 参数名                          | 缩写 | 含义                          |
| :------------------------------ | :--- | :---------------------------- |
| --host                          | -h   | 服务器IP地址                  |
| --port                          | -P   | 服务器端口号                  |
| --user                          | -u   | MySQL 用户名                  |
| --pasword                       | -p   | MySQL 密码                    |
| --databases                     |      | 指定要备份的数据库            |
| --all-databases                 |      | 备份mysql服务器上的所有数据库 |
| --compact                       |      | 压缩模式，产生更少的输出      |
| --comments                      |      | 添加注释信息                  |
| --complete-insert               |      | 输出完成的插入语句            |
| --lock-tables                   |      | 备份前，锁定所有数据库表      |
| --no-create-db/--no-create-info |      | 禁止生成创建数据库语句        |
| --force                         |      | 当出现错误时仍然继续备份操作  |
| --default-character-set         |      | 指定默认字符集                |
| --add-locks                     |      | 备份数据库表时锁定数据库表    |
| --single-transaction            | 必加 | 备份时不影响数据库运行        |

备份所有数据库：

```shell
mysqldump -uroot -p --all-databases >/all.db
```





### outfile & infile

#### outfile 

语法：

```sql
select * from table into outfile '/dic/dic/file' fields terminated by '|'
```

`fields terminated by '|'`代表以竖线分隔，默认为空格分隔

#### infile

语法：

```sql
load data infile '/dic/dic/file' into table database_name. tablename fields terminated by '|'
```





## 正则表达式

MySQL中使用 REGEXP 操作符来进行正则表达式匹配。正则表达式不区分大小写，如果需要区分，则加上 BINARY 关键字 

```sql
SELECT * FROM table WHERE name REGEXP (BINARY)  'jayce';
```



常规的三个：（如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。）

`^`	匹配输入字符串的开始位置。

`$`	匹配输入字符串的结束位置。

`*`	匹配 0 次或多次。

```sql
-- 查找name字段中以'st'为开头的所有数据：
SELECT name FROM person_tbl WHERE name REGEXP '^st';
-- 查找name字段中以'ok'为结尾的所有数据：
SELECT name FROM person_tbl WHERE name REGEXP 'ok$';
-- 查找name字段中包含'mar'字符串的所有数据：
SELECT name FROM person_tbl WHERE name REGEXP 'mar';
-- 查找name字段中以'mar' 结尾并以任何前缀开头
SELECT name FROM person_tbl WHERE name REGEXP '.*mar';
```

星号可用于与字符匹配 0 次或多次。例如，`REGEXP '.*abc'` 匹配的字符串以 abc 结尾并以任何前缀开头。因此，aabc、xyzabc 和 abc 匹配，但 bc 和 abcc 则不匹配。



.	匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用像 '[.\n]' 的模式

例如：

```sql
SELECT name FROM person_tbl WHERE name REGEXP '.*mar';
SELECT name FROM person_tbl WHERE name REGEXP '[*mar]';
```

但是不可以写成：`'*mar*'`



`+`	匹配前面的子表达式**一次或多次**。

> 加号可用于与字符匹配 1 次或多次。例如，`'bre+'` 匹配 bre 和 bree，但不匹配 br。

![image-20220328170923052](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202203281709030.png)

意思就是刚好匹配还不行，必须前面多一个

| 元字符 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| \*     | 匹配它前面的零个或多个子表达式，0个或多个匹配，效果与{0,}—致 |
| \+     | 匹配它前面的一个或多个子表达式，1个或多个匹配，效果与{1,}一致 |
| ?      | 匹配它前面的零个或一个子表达式，1个或0匹配，效果与{0,1}一致  |
| {n}    | 等于n个匹配数目                                              |
| {n,}   | 大于等于n个匹配                                              |
| {n,m}  | 大于等于n小于等于m个, m<255                                  |



`[] `字符集合。匹配所包含的任意一个字符

```sql
SELECT * FROM runoob_test_tbl WHERE runoob_author REGEXP '[OB]';
```

![image-20220328165035427](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202203281650265.png)

`[^...] `  负值字符集合。匹配未包含的任意字符。

```sql
SELECT * FROM runoob_test_tbl WHERE runoob_author REGEXP '[^RUNOOB]';
```

结果：

![image-20220328165604954](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202203281656914.png)

结果发现RUNOOB并没有出现，原因是`[^RUNOOB]`过滤掉了`RUNOOB`



`p1|p2|p3`	匹配 p1 或 p2 或 p3。例如，'z|food' 能匹配 "z" 或 "food"。'(z|f)ood' 则匹配 "zood" 或 "food"。



| 模式       | 描述（具体匹配什么）                                         |
| ---------- | ------------------------------------------------------------ |
| .          | 匹配除 "\n" 之外的任何单个字符。                             |
| [abc]      | 匹配方括号之间列出的任何字符。                               |
| [^abc]     | 匹配方括号之间未列出的任何字符。                             |
| [A-Z]      | 匹配任何大写字母。                                           |
| [a-z]      | 匹配任何小写字母。                                           |
| [0-9]      | 匹配从0到9的任何数字。                                       |
| [[:<:]]    | 匹配单词的开头。                                             |
| [[:>:]]    | 匹配单词的结尾。                                             |
| [:class:]  | 匹配字符类，即[：alpha：]匹配字母，[：space：]匹配空格，[：punct：]匹 |
| 配标点符号 | ，[：upper：] 匹配上层字母。                                 |

### 正则表达式测试

可以在不使用数据库表的情况下用 SELECT 语句来测试正则表达式，REGEXP 检查总是**返回0（没有匹配）或1（匹配）**。可以用带文字串的 REGEXP 来测试表达式，并试验它们。

```sql
SELECT 'Hern' REGEXP '[0-9]';
```

![image-20220328164007959](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202203281640805.png)



