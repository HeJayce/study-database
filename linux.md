# Linux

## 终端

1. 物理终端

    直接接入本机

2. 虚拟终端

    在物理终端商软件虚拟实现的终端，centos6 有默认6个虚拟终端

    Ctrl + Alt + Fn （n = 1-6）

    设备文件路径：/dev/tty#

3. 模拟终端

    图形界面打开的命令行、ssh、telnet

    设备文件路径：/dev/pts/# 无穷个

查看终端设备`tty`



## 命令提示符

`prompt`

[root@localhost ~]# 

​	[root@localhost ~]  : PS1

​		prompt:

​			管理员：#

​			普通用户：$



## 命令

输入命令，回车:

​	shell程序找到键入命令所对应的可执行程序或代码，并由其分析后提交给内核分配资源将其运行起来。表现为一个或多个进程。

在shell中可执行的命令有两类:

​	内建命令:由shell自带的，而且通过某命令形式提供;

​	外部命令:在当前系统的某文件系统路径下有对应的可执行程序文件:

​	which, whereis

区别内部或外部命令:

​		`type COMMAND`

shell 程序可搜寻的执行文件的路径定义在PATH环境变量中：

```shell
ehco $PATH	
```

![image-20211204233557973](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/image-20211204233557973.png)

自左至右依次查询，查到停止

shell搜寻代外部命令的路径结果会缓存至kv（key-value）存储中：

可用命令`hash`命令查看

![image-20211204234259452](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/image-20211204234259452.png)

**缓存副作用：**

如果命令位置发生了转移，系统还是会按缓存路径运行，导致命令失效

```sh
mv /bin/ls /usr/bin/ls
ls
# -bash: /bin/ls: NO such file or directory
```

清除缓存：

```sh
hash -d [command] #删除一个命令缓存
hash -r  #删除所有缓存
```



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



文件



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

## `man`

​	查看指令手册

`/usr/share/man` 

man1---man9

有多个man的目的是为了区分权限

​	`man1` 用户命令

​	`man2` 系统调用

​	`man3` c库调用

​	`man4` 设备及特殊文件

​	`man5` 配置文件格式

​	`man6` 游戏

​	`man7` 杂项

​	`man8` 管理类命令

有些关键字在不止一个章节中

指导章节：man # [COMMAND]

`man`命令配置文件`/etc/man.config`

​	`MANPATH  /.../.../...`

### 操作方法

Space，向文件尾翻屏: 

b，向文件首部翻屏:

d，向文件尾部翻半屏:

u，向文件首部翻半屏:

RETURN，向文件尾部翻一行

y ，向文件首部翻一行

q ，退出



## `pwd`

​	查看当前位置的绝对路径

## `ls`

​	-a 显示所有文件包括隐藏

​	-l 以列表的形式

## `mkdir`

​	创建目录

​	-p 创建多级目录

## `rm`	

​	-r 递归删除

​	-f 强制删除

## `cp`

```
cp 	1.txt /home
```

​	-r 递归复制

​	`\cp`强制覆盖复制，不需要单独确认



## `mv`

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

## `cat`

​	查看文件内容

​	-n 显示行号

​	配合 | more 按页显示文本内容

### more

* space 向下翻页
* enter 向下翻行
* q 退出
* ctrl F 向下滚一屏
* ctrl B 返回上一屏
* = 输出当前行号
* :f 输出文件名和当前行号



### less	

功能与more类似，但比more强大

less在显示文件内容时，根据显示需要进行加载内容，对大型文件有较高效率

* space 向下翻页

* enter 向下翻行
* q 退出
* pageup
* pagedown
* /字串    向下搜寻【字串】功能：n：向下查找：N：向上查找
* ?字串    向上搜寻【字串】功能：n：向上查找：N：向下查找



## echo

#### `>`和`>>`

`>`输出重定向

`>>`追加



## `history`

记录历史操作

登陆shell时，会读取命令历史文件中记录下来的命令： ~/.bash_history	

登陆进shell后，新执行的命令只会记录在缓存中

登出shell后，会被追加至记录里

* `-a `  更新历史记录文件
* `-d`  删除历史记录命令 `history -d 100`
* `-c`  清空命令历史记录

快捷操作：

`!100`  运行第100行历史记录的命令

`!string`  调用历史中最近一个以string开头的命令

`!!` 重复运行上一条命令







