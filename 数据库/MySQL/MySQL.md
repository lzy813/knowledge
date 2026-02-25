# 一、基础篇

## 1、Mysql概述

|      名称      |                             全称                             |                简称                |
| :------------: | :----------------------------------------------------------: | :--------------------------------: |
|     数据库     |            存储数据的仓库，数据是有组织的进行存储            |           DataBase（DB）           |
| 数据库管理系统 |                  操纵和管理数据库的大型软件                  | DataBase Management System（DBMS） |
|      SQL       | 操作关系型数据库的编程语言，定义了一套操作关系型数据库统一**标准** |  Structured Query Language（SQL）  |

![Mysql概述](MySQL基础篇图片/Mysql概述.png)

## 2、Mysql的前置准备

### 2.1 Mysql的下载与安装

- MySQL 下载地址为：https://dev.mysql.com/downloads/windows/installer/，这里下载的是mysql的msi安装文件，选择下面的470.2M的版本，点击download进行下载。

![1](Windows下载安装/1.png)

- 在下载页面直接选择：**No thanks, just start my download.进行下载**

![1](Windows下载安装/2.png)

- 下载好mysql的msi安装文件后，双击进行安装，选择最下面的Custom模式安装，点击右下角 Next 到下一步

![1](Windows下载安装/3.png)

- 选择MySql Server 8.0.27 移动到右侧，点击Next

![4](Windows下载安装/4.png)

- 点击Execute，检查必要配置

![5](Windows下载安装/5.png)

- 检查过程中，可能会提示要安装微软文件，这时安装即可；等到后面的status提示的状态完成后，点击Next进行下一步

![6](Windows下载安装/6.png)

- 然后，点击Execute执行

![7](Windows下载安装/7.png)

- 此时会进行下载文件

![8](Windows下载安装/8.png)

- 全部下载完成后，Status提示状态Complete表示已完成，此时点击Next

![9](Windows下载安装/9.png)

- 此时到下一步再点击Next

![10](Windows下载安装/10.png)

- 再 Type and NetWorking 页面中，Port是mysql数据库的默认端口3306，直接点击Next

![11](Windows下载安装/11.png)

- 在认证方式页面有两个选项，强密码认证和以前的认证方式，我这里选择的是强密码认证，然后Next

![12](Windows下载安装/12.png)

- 然后输入root用户的密码，密码一定要记住，否则无法登录。

![12](Windows下载安装/13.png)

- 检查mysql的服务，建议使用默认不修改，点击Next

![14](Windows下载安装/14.png)

- 在Apply Configuration 点击Execute执行

![15](Windows下载安装/15.png)

- 应用配置完成后，点击Finish继续

![16](Windows下载安装/16.png)

- 在Product Configuration 中，status提示状态完成后，点击Next继续

![17](Windows下载安装/17.png)

- 最后一步，提示安装完成，点击Finish结束

![18](Windows下载安装/18.png)



### 2.2 Mysql的开启与关闭

- Mysql的开启与关闭

~~~bash
net start mysql80						# 启动mysql服务
net stop mysql80						# 关闭mysql服务
#注意：mysql服务一般是开机自启的
~~~

- Mysql的客户端连接

~~~bash
# cmd打开，然后输入命令
mysql -u root -p					# 前提是得配置环境变量，否则得从mysql的安装目录下进入cmd
~~~



### 2.3 Mysql的数据模型

- 关系型数据库（RDBMS）
  - 概念：建立在关系模型基础上，由多张相互连接的二维表组成的数据库
  - 特点：
    - 使用表存储数据，格式统一，便于维护
    - 使用SQL语言操作，标准统一，使用方便
- 数据模型
  - 客户端连接上服务器
  - 客户端通过DBMS创建多个数据库
  - 每个数据库里可以创建多张表
  - 每个表里可以存放多条数据

![mysql数据模型](MySQL基础篇图片/mysql数据模型.png)



## 3、基础SQL

### 3.1 SQL通用语法

- SQL语句可以单行或者多行书写，以分号结尾
- SQL语句可以使用空格/缩进来增强语句的可读性
- MySQL数据库的SQL语句不区分大小写，关键字建议使用大写
- 注释：
  - 单行注释： **-- 注释内容**  或者 **# 注释内容**(mysql特有)
  - 多行注释： **/* 注释内容 */**



### 3.2 SQL分类

| 分类 |            全称            |                          说明                          |
| :--: | :------------------------: | :----------------------------------------------------: |
| DDL  |  Data Definition Language  |   数据定义语言，用来定义数据库对象(数据库、表、字段)   |
| DML  | Data Manipulation Language |     数据操作语言，用来对数据库表中的数据进行增删改     |
| DQL  |    Data Query Language     |         数据查询语言，用来查询数据库中表的记录         |
| DCL  |   Data Control Language    | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |





### 3.3 DDL(数据定义语言)

#### 3.3.1 库操作

- 查询操作

~~~sql
-- 查询所有数据库
show databases;
-- 查询当前数据库
select database();
~~~

- 创建

~~~sql
create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
~~~

- 删除

~~~sql
drop database [if exists] 数据库名;
~~~

- 使用

~~~sql
use 数据库名;
~~~



#### 3.3.2 表操作——查询

- 查询当前数据库所有表

~~~sql
show tables;
~~~

- 查询表结构

~~~sql
desc 表名;
~~~

- 查询指定表的建表语句

~~~sql
show create table 表名;
~~~



#### 3.3.3 表操作——创建

~~~sql
create table 表名(
	字段1 字段类型[comment 字段1注释],
    字段2 字段类型[comment 字段2注释],
    字段3 字段类型[comment 字段3注释],
    ...
    字段n 字段类型[comment 字段n注释]
)[comment 表注释];
~~~



#### 3.3.4 表操作——数据类型

- 数值类型
  - 如果指定的是无符号的，就在后面加unsigned，如：age tinyint unsigned
  - double，decimal这种需要加参数的，第一个参数是整体长度，第二个参数是小数点所占位数

|     类型     |                   大小                   |                        范围（有符号）                        |                    范围unsigned（无符号）                    |      用途      |
| :----------: | :--------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :------------: |
|   tinyint    |                 1 Bytes                  |                         (-128，127)                          |                           (0，255)                           |    小整数值    |
|   smallint   |                 2 Bytes                  |                      (-32 768，32 767)                       |                         (0，65 535)                          |    大整数值    |
|  mediumint   |                 3 Bytes                  |                   (-8 388 608，8 388 607)                    |                       (0，16 777 215)                        |    大整数值    |
| int或integer |                 4 Bytes                  |               (-2 147 483 648，2 147 483 647)                |                      (0，4 294 967 295)                      |    大整数值    |
|    bigint    |                 8 Bytes                  |   (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)    |               (0，18 446 744 073 709 551 615)                |   极大整数值   |
|    float     |                 4 Bytes                  | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) |         0，(1.175 494 351 E-38，3.402 823 466 E+38)          | 单精度浮点数值 |
|    double    |                 8 Bytes                  | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度浮点数值 |
|   decimal    | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 |                        依赖于M和D的值                        |                        依赖于M和D的值                        |     小数值     |

- 字符串类型
  - char：如果设置的10位，但是数据没有10位，那么剩下的就会以空格补齐
    - 性能高
  - varchar：如果设置的10位，但是数据没有10位，那么这个字段所占空间就是数据的实际位数
    - 性能相对较低

|    类型    |         大小          |              用途               |
| :--------: | :-------------------: | :-----------------------------: |
|    char    |      0-255 bytes      |           定长字符串            |
|  varchar   |     0-65535 bytes     |           变长字符串            |
|  tinyblob  |      0-255 bytes      | 不超过 255 个字符的二进制字符串 |
|  tinytext  |      0-255 bytes      |          短文本字符串           |
|    blob    |    0-65 535 bytes     |     二进制形式的长文本数据      |
|    text    |    0-65 535 bytes     |           长文本数据            |
| mediumblob |  0-16 777 215 bytes   |  二进制形式的中等长度文本数据   |
| mediumtext |  0-16 777 215 bytes   |        中等长度文本数据         |
|  longblob  | 0-4 294 967 295 bytes |    二进制形式的极大文本数据     |
|  longtext  | 0-4 294 967 295 bytes |          极大文本数据           |

- 日期类型

|   类型    | 大小( bytes) |                             范围                             |        格式         |           用途           |
| :-------: | :----------: | :----------------------------------------------------------: | :-----------------: | :----------------------: |
|   date    |      3       |                    1000-01-01/9999-12-31                     |     YYYY-MM-DD      |          日期值          |
|   time    |      3       |                   '-838:59:59'/'838:59:59'                   |      HH:MM:SS       |     时间值或持续时间     |
|   year    |      1       |                          1901/2155                           |        YYYY         |          年份值          |
| datetime  |      8       |           1000-01-01 00:00:00/9999-12-31 23:59:59            | YYYY-MM-DD HH:MM:SS |     混合日期和时间值     |
| timestamp |      4       | 1970-01-01 00:00:00/2038结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 |   YYYYMMDD HHMMSS   | 混合日期和时间值，时间戳 |

- 案例

~~~sql
/* 设计一张员工信息表，要求如下：
	1、编号（纯数字）
	2、员工工号（字符串类型，长度不超过10位）
	3、员工姓名（字符串类型，长度不超过10位）
	4、性别(男/女，存储一个汉字)
	5、年龄（正常人年龄，不可能存储负数）
	6、身份证号（二代身份证号均为18位，身份证中有X这样的字符）
	7、入职时间（取值年月日即可）
*/
create table emp(
	id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '姓名',
    sex char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    entrydate date comment '入职时间'
)comment '员工表';
~~~



#### 3.3.5 表操作——修改

- 添加字段

~~~sql
alter table 表名 add 字段名 类型(长度) [comment 注释] [约束]；
~~~

~~~sql
-- 案例：为emp表增加一个新的字段“昵称”为nickname，类型为varchar(20)
alter table emp add nickname varchar(20) comment '昵称';
~~~

- 修改数据类型

~~~sql
alter table 表名 modify 字段名 新数据类型(长度);
~~~

- 修改字段名和字段类型

~~~sql
alter table 表名 change 旧字段名 新字段名 类型(长度) [comment 注释] [约束];
~~~

~~~sql
-- 案例：将emp表的nickname字段修改为username，类型为varchar(30)
alter table emp change nickname username varchar(30) comment '昵称';
~~~

- 修改表名

~~~sql
alter table 表名 rename to 新表名;
~~~

~~~sql
-- 案例：将emp表的表名修改为employee
alter table emp rename to employee;
~~~



#### 3.3.6 表操作——删除

- 删除字段

~~~sql
alter table 表名 drop 字段名;
~~~

~~~sql
-- 案例：将emp表的字段username删除
alter table emp drop username;
~~~

- 删除表

~~~sql
drop table [if exists] 表名;
~~~

- 删除表，并重新创建该表

~~~sql
truncate table 表名;
~~~

**注意：删除表的时候，表里面的全部数据都会被删除**





### 3.4 DML(数据操作语言)

#### 3.4.1 增加(insert)

- 给指定字段添加数据

~~~sql
insert into 表名(字段1,字段二,...) values(值1,值2,...);
~~~

- 给全部字段添加数据

~~~sql
insert into 表名 values(值1,值2,...);
~~~

- 批量添加数据

~~~sql
insert into 表名(字段1,字段二,...) values(值1,值2,...),(值1,值2,...),(值1,值2,...);
-- 或者
insert into 表名 values(值1,值2,...),(值1,值2,...),(值1,值2,...);
~~~

- 注意：
  - 插入数据时，指定的字段顺序需要与值得顺序是一一对应的
  - 字符串和日期型数据应该包含在引号中
  - 插入的数据大小，应该在字段的规定范围内

~~~sql
-- 案例：插入一条数据
insert into employee(id,workno,name,sex,age,idcard,entrydate) values(1,'1','lzy','男',18,'123456789012345678','2000-01-01');
-- 案例：插入多条数据
insert into employee(id,workno,name,sex,age,idcard,entrydate) values(1,'1','lzy','男',18,'123456789012345678','2000-01-01'),(2,'2','lsq','女',18,'123456789012345678','2000-01-01');
~~~

	

#### 3.4.2 修改(update)

~~~sql
update 表名 set 字段1 = 值1, 字段2 = 值2, ... [where 条件];
~~~

**注意：修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据**

~~~sql
-- 案例1：修改id为1的数据，将name改为lyx
update employee set name = 'lyx' where id = 1;
-- 案例2：修改id为1的数据，将name改为lzy，sex改为男
update employee set name = 'lzy',sex = '女' where id = 1;
-- 案例3：将所有的员工入职日期改为2008-01-01
update employee set entrydate = '2008-01-01';
~~~



#### 3.4.3 删除(delete)

~~~sql
delete from 表名 [where 条件];
~~~

- 注意：
  - delete语句的条件可以有，也可以没有，没有的话，则会删除整个表的所有数据
  - delete不能删除某一个字段的值(可以使用update将其改为null)

~~~sql
-- 案例1：删除sex为女的数据
delete from employee where sex = '女';
-- 案例2：删除所有员工
delete from employee;
~~~



### 3.5 DQL(数据查询语言)

#### 3.5.1 基本查询

- 查询多个字段

~~~sql
-- 查询多个字段
select 字段1,字段2,... from 表名;
-- 查询所有字段
select * from 表名;
~~~

- 设置别名

~~~sql
select 字段1 [as 别名1],字段2 [as 别名2],... from 表名;		-- as也可以省略
~~~

- 去除重复记录

~~~sql
select distinct 字段列表 from 表名;
~~~

- 案例

~~~sql
-- 查询指定字段name，workno，age
select name,workno,age from employee;

-- 查询所有字段返回
select * from employee;		-- 但是实际开发中尽量不要写*，会损失效率且不易观看

-- 查询所有员工的工作地址，并起别名
select workaddress as '工作地址' from employee;
select workaddress '工作地址' from employee;

-- 查询公司员工的上班地址(不要重复)
select distinct workaddress '工作地址' from employee;
~~~



#### 3.5.2 条件查询

- 语法

~~~sql
select 字段列表 from 表名 where 条件列表;
~~~

- 比较运算符条件

|     比较运算符      |                    功能                    |
| :-----------------: | :----------------------------------------: |
|          >          |                    大于                    |
|         >=          |                  大于等于                  |
|          <          |                    小于                    |
|         <=          |                  小于等于                  |
|          =          |                    等于                    |
|      <> 或 !=       |                   不等于                   |
| between ... and ... |      在某个范围之间（含最小、最大值）      |
|       in(...)       |        在in之后的列表中的值，多选一        |
|     like 占位符     | 模糊匹配（_匹配单个字符，%匹配任意个字符） |
|       is null       |                   是null                   |

- 逻辑运算符条件

| 逻辑运算符 |            功能            |
| :--------: | :------------------------: |
| and 或 &&  |   并且(多个条件同时成立)   |
| or 或 /|/| | 或者(多个条件任意一个成立) |
| not 或 ！  |          非，不是          |

- 案例

~~~sql
-- 1、查询年龄等于88的员工
select * from emp where age = 88;

-- 2、查询年龄小于20的员工信息
select * from emp where age < 20;

-- 3、查询年龄小于等于20的员工信息
select * from emp where age <= 20;

-- 4、查询没有身份证号的员工信息
select * from emp where idcard is null;

-- 5、查询有身份证号的员工信息
select * from emp where idcard is not null;

-- 6、查询年龄不等于88的员工信息
select * from emp where age != 88;
select * from emp where age <> 88;

-- 7、查询年龄在15岁(包含)到20岁(包含)之间的员工信息
select * from emp where age between 15 and 20;			--between后跟小值，and后跟大值
select * from emp where age >= 15 && age <= 20;
select * from emp where age >= 15 and age <= 20;

-- 8、查询性别为女，且年龄小于25岁的员工信息
select * from emp where sex = '女' and age < 25;

-- 9、查询年龄等于18 或 20 或40 的员工信息
select * from emp where age = 18 or age = 20 or age = 40;
select * from emp where age in(18,20,40);

-- 10、查询姓名为两个字的员工信息
select * from emp where name like '__';

-- 11、查询身份证号最后一位是X的员工信息
select * from emp where idcard like '%X';
~~~



#### 3.5.3 聚合函数

- 将一列数据作为一个整体，进行纵向计算
- 常见的聚合函数
  - count：统计数量
  - max：最大值
  - min：最小值
  - avg：平均值
  - sum：求和
- 语法：select 聚合函数(字段列表) from 表名;
- **注意：null值不参与所有聚合函数运算**

~~~sql
-- 1、统计该企业员工数据
select count(*) from emp;

-- 2、统计该企业员工的平均年龄
select avg(age) from emp;

-- 3、统计该企业员工的最大年龄
select max(age) from emp;

-- 4、统计该企业员工的最小年龄
select min(age) from emp;

-- 5、统计西安地区员工的年龄之和
select sum(age) from emp where workaddress = '西安';
~~~



#### 3.5.4 分组查询

- 语法

~~~ sql
select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件];
~~~

- where和having的区别
  - 执行时机不同：where是分组之前进行过滤，不满足where条件，则不参与分组；而having是分组之后对结果进行过滤
  - 判断条件不同：where不能对聚合函数进行判断，而having可以
- 注意：
  - 执行顺序：where > 聚合函数 > having
  - 分组之后，查询的字段一般为聚合函数和分组字段，查询其他的字段无任何意义

~~~sql
-- 案例1：根据性别分组，统计男性员工 和 女性员工的数量
select sex,count(*) from emp group by sex;

-- 案例2：根据性别分组，统计男性员工 和 女性员工的平均年龄
select sex,avg(age) from emp group by sex;

-- 案例3：查询年龄小于45的员工数量，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress,count(*) from emp where age < 45 group by workaddress having count(*) >= 3;
~~~



#### 3.5.5 排序查询

~~~sql
select 字段列表 from 表名 order by 字段1 排序方式1,字段2 排序方式2;
~~~

- 排序方式
  - asc：升序（默认值）
  - desc：降序
- 注意：**如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序**

~~~sql
-- 案例1：根据年龄对公司的员工进行升序排序
select * from emp order by age asc;

-- 案例2：根据入职时间，对员工进行降序排序
select * from emp order by entrydate desc;

-- 案例3：根据年龄对公司的员工进行升序排序，年龄相同，再按照入职时间进行降序排序
select * from emp order by age asc, entrydate desc;
~~~



#### 3.5.6 分页查询

~~~sql
select 字段列表 from 表名 limit 起始索引，查询记录数;
~~~

- 注意：
  - 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
  - 分页查询是数据库的方言，不同的数据库有不同的实现，mysql中式limit
  - 如果查询的是第一页数据，起始索引可以省略，直接简写为limit 10

~~~sql
-- 案例1：查询第一页员工数据，每页显示10条记录
select * from emp limit 0,10;
select * from emp limit 10;

-- 案例2：查询第2页员工数据，每页显示10条数据
select * from emp limit 10,10;     				-- 起始索引 = (页码-1)*页展示记录数
~~~



#### 3.5.7 经典案例

~~~sql
-- 案例1：查询年龄为20,21,22,23岁的女性员工信息
select * from emp where sex = '女' and age in(20,21,22,23);

-- 案例2：查询性别为男，并且年龄在20~40岁(含)以内的姓名为三个字的员工
select * from emp where sex = '女' and (age between 20 and 40) and name like '___'; 

-- 案例3：统计员工表中，年龄小于60岁的，男性员工和女性员工的人数
select sex,count(*) from emp where age < 60 group by sex;

-- 案例4：查询所有年龄小于等于35岁员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按入职时间降序排序
select name,age from emp where age < 35 order by age asc,entrydate desc;

-- 案例5：查询性别为男，且年龄在20~40岁(含)以内的前五个员工信息，对查询结果按年龄升序排序，年龄相同按入职时间升序排序
select * from emp where sex = '男' and age between 20 and 40 order by age asc,entrydate asc limit 5;
~~~



#### 3.5.8 执行顺序

![执行顺序](MySQL基础篇图片/执行顺序.png)



### 3.6 DCL(数据控制语言)

- 作用：管理数据库用户、控制数据库的访问权限



#### 3.6.1 管理用户

- 查询用户

~~~sql
use mysql;
select * from user;
~~~

- 创建用户

~~~ sql
create user '用户名'@'主机名' identified by '密码';
~~~

- 修改用户密码

~~~sql
alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';
~~~

- 删除用户

~~~sql
drop user '用户名'@'主机名';
~~~

- 案例

~~~sql
-- 创建用户lzy，只允许在当前主机localhost访问，密码为123456
create user 'lzy'@'localhost' identified by '123456';

-- 创建用户lsq，可以在任意主机访问，密码为123456
create user 'lsq'@'%' identified by '123456';

-- 修改用户lzy的密码为1234
alter user 'lzy'@'localhost' identified with mysql_native_password by '1234';

-- 删除lzy用户
drop user 'lzy'@'localhost';
~~~



#### 3.6.2 权限控制

|        权限         |        说明        |
| :-----------------: | :----------------: |
| all，all privileges |      所有权限      |
|       select        |      查询数据      |
|       insert        |      插入数据      |
|       update        |      修改数据      |
|       delete        |      删除数据      |
|        alter        |       修改表       |
|        drop         | 删除数据库/表/视图 |
|       create        |   创建数据库/表    |

- 查询权限

~~~sql
show grants for '用户名'@'主机名';
~~~

- 授予权限

~~~sql
grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
~~~

- 撤销权限

~~~sql
revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名'; 
~~~

- 案例

~~~sql
-- 案例1：查询权限
show grants for 'root'@'localhost';

-- 案例2：授予权限
grant all on demo.* to 'root'@'localhost';

-- 回收权限
revoke all on demo.* from 'root'@'localhost';
~~~

- 注意：
  - 多个权限之间，使用逗号分隔
  - 授权时，数据库名和表名可以使用*进行通配，代表所有



## 4、函数

函数：是指一段可以直接被另一段程序调用的程序或代码



### 4.1 字符串函数

语法：select 函数(参数);

|           函数           |                           功能                            |
| :----------------------: | :-------------------------------------------------------: |
|   concat(s1,s2,...,sn)   |        字符串拼接，将s1,s2,...,sn拼接为一个字符串         |
|        lower(str)        |                  将字符串str全部转为小写                  |
|        upper(str)        |                  将字符串str全部转为大写                  |
|     lpad(str,n,pad)      | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
|     Rpad(str,n,pad)      | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
|        trim(str)         |                去掉字符串头部和尾部的空格                 |
| substring(str,start,len) |      返回从字符串str从start位置起的len个长度的字符串      |

- 案例

~~~sql
-- concat用法
select concat('hello',' lzy');

-- upper用法
select upper('hello');

-- lpad用法
select lpad('01', 5, '-');					-- 结果：---01

-- rpad用法
select rpad('01', 5, '-');					-- 结果：01---

-- trim用法
select trim(' 123 ');

-- substring用法
select substring('1234567890', 3, 3);		 -- 结果：345

-- 企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0.比如：1号员工的工号应该为00001
update emp set workno = lpad(workno, 5, '0');
~~~



### 4.2 数值函数

|    函数    |                功能                |
| :--------: | :--------------------------------: |
|  ceil(x)   |              向上取整              |
|  floor(x)  |              向下取整              |
|  mod(x,y)  |            返回x/y的模             |
|   rand()   |          返回0~1的随机数           |
| round(x,y) | 求参数x的四舍五入的值，保留y位小数 |

~~~sql
-- ceil用法
select ceil(1.5);  				-- 结果：2

-- floor用法
select floor(1.5);  			-- 结果：1

-- mod用法
select mod(7,5);  				-- 结果：2

-- rand用法
select rand();  				-- 结果：0.3348114977914947 

-- round用法
select round(1.23456, 2);		 -- 结果：1.23

-- 通过数据库的函数，生成一个六位数的随机验证码
select lpad(round(rand()*1000000, 0), 6, '0');
~~~



### 4.3 日期函数

|                函数                |                       功能                        |
| :--------------------------------: | :-----------------------------------------------: |
|             curdate()              |                   返回当前日期                    |
|             curtime()              |                   返回当前时间                    |
|               now()                |                获取当前日期和时间                 |
|             year(date)             |                获取指定date的年份                 |
|            month(date)             |                获取指定date的月份                 |
|             day(date)              |                获取指定date的日期                 |
| date_add(date, interval expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
|       datediff(date1,date2)        |    返回起始时间date1和结束时间date2之间的天数     |

~~~sql
-- curdate()函数
select curdate();				-- 结果：2022-03-12

-- curtime()函数
select curtime();				-- 结果：12:17:19

-- now()函数
select now();					-- 结果：2022-03-12 12:17:26

-- year、month、day函数
select year(now());				-- 结果：2022
select month(now());			-- 结果：3
select day函数(now());		   -- 结果：12

-- date_add函数
select date_add(now(), interval 70 day);		-- 当前时间往后推70天，结果：2022-05-21 12:21:32 
select date_add(now(), interval 70 month);		-- 当前时间往后推70月，结果：2028-01-12 12:22:02 

-- datediff函数
select datediff('2021-12-01', '2021-12-12');     -- 结果：-11，注意：是第一个时间减去第二个时间

-- 查询所有员工的入职天数，并根据入职天数倒序排序
select name,datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
~~~



### 4.4 流程控制函数

- 流程控制函数，可以在SQL语句中实现条件筛选，从而提高语句的效率

|                            函数                            |                            功能                             |
| :--------------------------------------------------------: | :---------------------------------------------------------: |
|                      if(value, t, f)                       |             如果value为true，则返回t，否则返回f             |
|                   ifnull(value1, value2)                   |        如果value1不为空，返回value1，否则返回value2         |
|    case when [val1] then [res1] ... else [default] end     |    如果val1为true，返回res1, ... ,否则返回default默认值     |
| case [expr] when [val1] then [res1] ... else [default] end | 如果expr的值等于val1，返回res1，... ，否则返回default默认值 |

- 案例

~~~sql
-- if用法
select if(true, 'ok', 'error');					-- 结果：ok

-- ifnull用法
select ifnull('ok', 'default');					-- 结果：ok
select ifnull('', 'default');					-- 结果：''（空字符串）
select ifnull(null, 'default');					-- 结果：default

-- case when then else用法
-- 需求：查询emp表的员工姓名和工作地址（北京/上海 ------> 一线城市，其他 -------> 二线城市）
select 
	name,
	(case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end) as '工作地址'
	from emp;


/* 案例：统计班级各个学员的成绩，展示规则如下：
		>= 85，展示优秀
		>= 60，展示及格
		否则，展示不及格
*/
select 
	id,
	name,
	(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end) '数学',
	(case when english >= 85 then '优秀' when math >=60 then '及格' else '不及格' end) '英语',
	(case when chinese >= 85 then '优秀' when math >=60 then '及格' else '不及格' end) '语文'
from score;
~~~



## 5、约束

### 5.1 概述

- 概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据
- 目的：保证数据库中数据的正确、有效性和完整性
- 分类

|   约束   |                           描述                           |   关键字    |
| :------: | :------------------------------------------------------: | :---------: |
| 非空约束 |                限制该字段的数据不能为null                |  not null   |
| 唯一约束 |          保证该字段的所有数据都是唯一、不重复的          |   unique    |
| 主键约束 |         主键是一行数据的唯一标识，要求非空且唯一         | primary key |
| 默认约束 |      保存数据时，如果未指定该字段的值，则采用默认值      |   default   |
| 检查约束 |                 保证字段值满足某一个条件                 |    check    |
| 外键约束 | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | foreign key |

- 注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束



### 5.2 演示

| 字段名 |  字段含义  |  字段类型   |         约束条件          |
| :----: | :--------: | :---------: | :-----------------------: |
|   id   | ID唯一标识 |     int     |    主键，并且自动增长     |
|  name  |    姓名    | varchar(10) |     不为空，并且唯一      |
|  age   |    年龄    |     int     |  大于0，并且小于等于120   |
| status |    状态    |   char(1)   | 如果没有指定该值，默认为1 |
| gender |    性别    |   char(1)   |            无             |

~~~sql
-- 创建表
create table users(
    id int primary key auto_increment comment '主键',
    name varchar(10) not null unique comment '姓名',
    age int check(age > 0 && age <= 120) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
) comment '用户表';

-- 插入数据
insert into users(name,age,status,gender) values('tom1',19,1,'男'),('tom2',19,1,'女');
~~~



### 5.3 外键约束

- 概念：外键用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性
- 注意：**下述两张表，在数据库层面，并未建立外键关联，所以是无法保证数据的一致性和完整性的**

![外键约束](MySQL基础篇图片/外键约束.png)

- 语法

~~~sql
-- 添加外键
create table 表名(
    字段名 数据类型,
    ...
    [constraint] [外键名称] foreign key(外键字段名) references 主表(主表列名)
)；
-- 或
alter table 表名 add constraint 外键名称 foreign key(外键字段名) references 主表(主表列名);

-- 删除外键
alter table 表名 drop foreign key 外键名称;
~~~

- 案例

~~~sql
-- 添加外键
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id);

-- 删除外键
alter table 表名 drop foreign key fk_emp_dept_id;
~~~



### 5.4 外键约束的删除/更新行为

|    行为     |                             说明                             |
| :---------: | :----------------------------------------------------------: |
|  no action  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新 |
|  restrict   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新 |
|   cascade   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录 |
|  set null   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中的该外键值为null |
| set default |   父表有变更时，子表将外键列设置为一个默认值(Innodb不支持)   |

- 语法

~~~sql
-- 添加行为
alter table 表名 add constraint 外键名称 foreign key(外键字段名) references 主表(主表列名) on update 行为 on delete 行为;
~~~

- 案例

~~~sql
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id) on update cascade on delete cascade;
-- 或
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id) on update set null on delete set null;
~~~





## 6、多表查询

### 6.1 多表关系

- 各个表结构之间的关系基本分三种
  - 一对多(多对一)
  - 多对多
  - 一对一
- 一对多(多对一)
  - 案例：部门与员工的关系
  - 关系：一个部门对应多个员工，一个员工对应一个部门
  - 实现：**在多的一方建立外键，指向一的一方的主键**

![一对多](MySQL基础篇图片/一对多.png)

- 多对多
  - 案例：学生与课程的关系
  - 关系：一个学生可以选修多门课程，一门课程也可以供多个学生选择
  - 实现：**建立第三张中间表，中间表至少包括两个外键，分别关联两方主键**

![多对多](MySQL基础篇图片/多对多.png)

- 一对一

  - 案例：用户与用户详情的关系
  - 关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
  - 实现：**在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的**

  ![一对一](MySQL基础篇图片/一对一.png)

  ​

### 6.2 多表查询

- 概述：指从多张表中查询数据
- 笛卡尔积：笛卡尔积是指在数学中，两个集合A和集合B的所有组合情况
- 多表查询分类
  - 连接查询
    - 内连接：相当于查询A、B交集部分数据
    - 外连接：
      - 左外连接：查询左表所有数据，以及两张表交集部分数据
      - 右外连接：查询右表所有数据，以及两张表交集部分数据
    - 自连接：当前表与自身的连接查询，自连接必须使用表别名
  - 子查询



### 6.3 	内连接

- 内连接查询的是两张表交集的部分
- 语法

~~~sql
-- 隐式内连接
select 字段列表 from 表1,表2 where 条件;
-- 显示内连接
select 字段列表 from 表1 [inner] join 表2 on 连接条件;
~~~

- 案例：

~~~sql
-- 查询每一个员工的姓名，及关联的部门的名称
-- 隐式内连接
select emp.name,dept.name from emp,dept where emp.dept_id = dept.id;

-- 显示内连接
select emp.name,dept.name from emp inner join dept on emp.dept_id = dept.id;
~~~



### 6.4 外连接

- 左外连接：相当于查询表1(左表)的所有数据 包含 表1和表2交集部分的数据

~~~sql
select 字段列表 from 表1 left [outer] join 表2 on 条件;
~~~

- 右外连接：相当于查询表2(右表)的所有数据 包含 表1和表2交集部分的数据

~~~sql
select 字段列表 from 表1 right [outer] join 表2 on 条件;
~~~

- 案例：

~~~sql
-- 查询emp表的所有数据，和对应的部门信息(左外连接)
select emp.*,dept.name from emp left join dept on emp.dept_id = dept.id;

-- 查询dept表的所有数据，和对应的员工信息(右外连接)
select dept.*,emp.name from emp right join dept on emp.dept_id = dept.id;
~~~



### 6.5 自连接

~~~sql
select 字段列表 from 表A 别名A join 表A 别名B on 条件;
~~~

**自连接查询，可以是内连接查询，也可以是外连接查询**

~~~sql
-- 查询员工及其所属领导的名字
select a.name, b.name from emp a, emp b where a.managerid = b.id;

-- 查询所有员工emp 及其领导的名字emp，如果没有领导，也需要查询出来
select a.name,b.name from emp a left join emp b on a.managerid = b.id;
~~~



### 6.6 联合查询

- union，union all
- 对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集
- 语法：

~~~sql
select 字段列表 from 表A ...
union [all]
select 字段列表 from 表B ...;
~~~

- 案例

~~~sql
-- 将薪资低于5k的员工，和年龄大于50的员工全部查询出来
select * from emp where salary < 5000
union all
select * from emp where age > 50;
~~~

- 注意：
  - 如果不需要去重就加上all，需要就去掉all
  - **对于联合查询的多张表的列数必须保持一致，字段类型也需要一致**



### 6.7 子查询

- 概念：SQL语句中嵌套select语句，称为嵌套查询，又称子查询

~~~sql
select * from t1 where column1 = (select column1 from t2);
~~~

- 注意：子查询外部的语句可以是insert/update/delete/select的任何一个
- 根据子查询结果不同，分为
  - 标量子查询(子查询结果为单个值)
  - 列子查询(子查询结果为一列)
  - 行子查询(子查询结果为一行)
  - 表子查询(子查询结果为多行多列)
- 根据子查询位置，分为：where之后，from之后，select之后



#### 6.7.1 标量子查询

- 子查询返回的结果是单个值(数字、字符串、日期等)，最简单的形式，这种子查询称为标量子查询
- 常用符号：= <> > >= < <=
- 案例

~~~sql
-- 案例1：查询“销售部”的所有员工id
-- 步骤一：查询“销售部”部门id
select id from dept where name = '销售部';
-- 步骤二：根据销售部部门id，查询员工信息
select * from emp where dept_id = 4;
-- 合并
select * from emp where dept_id = (select id from dept where name = '销售部');

-- 案例2：查询在“东方白”入职之后的员工信息
select * from emp where entrydate > (select entrydate from emp where name = '东方白');
~~~



#### 6.7.2 列子查询

- 子查询返回的结果是一列(可以是多行)，这种子查询称为**列子查询**
- 常用的操作符：in、not in、any、some、all

| 操作符 |                  描述                  |
| :----: | :------------------------------------: |
|   in   |      在指定的集合范围之内，多选一      |
| not in |         不在指定的集合范围之内         |
|  any   |  子查询返回列表中，有任意一个满足即可  |
|  some  | 与any等同，使用some的地方都可以使用any |
|  all   |    子查询返回列表的所有值都必须满足    |

- 案例：

~~~sql
-- 案例1：查询“销售部”和“市场部”的所有员工信息
select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');

-- 案例2：查询比财务部所有人工资都高的员工信息
select * from emp where salary > all (select salary from emp where dept_id = (select id from dept where name = '财务部'));

-- 案例3：查询比研发部其中任意一人工资高的员工信息
select * from emp where salary > any (select salary from emp where dept_id = (select id from dept where name = '研发部'));
~~~



#### 6.7.3 行子查询

- 子查询返回的结果是一行（可以是多列），这种子查询称为**行子查询**
- 常用操作符：= 、<>、in、not in
- 案例

~~~sql
-- 查询与“张无忌”的薪资及直属领导相同的员工信息
-- 步骤1：查询“张无忌”的薪资及直属领导
select salary, managerid from emp where name = '张无忌';
-- 步骤2：查询与“张无忌”的薪资及直属领导相同的员工信息
select * from emp where (salary,managerid) = (12500,1);
-- 合并
select * from emp where (salary,managerid) = (select salary, managerid from emp where name = '张无忌');
~~~



#### 6.7.4 表子查询

- 子查询返回的结果是多行多列，这种子查询称为**表子查询**
- 常用操作符：in

~~~sql
-- 案例1：查询与“鹿杖客”，“宋远桥”的职位和薪资相同的员工信息
select * from emp where (job,salary) in (select job,salary frmo emp where name = '鹿杖客' or name = '宋远桥');
~~~



## 7、事务

### 7.1 事务简介

- 事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作**要么同时成功，要么同时失败**。
- **默认mysql的事务是自动提交的，也就是说，当执行一条DML语句，mysql会立即隐式的提交事务**



### 7.2 事务操作

- 查询/设置事务提交方式

~~~sql
select @@autocommit;
set @@autocommit = 0;			-- 1为自动提交，0为手动提交
~~~

- 提交事务

~~~sql
commit;
~~~

- 回滚事务

~~~sql
rollback;
~~~

- 方式2

~~~sql
-- 开启事务
start transaction 或 begin;
-- 提交事务
commit;
-- 回滚事务
rollback;
~~~

- 案例

~~~sql
-- 开启事务
start transaction;
-- 张三余额-1000
update accout set money = money - 1000 where name = '张三';
-- 李四余额+1000
update accout set money = money + 1000 where name = '李四';
-- 提交事务
commit;
-- 回滚事务
rollback;
~~~



### 7.3 事务的四大特性

- 原子性：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
- 一致性：事务完成时，必须使所有的数据都保持在一致状态
- 隔离性：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的



### 7.4 并发事务问题

- 脏读：一个事务读到另一个事务还没有提交的数据
- 不可重复读：一个事务先后读取同一条数据，但两次读取的数据不同，称之为不可重复读
- 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了“幻影”



### 7.5 事务的隔离级别

|            隔离级别             | 脏读 | 不可重复读 | 幻读 |
| :-----------------------------: | :--: | :--------: | :--: |
|    Read uncommittd——读未提交    |  √   |     √      |  √   |
|     Read committd——读已提交     |  ×   |     √      |  √   |
| Repeatable Read(默认)——可重复读 |  ×   |     ×      |  √   |
|      Serializable——串行化       |  ×   |     ×      |  ×   |

- 用法

~~~sql
-- 查看事务的隔离级别
select @@transaction_isolation;

-- 设置事务隔离级别
set [session | global] transaction isolation level {Read uncommittd | Read committd | Repeatable Read | Serializable};			-- 注意：session：当前用户窗口有效; global：全局所有用户床口有效
~~~





# 二、进阶篇

## 1、存储引擎

### 1.1 mysql的体系结构

![mysql体系结构](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/mysql%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84.png)

- 连接层：最上层是一些客户端和链接服务，主要完成一些类似于连接处理、授权认证、及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限
- 服务层：第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等
- 引擎层：存储引擎真正的负责了mysql中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎
- 存储层：主要时将数据存储在文件系统之上，并完成与存储引擎的交互



### 1.2 存储引擎简介

- 存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式
- 存储引擎是基于表的，而不是基于库的
- 存储引擎也可被称为表类型
- 默认是：InnoDB

```SQL
-- 1、创建表时，指定存储引擎
create table 表名(
	字段1 类型1 [comment 字段1注释],
    ...
    字段n 类型n [comment 字段n注释]
) engine = InnoDB [comment 表注释];

-- 2、查看当前数据库支持的存储引擎
show engines;
```



### 1.3 存储引擎特点

#### 1.3.1 InnoDB

- 简介
  - 一种兼顾**高可靠性**和**高性能**的通用存储引擎，在MYSQL5.5之后，InnoDB是默认的MYSQL存储引擎
- 特点
  - DML操作遵循ACID模型，支持**事务**
  - **行级锁**，提高并发访问性能
  - **支持外键**FOREIGN KEY约束，保证数据的完整性和正确性
- 文件
  - xxx.ibd：xxx代表的是表名，InnoDB引擎的每张表都会对应这样一个表空间文件，存储该表的表结构(frm，sdi)、数据和索引
  - 参数：innodb_file_per_table

![InnoDB存储结构](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/InnoDB%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png)



#### 1.3.2 MyISAM

- 简介
  - MyISAM是MySQL早期的默认存储引擎
- 特点
  - 不支持事务，不支持外键
  - 支持表锁，不支持行锁
  - 访问速度快
- 文件
  - xxx.sdi：存储表结构信息
  - xxx.MYD：存储数据
  - xxx.MYI：存储索引



#### 1.3.3 Memory

- 简介
  - Memory引擎的表数据是存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用
- 特点
  - 内存存放
  - hash索引(默认)
- 文件
  - xxx.sdi：存储表结构信息



#### 1.3.4 区别

|     特点     | InnoDB | MyISAM | Memory |
| :----------: | :----: | :----: | :----: |
|   存储限制   |  64TB  |   有   |   有   |
| **事务安全** |  支持  |   -    |   -    |
|  **锁机制**  |  行锁  |  表锁  |  表锁  |
|  B+tree索引  |  支持  |  支持  |  支持  |
|   Hash索引   |   -    |   -    |  支持  |
|   全文索引   |  支持  |  支持  |   -    |
|   空间使用   |   高   |   低   |  N/A   |
|   内存使用   |   高   |   低   |  中等  |
| 批量插入速度 |   低   |   高   |   高   |
| **支持外键** |  支持  |   -    |   -    |



### 1.4 存储引擎选择

- InnoDB：**支持事务、外键**。如果应用对**事务的完整性**有比较高的要求，在**并发条件下要求数据的一致性**，数据操作除了插入和查询之外，还包含很多的更新、删除操作，就选InnoDB
- MyISAM：如果应用是**以读操作和插入操作为主，只有很少的更新和删除操作**，并且对事务的完整性、并发性要求不是很高，就选MyISAM
- Memory：将所有数据保存在内存中，访问速度快，**通常用于临时表及缓存**。Memory的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性





## 2、索引

### 2.1 概述

- 索引（index）是帮助MySQL**高效获取数据**的**数据结构（有序）**。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

![有无索引比较](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E6%9C%89%E6%97%A0%E7%B4%A2%E5%BC%95%E6%AF%94%E8%BE%83.png)

- 备注：上述二叉树索引结构的只是一个示意图
- 优缺点

|                            优势                             |                             劣势                             |
| :---------------------------------------------------------: | :----------------------------------------------------------: |
|           提高数据检索的效率，降低数据库的IO成本            |                    索引列也是要占用空间的                    |
| 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗 | 索引大大提高了查询效率，同时却也降低更新表的速度，如对表进行insert、update、delete时，也需要对索引维护，所以效率低 |



### 2.2 索引结构

- MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的结构
- 平时说的索引，如果没有特别指明，就是指**B+树结构组织的索引**

|      索引结构       |                             描述                             |     InnoDB      | MyISAM | Memory |
| :-----------------: | :----------------------------------------------------------: | :-------------: | :----: | :----: |
|     B+Tree索引      |          最常见的索引类型，大部分引擎都支持B+树索引          |      支持       |  支持  |  支持  |
|      Hash索引       | 底层数据结构是用哈希表实现的，只有精确匹配索引列的查询才有效，不支持范围查询 |     不支持      | 不支持 |  支持  |
|  R-tree(空间索引)   | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |     不支持      |  支持  | 不支持 |
| Full-text(全文索引) | 是一种通过建立倒排索引，快速匹配文档的方式。类似于Lucene，Solr，ES | 5.6版本之后支持 |  支持  | 不支持 |



### 2.3 BTree树

- 二叉树结构

![二叉树](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%BA%8C%E5%8F%89%E6%A0%91.png)

- B-Tree树

![B-Tree树](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/B-Tree%E6%A0%91.png)

- B+Tree树
  - 所有数据都会出现在叶子节点
  - 叶子节点会形成一个单向链表

![B+Tree树](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/B+Tree%E6%A0%91.png)

- Mysql的B+Tree树
  - 在原B+Tree的基础上，增加了一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree

![Mysql的B+Tree](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/Mysql%E7%9A%84B+Tree.png)





### 2.4 Hash索引

- 哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中
- 如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突，可以通过链表来解决

![hash索引](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/hash%E7%B4%A2%E5%BC%95.png)

- 特点
  - hash索引只能用于对等比较（=，in），不支持范围查询（between，>，<，...）
  - 无法利用索引完成排序操作
  - 查询效率高，通常只需要一次检索就可以了，效率通常要高于B+tree索引
- 存储引擎支持
  - 在MySQL中，支持hash索引的是Memory引擎
  - InnoDB中具有自适应hash功能
  - hash索引是存储引擎根据B+Tree索引在指定条件下自动构建的 



### 2.5 思考

- 为什么InnoDB存储引擎选择使用B+tree索引结构
  - 相对二叉树，层级更少，搜索效率高
  - 对于B-tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
  - 相对Hash索引，B+tree支持范围匹配及排序操作



### 2.6 索引分类

|   分类   |                         含义                         |           特点           |  关键字  |
| :------: | :--------------------------------------------------: | :----------------------: | :------: |
| 主键索引 |               针对于表中主键创建的索引               | 默认自动创建，只能有一个 | primary  |
| 唯一索引 |           避免同一个表中某数据列中的值重复           |        可以有多个        |  unique  |
| 常规索引 |                   快速定位特定数据                   |        可以有多个        |          |
| 全文索引 | 全文索引查找的是文本中的关键字，而不是比较索引中的值 |        可以有多个        | fulltext |

- 在InnoDB存储引擎中，根据索引的存储形式，又可以分为两种

|            分类             |                            含义                            |         特点         |
| :-------------------------: | :--------------------------------------------------------: | :------------------: |
| 聚集索引（Clustered Index） | 将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据 | 必须有，而且只有一个 |
| 二级索引（Secondary Index） | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 |     可以存在多个     |

- 聚集索引的选取规则

  - 如果存在主键，主键索引就是聚集索引
  - 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引
  - 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引

  ![聚集索引和二级索引](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E8%81%9A%E9%9B%86%E7%B4%A2%E5%BC%95%E5%92%8C%E4%BA%8C%E7%BA%A7%E7%B4%A2%E5%BC%95.png)

- 回表查询
  - 先根据名字通过二级索引查询出Arm的id值
  - 再根据id值通过聚集索引查询数据信息

![回表查询](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%9B%9E%E8%A1%A8%E6%9F%A5%E8%AF%A2.png)

- 思考题

```sql
-- 以下哪个语句执行效率高,注意：id为主键，name字段创建的有索引
select * from user where id = 10;
select * from user where name = 'arm';
-- 第一条效率高，第二条有回表查询
```



### 2.7 索引语法

- 创建索引

```sql
create [unique | fulltext] index index_name on table_name(index_col_nmae,...);
-- unique：创建的是唯一索引
-- fulltext：创建的是全局索引
-- 只在一列上创建的索引：单列索引
-- 在多列上创建的是：联合索引或组合索引·
```

- 查看索引

```sql
show index from table_name;
```

- 删除索引

```sql
drop index index_name on table_name;W
```

- 案例

```sql
-- 1、name字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);

-- 2、phone手机号字段的值，是非空，且唯一的，为该字段创建唯一索引
cretae unique index idx_user_phone on tb_user(phone);

-- 3、为profession、age、status创建联合索引
create index idx_user_pro_age_sta on tb_user(profession,age, status);

-- 4、为email建立合适的索引来提升查询效率
create index idx_user_emial on tb_user(email);
```



### 2.8 SQL性能分析

- SQL执行频率
  - MySQL客户端连接成功后，通过show [session | global] status命令可以提供服务器状态信息
  - 通过下面指令可以查看当前数据库的insert、update、delete、select的访问频次

```sql
-- 7个下划线
-- global：全局
-- session：当前会话
show global status like 'Com_______';
```

![SQL性能查询](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/SQL%E6%80%A7%E8%83%BD%E6%9F%A5%E8%AF%A2.png)

- 慢查询日志
  - 慢查询日志记录了所以执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有语句的日志
  - MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息
  - 通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息/var/lib/mysql/localhost-slow.log

```bash
# 开启MySQL慢日志查询开关
slow_query_log = 1
# 设置慢查询日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
```

```sql
-- 查看慢查询是否开启
show variables like 'slow_query_log';
```

- profile详情

  - show profiles能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have_profiling参数，能够看到当前MySQL是否支持profiles操作：

  - ```sql
    select @@have_profiling;
    ```

  - 默认profiling是关闭的，可以通过set语句在session/global 级别开启profiling：

  - ```sql
    set profiling = 1;
    ```

  - 执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时

  - ```sql
    -- 查看每一条SQL的耗时基本情况
    show profiles;
    -- 查看指令query_id的SQL语句各个阶段的耗时情况
    show profile for query query_id;
    -- 查看指定query_id的SQL语句CPU的使用情况
    show profile cpu for query query_id;
    ```

- explain执行计划

  - explain或者desc命令获取MySQL如何执行select语句的信息，包括了select语句执行过程中表如何连接和连接的顺序

  - ```sql
    -- 直接在select语句之前加上关键字explain/desc
    explain select 字段列表 from 表名 where 条件;
    ```

  - explain各字段的含义

    - id：select查询的序列号，表示查询中执行select子句或者是操作表的顺序（id相同，执行顺序从上到下；id不同，值越大，越先执行）
    - select_type：表示select的类型，常见的取值有：
      - simple（简单表，即不适用表连接或者子查询）
      - primary（主查询，即外层的查询）
      - union（union中的第二个或者后面的查询语句）
      - subquery（select/where之后包含了子查询）
    - **type**：表示连接类型，性能由好到差的连接类型为：null、system、const、eq_ref、ref、range、index、all
    - possible_key：显示可能应用在这张表上的索引，一个或多个
    - key：实际用到的索引，如果是null，则没有使用索引
    - key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
    - rows：MySQL认为必须要执行查询的行数，在InnoDB引擎的表中，是一个估计值，可能并不总是准确的
    - filtered：表示返回结果的行数占需读取行数的百分比，filtered的值越大越好



### 2.9 索引使用

#### 2.9.1 联合索引使用

- 最左前缀法则：
  - 如果索引了多列（联合索引），要遵守最左前缀法则
  - 查询从索引的最左列开始，并且不跳过索引中的列
  - 如果跳跃某一列，**索引将部分失效（后面的字段索引失效）**
  - 注意：只要where条件有即可，对前后顺序并没有固定要求

```sql
explain select * from tb_user where profession = '软件工程' and age = 31 and status = '0';
explain select * from tb_user where profession = '软件工程' and age = 31;
explain select * from tb_user where profession = '软件工程' and status = '0';
explain select * from tb_user where profession = '软件工程';
explain select * from tb_user where age = 31 and status = '0';
explain select * from tb_user where status = '0';
explain select * from tb_user where status = '0' and age = 31 and profession = '软件工程' ;
-- 第1条会直接用到联合索引，且索引字段长度为54
-- 第2条会直接用到联合索引，且索引字段长度为49，索引部分失效
-- 第3条会直接用到联合索引，且索引字段长度为47，索引部分失效
-- 第4条会直接用到联合索引，且索引字段长度为47，索引部分失效
-- 第5条不会用到索引，不满足最左前缀法则，不满足最左前缀法则
-- 第6条也不会用到索引，不满足最左前缀法则
-- 第7条会直接用到联合索引，且索引字段长度为54
```

- 在联合索引中，出现范围查询(>，<)，范围查询右侧的列索引失效
  - 所以，在业务允许的条件下，尽量使用>=或者<=可以规避

```sql
explain select * from tb_user where profession = '软件工程' and age > 30 and status = '0';
explain select * from tb_user where profession = '软件工程' and age >= 30 and status = '0';
-- 第1条会直接用到联合索引，且索引字段长度为49，status失效
-- 第2条会直接用到联合索引，且索引字段长度为54
```



#### 2.9.2 索引列

- 不要在索引列上进行运算操作，**索引将会失效**

```sql
explain select * from tb_user where substring(phone, 10, 2) = '15';
-- 索引失效了
```

- 字符串不加引号
  - 字符串类型字段使用时，不加引号，**索引将失效**

```sql
explain select * from tb_user where profession = '软件工程' and age = 31 and status = 0;
explain select * from tb_user where phone = 12345678900;
-- 第一条的status索引会失效
```



#### 2.9.3 模糊查询

- 如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效

```sql
explain select * from tb_user where profession like '软件%';
explain select * from tb_user where profession like '%工程';
explain select * from tb_user where profession like '%工%';
-- 只有第一个有效，后面两个索引会失效，变成全表扫描
```



#### 2.9.4 or连接

- 用or分隔开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么设计的索引都不会被用到
  - 为了规避这种情况，只需要在没有索引的列上创建索引即可 

```sql
explain select * from tb_user where id = 10 or age = 23;
explain select * from tb_user where phone = '12345678900' or age = 23;
-- 索引失效，因为age没有索引	
```

- 如果MySQL评估使用索引比全表更慢，则不适用索引

```sql
select * from tb_user where phone >= '17799990005';
select * from tb_user where phone >= '17799990015';
-- 因为查询语句会把表中大部分数据查询出来，所以走全表查询可能还快点，就不会走索引
```



#### 2.9.5 SQL提示

SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的

- use index：使用哪个索引（Mysql还是会自动判定谁更优，用更优的）

```sql
explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';
```

- ignore index：忽略哪个索引

```sql
explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';
```

- force index：必须使用哪个索引

```sql
explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';
```



#### 2.9.6 覆盖索引

- 尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到），减少select *

```sql
explain select id,profession from tb_user where profession = '软件工程' and age = 31 and status = '0';
explain select id,profession,age,status from tb_user where profession = '软件工程' and age = 31 and status = '0';
explain select id,profession,age,status,name from tb_user where profession = '软件工程' and age = 31 and status = '0';
explain select * from tb_user where profession = '软件工程' and age = 31 and status = '0';
```

- using index condition：查找使用了索引，但是需要回表查询数据
- using where; using index：查找使用了索引，但是需要的数据都在索引列中能找到，索引不需要回表查询

![覆盖索引](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E8%A6%86%E7%9B%96%E7%B4%A2%E5%BC%95.png)

- 第一条语句直接走的聚集索引，没有发生回表，故而效率高
- 第二条根据name查询，name索引下的值存放的就是id值，故而是覆盖索引，不需要回表
- 第三条需要查询gender，但是在索引中没有覆盖，故而需要用id值去走聚集索引，发生回表

```sql
-- 一张表，有四个字段（id，username，password，status），由于数据量大，需要对以下SQL语句进行优化，该如何优化才是最优方案
select id,username,password from tb_user where username = 'lzy';
-- 对username和password建立联合索引，这样节点下面存放的就是id值，达到覆盖索引的效果，不必发生回表查询
```



#### 2.9.7 前缀索引

- 当字段类型为字符串（varchar，text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率
- 语法

```sql
create index ids_xxxx on table_name(column(n));
```

- 前缀长度：可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的

```sql
select count(distinct email)/count(*) from tb_user;
select count(distinct substring(email,1,5))/count(*) from tb_user;
```

![前缀索引查询流程](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%89%8D%E7%BC%80%E7%B4%A2%E5%BC%95%E6%9F%A5%E8%AF%A2%E6%B5%81%E7%A8%8B.png)



#### 2.9.8 单列索引和联合索引

- 单列索引：即一个索引只包含单个列
- 联合索引：即一个索引包含了多个列
- 在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引
- **多条件联合索引查询时，MySQL优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询**

![联合索引](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95.png)



### 2.10 索引设计原则

- 针对于数据量较大，且查询比较频繁的表建立索引
- 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作得到字段建立索引
- 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
- 如果时字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引
- 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
- 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价就越大，会影响增删改查的效率
- 如果索引列不能存储NULL值，请在创建表时使用not null约束他。当优化器知道每列是否包含null值时，它可以更好的确定哪个索引最有效地用于查询





## 3、SQL优化

### 3.1 插入数据

- 批量插入：因为每次插入语句都会经过建立连接，数据交互等步骤，频繁插入会在这方面浪费很多性能，故而选择批量插入

```sql
insert into tb_user values(1,'tom'),(2,'cat'),(3,'Jerry');
```

- 手动提交事务：因为mysql的事务是自动提交的，所以每条语句执行都会开启事务关闭事务，频繁的开启关闭事务也会造成性能浪费，所以可以选择手动提交事务

```sql
start transaction;
insert into tb_user values(1,'tom'),(2,'cat'),(3,'Jerry');
insert into tb_user values(4,'tom'),(5,'cat'),(6,'Jerry');
insert into tb_user values(7,'tom'),(8,'cat'),(9,'Jerry');
commit;
```

- 主键顺序插入

```sql
-- 主键乱序插入：8 1 9 21 88 2 4 15 89 5 7 3
-- 主键顺序插入：1 2 3 4 5 7 8 9 15 21 88 89
```

- 大批量插入数据：如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入。**也需要顺序插入**。操作如下：

![load操作](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/load%E6%93%8D%E4%BD%9C.png)

```mysql
# 客户端连接服务端时，加上参数 --local-infile
mysql --local-infile -uroot -p
# 设置全局参数local-infile为1，开启从本地加载文件导入数据的开关
set global local-infile = 1
# 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '/n';
```



### 3.2 主键优化

- 数据组织方式：在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为**索引组织表 **
- 页分裂：页可以为空，也可以填充一半，也可以填充100%。每个页包含了2~N行数据（如果一行数据多大，会行溢出），根据主键排列
- 主键顺序插入

![主键顺序插入](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E9%94%AE%E9%A1%BA%E5%BA%8F%E6%8F%92%E5%85%A5.png)

- 主键乱序插入

![主键乱序插入](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E9%94%AE%E4%B9%B1%E5%BA%8F%E6%8F%92%E5%85%A5.png)

- 页分裂

  - 第一个页百分之50的地方进行分裂，放到第三个新开辟的页中
  - 然后50放到47后，组成第三页
  - 然后更换指针指向

  ![页分裂](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E9%A1%B5%E5%88%86%E8%A3%82.png)

  ​

- 页合并
  - 当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用
  - 当页中删除的记录达到MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间使用
  - MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或者创建索引时指定

![页合并](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E9%A1%B5%E5%90%88%E5%B9%B6.png)

- 主键设计原则
  - 满足业务需求的情况下，尽量降低主键的长度
    - 因为二级索引存放的是主键，太长会造成空间浪费并且查询时浪费IO
  - 插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键
  - 尽量不要使用UUID做主键或者其他自然主键，如身份证号
  - 业务操作时，避免对主键的修改





### 3.3 order by优化

- Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都交FileSort排序
- Using index：通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高

```sql
-- 没有创建索引时，根据age、phone进行排序
explain select id,age,phone from tb_user order by age,phone;		-- using filesort
-- 创建索引
create index idx_user_age_phone_aa on table tb_user(age,phone);		
-- 创建索引之后，根据age，phone进行升序排序	
explain select id,age,phone from tb_user order by age,phone;		-- using index
-- 创建索引之后，根据age，phone进行降序排序
explain select id,age,phone from tb_user order by age desc,phone desc;		--Backward index scan; Using index :逆向扫描索引
explain select id,age,phone from tb_user order by phone,age;		-- Using index;Using filesort：因为违背了最左前缀法则，order by要看顺序的
explain select id,age,phone from tb_user order by age asc,phone desc;    -- Using index;Using filesort：两者要保持一致的，不能一个正向扫描，一个逆向扫描
-- 再创建一个索引
create index idx_user_age_phone_ad on table tb_user(age asc,phone desc);
-- 创建索引之后
explain select id,age,phone from tb_user order by age asc,phone desc;    -- Using index
```

- order by优化方式
  - 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
  - 尽量使用覆盖索引
  - 多字段排序，一个升序一个降序，此时需要注意联合索引再创建时的规则（asc/desc）
  - 如果不可避免的出现了filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size(默认256K)。因为一旦超过这个量，就会跑到磁盘中进行排序，很浪费性能



### 3.4 group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足最左前缀法则

```sql
explain select profession,count(*) from tb_user group by profession;
-- Using temporary：使用临时表，效率低
create index idx_user_pro_age_sta on table tb_user(profession,age,status);
-- 创建索引
explain select profession,count(*) from tb_user group by profession;
-- Using index：使用索引，效率高
explain select age,count(*) from tb_user group by age; 
-- Using index;Using temporary：因为不满足最左前缀法则，故而效率低
explain select age,count(*) from tb_user group by age; 
-- Using index;Using temporary：因为不满足最左前缀法则，故而效率低
explain select profession,age,count(*) from tb_user group by progession,age; 
-- Using index;满足最左前缀法则，故而效率高
explain select age,count(*) from tb_user where profession = '软件工程' group by age; 
-- Using index;满足最左前缀法则，故而效率高
```



### 3.5 limit优化

- 一个常见的问题：limit 2000000,10，此时需要MySQL排序前2000010记录，仅仅返回2000000~2000010的记录，其他记录丢弃，查询排序的代价非常大
- 优化思路：
  - 一般分页查询时，通过创建覆盖索引能够较好地提高性能，可以通过覆盖索引子查询形式进行优化

```sql
select * from tb_user s,(select id from tb_user order by id limit 9000000,10) a where s.id = a.id;
```



### 3.6 count优化

- MyISAM引擎把一个表的总行数存在了磁盘上，因此执行count(*)的时候会直接返回这个数，效率很高
- InnoDB引擎：执行count(*)的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。
- 优化思路：**自己计数**
- count的几种用法：count()是一个聚合函数，对于返回的结果集，一行行的判断，如果count函数的参数不是null，累计值就加1，否则不加，最后返回累计值
  - count(*)：InnoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加
  - count(主键)：InnoDB引擎会遍历整张表，把每一行的主键id值都取出来，返回给服务层。服务层拿到主键后，直接按行进行累加（主键不可能为null）
  - count(字段)：
    - 没有not null约束：InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加
    - 有not null约束：InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按进行累加
  - count(1)：InnoDB引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1”进去，直接按行进行累加
- **按照效率排序：count(字段) < count(主键id) < count(1) < count(*)，所以尽量使用count(*)**



### 3.7 update优化

- InnoDB的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级到表锁，影响并发效率

```sql
select student set no = '2000100100' where id = 1;
select student set no = '2000100105' where name = 'lzy';
-- 如果同时开启两个客户端连接，在事务中执行，第一条根据主键id进行修改，只会锁住这行数据，其他事务可以对其他行数据进行操作；但是根据name进行修改的话，不是索引项，就会锁住整张表，其他事务就不能对表进行数据操作
```





## 4、视图

### 4.1 概述

- 介绍：
  - 视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的
  - 通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的work就落在创建这条SQL查询语句上
- 创建

```sql
create [or replace] view 视图名称[(列名列表)] as select语句 [with [cascaded | local] check option]
-- or replace:替换
```

- 查询

```sql
-- 查看创建视图语句
show create view 视图名称;
-- 查看视图数据
select * from 视图名称 ....;
```

- 修改

```sql
-- 方式一
create [or replace] view 视图名称[(列名列表)] as select语句 [with [cascaded | local] check option]
-- or replace:替换
-- 方式二
alter view 视图名称[(列名列表)] as select语句 [with [cascaded | local] check option]
```

- 删除

```sql
drop view [if exists] 视图名称 [视图名称] ...
```

- 案例

```sql
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;

-- 查看创建视图语句
show create view stu_v_1;

-- 查询视图
select * from stu_v_1;
select * from stu_v_1 where id < 3;

-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 20;
alter view stu_v_1 as select id,name,no from student where id <= 20;
 
-- 删除视图
drop view if exists stu_v_1;
```



### 4.2 视图的检查选项

- 当使用with check option子句创建视图时，MySQL会通过视图检查正在更改的每个行
- 例如：插入、更新、删除，以使其符合视图的定义
- MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。
- 为了确定检查的范围，MySQL提供了两个选项，默认是cascaded
  - cascaded：不仅会检查当前的视图，还会检查他基于的视图，即使嵌套的视图没有检查选项，依然会把本视图的检查选项带入到下面
  - local：也会嵌套的去检查上面的视图，但是上面的视图如果有，就检查，没有则不检查
- cascade的案例

```sql
-- 案例1：此视图添加id为30的数据不会报错，依然会成功
create view v1 as select id,name from student where id <= 20;
-- 案例2：基于v1创建的视图v2，加上了cascaded，添加数据，不仅要符合>=10，也要符合v1的要求<=20，相当于给v1加了一个 with cascaded check option
create view v2 as select id,name from v1 where id >= 10 with cascaded check option;
-- 案例3：基于v2创建的视图v3，即使不加检查选项，但是添加数据的时候，会去寻找到嵌套下的v2，然后v2必须满足检查项，v1也需要满足with cascaded check option
create view v3 as select id,name from v2 where id >= 15;
```

- local的案例

```sql
-- 案例1：此视图添加id为30的数据不会报错，依然会成功
create view v1 as select id,name from student where id <= 20;
-- 案例2：基于v1创建的视图v2，加上了local，只需要满足v2的>=10,v1没有检查项，不用管
create view v2 as select id,name from v1 where id >= 10 with local check option;
-- 案例3：基于v2创建的视图v3，即使不加检查选项，但是添加数据的时候，会去寻找到嵌套下的v2，然后v2必须满足检查项
create view v3 as select id,name from v2 where id >= 15;
```



### 4.3 更新

- 要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则视图不可更新
  - 聚合函数或窗口函数：sum()、min()、max()、count()等
  - distinct
  - group by
  - having
  - union或者union all



### 4.4 作用

- 简单：
  - 视图可以简化用户对数据的理解，也可以简化操作。
  - 经常使用的查询可以被定义为视图，从而是的用户不必为以后的操作每次指定全部的条件
- 安全
  - 数据库可以授权，但不能授权到数据库特定行和特定列上。通过视图用户只能查询和修改他们所能见到的数据
- 数据独立
  - 视图可帮助用户屏蔽真实表结构变化带来的影响



## 5、存储过程

### 5.1 概述

- 介绍
  - 存储过程是事先经过编译并存储在数据库中的一段SQL语句的集合
  - 调用存储过程可以简化应用开发人员的很多work，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的
  - 存储过程的思想：就是数据库SQL语言层面的代码封装和重用
- 特点
  - 封装、复用
  - 可以接收参数，也可以返回数据
  - 减少网络交互，效率提升



### 5.2 操作

- 创建

```sql
create procedure 存储过程名称([参数列表])
begin
	-- SQL语句
end;
```

- 调用

```sql
call 名称([参数]);
```

- 查看

```sql
select * from information_schema.routines where routine_schema = 'xxx';  -- 查询指定数据库的存储过程及状态信息
show create procedure 存储过程名称;     -- 查询某个存储过程的定义
```

- 删除

```sql
drop procedure [if exists] 存储过程名称;
```

- 案例

```sql
-- 创建一个存储过程p1
create procedure p1()
begin
	select * from user;
end;

-- 调用存储过程p1
call p1();

-- 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'demo';
show create procedure p1;

-- 删除
drop procedure if exists p1;
```

- 注意：可以用delimiter设置SQL语句的结束语

```sql
delimiter $$;
-- 现在执行语句后面就必须加上$$
select * from user;$$
```



### 5.3 变量

#### 5.3.1 系统变量

- **系统变量**：MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（**GLOBAL**）、会话变量（**SESSION**）
- 查看系统变量

```sql
show [session | global] variables;   -- 查看所有系统变量
show [session | global] variables like '...';   -- 通过like模糊匹配查找系统变量
select @@[session | global]系统变量名;       -- 查看指定变量的值
```

- 设置系统变量

```sql
set [session | global] 系统变量名 = 值;
set @@[session | global] 系统变量名 = 值;
```

- 案例

```sql
-- 查看所有系统变量
show variables;
-- 查看有关事务提交的变量
show session variables like 'auto%';
-- 查找具体的系统变量
select @@autocommit;
select @@global.autocommit;

-- 设置系统变量
set session autocommit = 0;
set @@global.autocommit = 0;
```

- 注意
  - **如果没有指定session/global，默认值是session，会话变量**
  - **mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在/etc/my.cnf中配置**



#### 5.3.2 用户自定义变量

- 用户自定义变量：是用户根据自己需要定义的变量，用户变量不用提前声明，在用的时候直接用“@变量名”使用就可以。
- 作用域：当前连接
- 赋值

```sql
set @var_name = expr [,@var_name = expr] ...;
set @var_name := expr [,@var_name := expr] ...;

-- 或者
select @var_name := expr [,@var_name := expr] ...;
select 字段名 into @var_name from 表名;
```

- 使用

```sql
select @var_name;	
```

- 案例

```sql
-- 赋值
set @myname = 'lzy';
set @myage := 18;
set @mysex := '男',@myhobby := 'java';

select @mycolor := 'red';
select count(*) into @mycount from user;

-- 使用
select @myname,@myage,@mysex,@myhobby;
select @mycount;
```

- 注意：**用户定义的变量无需对其进行声明或初始化，只不过获取到的值是NULL**





#### 5.3.3 局部变量

- 局部变量：根据需要定义的在局部生效的变量
- 访问之前，需要declare声明
- 可以用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的begin ... end块

- 声明：变量类型就是数据库字段类型：int、bigint、char、varchar、date、time等

```sql
declare 变量名 变量类型 [default ...];
```

- 赋值

```sql
set 变量名 = 值;
set 变量名 := 值;
select 字段 into 变量名 from 表名 ...;
```

- 案例

```sql
-- 声明、赋值
create procedure p2()
begin
	declare user_count int default = 0;
	select count(*) into user_count from user;
	select user_count;
end;

call p2();
```



### 5.4 if条件判断

- 语法

```sql
if 条件1 then
	...
elseif 条件2 then			-- 可选
	...
else
	...
end if;
```

- 案例

```mysql
-- 根据定义的分数score，判定当前分数对应的分数等级
-- score >= 85，优秀
-- score >=60 且 score < 85，及格
-- score < 60，不及格
create procedure p3()
begin
	declare score int default 58;
	declare result varchar(10);
	if score >= 85 then
		set result = '优秀';
	elseif score >=60 then
		set result = '及格';
	else 
		set result = '不及格';
	end if;
	select result;
end;
```



### 5.5 存储过程参数

| 类型  |                     含义                     | 备注 |
| :---: | :------------------------------------------: | :--: |
|  in   |   该类参数作为输入，也就是需要调用时传入值   | 默认 |
|  out  | 该类参数作为输出，也就是该参数可以作为返回值 |      |
| inout |    既可以作为输入参数，也可以作为输出参数    |      |

- 语法

```sql
create procedure 存储过程名称([in/out/inout 参数名 参数类型])
begin
	-- sql语句
end;
```

- 案例

```sql
-- 案例1
-- 根据传入的分数score，判定当前分数对应的分数等级，并返回
-- score >= 85，优秀
-- score >=60 且 score < 85，及格
-- score < 60，不及格
create procedure p4(in score int, out result varchar(10))
begin
	if score >= 85 then
		set result = '优秀';
	elseif score >=60 then
		set result = '及格';
	else 
		set result = '不及格';
	end if;
end;
-- 调用
call p4(99, @result);


-- 案例2
-- 将传入的200分制的分数，进行换算，换算百分制，然后返回分数
create procedure p5(inout score double)
begin
	set score := score/2;
end;
-- 调用
set @score = 198;
select @score;
```



### 5.6 case语法

- 语法1

```sql
case case_value
	when when_value1 then statement_list1
	[when when_value2 then statement_list2]
	...
	[else statement_list]
end case;
```

- 语法2

```sql
case
	when search_condition1 then statement_list1 
	[when search_condition2 then statement_list2]
	...
	[else statement_list]
end case;
```

- 案例

```sql
/*
 * 根据传入的月份，判定月份所属的计阶（要求采用case结构）
 * 1-3月份，第一季度
 * 4-6月份，第二季度
 * 7-9月份，第三季度
 * 10-12月份，第四季度
 */ 
 create procedure p6(in month int)
 begin
 	declare result varchar(10);
 	case 
 		when month >= 1 and month <= 3 then
 			set result := '第一季度';
 		when month >= 4 and month <= 6 then
 			set result := '第二季度';
 		when month >= 7 and month <= 9 then
 			set result := '第三季度';
 		when month >= 10 and month <= 12 then
 			set result := '第四季度';
 		else
 			set result := '非法参数';
 	end case;
 	select concat('所属的季度是：', result);
 end;
```



### 5.7 循环

#### 5.7.1 while

- while循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。具体语法为

```sql
-- 先判定条件，如果条件为ture，则执行逻辑，否则，不执行逻辑
while 条件 do
	SQL逻辑...;
end whilel
```

- 案例

```sql
-- 计算从1累加到n的值，n为传入的参数值
create procedure p7(in n int)
begin
	declare result int default 0;
	while n >= 1 do
		set result = result + n;
		set n = n - 1;
	end while;
	select result;
end;

-- 执行
call p7(3);
```



#### 5.7.2 repeat

- repeat是有条件的循环控制语句，**当满足条件的时候退出循环**。具体语法

```sql
-- 先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出；不满足，则继续下一次循环
repeat
	SQL逻辑;
	until 条件
end repeat;
```

- 案例

```sql
-- 计算从1累加到n的值，n为传入的参数值
create procedure p8(in n int)
begin
	declare result int default 0;
	repeat
		set result = result + n;
		set n = n - 1;
		until n <= 0
	end repeat;
	select result;
end;

-- 执行
call p8(3);
```



#### 5.7.3 loop

- loop实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环
  - leave：配合循环使用，退出循环
  - iterate：必须用在循环中，作用就是跳过当前循环剩下的语句，直接进入下一次循环

```sql
[begin_label:] loop
	SQL逻辑...
end loop [end_label];

leave label;  			-- 退出指定标记的循环体
iterate label;			-- 直接进入下一次循环
```

- 案例

```sql
-- 计算从1累加到n的值，n为传入的参数值
create procedure p9(in n int)
begin
	declare result int default 0;
	sum:loop
		if n <=0 then
			leave sum;
		end if;
		set result = result + n;
		set n = n-1;
	end loop sum;
	select result;
end;

-- 计算1到n的偶数相加
create procedure p10(in n int)
begin
	declare result int default 0;
	sum:loop
		if n <=0 then
			leave sum;
		end if;
		if n%2 = 1 then
			set n = n-1;
			iterate sum;
		end if;
		set result = result + n;
		set n = n-1;
	end loop sum;
	select result;
end;
```



### 5.8 游标

- 游标：是用来存储和查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。
- 游标的使用包括游标的声明、open、fetch和close
- 声明游标

```sql
declare 游标名称 cursor for 查询语句;
```

- 打开游标

```sql
open 游标名称;
```

- 获取游标记录

```sql
fetch 游标名称 into 变量[,变量];
```

- 关闭游标

```sql
close 游标名称;
```

- 条件处理程序：可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤

```sql
declare handler_action handler for condition_vaule [,condition_vaule] ... statement;

handler_action
	continue: 继续执行当前程序
	exit: 中止执行当前程序
condition_vaule
	SQLSTATE sqlstate_value: 状态码，如02000
	SQLWARNING: 所有以01开头的SQLSTATE代码的简写
	NOT FOUND: 所有以02开头的SQLSTATE代码的简写
	SQLEXCEPTION: 所有没有被SQLWARNING或NOT FOUND捕获的SQLSTATE代码的简写
```

- 案例

```sql
-- 根据传入的uage，来查询用户表tb_user中，所有用户年龄小于等于uage的用户姓名（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表中（id，name，profession）
-- A、声明游标，存储查询结果集
-- B、准备：创建表结构
-- C、开启游标
-- D、获取游标中的记录
-- E、插入数据到新表中
-- F、关闭游标
create procedure p12(in uage int)
begin
	declare uname varchar(100);
	declare uaddress varchar(100);
	declare u_cursor cursor for select name,address from user where age <= uage;
	declare exit handler for SQLSTATE '02000' close u_cursor;
	
	drop table if exists tb_user_add;
	create table if not exists tb_user_add(
    	id int primary key auto_increment,
        name varchar(100),
        address varchar(100)
    );
    
    open u_cursor;
    while true do
    	fetch u_cursor into uname,uaddress;
    	insert into tb_user_add values(null, uname, uaddress);
    end while;
    close u_cursor;
end;

-- 或者
create procedure p12(in uage int)
begin
	declare uname varchar(100);
	declare uaddress varchar(100);
	declare u_cursor cursor for select name,address from user where age <= uage;
	declare exit handler for not found close u_cursor;
	
	drop table if exists tb_user_add;
	create table if not exists tb_user_add(
    	id int primary key auto_increment,
        name varchar(100),
        address varchar(100)
    );
    
    open u_cursor;
    while true do
    	fetch u_cursor into uname,uaddress;
    	insert into tb_user_add values(null, uname, uaddress);
    end while;
    close u_cursor;
end;
```



### 5.9 存储函数

- 存储函数是有返回值的存储过程，存储函数的参数只能是in类型的。具体语法

```sql
create function 存储函数名称([参数列表])
returns type [characteristic ...]
begin
	-- SQL语句
	return ...;
end;

-- characteristic说明
-- deterministic：相同的输入参数总是产生相同的结果
-- no sql：不包含sql语句
-- reads sql data：包含读取数据的语句，但不包含写入数据的语句
```

- 案例

```sql
-- 从1到n的累加
create function fun1(n int)
returns int deterministic
begin
	declare total int default 0;
	while n > 0 do
		set total = total + n;
		set n = n - 1;
	end while;
	return total;
end;
-- 调用
select fun1(3);
```



## 6、触发器

### 6.1 介绍

- 触发器是与表有关的数据库对象，指在insert/update/delete之前或之后，触发并执行触发器中定义的SQL语句集合
- 触发器的这种特殊性可以协助应用哦个在数据库端确保数据的完整性，日志记录，数据校验等操作
- 使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的
- 现在的触发器还只支持行级触发，不支持语句级触发

|  触发器类型  |                       NEW和OLD                       |
| :----------: | :--------------------------------------------------: |
| insert触发器 |            new表示将要或者已经新增的数据             |
| update触发器 | old表示修改之前的数据，new表示将要或已经修改后的数据 |
| delete触发器 |            old表示将要或者已经删除的数据             |



### 6.2 语法

- 创建

```sql
create trigger trigger_name
before/after insert/update/delete
on table_name for each row 		-- 行级触发器
begin
	trigger_stmt;
end;
```

- 查看

```sql
show triggers;
```

- 删除

```sql
drop trigger [schema_name.]trigger_name;		-- 如果没有指定schema_name，默认为当前数据库
```



### 6.3 案例

- 通过触发器记录user表的数据变更日志，将变更日志插入到日志表中user_logs中，包含增加、修改、删除

```sql
create table user_logs(
	id int(11) not null auto_increment,
    operation varchar(20) not null comment '操作类型,insert/update/delete', 
    operate_time datetime not null comment '操作时间',
    operate_id int(11) not null comment '操作的id',
    operate_params varchar(500) comment '操作参数',
    primary key(id)
) engine=innodb default charset=utf8;
```

- 插入数据触发器

```sql
create trigger user_insert_trigger
	after insert on user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) values(null, 'insert', now(), new.sno, concat('插入的数据内容为：id=', new.sno, ',name=', new.name, ',age=', new.age, ',sex=', new.sex, ',hobby=', new.hobby, ',address=', new.address));
end;

-- 插入测试
insert into user values('10004','lx',24,'女','asdas','湖北');
```

- 修改数据触发器

```sql
create trigger user_update_trigger
	after update on user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) values(null, 'update', now(), new.sno, concat('修改前的数据内容为：id=', old.sno, ',name=', old.name, ',age=', old.age, ',sex=', old.sex, ',hobby=', old.hobby, ',address=', old.address, ' | ', '修改后的数据内容为：id=', new.sno, ',name=', new.name, ',age=', new.age, ',sex=', new.sex, ',hobby=', new.hobby, ',address=', new.address));
end;

-- 修改测试
update user set name = 'lyx' where sno = '10004';
```

- 删除数据触发器

```sql
create trigger user_delete_trigger
	after delete on user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) values(null, 'delete', now(), old.sno, concat('删除前的数据内容为：id=', old.sno, ',name=', old.name, ',age=', old.age, ',sex=', old.sex, ',hobby=', old.hobby, ',address=', old.address));
end;

-- 修改测试
delete from user where sno = '10004';

-- 若删除所有的数据，则会生成多条日志记录，因为这是行级触发器，没条行数据变化的时候都会触发
delete from user;
```



## 7、锁

### 7.1 概述

- 锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂
- MySQL中的锁，按照锁的粒度分，分为以下三类
  - 全局锁：锁定数据库中的所有表
  - 表级锁：每次操作锁住整张表
  - 行级锁：每次操作锁住对应的行数据



### 7.2 全局锁

#### 7.2.1 介绍

- 全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都会被阻塞
- 其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性

![全局锁](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%85%A8%E5%B1%80%E9%94%81.png)

```sql
-- 加上全局锁
flush tables with read lock;

-- 备份
mysqldump -用户名 -密码 数据库 > 存放到哪个sql文件中;

-- 释放锁
unlock tables;
```



#### 7.2.2 演示

- 客户端1

```sql
-- 加上全局锁
flush tables with read lock;
```

- 客户端2

```sql
select * from user;				-- 可以正常查询数据
delete from user where sno = '10004';       -- 删除会阻塞
```

- 客户端3

```sql
-- 数据备份
mysqldump -h 192.168.233.234 -uroot -plzy123456 demo > D:/demo.sql;
```

- 客户端1

```sql
-- 释放锁
unlock tables;
```

- 客户端2

```sql
delete from user where sno = '10004';       -- 删除不会阻塞
```



#### 7.2.3 特点

- 数据库加全局锁，是一个比较重的操作，存在以下问题：
  - 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆
  - 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟
- 在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份

```sql
-- 备份
mysqldump --single-transaction -用户名 -密码 数据库 > 存放到哪个sql文件中;
```



### 7.3 表级锁

#### 7.3.1 表锁

- 表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中

- 对于表级锁，主要分为以下三类

  - 表锁
  - 元数据锁（meta data lock，MDL）
  - 意向锁

- 对于表锁，分为两类

  - 表共享读锁（read lock）：当前客户端可以读，但是不可以写；其他客户端也可以读，但是不可写

  ![表锁读操作](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E8%A1%A8%E9%94%81%E8%AF%BB%E6%93%8D%E4%BD%9C.png)

  - 表独占写锁（write lock）：当前客户端可读也可写；其他客户端不可读也不可写

  ![表锁写操作](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E8%A1%A8%E9%94%81%E5%86%99%E6%93%8D%E4%BD%9C.png)

- 语法

```sql
-- 加锁
lock tables 表名... read/write;
-- 释放锁
unlock tables / 客户端断开连接	
```

- 客户端1

```sql
-- 给user表加上锁
lock tables user read;

select * from user;			-- 查询可以正常
update user set name = 'las' where sno = '10003';			-- 更新不可用，因为加上了锁

-- 释放锁
unlock tables：
```

- 客户端2

```sql
select * from user;			-- 查询可以正常
update user set name = 'las' where sno = '10003';			-- 更新不可用，因为加上了锁
```



#### 7.3.2 元数据锁

- MDL加锁过程是系统自动控制，无需显示使用，在访问一张表的时候会自动加上。
- MDL锁主要作用是维护表**元数据（表结构）**的数据一致性，在表上有活动事务的时候，不可以对**元数据（表结构）**进行写入操作
- **为了避免DML和DDL冲突，保证读写的正确性**
- 在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁（共享）；当对表结构进行变更操作的时候，加MDL写锁（排他）
- 查看元数据锁

```sql
select object_type, object_schema, object_name, lock_type, lock_duration from performance_schema.metadata_locks; 
```

|                    对应SQL                    |                 锁类型                  |                       说明                       |
| :-------------------------------------------: | :-------------------------------------: | :----------------------------------------------: |
|          lock tables xxx read/write           | SHARED_READ_ONLY / SHARED_NO_READ_WRITE |                                                  |
|    select 、select ... lock in share mode     |               SHARED_READ               | 与SHARED_READ、SHARED_WRITE兼容，与EXCLUSIVE互斥 |
| insert、update、delete、select ... for update |              SHARED_WRITE               | 与SHARED_READ、SHARED_WRITE兼容，与EXCLUSIVE互斥 |
|                alter table ...                |                EXCLUSIVE                |                与其他的MDL都互斥                 |

- 客户端1

```sql
-- 开启事务
begin;

-- 使用select语句，会自动添加SHARED_READ锁
select * from user;

-- 提交事务
commit;
```

- 客户端2

```sql
-- 开启事务
begin;

-- 第一个事务没提交，可以查询，修改、新增等，因为SHARED_WRITE和SHARED_READ不互斥
select * from user;
-- 但是不能使用alter，因为SHARED_READ和SHARED_READ互斥，此时会阻塞
alter table user add column score int;

-- 提交事务
commit;
```



#### 7.3.3 意向锁

- 为了避免DML在执行时，加的行锁和表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查
- 意向锁分为两类
  - 意向共享锁（IS）：由语句select ... lock in share mode添加，与表锁共享锁（read）兼容，与表锁排他锁（write）互斥
  - 意向排他锁（IX）：由insert、update、delete、select ... for update添加，与表锁共享锁（read）及排他锁（write）都互斥。意向锁之间不会互斥
- 根据以下SQL可以查询意向锁及行锁的加锁情况

```sql
select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.metadata_locks; 
```

![意向锁](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E8%BF%9B%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E6%84%8F%E5%90%91%E9%94%81.png)

- 演示客户端1

```sql
-- 开启事务
begin;

-- 查询语句加锁
select * from user where sno = '10001' lock in share mode;
```

- 客户端2

```sql
select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.metadata_locks; 
```



### 7.4 行级锁

#### 7.4.1 介绍

- 行级锁：每次操作锁住对应的行数据。锁度粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中
- InnoDB的数据是基于索引组成的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁。
- 行级锁主要分类
  - 行锁：锁定单个行记录的锁，防止其他事务对此进行update和delete。在RC、RR隔离级别下都支持
  - 间隙锁：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离界别下都支持
  - 临键锁：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持



#### 7.4.2 行锁

- InnoDB实现了以下两种类型的行锁
  - 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁
  - 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁

|        | 共享锁 | 排他锁 |
| :----: | :----: | :----: |
| 共享锁 |  兼容  |  冲突  |
| 排他锁 |  冲突  |  冲突  |

|              SQL              |  行锁类型  |                   说明                   |
| :---------------------------: | :--------: | :--------------------------------------: |
|            insert             |   排他锁   |                 自动加锁                 |
|            update             |   排他锁   |                 自动加锁                 |
|            delete             |   排他锁   |                 自动加锁                 |
|            select             | 不加任何锁 |                                          |
| select ... lock in share mode |   排他锁   | 需要手动在select之后加lock in share mode |
|     select .. for update      |   排他锁   |     需要手动在select之后加for update     |





# 三、高级篇

## 1、日志

### 1.1 错误日志

- 错误日志是MySQL中最重要的日志之一，它记录了当mysqld启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，建议首先查看此日志
- 该日志是默认开启的，默认存放目录/var/log/，默认的日志文件名mysqld.log。该日志位置

```mysql
show variables like '%log_error%';
```

- 可以通过以下方式进行错误日志查看，并且定位到出错位置

![错误日志的存放位置](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E9%94%99%E8%AF%AF%E6%97%A5%E5%BF%97%E7%9A%84%E5%AD%98%E6%94%BE%E4%BD%8D%E7%BD%AE.png)

```bash
# 查看错误日志位置
show variables like '%log_error%';

# 查看该日志文件下的50行日志
tail -50 /var/log/mysqld.log

# 对日志进行实时监听，如果出现错误，那么日志文件会刷新并输出错误信息，我们便可根据此来定位错误
tail -f /var/log/mysqld.log
```



### 1.2 二进制日志

- 二进制日志（BINLOG）记录了所有的DDL（数据定义语言）语句和DML（数据操纵语言）语句，但不包括数据查询（select、show）语句
- 作用
  - 灾难时的数据恢复
  - MySQL的主从复制。在MySQL8版本中，默认二进制日志是开启的，涉及到的参数如下

```sql
show variables like '%log_bin%';
-- log_bin：二进制日志默认开启
-- log_bin_basename：二进制日志存放目录及前缀，默认从000000开始自增
-- log_bin_index：二进制日志文件索引文件
```

![二进制日志参数](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%BA%8C%E8%BF%9B%E5%88%B6%E6%97%A5%E5%BF%97%E5%8F%82%E6%95%B0.png)

- 日志格式

| 日志格式  |                             含义                             |
| :-------: | :----------------------------------------------------------: |
| statement | 基于SQL语句的日志记录，记录的是SQL语句，对数据进行修改的SQL都会记录在此日志文件中 |
|    row    |       基于行的日志记录，记录的是每一行的数据变更(默认)       |
|   mixed   | 混合了statement和row两种格式，默认采用了statement，在某些特殊情况下会自动切换为row进行记录 |

```sql
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
```

- 日志删除：对于比较繁忙的业务系统，每天生成的binlog数据巨大，如果长时间不清理，将会占用大量的磁盘空间，可以通过以下几种方式清理日志

|                       指令                       |                             含义                             |
| :----------------------------------------------: | :----------------------------------------------------------: |
|                   reset master                   | 删除全部binlog日志，删除之后，日志编号，将从binlog.000001重新开始 |
|       purge master logs to 'binlog.******'       |                 删除******编号之前的所有日志                 |
| purge master logs before 'yyyy-mm-dd hh24:mi:ss' |     删除日志为“yyyy-mm-dd hh24:mi:ss”之前产生的所有日志      |

```mysql
# 删除binlog.000002之前的所有日志
purge master logs to 'binlog.000002';

# 删除所有日志
reset master;

-- 设置二进制日志的过期时间，时间过期后会自动删除，默认30天
show variables like '%binlog_expire_logs_seconds%';
```



### 1.3 查询日志

- 查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。
- 默认情况下，查询日志是未开启的。需要开启的话，可以通过以下代码进行查看

```sql
-- 配置日志
show variables like '%general%';
```

- 通过以下方式修改查询日志

```bash
# 修改MySQL的配置文件/etc/my.cnf文件，添加如下内容
general_log=1
# 该选项是用来开启查询日志的，1代表开启，0代表关闭
general_log_file=mysql_query.log
# 设置日志的文件名，如果没有指定，默认的文件名为host_name.log
```



### 1.4 慢查询日志

- 慢查询日志记录了所有执行时间超过参数long_query_time设置值并且扫描记录数不小于min_examined_row_limit的所有的SQL语句的日志，默认未开启。long_query_time默认为10秒，最小为0，精度可以到微秒

```bash
# /etc/my.cnf里面开启，慢查询日志，1为开启慢查询日志
slow_query_log=1
# 执行时间参数
long_query_time=2
```

- 默认情况下，不会记录管理语句，也不会记录不适用索引进行查找的查询。可以使用如下两个参数进行修改

```bash
# 记录执行较慢的管理语句
log_slow_admin_statements=1
# 记录执行较慢的未使用索引的语句
log_queries_not_using_indexes=1
```





## 2、主从复制

### 2.1 概述

- 主从复制是指将主数据库的DDL（数据定义语言）语句和DML（数据操纵语言）语句操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步
- MySQL支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制
- 好处
  - 主库出现问题，可以快速切换到从库提供服务
  - 实现读写分离，降低主库的访问压力
  - 可以在从库中执行备份，以避免备份期间影响主库服务

![主从复制](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6.png)



### 2.2 原理

![主从复制原理](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6%E5%8E%9F%E7%90%86.png)

- Master主库在事务提交时，会把数据变更记录在二进制日志文件binlog中
- 从库读取主库的二进制日志文件binlog，写入到从库的中继日志Relay log中
- slave重做中继日志中的事件，将改变反映它自己的数据



### 2.3 搭建

#### 2.3.1 服务器准备

- 准备好两台服务器，并都在上面安装好mysql，并把端口设置好

![主从复制服务器准备](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%87%86%E5%A4%87.png)

```bash
# 开放指定的3306端口号
firewall-cmd --zone=public --add-port=3306/tcp --permanent
# 重启防火墙
firewall-cmd -reload

# 关闭服务器的防火墙
systemctl stop firewalld
systemctl disable firewalld
```



#### 2.3.2 主库配置

- 修改配置文件/etc/my,cnf

```bash
# mysql服务ID，保证整个集群环境中唯一，取值范围：1~2的32次方-1，默认为1
server-id=1
# 是否只读，1代表只读，0代表读写
read-only=0
# 忽略的数据，指不需要同步的数据库
#binlog-ignore-db=mysql
# 指定同步的数据库
#binlog-do-db=db01
```

- 重启服务器

```bash
systemctl restart mysqld
```

- 登录mysql，创建远程连接的账号，并授予主从复制权限

```mysql
# 创建lzy用户，并设置密码，该用户可在任意主机连接该MySQL服务
create user 'lzy'@'%' identified with mysql_native_password by 'lzy123456';
# 为'lzy'@'%'用户分配主从复制权限
grant replication slave on *.* to 'lzy'@'%';
```

- 通过指令，查看二进制日志坐标

```mysql
show master status;
-- file：从哪个日志文件开始推送日志文件
-- position：从哪个位置开始推送日志
-- binlog_ignore_db：指定不需要同步的数据库 
```



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

```bash
systemctl restart mysqld
```

- 登录mysql，设置主库配置

```mysql
change replication source to source_host='xxx.xxx', source_user='xxx', source_password='xxx', source_log_file='xxx', source_log_pos=xxx;

-- 例如，从binlog.000004的663位置开始复制
change replication source to source_host='192.168.200.200', source_user='lzy', source_password='lzy123456', source_log_file='binlog.000004', source_log_pos=663;
```

- 开启同步操作

```myysql
start replica;
```

- 查看主从同步状态

```mysql
show replica status/G;
```

![主从复制是否正常](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6%E6%98%AF%E5%90%A6%E6%AD%A3%E5%B8%B8.png)



#### 2.3.4 测试

直接在主库中进行增删改查，在从库中查看数据是否同步过来了



## 3、分库分表

### 3.1 介绍

- 单数据库存在的性能缺陷
  - IO瓶颈：热点数据太多，数据库缓存不足，产生大量的磁盘IO，效率较低。请求数据太多，带宽不够，网络IO瓶颈
  - CPU瓶颈：排序、分组、连接查询、聚合统计等SQL会耗费大量的CPU资源，请求数太多，CPU出现瓶颈
- **分库分表的中心思想就是将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能问题，从而达到提升数据库性能的目的**

![单数据库体系问题](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%88%86%E5%BA%93%E5%88%86%E8%A1%A8.png)



### 3.2 拆分策略

- 拆分策略
  - 分库：将一个数据库的数据存放在多个数据库中
  - 分表：将一个表的数据分散的存放在多个表中

![拆分策略](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E6%8B%86%E5%88%86%E7%AD%96%E7%95%A5.png)

- 垂直拆分

  - 垂直分库：以表为依据，根据业务将不同表拆分到不同库中
    - 每个库的表结构都不一样
    - 每个库的数据也不一样
    - 所有库的并集是全量数据

  ![垂直分库](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%9E%82%E7%9B%B4%E5%88%86%E5%BA%93.png)

  - 垂直分表：以字段为依据，根据字段属性将不同字段拆分到不同表中
    - 每个表的结构都不一样
    - 每个表的数据也不一样，一般通过一列（主键/外键）关联
    - 所有表的并集是全量数据

  ![分库分表](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E5%9E%82%E7%9B%B4%E5%88%86%E8%A1%A8.png)

  ​

  ​

- 水平拆分

  - 水平分库：以字段为依据，按照一定策略，将一个库的数据拆分到多个库中
    - 每个库的表结构都一样
    - 每个库的数据都不一样
    - 所有库的并集是全量数据

  ![水平分库](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E6%B0%B4%E5%B9%B3%E5%88%86%E5%BA%93.png)

  - 水平分表：以字段为依据，按照一定策略，将一个表的数据拆分到多个表中
    - 每个表的结构都一样
    - 每个表的数据都一样
    - 所有表的并集是全量数据

  ![水平分表](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/%E6%B0%B4%E5%B9%B3%E5%88%86%E8%A1%A8.png)

- 实现技术

  - shardingJDBC：基于AOP原理，在应用程序中对本地执行的SQL进行拦截，解析、改写、路由处理。需要自行编码配置实现，只支持java语言，性能较高
  - MyCat：数据库分库分表中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及前者

![MyCat实现技术](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/MyCat%E5%AE%9E%E7%8E%B0%E6%8A%80%E6%9C%AF.png)



### 3.3 MyCat

#### 3.3.1 MyCat概述

- MyCat是开源的、活跃的、基于Java语言编写的MySQL**数据库中间件**。可以像使用mysql一样来使用mycat，对于开发人员来说根本感受不到mycat的存在
- 优点
  - 性能可靠稳定
  - 强大的技术团队
  - 体系完善
  - 社区活跃

![MyCat](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/MyCat.png)



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

![MyCat概念图](D:/knowledge/knowledge/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL/MySQL%E9%AB%98%E9%98%B6%E7%AF%87%E5%9B%BE%E7%89%87/MyCat%E6%A6%82%E5%BF%B5%E5%9B%BE.png)

