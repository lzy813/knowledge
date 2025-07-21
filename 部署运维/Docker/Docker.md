# 一、简介

## 1、为什么会出现docker

![docker出现前后对比](图片\基础知识\docker出现前后对比.png)

- 出现之前
  - 开发提交src代码，在本地可以运行，但是在运维部署服务器上环境可能导致不一致，就会发生启动不了的情况
  - 安装麻烦，比如代码所需环境很多，传统意义上保持一致需要安装大量的环境，很是繁琐
  - 扩容、缩容问题。比如现在两个服务无法满足要求，要立即扩容成四个服务器，传统方式必定无法满足
- 出现之后
  - 将所有的源码、配置、环境等直接打包成一个镜像文件，然后放置到docker中
  - 无需管代码所需环境，只需要一步生成整个文件运行，运行扩容等都方便
- 类推举例
  - 以前搬家，都是把家具、衣服啥的都打包带到新房子里，中途肯定会导致很多东西丢失或者新房子装不下啥的
  - 现在就直接搬楼，把整个房子搬走，这样就不用一点点打包东西搬运，然后重新整理房间啥的，也不怕东西丢失或装不下




## 2、docker优点

- 针对上述问题，docker给了一个标准解决方案——**系统平滑移植，容器虚拟化技术**
- **docker打破了过去程序即应用的观念。通过镜像(images)将作业系统核心除外，运作应用程序所需要的系统环境，由下而上打包，达到应用程序跨平台间的无缝接轨运作**


![镜像文件的作用](图片\基础知识\镜像文件的作用.png)



## 3、docker理念

![docker理念](图片\基础知识\docker理念.png)

- 跟linux虚拟机差不多，在VMware上面运行centos7.iso镜像，就可以保证环境全部迁移
- **类比过来，docker就是就是把软件代码以及运行环境啥的全部打包成镜像文件，然后交给docker运行，就可以实现一步启动项目**
- 在不同的docker上运行同一个镜像，还是能达到相同的效果
- 对比
  - VMware —> docker
  - centos7.iso镜像 —> redis镜像文件




## 4、docker是什么

- **docker是基于go语言实现的云开源项目**
- docker的主要目标：通过对应组件的封装、分发、部署、运行等生命周期的管理，使用户的APP（可以是一个web应用或数据库应用等）及其与逆行环境能够做到——**一次镜像，处处运行**
- **Linux容器技术的出现就解决了这样一个问题，而docker就是在它的基础上发展过来的**
- 将应用打成镜像，通过镜像成为运行在docker容器上的实例。而docker容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。
- **只需要一次配置好环境，换到别的机子上就可以一键部署好，大大简化了操作**




## 5、总结

- **解决了运行环境和配置问题的软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术**




## 6、容器和传统虚拟机比较

### 6.1 虚拟机

- **虚拟机就是带环境安装的一种解决方案**
- 它可以在一种操作系统里面运行另一种操作系统，比如在win10系统里面运行Linux的系统Centos7.
- **应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删除，对其他部分毫无影响。**
- 这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变

![传统虚拟机](图片\基础知识\传统虚拟机.png)

- 缺点：
  - **资源占用多**：因为他模拟了从内核、硬件、软件的所有资源，很大
  - **冗余步骤多**：有些东西是用不到的，比如镜像1只需要Mysql，镜像2只需要tomcat，所有都放在一个镜像就很多冗余
  - **启动慢**：启动要很多时间




### 6.2 容器

- Linux容器是与系统其他部分隔离开的一系列进程，从另一个镜像运行，并由该镜像提供支持进程所需的全部文件。**容器提供的镜像包含了应用的所有依赖项，因而在从开发到测试再到生产的整个过程中，它都具有可移植性和一致性**
- **Linux容器不是模拟一个完整的操作系统而是对进程进行隔离**。有了容器，就可以将软件运行所需要的所有资源打包到一个隔离的容器中。**容器和虚拟机不同，不需要捆绑一整套操作系统，只需要软件工作所需要的库资源和设置**。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行

![容器](图片\基础知识\容器.png)



### 6.3 比较总结

- 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程
- 容器内的应用程序直接运行于宿主的内核（复用本地主机的操作系统），**容器内没有自己的内核且没有进行硬件虚拟**。因此容器要比传统虚拟机更为轻便
- 每个容器之间互相隔离，每个容器都有自己的文件系统，容器之间进程不会互相影响，能区分计算资源




## 7、docker解决哪些问题

- 优势
  - **更快速的应用交付和部署**
    - 传统的应用开发完成后，需要提供一堆安装程序和配置文档说明，安装部署后需要根据配置文档进行复杂的配置才能正常运行。docker化之后只需要交付少量容器镜像文件，在正式生产环境加载镜像并运行即可，应用安装配置在镜像里已经内置好，大大节省部署配置和测试验证时间
  - **更快捷的升级和扩缩容**
    - 随着微服务架构和docker的发展，大量的应用会通过微服务方式架构，应用的开发构建将变成搭积木的方式，每个docker容器将变成一块积木，应用的升级就会变得非常容易。当现有的容器不足以支撑业务处理时，可通过镜像运行新的容器进行快速扩容，使应用系统的扩容从原先的天极变成分钟级甚至秒级
  - **更简单的系统运维**
    - 应用容器化运行后，生产环境运行的应用可与开发、测试环境的应用高度一致，容器会将应用程序相关的环境和状态完全封装起来，不会因为底层基础架构和操作系统的不一致性给应用带来影响，产生新的bug。当出现程序异常时，也可以通过测试环境的相同容器进行快速定位和修复
  - **更高效的计算资源利用**
    - **docker是内核级虚拟化**，不像传统的虚拟化技术一样需要额外的Hypervisor支持，所以在一台物理机上可以运行很多个容器实例，大大提升物理服务器的CPU和内存的利用率





# 二、入门

## 1、三要素

### 1.1 镜像

- Docker镜像（Image）就是一个**只读**的模板。镜像可以用来创建Docker容器，**一个镜像可以创建很多容器**
- **docker镜像文件类似于Java的类模板，而docker容器实例类似于Java中new出来的实例对象**




### 1.2 容器

- 从面向对象角度
  - Docker利用容器（Container）独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，**容器是用镜像创建的运行实例**。就像是Java中的类和实例对象一样，镜像是静态的定义，容器时镜像运行时的实体。容器位镜像提供了一个标准的和隔离的运行环境，它可被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台
- 从镜像容器角度
  - **可以把容器看作是一个简易版的Linxu环境**（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序




### 1.3 仓库

- **仓库（Repository）是集中存放镜像文件的场所**
- 类似于
  - maven仓库：存放各种jar包的地方
  - github仓库：存放各种git项目的地方
  - docker hub：docker公司提供的官方仓库，存放各种镜像模板的地方
- 仓库分为公开仓库（Public）和私有仓库（Private）两种形式
- **最大的公开仓库是Docker hub**，存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等




### 1.4 总结

- docker本身是一个容器运行载体或称之为管理引擎。把应用程序和配置依赖打包好成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成docker容器实例
- image文件可以看作是容器的模板。docker根据image文件生成容器的实例。同一个iamge文件，可以生成多个同时运行的容器实例
- 镜像文件
  - **把应用程序和配置依赖打包好成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件**，镜像文件可以运行成一个服务实例
- 容器实例
  - 一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的实例
- 仓库
  - 就是放一堆镜像文件的地方，我们可以把镜像文件发布到仓库中，需要的时候再从仓库中拉出来就行

![三要素](图片\基础知识\三要素.png)



## 2、docker运行图解（入门版）

![docker工作原理](图片\基础知识\docker工作原理.png)

- docker是一个Client-Server结构的系统
- **docker守护进程运行在主机上，然后通过Socket连接客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器**
- 客户端发送请求，守护进程接收，如果本机有对应的镜像文件，就直接操作，没有就从远程上拉取




## 3、docker架构

- Docker 是一个 C/S 模式的架构，后端是一个**松耦合架构，众多模块各司其职**
- Docker运行的基本流程为
  - 用户是使用Docker Client与Docker Daemon建立通信，并发送请求给后者
  - Docker Daemon作为Docker架构中的主体部分，首先提供Docker Server的功能使其可以接受Docker Client的请求
  - Docker Engine执行Docker内部的一系列工作，每一项工作都是以一个Job的形式的存在
  - Job的运行过程中，当需要容器镜像时，则从Docker Registry中下载镜像，并通过镜像管理驱动Graph driver将下载镜像以 Graph 形式存储
  - 当需要为Docker创建网络环境时，通过网络管理驱动Network driver创建并配置Docker容器网络环境。
  - 当需要限制Docker容器运行资源或执行用户指令等操作时，则通过Exec driver来完成
  - Libcontainer是一项独立的容器管理包，Network driverl以及Exec driver都是通过Libcontainer来实现具体对容器进行的操作

![docker架构](图片\基础知识\docker架构.png)





## 4、在ubuntu上安装docker

- 首先，更新软件包索引，并且安装必要的依赖软件，来添加一个新的 HTTPS 软件源

~~~bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
~~~

- 使用下面的 curl 导入源仓库的 GPG key

~~~bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
~~~

- 将 Docker APT 软件源添加到你的系统

~~~bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
~~~

- 现在，Docker 软件源被启用了，你可以安装软件源中任何可用的 Docker 版本

  - 想要安装 Docker 最新版本，运行下面的命令。如果你想安装指定版本，跳过这个步骤，并且跳到下一步

  ~~~bash
  sudo apt update
  sudo apt install docker-ce docker-ce-cli containerd.io
  ~~~

  - 想要安装指定版本，首先列出 Docker 软件源中所有可用的版本，可用的 Docker 版本将会在第二列显示，例子：`5:19.03.9~3-0~ubuntu-focal`

  ~~~bash
  sudo apt update
  apt list -a docker-ce
  # 可用的 Docker 版本将会在第二列显示
  docker-ce/focal 5:19.03.9~3-0~ubuntu-focal amd64
  # 通过在软件包名后面添加版本=<VERSION>来安装指定版本
  sudo apt install docker-ce=<VERSION> docker-ce-cli=<VERSION> containerd.io
  ~~~

- 一旦安装完成，Docker 服务将会自动启动。你可以输入下面的命令，验证它

  ~~~bash
  sudo systemctl status docker
  ~~~

- 输出将会类似下面这样

~~~bash
docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-05-21 14:47:34 UTC; 42s ago
...
~~~

- 当一个新的 Docker 发布时，你可以使用标准的`sudo apt update && sudo apt upgrade`流程来升级 Docker 软件包
- 如果你想阻止 Docker 自动更新，锁住它的版本

~~~bash
sudo apt-mark hold docker-ce
~~~

- 默认情况下，只有 root 或者 有 sudo 权限的用户可以执行 Docker 命令
  - 想要以非 root 用户执行 Docker 命令，你需要将你的用户添加到 Docker 用户组，该用户组在 Docker CE 软件包安装过程中被创建。想要这么做，输入

~~~bash
# $USER是一个环境变量，代表当前用户名
sudo usermod -aG docker $USER
~~~

- 验证安装过程
  - 如果本地没有该镜像，这个命令将会下载测试镜像，在容器中运行它，打印出 “Hello from Docker”，并且退出

~~~bash
docker container run hello-world
~~~



## 5、配置阿里云加速器

- 注册一个阿里云账号：<https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors>
- 输入命令配置

~~~bash
# 第一步，输入以下指令
sudo mkdir -p /etc/docker
# 第二步，复制你的加速网址，然后输入以下指令
sudo tee /etc/docker/daemon.json <<-'EOF' { "registry-mirrors": ["替换"] } EOF
# 例如：
sudo tee /etc/docker/daemon.json <<-'EOF' 
{ 
	"registry-mirrors": ["https://mirror.ccs.tencentyun.com"] 
} 
EOF



sudo tee /etc/docker/daemon.json <<-'EOF'
{
	"registry-mirrors": [
        "https://do.nark.eu.org",
        "https://dc.j8.work",
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
	]
}
EOF



# 第三步，输入以下指令重新启动服务
sudo systemctl daemon-reload
sudo systemctl restart docker
~~~



## 6、docker run的流程

![docker run的工作流程](图片\基础知识\docker run的工作流程.png)



## 7、docker为什么比虚拟机快

- **docker有着比虚拟机更少的抽象层**
  - 由于docker不需要Hypervisor(虚拟机)实现硬件资源虚拟化，运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势
- **docker利用的是宿主机的内核，而不需要加载操作系统OS内核**
  - 当新建一个容器时，docker不需要和虚拟机一样重新加载一个操作系统内核。进而避免引寻、加载操作系统内核返回等比较费时费资源的过程，当新建一个虚拟机时，虚拟机软件需要加载OS，返回新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统，则省略了返回过程，因此新建一个docker容器只需要几秒钟

![docker和虚拟机比较](图片\基础知识\docker和虚拟机比较.png)



## 8、

~~~bash
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils

# Step 2: 添加软件源信息
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum软件包索引
yum makecache fast

# Step 3: 安装Docker
sudo yum install docker-ce docker-ce-cli containerd.io

# Step 4: 开启Docker服务
sudo service docker start

# 注意：
# 官方软件源默认启用了最新的软件，您可以通过编辑软件源的方式获取各个版本的软件包。例如官方并没有将测试版本的软件源置为可用，您可以通过以下方式开启。同理可以开启各种测试版本等。
# vim /etc/yum.repos.d/docker-ce.repo
#   将[docker-ce-test]下方的enabled=0修改为enabled=1
#
# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# yum list docker-ce.x86_64 --showduplicates | sort -r
#   Loading mirror speeds from cached hostfile
#   Loaded plugins: branch, fastestmirror, langpacks
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable
#   docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable
#   Available Packages
# Step2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.0.ce.1-1.el7.centos)
# sudo yum -y install docker-ce-[VERSION]

~~~





# 三、常用命令

## 1、帮助启动类命令

~~~bash
# 启动docker
systemctl start docker
# 停止docker
systemctl stop docker
# 重启docker
systemctl restart docker
# 查看docker状态
systemctl status docker
# 开机启动
systemctl enable docker
# 查看docker概要信息
docker info
# 查看docker总体帮助文档
docker --help
# 查看docker命令帮助文档
docker 具体命令 -help
~~~



## 2、镜像命令

### 2.1 docker images

- 作用：**列出本地主机上的镜像**
- options说明
  - -a：列出本地所有镜像（含历史映像层）
  - -q：只显示镜像ID
- 结果说明
  - REPOSITORY：表示镜像的仓库源
  - TAG：镜像的标签版本号
  - IMAGE ID：镜像ID
  - CREATED：镜像创建时间
  - SIZE：镜像大小

![docker images](图片\命令\docker images.png)

- 同一个仓库源可以有多个TAG版本，代表这个仓库源的不同个版本，我们使用REPOSITORY:TAG来定义不同的镜像。
- 如果不指定一个镜像的版本标签，例如使用ubuntu，docker默认使用ubuntu:latest镜像（最新版）





### 2.2 docker search

- 作用：**查看远程库中是否有这个镜像**
- 用法：docker search [options] 镜像名字
- options
  - **--limit：只列出N个镜像，默认25个**。如：docker search --limit 5 redis
- 结果说明
  - NAME：镜像名称
  - DESCRIPTION：镜像说明
  - STARS：点赞数量
  - OFFICIAL：是否是官方的
  - AUTOMATED：是否是自动构建的

![docker search](图片\命令\docker search.png)



### 2.3 docker pull

- 作用：**下载镜像**
- 用法：
  - docker pull 镜像名字[:tag]      							——指定版本下载
  - docker pull 镜像名字 == docker pull 镜像名字:lateset          ——下载最新版本

![docker pull](图片\命令\docker pull.png)



### 2.4 docker system df

- 作用：**查看镜像/容器/数据卷所占的空间**

![docker system df](图片\命令\docker system df.png)



### 2.5 docker rmi

- 作用：**删除某个XXX镜像名字ID**
- 用法：
  - 删除单个：docker rmi -f 镜像ID
  - 删除多个：docker rmi -f 镜像名1:TAG 镜像名2:TAG
  - 删除全部：docker rmi -f $(docker images -qa)
- options:
  - **--f：强制删除**

![docker rmi](图片\命令\docker rmi.png)

- 删除所有

![docker rmi -qa](图片\命令\docker rmi -qa.png)



### 2.6 docker的虚悬镜像

- 仓库名、标签都是<none>的镜像，俗称虚悬镜像dangling image

![虚悬镜像](图片\命令\虚悬镜像.png)



## 3、容器命令

### 3.1 docker run

- 作用：**启动交互式容器（前台命令行）**


- 用法：docker run [options] images \[COMMAN\][ARG...]
- options
  - 第一组
    - **--name=容器名称：**为容器指定一个名称
    - **-d：**后台运行容器并返回容器ID，也即启动守护式容器(后台运行)
  - 第二组
    - **-i：以交互模式运行容器，通常与-t同时使用**
    - **-t：为容器重新分配一个伪输入终端，通常与-i同时使用**
    - **也即启动交互式容器（前台有伪终端，等待交互）**
  - 第三组
    - -P：**随机**端口映射，大写P
    - -p：**指定**端口映射，小写p

![docker run](图片\命令\docker run.png)

- 例子
  - docker run -it ubuntu /bin/bash
  - 使用镜像ubuntu以**交互模式**启动一个容器，在容器内执行/bin/bash命令
  - 参数说明
    - -i：交互式操作
    - -t：终端
    - ubuntu：ubuntu镜像
    - /bin/bash：放在镜像名后的命令，这里希望有一个交互式shell，因此用的是/bin/bash
  - 要退出终端，直接输入exit



### 3.2 docker ps

- 作用：**列出当前所有正在运行的容器**


- 用法：docker ps [options]
- options
  - **--a：列出当前所有正在运行的容器+历史上运行过的**
  - **--l：显示最近创建的容器**、
  - **--n：显示最近n个创建的容器**
  - **--q：静默模式，只显示容器编号**

![docker ps](图片\命令\docker ps.png)



### 3.3 退出容器

- 两种退出方式

  - exit：run进去容器，exit退出，容器停止

  ![exit退出容器](图片\命令\exit退出容器.png)

  - ctrl+p+q：run进去容器，ctrl+p+q退出，容器不停止

  ![ctrl+p+q退出容器](图片\命令\ctrl+p+q退出容器.png)

- 两种比较

![两种退出比较](图片\命令\两种退出比较.png)



### 3.4 启动/停止容器

- 启动已停止运行的容器：docker start 容器ID或者容器名


- 重启容器：docker restart  容器ID或者容器名
- 停止容器：docker stop 容器ID或者容器名
- 强制停止容器：docker kill 容器ID或容器名



### 3.5 删除已停止的容器

- docker rm 容器ID
- 强制删除：docker rm -f 容器ID
- 一次性删除多个容器实例
  - docker rm -f $(dcoker ps -a -q)
  - docker ps -a -q | xargs docker rm

![docker rm](图片\命令\docker rm.png)



### 3.6 启动守护式容器(后台服务器)

- **docker run -d 容器名：启动守护式容器**
  - 使用镜像centos:latest以后台模式启动一个容器：docker run -d centos
  - 问题：然后docker ps -a 进行查看, 会发现容器已经退出
  - 很重要的要说明的一点: **Docker容器后台运行,就必须有一个前台进程.**
  - 容器运行的命令如果不是那些一直挂起的命令（比如运行top，tail），就是会自动退出的

![没有前台进程的容器会自动退出](图片\命令\没有前台进程的容器会自动退出.png)

- 这个是docker的机制问题,比如你的web容器,我们以nginx为例，正常情况下，配置启动服务只需要启动响应的service即可。例如service nginx start，但是，这样做，nginx为后台进程模式运行，就导致docker前台没有运行的应用，这样的容器后台启动后，会立即自杀因为他觉得他没事可做了
- 所以，最佳的解决方案是，**将你要运行的程序以前台进程的形式运行，常见就是命令行模式，表示我还有交互操作，别中断。**



#### 3.6.1 redis前后台启动演示case

- 前台交互式启动：docker run -it redis

![前台启动redis](图片\命令\前台启动redis.png)

- 后台守护式启动 ：docker run -d redis

![后台运行redis](图片\命令\后台运行redis.png)

- 两者区别：前台的把服务或者当前的交互式命令终端关闭了，那么这个容器就会停止，后台的则不会



#### 3.6.2 查看容器日志

- docker logs 容器ID

![docker logs](图片\命令\docker logs.png)



#### 3.6.3 查看容器内运行的进程

- docker top 容器ID

![docker top](图片\命令\docker top.png)



#### 3.6.4 查看容器内部细节

- docker inspect 容器ID

![docker inspect](图片\命令\docker inspect.png)



### 3.7 进入正在运行的容器并以命令行交互

- docker exec -it 容器ID bashShell

![docker exec](图片\命令\docker exec.png)

- docker attach 容器ID

![docker attach](图片\命令\docker attach.png)

- 区别：

  - **exec是在容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止**

  ![进入容器方式区别1](图片\命令\进入容器方式区别1.png)

  - **attach直接进入容器启动命令的终端，不会启动新的进程，用exit退出，会导致容器的停止。**

  ![进入容器方式区别2](图片\命令\进入容器方式区别2.png)


- docker exec -it 容器ID /bin/bash
- docker exec -it 容器ID redis-cli
- **一般用-d后台启动的程序，再用exec进入对应容器实例**

![docker exec进入redis](图片\命令\docker exec进入redis.png)



### 3.8 从容器内拷贝文件到主机上

- 从容器中把文件拷贝到主机上


- docker cp  容器ID:容器内路径 目的主机路径

![docker cp](图片\命令\docker cp.png)



### 3.9 导入和导出容器

- export 导出容器的内容留作为一个tar归档文件[对应import命令]
  - **docker export 容器ID > 文件名.tar.gz**

![docker export](图片\命令\docker export.png)

- import 从tar包中的内容创建一个新的文件系统再导入为镜像[对应export]
  - **cat 文件名.tar.gz | docker import - 镜像用户/镜像名:镜像版本号**

![docker import](图片\命令\docker import.png)

- 用import生成的镜像文件

![docker import生成的镜像文件](图片\命令\docker import生成的镜像文件.png)

- 启动容器发现a.txt文件还在

![启动发现a.txt还在](图片\命令\启动发现a.txt还在.png)



### 3.10 命令总结

![命令总结](图片\命令\命令总结.png)

- attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像
- build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像
- commit    Create a new image from a container changes   # 提交当前容器为新的镜像
- cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中
- create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器
- diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化
- events    Get real time events from the server          # 从 docker 服务获取容器实时事件
- exec      Run a command in an existing container        # 在已存在的容器上运行命令
- export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ]
- history   Show the history of an image                  # 展示一个镜像形成历史
- images    List images                                   # 列出系统当前镜像
- import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]
- info      Display system-wide information               # 显示系统相关信息
- inspect   Return low-level information on a container   # 查看容器详细信息
- kill      Kill a running container                      # kill 指定 docker 容器
- load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]
- login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器
- logout    Log out from a Docker registry server          # 从当前 Docker registry 退出
- logs      Fetch the logs of a container                 # 输出当前容器日志信息
- port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口
- pause     Pause all processes within a container        # 暂停容器
- ps        List containers                               # 列出容器列表
- pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像
- push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器
- restart   Restart a running container                   # 重启运行的容器
- rm        Remove one or more containers                 # 移除一个或者多个容器
- rmi       Remove one or more images       # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
- run       Run a command in a new container              # 创建一个新的容器并运行一个命令
- save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]
- search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像
- start     Start a stopped containers                    # 启动容器
- stop      Stop a running containers                     # 停止容器
- tag       Tag an image into a repository                # 给源中镜像打标签
- top       Lookup the running processes of a container   # 查看容器中运行的进程信息
- unpause   Unpause a paused container                    # 取消暂停容器
- version   Show the docker version information           # 查看 docker 版本
- wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值



# 四、docker镜像

## 1、基本原理

### 4.1 镜像是什么

- **是一种轻量级、可执行的独立软件包**，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。
- 只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。



### 4.2 分层的镜像

- 以pull为例，在下载的过程中可以看到docker的镜像好像是在一层一层的在下载

![分层镜像](图片\docker镜像\分层镜像.png)



### 4.3 UnionFS（联合文件系统）

- UnionFS（联合文件系统）：Union文件系统（UnionFS）**是一种分层、轻量级并且高性能的文件系统**，它支持**对文件系统的修改作为一次提交来一层层的叠加**，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。**Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像**。
- 特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录



### 4.4 Docker镜像加载原理

- **docker的镜像实际上由一层一层的文件系统组成**，这种层级的文件系统UnionFS。
- bootfs(boot file system)主要包含bootloader(根加载器)和kernel(linux内核), bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，**在Docker镜像的最底层是引导文件系统bootfs**。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。
- rootfs (root file system) ，在bootfs之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。 

![docker镜像加载原理](图片\docker镜像\docker镜像加载原理.png)

-  平时我们安装进虚拟机的CentOS都是好几个G，为什么docker这里才200M？？

![虚拟机内存很小](图片\docker镜像\虚拟机内存很小.png)

- 对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供 rootfs 就行了。由此可见对于不同的linux发行版, bootfs基本是一致的, rootfs会有差别, 因此不同的发行版可以公用bootfs。



### 4.5 为什么Docker镜像要采用这种分层结构

- 镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了**复用**。
- 比如说有多个镜像都从相同的 base 镜像构建而来，那么 Docker Host 只需在磁盘上保存一份 base 镜像
- 同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享


- 举个例子
  - ubuntu这个镜像内存很小，只有一些基础功能，但是没有其他高级点的东西
  - 这个时候想在上面用vim命令，那么只需要在基础款上加点改造就行
  - 这个时候比如还想用别的命令，但不用vim，那么也可以直接在基础款上改造后就可以，不需要从头再来
  - 同时这两个改造款也可以作为基础改造，复用且提高效率



### 4.6 镜像层只读,容器层可写

- **Docker镜像层都是只读的，容器层是可写的**
  - 当容器启动时，一个新的可写层被加载到镜像的顶部。
  - 这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”。


- 当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”。


- 所有对容器的改动 - 无论添加、删除、还是修改文件都只会发生在容器层中。只有容器层是可写的，容器层下面的所有镜像层都是只读的。

![容器层和镜像层](图片\docker镜像\容器层和镜像层.png)



## 2、自定义制作镜像

- Docker镜像commit操作案例
  - docker commit提交容器副本使之成为一个新的镜像
  - docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
- 从Hub上下载ubuntu镜像到本地并成功运行，原始的默认Ubuntu镜像是不带着vim命令的

![默认不带vim](图片\docker镜像\默认不带vim.png)

- 启动容器并安装vim，docker容器内执行两条命令：
  - **先更新我们的包管理工具：apt-get update**
  - **然后安装我们需要的vim：apt-get -y install vim**

![安装vim](图片\docker镜像\安装vim1.png)

![安装vim2](图片\docker镜像\安装vim2.png)

- 安装完成后，commit我们自己的新镜像：docker commit -m="自定义vim" -a="lzy" 容器ID 要创建的目标镜像名:[标签名]

  - docker commit -m="自定义vim" -a="lzy" e23a53a566bc ubuntun_vim:1.1

  ![dokcer commit](图片\docker镜像\dokcer commit.png)



## 3、将本地镜像推送到腾讯云

- 地镜像发布到阿里云流程

![阿里云推送生态](图片\推送镜像到本地仓库\阿里云推送生态.png)



### 3.1 创建镜像仓库

- 阿里云开发者平台：<https://promotion.aliyun.com/ntms/act/kubernetes.html>

![1](图片\推送镜像到本地仓库\1.png)

- 选择控制台，进入容器镜像服务

![2](图片\推送镜像到本地仓库\2.png)

- 选择个人实例

![3](图片\推送镜像到本地仓库\3.png)

- 命名空间

![4](图片\推送镜像到本地仓库\4.png)

![5](图片\推送镜像到本地仓库\5.png)

- 仓库名称

![6](图片\推送镜像到本地仓库\6.png)

![7](图片\推送镜像到本地仓库\7.png)

![8](图片\推送镜像到本地仓库\8.png)

- 进入管理界面获得脚本

![9](图片\推送镜像到本地仓库\9.png)

![10](图片\推送镜像到本地仓库\10.png)



### 3.2 用命令推送镜像进去

- docker login --username=aliyun1362327015 crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com
- docker tag [ImageId] crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:[镜像版本号]
- docker push crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:[镜像版本号]
- 例子：
  - docker login --username=aliyun1362327015 crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com
  - docker tag f9164af54d54 crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:1.1
  - docker push crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:1.1

![推送到本地仓库](图片\推送镜像到本地仓库\推送到本地仓库.png)



### 3.3 从本地仓下载

- docker pull crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:[镜像版本号]
- 例子：运行镜像容器，发现可以直接用vim
  - docker pull crpi-6iz21bc8tofut1aw.cn-hangzhou.personal.cr.aliyuncs.com/lzy813/myubuntu:1.1

![从本地仓库拉取镜像](图片\推送镜像到本地仓库\从本地仓库拉取镜像.png)



## 4、将本地镜像发布到私有库

- 下载镜像Docker Registry
  - docker pull registry

![1](图片\推送镜像到私有库\1.png)

- 运行私有库Registry，相当于本地有个私有Docker hub
  - docker run -d -p 5000:5000  -v /lzy/myregistry/:/tmp/registry --privileged=true registry
  - 默认情况，仓库被创建在容器的/var/lib/registry目录下，建议自行用容器卷映射，方便于宿主机联调

![2](图片\推送镜像到私有库\2.png)

- 制作一个带ifconfig的镜像：docker run -it ubuntu /bin/bash
  - apt-get update
  - apt-get install net-tools
  - ifconfig

![3](图片\推送镜像到私有库\3.png)

- 安装完成后，commit我们自己的新镜像
  - 公式： docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
  - 命令：在容器外执行，记得: docker commit -m="ifconfig cmd add" -a="lzy" c6037afa5ed8 lzyubuntu:1.2

![4](图片\推送镜像到私有库\4.png)

- curl验证私服库上有什么镜像
  -  curl -XGET http://127.0.0.1:5000/v2/_catalog

![5](图片\推送镜像到私有库\5.png)

- 将新镜像lzyubuntu:1.2修改符合私服规范的Tag
  - 按照公式：docker tag  镜像:Tag  Host:Port/Repository:Tag
  - docker tag  lzyubuntu:1.2  127.0.0.1:5000/lzyubuntu:1.2

![6](图片\推送镜像到私有库\6.png)

- 修改配置文件使之支持http

  - docker默认不允许http方式推送镜像，通过配置选项来取消这个限制。====> 修改完后如果不生效，建议重启docker

  - vim /etc/docker/daemon.json

  - {

      	"registry-mirrors": ["https://aa25jngu.mirror.aliyuncs.com"],

      	"insecure-registries": ["127.0.0.1:5000"]

    }

![7](图片\推送镜像到私有库\7.png)

![8](图片\推送镜像到私有库\8.png)

- push推送到私服库
  - docker push 127.0.0.1:5000/lzyubuntu:1.2


- 再次curl验证私服库上有什么镜像
  - curl -XGET http://127.0.0.1:5000/v2/_catalog

![9](图片\推送镜像到私有库\9.png)

- pull到本地执行，可以直接用ifconfig
  - docker pull 127.0.0.1:5000/lzyubuntu:1.2

![10](图片\推送镜像到私有库\10.png)



# 五、容器数据卷

## 1、坑避免

- Docker挂载主机目录访问如果出现**cannot open directory .: Permission denied**
- 解决办法：在挂载目录后多加一个**--privileged=true**参数即可
- 原因：如果是CentOS7安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为，在SELinux里面挂载目录被禁止掉了，如果要开启，我们一般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即使用该参数，container内的root拥有真正的root权限，否则，container内的root只是外部的一个普通用户权限。



## 2、什么是容器数据卷

- 卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，**但不属于联合文件系统**，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性：
- **卷的设计目的就是数据的持久化，完全独立于容器的生存周期**，因此Docker不会在容器删除时删除其挂载的数据卷
  - 一句话：有点类似我们Redis里面的rdb和aof文件
  - **将docker容器内的数据保存进宿主机的磁盘中**


- 运行一个带有容器卷存储功能的容器实例
  - docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录  镜像名

![容器数据卷](图片\容器数据卷\容器数据卷.png)



## 3、能干嘛

- 将运用与运行的环境打包镜像，run后形成容器实例运行 ，但是我们对数据的要求希望是**持久化**的

- **Docker容器产生的数据，如果不备份，那么当容器实例删除后，容器内的数据自然也就没有了。为了能保存数据在docker中我们使用卷**

- 特点：

  1：数据卷可在容器之间共享或重用数据

  2：卷中的更改可以直接**实时生效**，爽

  3：数据卷中的更改不会包含在镜像的更新中

  4：数据卷的生命周期一直持续到没有容器使用它为止



## 4、数据卷案例

### 4.1 宿主vs容器之间映射添加容器卷

- docker run -it --privileged=true -v /lzy/u1:/lzy/u1 --name=u1 ubuntu /bin/bash
- 进入容器后，在/lzy/u1创建一个a.txt文件，在宿主机上也会出现；同时在宿主机上创建一个b.txt，容器内也会出现

![运行带数据卷的容器](图片\容器数据卷\运行带数据卷的容器.png)



### 4.2 查看数据卷是否挂载成功

- docker inspect 容器ID
  - Source：源地址
  - Destination：宿主地址

![查看数据卷是否挂载成功](图片\容器数据卷\查看数据卷是否挂载成功.png)



### 4.3 容器和宿主机之间数据共享

- docker修改，主机同步获得
- 主机修改，docker同步获得
- docker容器stop，主机修改，docker容器重启,数据也同步。

![数据卷之间数据共享](图片\容器数据卷\数据卷之间数据共享.png)

### 4.4 读写规则映射添加说明

- 读写(默认)：docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw  镜像名
  - rw = read + write
- 这时候数据的更新是双向的，即：容器修改宿主机就会同步更新，宿主机修改容器也会同步更新

![读写1](图片\容器数据卷\读写1.png)

![读写2](图片\容器数据卷\读写2.png)





### 4.5 只读权限

- 只读：docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
  - ro = readOnly
- 容器内不能修改，只能读

​     ![只读权限1](图片\容器数据卷\只读权限1.png)

- 宿主机可以修改，容器会实时同步

![只读权限2](图片\容器数据卷\只读权限2.png)



### 4.6 卷的继承和共享

- 继承命令：docker run -it  --privileged=true --volumes-from 父类  --name u2 ubuntu


- 容器1完成和宿主机的映射
  - docker run -it --privileged=true -v /lzy/u1:/lzy/u1:rw --name=u1 ubuntu /bin/bash

![映射1](图片\容器数据卷\映射1.png)

- 容器2继承容器1的卷规则
  - docker run -it --privileged=true --volumes-from u1 --name=u2 ubuntu /bin/bash

![映射2](图片\容器数据卷\映射2.png)

- 注意：
  - 映射关系：宿主机 = u1 = u2，三方互通，一方改动，其他两方都会实时同步
  - u1和u2是独立个体，即便u2是继承的u1，但若是u1挂了，u2还是能和宿主机互通
  - u1挂了，这时候u2和宿主机修改文件，u1重启，这时候u1还是能看到其他两方的改动



# 六、常用软件安装

## 1、总体步骤

- 搜索镜像 -> 拉取镜像 -> 查看镜像 -> 启动镜像(服务端口映射) -> 停止容器 -> 移除容器



## 2、tomcat安装

- 使用tomcat镜像创建容器实例(也叫运行镜像)
  - docker run -it -p 8080:8080 tomcat
    - -p 小写，主机端口:docker容器端口
    - -P 大写，随机分配端口
    - -i:交互
    - -t:终端
    - -d:后台

~~~bash
# 拉取镜像
docker pull tomcat
# 查看镜像是否拉取成功
docker images
# 启动镜像
docker run -it -d -p 8080:8080 tomcat
# 进入镜像
docker exec -it 424b1425a203 /bin/bash
# 查看文件夹
ll
# 删除webapps文件夹
rm -r webapps
# 将webapps.dist文件夹命名为webapps
mv webapps.dist webapps
~~~

![tomcat安装1](图片\常用软件安装\tomcat安装1.png)

![tomcat安装2](图片\常用软件安装\tomcat安装2.png)

- 直接访问可能会报错404

  - 可能没有映射端口或者没有关闭防火墙

    - 通过docker ps查看tomcat的端口映射

    - 如要外部访问,设置防火墙放行映射的端口

      ~~~bash
      [root@192 ~]# firewall-cmd --permanent --add-port=8080/tcp
      success
      [root@192 ~]# firewall-cmd --reload
      success
      [root@192 ~]# firewall-cmd --zone=public --list-ports
      8080/tcp
      ~~~

  - 需要把webapps.dist目录换成webapps，先成功启动tomcat

    ~~~bash
    # 删除webapps文件夹
    rm -r webapps
    # 将webapps.dist文件夹命名为webapps
    mv webapps.dist webapps
    ~~~


- 免修版：

~~~bash
# 拉取镜像
docker pull billygoo/tomcat8-jdk8
# 启动镜像
docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8
~~~



## 3、mysql安装

- 查看是否已经安装过：ps -ef | grep mysql

~~~bash
# 拉取镜像
docker pull mysql
# 查看镜像是否拉取好
docker images
~~~



### 3.1 简易版

~~~bash
# 运行容器
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
# 查看容器是否安装好
docker ps
# 进入容器
docker exec -it 容器ID /bin/bash
# 查看mysql的版本
mysql -V
mysql --version
# 登陆
mysql -uroot -p
~~~



### 3.2 实战版：数据备份和中文乱码

~~~bash
# 运行容器
docker run -d -p 3306:3306 --privileged=true -v /lzy/mysql/log:/var/log/mysql -v /lzy/mysql/data:/var/lib/mysql -v /lzy/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql
~~~

- 在宿主机的/lzy/conf下新建my.cnf：通过容器卷同步给mysql容器实例

~~~bash
[client]
default_character_set=utf8
[mysqld]
collation_server = utf8_general_ci
character_set_server = utf8
~~~

- 重启mysql容器实例再重新进入查看字符编码

~~~bash
# 重启
docker restart 容器ID
# 进入容器
docker exec -it 容器ID /bin/bash
# 查看mysql的版本
mysql -V
mysql --version
# 登陆
mysql -uroot -p
# 查看字符集
SHOW VARIABLES LIKE 'character%';
~~~

- 挂载数据卷的好处：即便这个容器被人强删了，只要重新绑定那个数据卷的映射启动就还是能够恢复

![重启之后的数据还是在](图片\常用软件安装\重启之后的数据还是在.png)



## 4、redis安装

~~~bash
# 下载镜像
docker pull redis
# 查看镜像
docker images
# 在CentOS宿主机下新建目录/app/redis   
mkdir -p /app/redis
# 将一个redis.conf文件模板拷贝进/app/redis目录下

# 使用redis镜像创建容器(也叫运行镜像)
docker run -p 6379:6379 --name redis --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis redis-server /etc/redis/redis.conf
# 测试redis-cli连接上来
docker exec -it redis /bin/bash
# 登陆
redis-cli
auth 123456
# 测试
set k1 v1
get k1
~~~

- 将一个redis.conf文件模板拷贝进/app/redis目录下，要修改的地方如下

![redis配置文件修改](图片\常用软件安装\redis配置文件修改.png)

- 证明用了自定义的配置文件

  - 修改配置文件个数为10

  ![redis配置文件修改数据库个数10](图片\常用软件安装\redis配置文件修改数据库个数10.png)

  - 重启服务：docker restart c112fabb2e32
  - 再登陆进去用select 10验证

  ![select 15报错](图片\常用软件安装\select 15报错.png)

  ​



# 七、复杂安装详说





# 八、DockerFile解析

## 1、是什么

- Dockerfile是用来**构建Docker镜像**的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

![DockerFile](图片\DockerFile\DockerFile.png)



## 2、构建三步骤

- 编写Dockerfile文件
- docker build命令构建镜像
- docker run依镜像运行容器实例



## 3、构建过程解析

- Dockerfile内容基础知识	
  - 每条保留字指令都**必须为大写字母**且后面要跟随至少一个参数
  - 指令按照从上到下，顺序执行
  - \#表示注释
  - 每条指令都会创建一个新的镜像层并对镜像进行提交
- Docker执行Dockerfile的大致流程
  - docker从基础镜像运行一个容器
  - 执行一条指令并对容器作出修改
  - 执行类似docker commit的操作提交一个新的镜像层
  - docker再基于刚提交的镜像运行一个新容器
  - 执行dockerFile中的下一条指令直到所有指令执行完成
- 总结
  - 从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，
    - Dockerfile是软件的原材料
    - Docker镜像是软件的交付品
    - Docker容器则可以认为是软件镜像的运行态，也即依照镜像运行的容器实例
  - Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石。

![dockerFile总结](图片\DockerFile\dockerFile总结.png)

- Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;
- Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像时会真正开始提供服务;
- Docker容器，容器是直接提供服务的。



## 4、DockerFile保留字

- FROM：基础镜像，当前新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是from
- MAINTAINER：镜像维护者的姓名和邮箱地址
- RUN：







九、将
