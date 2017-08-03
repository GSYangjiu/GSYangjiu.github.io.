---
layout:     post
title:      springMVC初探
subtitle:   "springMVC简单Demo"
date:       2017-08-3 23:09:00
author:     "Yang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - web
    - springMVC
    - 深圳
---

> “Yeah It's on. ”


## 前言

记得一两年前提起三大框架都是“SSH”，近年来使用springMVC来代替Struts2的公司越来越多，不光是Struts2本身存在安全问题，springMVC必然有其相对的优点，因为新公司用的是ssm所以先简单的接触了一下springMVC，主要是springMVC简单的工作流程和如何实现View和Controller之间值得传递。

## 正文

### springMVC工作流程

在整个spring MVC框架中，DispatcherServlet处于核心位置，它负责协调和组织不同组件完成请求处理并返回响应的工作。具体流程为：
1）客户端发送http请求，web应用服务器接收到这个请求，如果匹配DispatcherServlet的映射路径（在web.xml中配置），web容器将请求转交给DispatcherServlet处理；
2）DispatcherServlet根据请求的信息及HandlerMapping的配置找到处理该请求的Controller；
3）Controller完成业务逻辑处理后，返回一个ModelAndView给DispatcherServlet；
4）DispatcherServlet借由ViewResolver完成ModelAndView中逻辑视图名到真实视图对象View的解析工作；
5）DispatcherServlet根据ModelAndView中的数据模型对View对象进行视图渲染，最终客户端得到的响应消息可能是一个普通的html页面，也可能是一个xml或json串，甚至是一张图片或一个PDF文档等不同的媒体形式。

![](/img/in-post/post-spring-bean/bean-life.png) 

接下来让我们来看一个一个简单的springMVC的demo

#### 在web中配置DispatcherServlet拦截所有请求
一个web程序的入口当然是web.xml

	<servlet>
	    <servlet-name>hello</servlet-name>
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	    <load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
	    <servlet-name>hello</servlet-name>
	    <url-pattern>/</url-pattern>
	</servlet-mapping>
在web中配置了springMVC的核心DispatcheServlet，<url-pattern>/</url-pattern>表示拦截所有访问，值得一提的是<load-on-startup>，这个我搜了好好看了一下：

>1)load-on-startup元素标记容器是否在启动的时候就加载这个servlet(实例化并调用其init()方法)。

>2)它的值必须是一个整数，表示servlet应该被载入的顺序

>3)当值为0或者大于0时，表示容器在应用启动时就加载并初始化这个servlet；

>4)当值小于0或者没有指定时，则表示容器在该servlet被选择时才会去加载。

>5)正数的值越小，该servlet的优先级越高，应用启动时就越先加载。

>6)当值相同时，容器就会自己选择顺序来加载。

#### 通过HandlerMapping的配置找到处理该请求的Controller
我个人把HandlerMapping理解为了另一个xml配置文件（框架配置文件就是多），通过这个配置文件我们的DispatcheServlet知道“哦，这个请求是给这个controller来处理的，那个是给那个controller来处理的”。

	<bean name="/welcome" class="com.yang.controller.WelcomeController"></bean>
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--拼接视图地址的前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--拼接视图地址的后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
很显然第一个配置的bean就是url中的路径和用来处理这个请求的controller，但是第二个bean呢？这个我们稍后再讲。

#### Controller完成业务逻辑处理

	public class WelcomeController extends AbstractController {

		@Override
		protected ModelAndView handleRequestInternal(HttpServletRequest request,HttpServletResponse response) throws Exception {
			System.out.print("welcome!");
	        return new ModelAndView("welcome");
		}
		
	}
这个跟struts2中的action的功能一样，同属于MVC中的controller，注意返回了一个“welcome”字段，这个跟Struts2有些不同，struts2中是根据返回字段Struts-config.xml找跳转的jsp或者servlet，而springMVC是根据我们上面提到的视图拼接来寻找跳转地址，不需要通过配置文件的映射。prefix+返回字段+suffix：/WEB-INF/jsp/+welcome+.jsp。怎么样，很简单方便吧。所以我们WEB-INF目录下要有jsp目录，且必须要有一个welcome.jsp文件。
