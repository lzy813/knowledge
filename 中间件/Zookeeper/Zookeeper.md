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
  - 单集群状态


![单集群启动成功](图片/简介/单集群启动成功.png)





# 二、zookeeper命令操作

## 1、zookeeper数据模型

- ZooKeeper 是一个<font color="red">**树形目录服务**</font>，其数据模型和 Unix 的文件系统目录树很类似，拥有一个层次化结构。
- 这里面的每一个节点都被称为：ZNode，<font color="red">**每个节点上都会保存自己的数据和节点信息。**</font>
- 节点可以拥有子节点，同时也允许少量（1MB）数据存储在该节点之下。
- 节点可以分为四大类：
  - PERSISTENT 持久化节点
  - EPHEMERAL 临时节点：-e
  - PERSISTENT_SEQUENTIAL 持久化顺序节点：-s
  - EPHEMERAL_SEQUENTIAL 临时顺序节点：-es

![数据模型](图片/命令操作/数据模型.png)



## 2、服务端常用命令

![服务端命令](图片/命令操作/服务端命令.png)

- 启动 ZooKeeper 服务：./zkServer.sh start

![start](图片/命令操作/start.png)

- 查看 ZooKeeper 服务状态：./zkServer.sh status

![status](图片/命令操作/status.png)

- 停止 ZooKeeper 服务：./zkServer.sh stop

![stop](图片/命令操作/stop.png)

- 重启 ZooKeeper 服务：./zkServer.sh restart

![restart](图片/命令操作/restart.png)





## 3、客户端命令

![客户端命令](图片/命令操作/客户端命令.png)

- 连接 Zookeeper服务端：./zkCli.sh -server ip:port
  - 如果是本机，后面的ip和端口可以省略不写


![连接服务端](图片/命令操作/连接服务端.png)

- 断开连接：quit

![quit](图片/命令操作/quit.png)

- 查看指定节点下有哪些子节点：ls 节点路径

![quit](图片/命令操作/ls.png)

- 创建节点包含数据：create 节点路径 [数据]

![create](图片/命令操作/create.png)

- 创建子节点：create 节点路径 [数据]

![创建子节点](图片/命令操作/创建子节点.png)

- 获取节点数据：get 节点路径

![get](图片/命令操作/get.png)

- 设置节点数据：set 节点路径 [数据]

![set](图片/命令操作/set.png)

- 删除节点：delete 节点路径

![delete](图片/命令操作/delete.png)



## 4、客户端非持久化命令

![客户端非持久化命令](图片/命令操作/客户端命令2.png)

- 创建临时节点：create -e /节点path value

![临时节点](图片/命令操作/临时节点.png)

- 创建顺序节点：create -s /节点path value

![顺序节点](图片/命令操作/顺序节点.png)

- 创建临时的顺序节点：create -es /节点path value

![非持久化顺序节点](图片/命令操作/非持久化顺序节点.png)

- 查询节点详细信息：ls -s /节点path

![打印详细信息](图片/命令操作/打印详细信息.png)



# 三、Java API操作

## 1、Curator

### 1.1 Curator介绍

- Curator 是 Apache ZooKeeper 的 Java 客户端库。
- 常见的 ZooKeeper Java API：
  - 原生 Java API
  - ZkClient
  - Curator
- Curator 项目的目标是简化 ZooKeeper 客户端的使用。
- Curator 最初是 Netflix 研发的，后来捐献了 Apache 基金会，目前是 Apache 的顶级项目。
- 官网：http://curator.apache.org/



### 1.2 常用API操作

- Curator API 常用操作

  - 建立连接

  - 添加节点

  - 删除节点

  - 修改节点

  - 查询节点

  - Watch 事件监听

  - 分布式锁实现



### 1.3 工程代码

- pom代码

~~~pom
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>zookeeperDemo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!--curator-->
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>4.0.0</version>
        </dependency>
        <!--日志-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.21</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.21</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
~~~

- log4j.properties

~~~properties
log4j.rootLogger=off,stdout

log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%d{yyyy-MM-dd HH/:mm/:ss}]%-5p %c(line/:%L) %x-%m%n
~~~



#### 1.3.1 建立连接

~~~java
	private CuratorFramework client;

    /**
     * 建立连接
     */
    @Before
    public void testConnect() {
//        // 1.第一种方式
//        // 重试策略：间隔时间重试
//        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000,10);
//        /**
//         * @param connectString         连接字符串。zk server 地址和端口 "192.168.149.135:2181,192.168.149.136:2181"
//         * @param sessionTimeoutMs      会话超时时间 单位ms
//         * @param connectionTimeoutMs   连接超时时间 单位ms
//         * @param retryPolicy           重试策略
//         */
//        CuratorFramework client = CuratorFrameworkFactory.newClient("111.231.33.58:2181",
//                60 * 1000, 15 * 1000, retryPolicy);
//        // 开启连接
//        client.start();


        // 2.第二种方式
        // 重试策略：间隔时间重试
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
        client = CuratorFrameworkFactory.builder()
                .connectString("111.231.33.58:2181")
                .sessionTimeoutMs(60 * 1000)
                .connectionTimeoutMs(15 * 1000)
                .retryPolicy(retryPolicy)
                .namespace("lzy")
                .build();
        //开启连接
        client.start();
    }

    @After
    public void close() {
        if (client != null) {
            client.close();
        }
    }
~~~



#### 1.3.2 创建节点

~~~java

~~~







# 四、zookeeper集群

## 1、集群介绍

- Leader选举
  - ServiceId：服务器ID
    - 比如有三台服务器，编号分别是1、2、3，编号越大在选举算法中权重越大
  - Zxid：数据ID
    - 服务器中存放的最大数据ID，值越大说明数据越新，在选举算法中数据越新权重越大
  - 在Leader选举过程中，如果某台zookeeper获得了超过半数的选票，则此zookeeper就可以成为leader了

![打印详细信息](图片/集群/集群介绍.png)