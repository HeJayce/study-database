# Linux

## 连接

### linux&macos

```sh
ssh username@ip 
```

-p：指定端口

使用私钥登陆：

```
ssh -i ~/.ssh/jayce.pem username@ip
```





## 关机与重启

### `shutdown`

立即关机：

```sh
shutdown -h now
```

延迟一分钟关机

```sh
shutdown -h 1
```



### `halt`

直接关机



### `reboot`

重启系统



### `sync`

将内存的数据同步到磁盘

作用：关机和重启之前执行。放止数据丢失



## 登陆和注销

登陆少用root账号登陆，可避免操作失误。

可使用普通用户登陆，再用`su -用户名`切换系统管理员身份

### `logout`

注销

logout在图形运行级别无效，在运行级别3下有效



## 运行级别

linux有7个运行级别

0：关机

1：单用户（可找回密码，不需要密码）

2：多用户无网络

3：多用户有网络

4：保留

5：图形界面

6：重启

常用的是3和5

运行级别配置文件：/etc/inittab  (centos7以前)

在配置文件中修改运行级别

### 切换指定运行级别

基本语法

init [012356]





## 用户管理

### 用户组

系统可以对有共性的多个用户进行统一的管理

每个用户至少属于一个组

比如root属于root组

### 增加删除组

`groupadd`



`groupdel`





### 家目录

home目录下有各个用户的家目录

当用户登陆时，会自动进入自己的家目录

``/home/jayce`



### 添加用户

`useradd`

```shell
useradd -d /home/jayce -m jayce
```

-d ： 指定用户目录

-m ： 如果目录为空，则新建

-g：指定用户组

`passwd`

设置密码

```shell
passwd jayce
```

在创建好用户后要设置密码



### 删除用户

`userdel`

默认加用户名是不会删除其所有文件的

-f ：强制删除，即时已经登陆

-r：连同相关用户文件一起删除

**另一种方法：**

进入`/etc/passwd`中，删除用户记录

⚠️不安全



### 修改用户的组

`usermod`

```sh
usermod -g usergroup username
```



### 查看用户

`id username`



### 切换用户

`su - username`

从高权限到低权限不需要密码

返回root用`exit`

#### 查看当前用户

`who am i` = `whoami`



### 用户信息存放

* 用户配置文件：/etc/passwd
    * ![image-20210606010043121](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202109171526648.png)
    * 用户名:密码(加密在shadow):用户id:组id::家目录:对应的shell

* 组配置文件：/etc/group
    * 组名:口令:组id:列表(看不到)

* 口令配置文件：/etc/shadow
    * 加密文件存放密码



## 常用指令

### `man`

​	查看指令手册

### `pwd`

​	查看当前位置的绝对路径

### `ls`

​	-a 显示所有文件包括隐藏

​	-l 以列表的形式

### `mkdir`

​	创建目录

​	-p 创建多级目录

### `rm`	

​	-r 递归删除

​	-f 强制删除

### `cp`

```
cp 	1.txt /home
```

​	-r 递归复制

​	`\cp`强制覆盖复制，不需要单独确认



### `mv`

​	移动或重命名

​	移动：	

```sh
mv /home/pig.txt /root
```

​	重命名：

```sh
mv oldname newname
```

​	

### `cat`

​	查看文件内容

​	-n 显示行号

​	配合 | more 按页显示文本内容

#### more

* space 向下翻页
* enter 向下翻行
* q 退出
* ctrl F 向下滚一屏
* ctrl B 返回上一屏
* = 输出当前行号
* :f 输出文件名和当前行号



#### less	

功能与more类似，但比more强大

less在显示文件内容时，根据显示需要进行加载内容，对大型文件有较高效率

* space 向下翻页

* enter 向下翻行
* q 退出
* pageup
* pagedown
* /字串    向下搜寻【字串】功能：n：向下查找：N：向上查找
* ?字串    向上搜寻【字串】功能：n：向上查找：N：向下查找



#### echo

​	

#### `>`和`>>`

`>`输出重定向

`>>`追加









