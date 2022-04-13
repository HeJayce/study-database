# Redis

## 简介

一个key-value 存储系统，但redis支持的value类型相对很多，包括字符串（string），哈希（hash）、链表（list）、集合（set）、有序集合（zset）等，这些数据都支持push/pop、add/remove和取交集、并集和差集及更丰富的操作，这些操作都是原子性的，意思就是要么成功执行要么失败完全不执行。在此基础上，redis支持各种不同方式的排序。

![image-20220413143042223](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202204131430297.png)



redis的数据都是缓存在内存中，redis会周期性地把更新的数据写入磁盘，或者把修改操作写入追加的记录文件中，并在此基础上实现主从复制

支持数据的持久化，数据的备份

## 数据类型

### string 

- 最常规的set/get操作，一般做一些复杂的计数功能的缓存，string 类型的值最大能存储 512MB。

  ```
  SET string_a "jayce"
  GET string_a
  ```

  <img src="https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202204131454313.png" alt="image-20220413145424274" style="zoom:150%;" />

### hash 

- key-hashmap存放结构化对象，可以很方便的读取和更新某些属性，操作某个字段，每个 hash 可以存储 232 -1 键值对（40多亿）

  ```
  HMSET name first_name "jayce" last_name "he"
  ```

  ![image-20220413145757187](https://jaycehe.oss-cn-hangzhou.aliyuncs.com/markdown/202204131457232.png)

### list 

- 是一个双向链表，支持双向pop/push 可做简单的消息队列的功能，列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

  ```
  
  ```

  

### set 

- 堆放不重复的集合，可做去重功能

### zset  

- 在set基础上多个score 能按照score进行排序，zset的成员是唯一的，但分数可以重复

lua sript 脚本支持

操作

set

get

mset

mget

strlen

append

decr

setex

psetex







