---
title: servlet学习笔记
date: 2018-09-08 14:04:46
tags: servlet javaweb
---

## time: 2018/9/6
### servlet是什么?  
tomcat是web应用服务器，是一个servlet/jsp容器，负责处理客户请求，把http请求传给servlet，并将servlet的响应传回给客户。

java servlet是运行在web服务器上(例如 **tomcat** )的小型应用程序，它作为浏览器和服务器的中转站，接收网页表单的数据，响应浏览器的请求。  
### 为什么要有servlet?
当向web服务器请求一个资源时，web服务器擅长提供静态页面(web服务器不能提供动态即使页面,不能向数据库存程序)，如果需要一个动态的内容，则需要一种辅助程序，web服务器会调用辅助程序来完成动态页面的展示。

例如说在一个网页输入账号密码，然后登陆跳转，这个时候页面就会跳转(跳转方法有get/post)，在web.xml中配置servlet路径，跳转到servlet去，讲请求交给servlet处理。servlet就是接受页面信息然后进行逻辑处理的一个java类。

### Servlet的生命周期
Servlet的生命周期即Servlet从出生到死亡，经历了*加载，初始化，服务，销毁*四个阶段。这些都是由web容器(tomcat)控制的，而初始化，服务，销毁时我们程序员可以自己去添加内容的，然后由web容器调用。

Servlet有几个方法，实现了初始化，服务，销毁的功能。
* init()方法，初始化，仅第一次创建这个Servlet时调用。
* service()方法，执行实际任务的主要方法，每当服务器接收到Servlet请求，服务器产生一个新的线程并调用服务，Service()根据Http请求在适当的时候调用doGet,doPost,doPut,dodelete等方法。
* doGet()方法，接收正常的URL请求，或未指定method的HTML表单。
* doPost()方法，接收method为POST的HTML表单。
* destory()方法，destory方法只会调用一次，生命周期结束时调用，什么时候结束由web容器决定，或者关闭服务器时自然结束。
#### 生命周期示例
启动服务器，访问页面后，看控制台信息。
![控制台信息](/images/con1.png)
点击页面

![form](/images/form1.png)

控制台信息，可以看出先后进行了执行了init(),service(),doPost().
![con2](/images/con2.png)

即如下顺序
![coode](/images/code2.png)
再一起发起提交，并没有第二次初始化，初始化仅进行一次。
![con3](/images/con3.png)

    其中，service()方法由我们重写了，所以要在service中调用一下父类，否则不会调用doPost等，因为自动分配调用是由tomcat自带的service中的代码实现的。
