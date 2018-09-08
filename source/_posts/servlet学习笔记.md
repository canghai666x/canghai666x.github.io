---
title: servlet学习笔记
date: 2018-09-08 14:04:46
tags: servlet javaweb
---

### servlet是什么?  
tomcat是web应用服务器，是一个servlet/jsp容器，负责处理客户请求，把http请求传给servlet，并将servlet的响应传回给客户。

java servlet是运行在web服务器上(例如 **tomcat** )的小型应用程序，它作为浏览器和服务器的中转站，接收网页表单的数据，响应浏览器的请求。  
### 为什么要有servlet?
当向web服务器请求一个资源时，web服务器擅长提供静态页面(web服务器不能提供动态即使页面,不能向数据库存程序)，如果需要一个动态的内容，则需要一种辅助程序，web服务器会调用辅助程序来完成动态页面的展示。

例如说在一个网页输入账号密码，然后登陆跳转，这个时候页面就会跳转(跳转方法有get/post)，在web.xml中配置servlet路径，跳转到servlet去，讲请求交给servlet处理。servlet就是接受页面信息然后进行逻辑处理的一个java类。
