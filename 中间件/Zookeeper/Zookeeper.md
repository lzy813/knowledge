# 一、简介

## 1、是什么

- zookeeper是Apache公司Hadoop项目下的一个子项目，是一个<font color="red">**树形目录结构**</font>
- zookeeper翻译过来就是动物园管理员，用来管Hadoop（大象）、Hive（蜜蜂）、Pig（小猪）的管理员，简称zk
- zookeeper是一个<font color="red">**分布式的、开源的分布式应用程序的协调服务**</font>
- zookeeper提供的主要功能包括
  - 配置管理
  - 分布式锁
  - 集群管理



## 2、下载地址

- 环境准备：zookeeper服务器是用Java创建的，它运行在JVM之上。需要安装JDK 7或更高版本。
- 官网下载地址：https://zookeeper.apache.org/releases.html
- 首先，需要下载zookeeper，点击链接打开zookeeper官网[Apache zookeeper](https://zookeeper.apache.org/)，在官网首页点击Getting Started下面的Download进入下载页面

![安装位置1](图片/简介/安装位置1.png)

- 选择下载最新的稳定版本

![安装位置2](图片/简介/安装位置2.png)

- 然后点击任意链接开始下载zookeeper的压缩包

![安装位置3](图片/简介/安装位置3.png)



## 3、jdk安装

~~~bash
# 创建目录，将jdk安装包上传
mkdir/usr/java
cd /usr/java

# 解压
tar -zxvf jdk-8u121-linux-x64.tar.gz
~~~

- 修改/etc/profile，在结尾添加

~~~bash
export JAVA_HOME=/usr/java/jdk1.8.0_121
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin
~~~

- 让修改生效：source /etc/profile
- 验证JDK有效性：java -version

~~~bash
java version "1.8.0_121"
Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
~~~



## 4、zookeeper安装

- 将下载的zookeeper放到/opt/zookeeper目录下
- tar -zxvf apache-zookeeper-3.8.4-bin.tar.gz
- 进入到conf目录拷贝一个zoo_sample.cfg并完成配置

~~~bash
#进入到conf目录
cd /opt/zookeeper/apache-zookeeper-3.8.4-bin/conf/
#拷贝
cp  zoo_sample.cfg  zoo.cfg
~~~

- 修改zoo.cfg

~~~bash
# 修改存储目录
dataDir=/opt/zookeeper/zkdata

# 默认是8080，因为8080可能被web应用使用，Springboot项目中tomcat的默认启动端口就是8080
admin.serverPort=9099
 
# 添加这个配置才能使用create -t ttl /node_name
extendedTypesEnabled=true
~~~

- 创建存储目录：mkdir  zkdata

- 启动zooKeeper：
  - 切换到zookeeper安装目录，通过以下命令之一启动zookeeper服务器：
    - cd /opt/zookeeper/apache-zookeeper-3.8.4-bin/bin
    - **./zkServer.sh start**
  - 如果配置文件名不是zoo.cfg，需要指定配置文件名：**./zkServer.sh start conf/配置文件名.cfg**

![启动成功](图片/简介/启动成功.png)

- 查看zookeeper状态：**./zkServer.sh status**

![单集群启动成功](图片/简介/单集群启动成功.png)





# 二、zookeeper命令操作

