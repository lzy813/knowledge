https://gitee.com/zwhddup/mysql-learning/blob/master/%E5%B0%9A%E7%A1%85%E8%B0%B7%E8%A7%86%E9%A2%91%E8%80%81%E5%B8%88%E7%AC%94%E8%AE%B0/mysql%E9%AB%98%E7%BA%A7%E7%AF%87%E7%AC%94%E8%AE%B0/%E7%AC%AC02%E7%AB%A0%20MySQL%E7%9A%84%E6%95%B0%E6%8D%AE%E7%9B%AE%E5%BD%95.md#1-mysql8%E7%9A%84%E4%B8%BB%E8%A6%81%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84



# 一、Linux下MySQL的安装与使用

## 1、安装前说明

### 1.1 查看是否安装过MySQL

- 如果是用rpm安装, 检查一下RPM PACKAGE：

~~~bash
rpm -qa | grep -i mysql # -i 忽略大小写
~~~

- 检查mysql service：

~~~bash
systemctl status mysqld.service
~~~



### 1.2 MySQL的卸载

- **1、关闭** **MySQL** **服务**

~~~bash
systemctl stop mysqld.service
~~~

- **2、查看当前** **MySQL** **安装状况**

~~~bash
rpm -qa | grep -i mysql
# 或
yum list installed | grep mysql
~~~

- **3、卸载上述命令查询出的已安装程序**
  - 务必卸载干净，反复执行`rpm -qa | grep -i mysql`确认是否有卸载残留

~~~bash
yum remove mysql-xxx mysql-xxx mysql-xxx mysqk-xxxx
~~~

-  **4、删除** **MySQL** **相关文件**

  - 查找相关文件

  ~~~bash
  find / -name mysql
  ~~~

  - 删除上述命令查找出的相关文件

  ~~~bash
  rm -rf xxx
  ~~~

- **5、删除 my.cnf**

~~~bash
rm -rf /etc/my.cnf
~~~



## 2、MySQL的Linux版安装

### 2.1 CentOS7下检查MySQL依赖

- **检查/tmp临时目录权限（必不可少）**、
  - 由于mysql安装过程中，会通过mysql用户在/tmp目录下新建tmp_db文件，所以请给/tmp较大的权限。执行 ：

~~~bash
chmod -R 777 /tmp
~~~

- **安装前，检查依赖**

~~~bash
rpm -qa|grep libaio
# 执行结果：libaio-0.3.109-13.el7.x86_64
rpm -qa|grep net-tools
# 执行结果：net-tools-2.0-0.25.20131004git.el7.x86_64
~~~



### 2.2 安装下载

- 下载地址：https://dev.mysql.com/downloads/mysql/

![安装包下载](高级图片\linux安装\安装包下载.png)

- 解压后需要抽出几个文件，上传到linux上的opt下

~~~bash
mysql-community-client-8.0.46-1.el7.x86_64.rpm
mysql-community-client-plugins-8.0.46-1.el7.x86_64.rpm
mysql-community-common-8.0.46-1.el7.x86_64.rpm
mysql-community-icu-data-files-8.0.46-1.el7.x86_64.rpm
mysql-community-libs-8.0.46-1.el7.x86_64.rpm
mysql-community-server-8.0.46-1.el7.x86_64.rpm
~~~

- 在mysql的安装文件目录下执行：（必须按照顺序执行）
  - `rpm`是Redhat Package Manage缩写，通过RPM的管理，用户可以把源代码包装成以rpm为扩展名的文件形式，易于安装。
  - `-i`, --install 安装软件包
  - `-v`, --verbose 提供更多的详细信息输出
  - `-h`, --hash 软件包安装的时候列出哈希标记 (和 -v 一起使用效果更好)，展示进度条
  - 若存在mariadb-libs问题，则执行<font color="red">**yum remove mysql-libs**</font>即可

~~~bash
rpm -ivh mysql-community-common-8.0.46-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-plugins-8.0.46-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.46-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.46-1.el7.x86_64.rpm
rpm -ivh mysql-community-icu-data-files-8.0.46-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.46-1.el7.x86_64.rpm
~~~



### 2.3 查看MySQL版本

~~~bash
mysql --version 
#或
mysqladmin --version

## 输出：mysql  Ver 8.0.46 for Linux on x86_64 (MySQL Community Server - GPL)
~~~



### 2.4 服务的初始化

- 为了保证数据库目录与文件的所有者为 mysql 登录用户，如果你是以 root 身份运行 mysql 服务，需要执行下面的命令初始化：

~~~bash
mysqld --initialize --user=mysql
~~~

- 说明： --initialize 选项默认以“安全”模式来初始化，则会为 root 用户生成一个密码并将`该密码标记为过期`，登录后你需要设置一个新的密码。生成的`临时密码`会往日志中记录一份
- 查看密码

~~~bash
cat /var/log/mysqld.log
~~~

- root@localhost: 后面就是初始化的密码

~~~bash
[root@VM-0-8-centos opt]# mysqld --initialize --user=mysql
[root@VM-0-8-centos opt]# cat /var/log/mysqld.log
2026-06-14T08:52:06.090704Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.46) initializing of server in progress as process 4263
2026-06-14T08:52:06.103066Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-06-14T08:52:07.463670Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-06-14T08:52:09.939303Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: X?95hccb+-km
~~~



### 2.5 启动MySQL，查看状态

~~~bash
#加不加.service后缀都可以 
启动：systemctl start mysqld.service 
关闭：systemctl stop mysqld.service 
重启：systemctl restart mysqld.service 
查看状态：systemctl status mysqld.service
~~~



### 2.6 **查看MySQL服务是否自启动**

- 查看命令：systemctl list-unit-files|grep mysqld.service

~~~bash
[root@VM-0-8-centos opt]# systemctl list-unit-files|grep mysqld.service
mysqld.service                                enabled 
~~~

- 如不是enabled可以运行如下命令设置自启动

~~~bash
systemctl enable mysqld.service
~~~

- 如果希望不进行自启动，运行如下命令设置

~~~bash
systemctl disable mysqld.service
~~~



## 3、**MySQL登录**

### 3.1 **首次登录**

- 通过`mysql -hlocalhost -P3306 -uroot -p`进行登录，在Enter password：录入初始化密码

~~~bash
mysql -hlocalhost -P3306 -uroot -p
~~~



### 3.2 **修改密码**

~~~bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'li998813';
~~~



### 3.3 **设置远程登录**

- **确认网络**

  - 在远程机器上使用ping ip地址`保证网络畅通`
  - 在远程机器上使用telnet命令`保证端口号开放`访问

- **关闭防火墙或开放端口**

  - **方式一：关闭防火墙**

  ~~~bash
  #开启防火墙
  systemctl start firewalld.service
  #查看防火墙状态
  systemctl status firewalld.service
  #关闭防火墙
  systemctl stop firewalld.service
  #设置开机启用防火墙 
  systemctl enable firewalld.service 
  #设置开机禁用防火墙 
  systemctl disable firewalld.service
  ~~~

  - **方式二：开放端口**

  ~~~bash
  # 查看开放的端口号
  firewall-cmd --list-all
  
  # 设置开放的端口号
  firewall-cmd --add-service=http --permanent
  firewall-cmd --add-port=3306/tcp --permanent
  
  # 重启防火墙
  firewall-cmd --reload
  ~~~



## 4、**Linux下修改配置**

- 修改允许远程登陆

~~~bash
use mysql;
select Host,User from user;
update user set host = '%' where user ='root';
flush privileges;
~~~

- `%`是个 通配符 ，如果Host=192.168.1.%，那么就表示只要是IP地址前缀为“192.168.1.”的客户端都可以连接。如果`Host=%`，表示所有IP都有连接权限。
- 注意：<font color="red">**在生产环境下不能为了省事将host设置为%，这样做会存在安全问题，具体的设置可以根据生产环境的IP进行设置**</font>

- 配置新连接报错：错误号码 2058，分析是 mysql 密码加密方法变了

  - **解决方法一：**升级远程连接工具版本
  - **解决方法二：**

  ~~~bash
  ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'li998813';
  ~~~

  

## 5、**字符集的相关操作**

~~~bash
show variables like 'character%';
~~~

- character_set_server：服务器级别的字符集
- character_set_database：当前数据库的字符集
- character_set_client：服务器解码请求时使用的字符集
- character_set_connection：服务器处理请求时会把请求字符串从character_set_client转为character_set_connection
- character_set_results：服务器向客户端返回数据时使用的字符集

- **小结**

  - 如果`创建或修改列`时没有显式的指定字符集和比较规则，则该列`默认用表的`字符集和比较规则

  - 如果`创建表时`没有显式的指定字符集和比较规则，则该表`默认用数据库的`字符集和比较规则

  - 如果`创建数据库时`没有显式的指定字符集和比较规则，则该数据库`默认用服务器的`字符集和比较规则

![请求过程中字符集的变化](高级图片/linux安装/请求过程中字符集的变化.png)



二、

