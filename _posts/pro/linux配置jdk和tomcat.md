---
title: linux 配置jdk 和tomcat  
tags: [java,linux]
---

# 一.下载安装对应的jdk,并配置Java环境。

官网下载地址：

http://www.oracle.com/technetwork/java/javase/downloads/jdk-6u26-download-400750.html

### 1.将jdk移至/usr/local目录下

解压jdk
> tar -zxvf jdk-6u5-linux-x64.tar.gz

下载将jdk加压后放到/usr/local目录下：

[root@master ~]#chmod 755 jdk-6u5-linux-x64.bin

[root@master ~]# ./jdk-6u5-linux-x64.bin

[root@master ~]#mv jdk1.6.0_05 /usr/local

> 建立/usr/local/下的jdk软连接方便以后版本升级 ：

> [root@master ~]# ln -s /usr/local/jdk1.6.0_05/ /usr/local/jdk


### 2.配置jdk环境变量
在 /etc/profile 中加入以下内容：

```
JAVA_HOME=/usr/local/jdk1.6.0_05

JAVA_BIN=/usr/local/jdk1.6.0_05/bin

PATH=$PATH:$JAVA_BIN

CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export JAVA_HOME JAVA_BIN PATH CLASSPATH
```
保存退出

[root@master ~]source /etc/profile  


查看java环境变量是否生效

[root@master ~]# java -version
```

Java version "1.6.0_05"

Java(TM) SE Runtime Environment (build 1.6.0_05-b13)

java HotSpot(TM) 64-Bit Server VM (build 10.0-b19, mixed mode)
```

测试成功

# 二．下载安装tomcat  [http://tomcat.apache.org](http://tomcat.apache.org "http://tomcat.apache.org")

[root@master ~]# unzip apache-tomcat-6.0.30.zip

[root@master ~]# mv apache-tomcat-6.0.30/ /usr/local/

[root@master ~]cd /usr/local/

[root@master local]# ln -s /usr/local/apache-tomcat-6.0.30/ /usr/local/tomcat

[root@master local]# cd tomcat/bin/

[root@master bin]#ls

[root@master bin]#vim catalina.sh

添加以下内容：

CATALINA_HOME=/usr/local/apache-tomcat-6.0.30/

[root@master local]#chmod +x *.sh  


# 三．启动tomcat服务器

[root@master tomcat]# /usr/local/tomcat /bin/catalina.sh start


# 四．在浏览器中输入

http://localhost:8080/（如果不是本机，则输入对应的ip地址）

测试出现tomcat页面则测试成功

ps：需要说明的是tomcat的默认测试页面是放在webapps下面，这个其实是在server.xml文件中配置的，如下所示：
```
<Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true"
            xmlValidation="false" xmlNamespaceAware="false">

```

### 注意点 

Connector 节点设置中文
URIEncoding="UTF-8" 