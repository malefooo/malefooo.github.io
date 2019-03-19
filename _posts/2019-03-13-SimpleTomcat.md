---
layout: post
title:  "SimpleTomcat手写一个简单的tomcat连接模块"
categories: sprimgmvc
tags:  java springmvc
author: springmvc
---

* content
{:toc}


## 前言

tomcat的基本流程和结构大致是看完了，也在规定时间内完成了，我想既然完成了，就写个simpletomcat当作测试自己吧，
就写了一个只有connector的tomcat，后面会把container补齐，不过由于要找工作，先把自己既定的复习计划完成先。

ps.结果写的这个还是个畸形儿。。。。

好了，开始正文。
<!-- more -->

![](http://i1.bvimg.com/679735/071d0714b34519df.png)

这是整个结构。

分为客户端client，连接器connector和容器container

![](http://i1.bvimg.com/679735/6f4b412fb70dcace.png)

这是我的设计思路

endpoint是启动acceptor和poller的

poller是轮询器，不停的等待连接队列不为空时然后获取selectkeys，循环取出准备就绪的key，然后交给process处理

这个在tomcat里就是把channel都封装了，我是没有封装，原汁原味

acceptor是一个接收器，专门接收客户端连接（这里弄成和tomcat一样为阻塞），然后把接收到的socketchannel注册到seletor之中，设为非阻塞

就不贴代码了，这次阅读源码和自我测试还是很满意的，懂得了很多新知识，吧原来一些模棱两可的知识点进行了巩固和刷新

多看看别人的智慧结晶，从中学习，让自己进步。