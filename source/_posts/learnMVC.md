---
title: learnMVC
date: 2018-09-19 19:10:54
tags: javaweb MVC
---
# Learn MVC
## model1
C/S:客户端/服务端

B/S：浏览器/服务器

jsp+javaBean
    javaBen是普通的java类，具有属性，默认构造器，和属性的setter/getter方法
## model2
### MVC开发模式
JSP+Servlet+javaBean.

M:Model模型，javaBean,封装数据

V:View视图 ，JSP

C:Controller控制器，servlet

    理解：浏览器请求数据，请求交给C,servlet处理转发，从数据库中提取数据封装到模型model中，将模型交给jsp形成视图html页面，把jsp页面返回给浏览器。
### 分层思想
在开发中，应用分层思想进行更细致的划分。
![mvc1](/images/mvc1.png)
Servlet有三个功能，获取表单数据，处理业务逻辑，分发转向，将业务逻辑剥离出来，作为业务层Service，专门处理业务逻辑。
再将处理数据即增删改查剥离出作为数据访问层Dao。
