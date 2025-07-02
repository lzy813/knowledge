# 一、基础篇

## 1、Mysql概述

|      名称      |                             全称                             |                简称                |
| :------------: | :----------------------------------------------------------: | :--------------------------------: |
|     数据库     |            存储数据的仓库，数据是有组织的进行存储            |           DataBase（DB）           |
| 数据库管理系统 |                  操纵和管理数据库的大型软件                  | DataBase Management System（DBMS） |
|      SQL       | 操作关系型数据库的编程语言，定义了一套操作关系型数据库统一**标准** |  Structured Query Language（SQL）  |

![Mysql概述](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\Mysql概述.png)

## 2、Mysql的前置准备

### 2.1 Mysql的下载与安装

- MySQL 下载地址为：https://dev.mysql.com/downloads/windows/installer/，这里下载的是mysql的msi安装文件，选择下面的470.2M的版本，点击download进行下载。

![1](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\1.png)

- 在下载页面直接选择：**No thanks, just start my download.进行下载**

![1](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\2.png)

- 下载好mysql的msi安装文件后，双击进行安装，选择最下面的Custom模式安装，点击右下角 Next 到下一步

![1](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\3.png)

- 选择MySql Server 8.0.27 移动到右侧，点击Next

![4](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\4.png)

- 点击Execute，检查必要配置

![5](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\5.png)

- 检查过程中，可能会提示要安装微软文件，这时安装即可；等到后面的status提示的状态完成后，点击Next进行下一步

![6](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\6.png)

- 然后，点击Execute执行

![7](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\7.png)

- 此时会进行下载文件

![8](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\8.png)

- 全部下载完成后，Status提示状态Complete表示已完成，此时点击Next

![9](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\9.png)

- 此时到下一步再点击Next

![10](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\10.png)

- 再 Type and NetWorking 页面中，Port是mysql数据库的默认端口3306，直接点击Next

![11](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\11.png)

- 在认证方式页面有两个选项，强密码认证和以前的认证方式，我这里选择的是强密码认证，然后Next

![12](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\12.png)

- 然后输入root用户的密码，密码一定要记住，否则无法登录。

![12](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\13.png)

- 检查mysql的服务，建议使用默认不修改，点击Next

![14](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\14.png)

- 在Apply Configuration 点击Execute执行

![15](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\15.png)

- 应用配置完成后，点击Finish继续

![16](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\16.png)

- 在Product Configuration 中，status提示状态完成后，点击Next继续

![17](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\17.png)

- 最后一步，提示安装完成，点击Finish结束

![18](C:\Users\lzy\Desktop\work\数据库\MySQL\Windows下载安装\18.png)



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

![mysql数据模型](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\mysql数据模型.png)



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

​	

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
| or 或 \|\| | 或者(多个条件任意一个成立) |
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

![执行顺序](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\执行顺序.png)



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

![外键约束](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\外键约束.png)

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

![一对多](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\一对多.png)

- 多对多
  - 案例：学生与课程的关系
  - 关系：一个学生可以选修多门课程，一门课程也可以供多个学生选择
  - 实现：**建立第三张中间表，中间表至少包括两个外键，分别关联两方主键**

![多对多](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\多对多.png)

- 一对一

  - 案例：用户与用户详情的关系
  - 关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
  - 实现：**在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的**

  ![一对一](C:\Users\lzy\Desktop\work\数据库\MySQL\MySQL基础篇图片\一对一.png)

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

