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