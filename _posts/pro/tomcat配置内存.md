---
title: tomcat配置之内存配置   
tags: [服务器,tomcat,内存]
---
### tomcat 配置内存  
> 进入tomcat bin目录windows下找到   


### 配置如下
<img src = "/images/a1.png"></img>

> 找到  
> cd ..  
set "CATALINA_HOME=%cd%"  
> 在后面添加上  
>  set JAVA_OPTS=-Xms256m -Xmx512m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=256m -Djava.awt.headless=true



### 修改tomcat title
>在catalina.bat 中添加 set title=8081[%DATE%] 即可  
> 效果如下

<img src = "/images/a2.jpg"></img> 
<img src = "/images/a3.jpg"></img> 




### 总结

Linux下修改JVM内存大小:
要添加在tomcat 的bin 下catalina.sh 里，位置cygwin=false前 。注意引号要带上,红色的为新添加的.
```
# OS specific support. $var _must_ be set to either true or false.
JAVA_OPTS="-Xms256m -Xmx512m -Xss1024K -XX:PermSize=128m -XX:MaxPermSize=256m"
cygwin=false
```
 
windows下修改JVM内存大小:
情况一:解压版本的Tomcat, 要通过startup.bat启动tomcat才能加载配置
要添加在tomcat 的bin 下catalina.bat 里
```
rem Guess CATALINA_HOME if not defined
set CURRENT_DIR=%cd%后面添加,红色的为新添加的.
set JAVA_OPTS=-Xms256m -Xmx512m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=256m -Djava.awt.headless=true

```