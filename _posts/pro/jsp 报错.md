---
title: jsp 标签报错
tags: [java]
---
### execl 内容替换
> <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
> 识别不了包，与报错



apache-tomcat-7.0.76\conf 修改中 catalina.properties 配置

将tomcat.util.scan.DefaultJarScanner.jarsToSkip=\, 上* 号去掉