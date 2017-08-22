---
layout:     post
title:      linux下部署两个tomcat
subtitle:   "小伙伴再也不用"
date:       2017-08-22 21:39:00
author:     "Yang"
header-img: "img/sea-bg-2017.jpg"
catalog: true
tags:
    - linux
    - tomcat
    - 深圳
---

## 前言

最近在工作总碰到本地无法测试，要上传war包到Linux服务器测试的情况，于是和小伙伴就轮流使用测试环境，异常麻烦和耽误时间，老大说“为什么不装两个tomcat？”,于是问题就....

## 安装步骤

### 编辑环境变量：vi /etc/profile

加入以下代码(tomcat路径要配置自己实际的tomcat安装目录)

	##########first tomcat###########
	CATALINA_BASE=/usr/local/tomcat
	CATALINA_HOME=/usr/local/tomcat
	TOMCAT_HOME=/usr/local/tomcat
	export CATALINA_BASE CATALINA_HOME TOMCAT_HOME
	##########first tomcat############

	##########second tomcat##########
	CATALINA_2_BASE=/usr/local/tomcat_2
	CATALINA_2_HOME=/usr/local/tomcat_2
	TOMCAT_2_HOME=/usr/local/tomcat_2
	export CATALINA_2_BASE CATALINA_2_HOME TOMCAT_2_HOME
	##########second tomcat##########

#### 修改第二个tomcat配置

修改bin目录下catalina.sh文件

打开catalina.sh ，找到下面一行字，

 <font color=red># OS specific support.  $var _must_ be set to either true or false.</font>

在下面增加如下代码

	export CATALINA_BASE=$CATALINA_2_BASE
	export CATALINA_HOME=$CATALINA_2_HOME

修改cofn目录下server.xml文件，主要是修改端口，防止端口被第一个tomcat占用

修改server.xml配置和第一个不同的启动、关闭监听端口。
修改后示例如下：

	<!-- 端口：8005->9005 -->
	　 <Server port="9005" shutdown="SHUTDOWN">　               
	<!-- Define a non-SSL HTTP/1.1 Connector on port 8080 端口：8080->9080-->
	   <Connector port="9080" maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" onnectionTimeout="20000" disableUploadTimeout="true" />
	<!-- Define an AJP 1.3 Connector on port 8009 端口：8009->9009-->
	   <Connector port="9009" enableLookups="false" redirectPort="8443" protocol="AJP/1.3" />

进入tomcat/bin,./starup.sh启动tomcat本以为万事大吉，结果报错

![](/img/in-post/post-tomcatInLinux/error.jpg) 

>错误：-bash: ./startup.sh: Permission denied

百度一下，.sh文件没有权限
解决办法：
用命令chmod 修改一下Tomcat的bin目录下的.sh权限就可以了
如chmod u+x *.sh赋予.sh权限即可
在此执行，OK了。