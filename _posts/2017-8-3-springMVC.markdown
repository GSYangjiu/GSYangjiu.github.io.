---
layout:     post
title:      springMVC初探
subtitle:   "springMVC简单Demo"
date:       2017-08-3 23:09:00
author:     "Yang"
header-img: "img/sea-bg-2017.jpg"
catalog: true
tags:
    - web
    - springMVC
    - 深圳
---

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

![](/img/in-post/post-springMVC/springMVC.jpg) 

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

这个跟struts2中的action的功能一样，同属于MVC中的controller，注意返回了一个“welcome”字段，这个跟Struts2有些不同，struts2中是根据返回字段Struts-config.xml找跳转的jsp或者servlet，而springMVC是根据我们上面提到的视图拼接来寻找跳转地址，不需要通过配置文件的映射。

>prefix+返回字段+suffix："/WEB-INF/jsp/"+"welcome"+".jsp"。

怎么样，很简单方便吧。所以我们WEB-INF目录下要有jsp目录，且必须要有一个welcome.jsp文件。

当我们在浏览器输入localhost:8080/springMVC/welcome时，执行WelcomeContorlle的业务逻辑，控制台输出“welcome”，页面跳转到welcome.jsp

![](/img/in-post/post-springMVC/result1.jpg) 

![](/img/in-post/post-springMVC/result2.jpg) 

但是上面这种继承AbstractController实现handleRequestInternal的方式来处理业务逻辑有一个很大的问题，有没有发现，那就是一个controller只能处理一种业务，增删改查就要写很多controller，是不是很蠢，所以一般我们不会用这种方方式，而是Annotation，即注解来实现。

*hello-servlet.xml中配置*

    <!--<bean name="/welcome" class="com.yang.controller.WelcomeController"></bean>-->

    <context:component-scan base-package="com.yang.controller"/>
    <mvc:annotation-driven />

    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--拼接视图地址的前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--拼接视图地址的后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

*HelloController*

	@Controller
	public class HelloController {

	    //表示对应url
	    @RequestMapping("/hello")
	    public String hello(){
	        System.out.print("hello");
	        return "hello";
	    }

	    @RequestMapping("/welcome")
	    public String welcome(){
	        System.out.print("welcome");
	        return "welcome";
	    }
	}

通过配置@Controller和@RequestMapping来处理，这时只需要return跳转jsp的名称就可以了。而且可以为多个的方法配置不同的@RequestMapping来处理不同的业务逻辑。

### springMVC传值

#### View传给Controller

通过*@RequestParam*来获取url中传递的值，向Controller中出传递参数

	@Controller
	public class HelloController {

	    //表示对应url
	    @RequestMapping("/hello")
	    public String hello(@RequestParam("useranme") String username){
	        System.out.print("hello "+username);
	        return "hello";
	    }
	}

![](/img/in-post/post-springMVC/springMVC-url.png) 

![](/img/in-post/post-springMVC/result3.png) 

但是这样也有一个问题，如果url中不包含我们预定的username的话，页面就会报错。

![](/img/in-post/post-springMVC/urlError.png) 

其实springMVC很聪明，可以去除@RequestParam，只向方法中预设一个String的username参数。当url中包含username时会自动注入，如果没有传递username，则username默认为null，页面不会报错。

	@Controller
	public class HelloController {

	    //表示对应url
	    @RequestMapping("/hello")
	    public String hello(String username){
	        System.out.print("hello "+username);
	        return "hello";
	    }
	}

![](/img/in-post/post-springMVC/result4.png) 

所以只有在该参数时必不可少的时候我们才会加上@RequestParam。

#### Controller传给View

参数中添加一个Model，利用model.aaddAttribute(),用键值对的方式传输数据，（其实直接传一个Map就可以，因为model本身就是一个Map），页面直接用EL表达式就可取到Controller传递过去的数据了

	@Controller
	public class HelloController {

	    //表示对应url
	    @RequestMapping("/hello")
	    public String hello(String username, Model model){
	        System.out.print("hello "+username);
	        model.addAttribute("username",username);
	        return "hello";
	    }
	}

![](/img/in-post/post-springMVC/result5.png) 

要提一下的时，如果不设置key值，直接这样model.addAttribute(username)，key值默认为value的数据类型，很方便。

struts2传值用get/set，不支持单例，springMVC支持单例。