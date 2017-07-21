---
layout:     post
title:      Unable to ping server at localhost:1099
subtitle:   IDEA启动tomcat报错 Unable to ping server at localhost:1099
date:       2017-07-19 23:39:00
author:     "Yang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 配置
    - Tomcat
    - 深圳
---

## 正文
在IDAE下启动tomcat时报错：

Application Server was not connected before run configuration stop, reason: Unable to ping server at localhost:1099

![](/img/in-post/post-Tomcat-Error/tomcat.png) 

---

## 解决方法

上网查了查，发现大家给了这么几个解决方法

### 1. jmx服务
IDEA启用了jmx服务，而要启用tomcat的jmx服务比较麻烦，所以我们可以修改host文件来关闭jmx服务

>在/etc/hosts 中添加 127.0.0.1 localhost [计算机名]
>	#	127.0.0.1       localhost  DESKTOP-TVV7CS3

计算机名可以通过cmd中输入hostname来查询

但我配置之后还是继续报错，所以不是这种原因

### 2.java环境配置错误

你的电脑里有多个jre，在IDEA里给Tomcat选jre时候必须要选你配置了环境变量的那个

### 3.tomcat配置文件冲突

tomcat bin文件夹中catalina.bat 文件中设置了jvm参数 set JAVA_OPTS= -Xmx1024M -Xms512M -XX:MaxPermSize=256m。把参数去掉就Ok了

![](/img/in-post/post-Tomcat-Error/host.png) 

问题解决