FROM centos
MAINTAINER lzy
 
ENV MYPATH /usr/local
WORKDIR $MYPATH
 
#安装vim编辑器
RUN yum -y install vim
#安装ifconfig命令查看网络IP
RUN yum -y install net-tools...
#安装Java8及lib库
RUN yun -y install glibc.1686
RUN mkdir /usr/local/java
#ADD 是相对路径jar,把jdk-8u171-inux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置 自己复制自己所下载jdk的名字，add会自己解压
ADD idk-8u171-linux-x64.tar.gz /usr/local/java/ 
#配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0171
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib: $CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 80

CMD echo $MYPATH
CMD echo "success------------------------ok"
CMD /bin/bash