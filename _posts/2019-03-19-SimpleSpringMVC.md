---
layout: post
title:  "SimpleSpringMVC手写一个简单的SpringMVC"
categories: springmvc
tags:  java springmvc
author: malefo
---

* content
{:toc}


## 前言

上一个简单的tomcat写完之后，就开始看springmvc的源码，发现我自己还是太嫩和浮躁，spring的命名一下子就比tomcat长了很多。
然后呢，再周末之余我把我和大学同学把一家淘宝店开起来了，不得不吐槽一下淘宝现在对业余人士可真不够友好，实在太繁杂了。

第一次写的tomcat源码阅读，因为图片链接都被删掉了，我就隐藏了，真的是坑死个屁的。

这次我是写了一个简单的springmvc程序，没有实现modelandview的功能，而且适配器和处理也是单一的只有一种。
<!-- more -->

![](http://i2.tiimg.com/679735/afa7b9148e75bf29.png)

这是整个程序的结构图

我自己定义了四个注解，不得不说再使用反射的时候有注解的帮助真的很方便

先来看看核心处理器DispatcherServlet，负责确认使用哪个处理器，然后去选择合适的适配器

适配器这里使用了适配者模式，这个设计模式真的精妙，如果没有使用，就要不停的写ifelse，
当适配器少的时候ifelse不用很多，但是一旦适配器有很多呢，那整个程序看上去就很臃肿了

为什么要有适配器呢？因为springmvc的处理器由关于注解的处理器也有关于xml配置文件的处理器，
不同的处理器肯定要有不同的东西来适配来处理，就好比，飞机和汽车都是交通工具，但他们是不同的工具，
那他们的发动机肯定也不相同，不可能用相同的发动机来处理这两种交通工具对吧。

![](http://i2.tiimg.com/679735/ad9eb68f1e970134.png)

这个是适配器的接口

![](http://i2.tiimg.com/679735/6c999a3f6a2457f1.png)

这是实现了接口的子类

里边getHandlerAdapter进行判断传入的handlerMapping是否属于此适配器需要的处理器

![](http://i2.tiimg.com/679735/94ffa59904d08677.png)

![](http://i2.tiimg.com/679735/5e09cea580c058d8.png)

在dispatcher里边，我们新建一个容器，用来存放我们的适配器实例，我们使用向上转型来获取这个适配器

先是由handlerMapping创建一个存放完整类名的容器，
然后为这个handlerMapping获取相应的适配器，
再经由适配器来注入相关信息和获取方法链（即map），
让方法链来处理相对应的请求

![](http://i1.fuimg.com/679735/393df2114ea62343.png)

再handlerAdapter之中，先是根据handlerMapping的list来得到一个instance的map，再根据map将实例注入到写了autowrite
的字段上，
然后生成一个方法链的map，供dispatcherServlet来调用。

![](http://i1.fuimg.com/679735/74af4241fdee6ef3.png)

通过反射来调用（整个项目我没有好好的写解析类名，所以直接就写死了。。。）

然后就执行我自己写的Controller和Service啦

最后附上自己开的淘宝店图片一张

![](http://i1.fuimg.com/679735/b731e6eca9f26ca4.png)



























