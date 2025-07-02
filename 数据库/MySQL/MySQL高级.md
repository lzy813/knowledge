# 三、高级篇

## 1、日志

### 1.1 错误日志

- 错误日志是MySQL中最重要的日志之一，它记录了当mysqld启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，建议首先查看此日志
- 该日志是默认开启的，默认存放目录/var/log/，默认的日志文件名mysqld.log。该日志位置

~~~mysql
show variables like '%log_error%';
~~~

- 可以通过以下方式进行错误日志查看，并且定位到出错位置

![错误日志的存放位置](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\错误日志的存放位置.png)

~~~bash
# 查看错误日志位置
show variables like '%log_error%';

# 查看该日志文件下的50行日志
tail -50 /var/log/mysqld.log

# 对日志进行实时监听，如果出现错误，那么日志文件会刷新并输出错误信息，我们便可根据此来定位错误
tail -f /var/log/mysqld.log
~~~



### 1.2 二进制日志

- 二进制日志（BINLOG）记录了所有的DDL（数据定义语言）语句和DML（数据操纵语言）语句，但不包括数据查询（select、show）语句
- 作用
  - 灾难时的数据恢复
  - MySQL的主从复制。在MySQL8版本中，默认二进制日志是开启的，涉及到的参数如下

~~~sql
show variables like '%log_bin%';
-- log_bin：二进制日志默认开启
-- log_bin_basename：二进制日志存放目录及前缀，默认从000000开始自增
-- log_bin_index：二进制日志文件索引文件
~~~

![二进制日志参数](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\二进制日志参数.png)

- 日志格式

| 日志格式  |                             含义                             |
| :-------: | :----------------------------------------------------------: |
| statement | 基于SQL语句的日志记录，记录的是SQL语句，对数据进行修改的SQL都会记录在此日志文件中 |
|    row    |       基于行的日志记录，记录的是每一行的数据变更(默认)       |
|   mixed   | 混合了statement和row两种格式，默认采用了statement，在某些特殊情况下会自动切换为row进行记录 |

~~~sql
-- 当前数据库所采用的日志格式
show variables like '%binlog_format%';

-- 修改数据库采用的日志格式
set session binlog_format=row;
-- 或者
vim /etc/my.cnf
-- 插入一行，保存后重启mysql
binlog_format=row;

-- 日志查看
/*
* 由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具mysqlbinlog来查看，具体语法
*/
mysqlbinlog [参数选项] logfilename;
-- -d：指定数据库名称，只列出指定的数据库相关操作
-- -o：忽略掉日志中的前n行命令
-- -v：将行事件（数据变更）重构为SQL语句
-- -w：将行事件（数据变更）重构为SQL语句，并输出注释信息
~~~

- 日志删除：对于比较繁忙的业务系统，每天生成的binlog数据巨大，如果长时间不清理，将会占用大量的磁盘空间，可以通过以下几种方式清理日志

|                       指令                       |                             含义                             |
| :----------------------------------------------: | :----------------------------------------------------------: |
|                   reset master                   | 删除全部binlog日志，删除之后，日志编号，将从binlog.000001重新开始 |
|       purge master logs to 'binlog.******'       |                 删除******编号之前的所有日志                 |
| purge master logs before 'yyyy-mm-dd hh24:mi:ss' |     删除日志为“yyyy-mm-dd hh24:mi:ss”之前产生的所有日志      |

~~~mysql
# 删除binlog.000002之前的所有日志
purge master logs to 'binlog.000002';

# 删除所有日志
reset master;

-- 设置二进制日志的过期时间，时间过期后会自动删除，默认30天
show variables like '%binlog_expire_logs_seconds%';
~~~



### 1.3 查询日志

- 查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。
- 默认情况下，查询日志是未开启的。需要开启的话，可以通过以下代码进行查看

~~~sql
-- 配置日志
show variables like '%general%';
~~~

- 通过以下方式修改查询日志

~~~bash
# 修改MySQL的配置文件/etc/my.cnf文件，添加如下内容
general_log=1
# 该选项是用来开启查询日志的，1代表开启，0代表关闭
general_log_file=mysql_query.log
# 设置日志的文件名，如果没有指定，默认的文件名为host_name.log
~~~



### 1.4 慢查询日志

- 慢查询日志记录了所有执行时间超过参数long_query_time设置值并且扫描记录数不小于min_examined_row_limit的所有的SQL语句的日志，默认未开启。long_query_time默认为10秒，最小为0，精度可以到微秒

~~~bash
# /etc/my.cnf里面开启，慢查询日志，1为开启慢查询日志
slow_query_log=1
# 执行时间参数
long_query_time=2
~~~

- 默认情况下，不会记录管理语句，也不会记录不适用索引进行查找的查询。可以使用如下两个参数进行修改

~~~bash
# 记录执行较慢的管理语句
log_slow_admin_statements=1
# 记录执行较慢的未使用索引的语句
log_queries_not_using_indexes=1
~~~





## 2、主从复制

### 2.1 概述

- 主从复制是指将主数据库的DDL（数据定义语言）语句和DML（数据操纵语言）语句操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步
- MySQL支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制
- 好处
  - 主库出现问题，可以快速切换到从库提供服务
  - 实现读写分离，降低主库的访问压力
  - 可以在从库中执行备份，以避免备份期间影响主库服务

![主从复制](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\主从复制.png)



### 2.2 原理

![主从复制原理](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\主从复制原理.png)

- Master主库在事务提交时，会把数据变更记录在二进制日志文件binlog中
- 从库读取主库的二进制日志文件binlog，写入到从库的中继日志Relay log中
- slave重做中继日志中的事件，将改变反映它自己的数据



### 2.3 搭建

#### 2.3.1 服务器准备

- 准备好两台服务器，并都在上面安装好mysql，并把端口设置好

![主从复制服务器准备](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\主从复制服务器准备.png)

~~~bash
# 开放指定的3306端口号
firewall-cmd --zone=public --add-port=3306/tcp --permanent
# 重启防火墙
firewall-cmd -reload

# 关闭服务器的防火墙
systemctl stop firewalld
systemctl disable firewalld
~~~



#### 2.3.2 主库配置

- 修改配置文件/etc/my,cnf

~~~bash
# mysql服务ID，保证整个集群环境中唯一，取值范围：1~2的32次方-1，默认为1
server-id=1
# 是否只读，1代表只读，0代表读写
read-only=0
# 忽略的数据，指不需要同步的数据库
#binlog-ignore-db=mysql
# 指定同步的数据库
#binlog-do-db=db01
~~~

- 重启服务器

~~~bash
systemctl restart mysqld
~~~

- 登录mysql，创建远程连接的账号，并授予主从复制权限

~~~mysql
# 创建lzy用户，并设置密码，该用户可在任意主机连接该MySQL服务
create user 'lzy'@'%' identified with mysql_native_password by 'lzy123456';
# 为'lzy'@'%'用户分配主从复制权限
grant replication slave on *.* to 'lzy'@'%';
~~~

- 通过指令，查看二进制日志坐标

~~~mysql
show master status;
-- file：从哪个日志文件开始推送日志文件
-- position：从哪个位置开始推送日志
-- binlog_ignore_db：指定不需要同步的数据库 
~~~



#### 2.3.3 从库配置

- 修改配置文件/etc/my,cnf

```bash
# mysql服务ID，保证整个集群环境中唯一，取值范围：1~2的32次方-1，和主库不一样即可
server-id=2
# 是否只读，1代表只读，0代表读写
read-only=1
# 如果是super-read-only，则超级管理员也是只读，上面的是超级管理员可读写
```

- 重启服务器

~~~bash
systemctl restart mysqld
~~~

- 登录mysql，设置主库配置

~~~mysql
change replication source to source_host='xxx.xxx', source_user='xxx', source_password='xxx', source_log_file='xxx', source_log_pos=xxx;

-- 例如，从binlog.000004的663位置开始复制
change replication source to source_host='192.168.200.200', source_user='lzy', source_password='lzy123456', source_log_file='binlog.000004', source_log_pos=663;
~~~

- 开启同步操作

~~~myysql
start replica;
~~~

- 查看主从同步状态

~~~mysql
show replica status\G;
~~~

![主从复制是否正常](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\主从复制是否正常.png)



#### 2.3.4 测试

直接在主库中进行增删改查，在从库中查看数据是否同步过来了



## 3、分库分表

### 3.1 介绍

- 单数据库存在的性能缺陷
  - IO瓶颈：热点数据太多，数据库缓存不足，产生大量的磁盘IO，效率较低。请求数据太多，带宽不够，网络IO瓶颈
  - CPU瓶颈：排序、分组、连接查询、聚合统计等SQL会耗费大量的CPU资源，请求数太多，CPU出现瓶颈
- **分库分表的中心思想就是将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能问题，从而达到提升数据库性能的目的**

![单数据库体系问题](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\分库分表.png)



### 3.2 拆分策略

- 拆分策略
  - 分库：将一个数据库的数据存放在多个数据库中
  - 分表：将一个表的数据分散的存放在多个表中

![拆分策略](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\拆分策略.png)

- 垂直拆分

  - 垂直分库：以表为依据，根据业务将不同表拆分到不同库中
    - 每个库的表结构都不一样
    - 每个库的数据也不一样
    - 所有库的并集是全量数据

  ![垂直分库](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\垂直分库.png)

  - 垂直分表：以字段为依据，根据字段属性将不同字段拆分到不同表中
    - 每个表的结构都不一样
    - 每个表的数据也不一样，一般通过一列（主键/外键）关联
    - 所有表的并集是全量数据

  ![分库分表](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\垂直分表.png)

  ​

  ​


- 水平拆分

  - 水平分库：以字段为依据，按照一定策略，将一个库的数据拆分到多个库中
    - 每个库的表结构都一样
    - 每个库的数据都不一样
    - 所有库的并集是全量数据

  ![水平分库](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\水平分库.png)

  - 水平分表：以字段为依据，按照一定策略，将一个表的数据拆分到多个表中
    - 每个表的结构都一样
    - 每个表的数据都一样
    - 所有表的并集是全量数据

  ![水平分表](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\水平分表.png)

- 实现技术

  - shardingJDBC：基于AOP原理，在应用程序中对本地执行的SQL进行拦截，解析、改写、路由处理。需要自行编码配置实现，只支持java语言，性能较高
  - MyCat：数据库分库分表中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及前者

![MyCat实现技术](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\MyCat实现技术.png)



### 3.3 MyCat

#### 3.3.1 MyCat概述

- MyCat是开源的、活跃的、基于Java语言编写的MySQL**数据库中间件**。可以像使用mysql一样来使用mycat，对于开发人员来说根本感受不到mycat的存在
- 优点
  - 性能可靠稳定
  - 强大的技术团队
  - 体系完善
  - 社区活跃

![MyCat](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\MyCat.png)



#### 3.3.2 MyCat安装

| 服务器  |  安装软件  |       说明        |
| :-----: | :--------: | :---------------: |
| 服务器1 | JDK、Mycat | Mycat中间件服务器 |
| 服务器1 |   MySQL    |    分片服务器     |
| 服务器2 |   MySQL    |    分片服务器     |
| 服务器3 |   MySQL    |    分片服务器     |

- MyCat目录结构
  - bin：存放可执行文件，用于启动停止mycat
  - conf：存放mycat的配置文件
  - lib：存放mycat的项目依赖包
  - logs：存放mycat的日志文件



#### 3.3.3 MyCat概述

![MyCat概念图](C:\Users\lzy\Desktop\work\数据库\Mysql\Mysql高阶篇图片\MyCat概念图.png)

