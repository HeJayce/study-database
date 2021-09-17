# MYSQL

## 安装与连接

### 安装

系统：Ubuntu16.04

```
apt-get update
apt-get install mysql-server
```

### 连接

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



## CREATE TABLE 创建表

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



## SQL 别名

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



## SQL 连接（JOIN）

sql join用于将两个表结合起来

下图展示了 LEFT JOIN、RIGHT JOIN、INNER JOIN、OUTER JOIN 相关的 7 种用法。

[![img](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525432.png)](https://www.runoob.com/wp-content/uploads/2019/01/sql-join.png)



### INNER JOIN

在表中存在至少一个匹配

```sql
SELECT column_name FROM table1 INNER JOIN table2 ON table1.column_name=table2.column_name;
SELECT column_name FROM table1 JOIN table2 ON table1.column_name=table2.column_name;
```

INNER JOIN 与JOIN相同

![image-20210723094810785](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525690.png)





### LEFT JOIN

从左表返回所有的行，如果右表没有匹配，结果为NULL

![image-20210725182757047](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171525903.png)

### RIGHT JOIN

从右表返回所有的行，如果左表没有匹配，结果为NULL

### FULL OUTER JOIN

只要左右表中存在一个表匹配，则返回行

FULL OUTER JOIN 关键字结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

```sql
SELECT column_name(s) FROM table1 FULL OUTER JOIN table2 ON table1.column_name=table2.column_name
```



## SQL UNION

`UNION`操作符**合并**两个以上`SELECT`语句

请注意，UNION 内部的每个 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每个 SELECT语句中的列的顺序必须相同。



## INSERT INTO SELECT

INSERT INTO SELECT 语句从一个表复制数据，然后把数据插入到一个已存在的表中。目标表中任何已存在的行都不会受影响。

复制希望的列插入到另一个已存在的表中

```sql
INSERT INTO table2 (column_name(s)) SELECT column_name(s) FROM table1;
INSERT INTO Websites (name, country) SELECT app_name, country FROM apps;
```







