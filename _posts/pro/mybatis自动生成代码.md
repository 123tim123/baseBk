---
title: mybatis 自动生成dao与po bean   
tags: [java,数据库,mybatis]
---

## mybatis自动生成代码工具
> 使用mybatis自动生成代码工具
> 1. 首先连接到数据库  
> 2. 映射数据生成文件到本地


### 1.新建一个maven项目
<img src = "/images/1.jpg"></img>

> 在pom.xml 文件中导入依赖

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.star.mybatis</groupId>
  <artifactId>mybatis-generator</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>mybatis-generator</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

  </properties>
  
  <build>
    <finalName>mybatis-generator</finalName>
    <pluginManagement>
  <plugins>
    <plugin>
	    <groupId>org.mybatis.generator</groupId>
	    <artifactId>mybatis-generator-maven-plugin</artifactId>
	    <version>1.3.2</version>
	    <configuration>
	        <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
	        <verbose>true</verbose>
	        <overwrite>true</overwrite>
	    </configuration>
	    <executions>
	        <execution>
	            <id>Generate MyBatis Artifacts</id>
	            <goals>
	                <goal>generate</goal>
	            </goals>
	        </execution>
	    </executions>
	    <dependencies>
	        <dependency>
	            <groupId>org.mybatis.generator</groupId>
	            <artifactId>mybatis-generator-core</artifactId>
	            <version>1.3.2</version>
	        </dependency>
	    </dependencies>
	</plugin>

    </plugins>
    </pluginManagement>
  </build>
</project>


```

### 2.配置generatorConfig.xml文件
> 在resource中新建一个generatorConfig.xml xml文件 配置如下

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<classPathEntry
		location="F:/work/java/project/base_sdk/qq/ojdbc14-10.2.0.4.0.jar" />
	<context id="my" targetRuntime="MyBatis3">
		<commentGenerator>
			<property name="suppressDate" value="false" />
			<property name="suppressAllComments" value="true" />
		</commentGenerator>

		<!-- <jdbcConnection driverClass="oracle.jdbc.driver.OracleDriver" connectionURL="jdbc:mysql:@localhost:3306" 
			userId="root" password="" /> -->
		<jdbcConnection driverClass="oracle.jdbc.driver.OracleDriver"
			connectionURL="jdbc:oracle:thin:@10.10.xx.xxx:1521:xxxx"
			userId="c##xxxxx"
			password="xxxx" />

		<!-- 配置生产po对象 -->
		<javaModelGenerator targetPackage="com.aizhuwu.pro.main.po"
			targetProject="F:\work\java\tab">
			<property name="enableSubPackages" value="true" />
			<property name="trimStrings" value="true" />
		</javaModelGenerator>

		<!-- mappring.xml 文件配置 -->
		<sqlMapGenerator targetPackage="com.aizhuwu.pro.main.dao"
			targetProject="F:\work\java\tab">
			<property name="enableSubPackages" value="true" />
		</sqlMapGenerator>

		<!-- DAO接口配置 -->
		<javaClientGenerator targetPackage="com.aizhuwu.pro.main.dao"
			targetProject="F:\work\java\tab" type="XMLMAPPER">
			<property name="enableSubPackages" value="true" />
		</javaClientGenerator>

		<!--<table tableName="T_FEE_AGTBILL" domainObjectName="FeeAgentBill" enableCountByExample="false" 
			enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" 
			selectByExampleQueryId="false"/> -->
		<!-- 配置数据表 和 对应的 java对象名 多个表一起生成数据 就多个table标签 -->
		<table tableName="TB_DQDP_ORGANIZATION" domainObjectName="Organization"
			enableCountByExample="false" enableUpdateByExample="false"
			enableDeleteByExample="false" enableSelectByExample="false"
			selectByExampleQueryId="false">
		</table>

	</context>
</generatorConfiguration>

```

```
	<classPathEntry
		location="F:/work/java/project/base_sdk/qq/ojdbc14-10.2.0.4.0.jar" />
```
> 改地址为oracle jdbc驱动包，如果是mysql则导入mysql 导入对应的驱动包即可


> ###注意 oracle c12 账号特性是c##开头

### 用maven执行 mybatis-generator:generate
<img src = "/images/3.jpg"></img>



#### 执行完后在配置的路径就可以看到对应的文件
<img src = "/images/2.jpg"></img>

<img src = "/images/4.jpg"></img>



#### 接下来吧对应的文件考到项目中就可以使用