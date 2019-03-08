---
layout: post
title:  "Tomcat源码分析心得"
categories: tomcat
tags:  java tomcat  
author: 家中男孩
---

* content
{:toc}


## 前言

突然就想tomcat是什么，他有什么用，然后就开始了源码阅读之路，本人是个刚转行1年多的小菜鸟。
基础也很不扎实，在看源码很多不懂就去查，一下子就理解了当时不太理解的一些定义，也就顺便写了下来。
真的很菜，逻辑也不清楚，希望大家看了能有帮助，有错误的地方多多包涵，告诉我我就去改正。

##  第一次撸码

打开整个项目，搜索了一下lifecycle（生命周：接口），就从这个类开始看了
查看了一下其实现类

![](https://wxt.sinaimg.cn/thumb300/006BeuLUgy1g0vgtyujmij30eo0a8753.jpg?tags=%5B%5D)

![](https://wxt.sinaimg.cn/thumb300/006BeuLUgy1g0vgtywc7bj30eo0abdgm.jpg?tags=%5B%5D)

![](https://wxt.sinaimg.cn/thumb300/006BeuLUgy1g0vgtyuxfaj30eo0acaas.jpg?tags=%5B%5D)

![](https://wxt.sinaimg.cn/thumb300/006BeuLUgy1g0vgtyupzqj30eq0abmxx.jpg?tags=%5B%5D)

这些关键的组件都继承来lifecycle这个接口，方便管理呀，关了就一起关，
启动初始化时也一起启动，具体实现我要慢慢研究一下，毕竟是一个很成熟的大项目，要细细研究一下。


放一张lifecycle的启动流程图（是在源代码中的哦）

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vgzffrqgj30de0esaac.jpg?tags=%5B%5D)

整个过程是非常清晰的，每个过程细化到启动前，启动中，启动后，等等。。

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vh3kzd6kj30g30bvq37.jpg?tags=%5B%5D)

```js

Server：服务器的意思，代表整个tomcat服务器，一个tomcat只有一个Server；
Service：Server中的一个逻辑功能层， 一个Server可以包含多个Service；
Connector：称作连接器，是Service的核心组件之一，一个Service可以有多个Connector，主要是连接客户端请求；
Container：Service的另一个核心组件，按照层级有Engine，Host，Context，Wrapper四种，一个Service只有一个Engine，其主要作用是执行业务逻辑；
Jasper：JSP引擎；
Session：会话管理；

```

看完了lifecycle，里边的方法和变量，定义了一些状态，我从这个类开始读，是因为我觉得所有的组件都含有这个接口的规范，好了，接下来看看server


interface server extends lifecycle
继承了lifecycle这个接口
其实现类就一个：standardServer

有一个疑问：有一个类叫做全局命名源：NamingResourcesImpl，我没理解用到这个有什么用，不过后面再补上把，先把大体框架弄懂
lifecycle<---server<---standardServer
继承关系：lifecycle<----lifecycleBase<----lifeMBeanBase<----StandardServer

```js

在看源码时，有很多的抽象类，我就在想，抽象类有什么用，只要接口不就行了么？而且抽象类不能被创建，这在面试中我还被问到一个问题：怎么样去阻止一个类不被创建，那就是抽象类啊，我竟然傻乎乎的没回答出来，可想而知我的基础时多么的糟糕，这么糟糕的基础就是因为我只会写代码，并不理解我所写代码语言的思想；有点跑题哈

接口：定义的是事物的属性和行为，主要是定义属性！！

抽象类：继承接口，然后实现接口中的方法，强迫子类必须重写，抽象类中更多是行为的实现。例如一个物种叫做LGDOG，他既是人又是狗，他不用狗的行为，却有狗的属性，同时他具有完整人类的行为，这时候他可以继承人类的抽象类和实现狗的接口，他不必实现狗的行为，但是他有狗的属性哦，但是如果他只实现狗的接口，这时候他就必须要强制实现狗的行为。

如果你想让其有一种抽象的行为又有另一种抽象的属性，就可以继承需要行为抽象的抽象类然后在继承需要属性的抽象接口。
当你实现了两个接口，两个接口中共有一个eat方法，参数也一样，这时候怎么判断是哪种抽象的吃呢？
没法判断，因为你是说名他们都要吃，并未实现这些方法是要怎么吃和吃什么，反正就知道是吃。要知道怎么吃，就只能靠各自的抽象类来重新说明，但是如果你想让LGDOG一会是人的吃法，一会又是狗的吃法，那这个是个特别的种类，需要重新把他定义为一种新的抽象，同时实现这两种实现，他有自己的抽象类来实现他自己的吃法，既不是人吃也不是狗吃，事LGDOG吃。
还是LGDOG把，他继承了人的行为含有狗的属性，这时候重新新建一个LGDOG，他的属性是父类的，但是行为是指向自己的。
从接口到抽象类在到一个普通类，所有的方法都是又来源可追的，每一个子类都有自己独特的个性同时也可溯源，找到最根本的方法。

```

继续看源码

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vha5nfb3j30c4093t8t.jpg?tags=%5B%5D)

这时候再来看继承关系图，就很好理解了。
StandardServer他的行为由LifecycleMBeanBase来实现。

server是最外层，所以他自己要先初始化

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhcdwdepj30fk051dfx.jpg?tags=%5B%5D)

这是个内部初始化方法，这里他初始化自己
当初始化顺利进行到最后就开始初始化内部的service方法

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhedcfg6j308w02l744.jpg?tags=%5B%5D)

service有很多个，所以要循环初始化
整个初始化我也看不是很懂，就知道他调用父类初始化，然后自己初始化，新建一个MBeanFactory工厂，里边有一个server容器，来装载这个新建的容器集此刻的Server，然后再把这个server注册到一个（我也不知道是啥的）地方。。。。
在看到catalina时，不知道是个啥玩意，这个Catalina在哪里出现呢？

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhga6aesj30e50g20te.jpg?tags=%5B%5D)


对，就在初始化service上面，先要进行判断catalina是否为空，catalina这个类调用standardServer中的setcatalina这个方法，这个类没有任何继承
我不知道是啥，然后百度了一下，又是一片新天地

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhiq9ribj30ef01fwed.jpg?tags=%5B%5D)

就是apache的servlet容器的名字，tomcat所做的只是把这个servlet容器进行加载，装配和调用！
就好比在使用liat一样底层封装的时数组，我们使用tomcat，底层封装的就是Catalina！！！
tomcat和apache是什么关系！就好比卡车和卡车上的桶的关系！

## 第二次撸码
昨天第一天开始看代码，看的晕晕乎乎的，然后第二天开始整理了一下思路，其中中间看了两个视频，我觉得还不错，在底部我放出连接
第一次撸完之后休息了一天，这一天就去找视频看了，因为有点混乱，找不到一个头

开始撸码！

那么server时怎么开始加载的呢？
首先，找到启动类

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vho3g5jgj307b00vt8h.jpg?tags=%5B%5D)

这个包下的Bootstrap
我们找到main函数

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhpcv2wzj30g10c1wex.jpg?tags=%5B%5D)

这里开始初始化

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhrkmed1j30im0frq3s.jpg?tags=%5B%5D)

再初始化里边，初始化一个类加载器
再在里边通过反射来新建一个catalina类
然后启动了catalina里边的有一个setParentClassLoader方法，这个方法竟然比初始化和start都优先，他里边是什么呢？我们去看一看吧

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhtg13jqj30c4052wei.jpg?tags=%5B%5D)

这个方法就是把Bootstrap里边新建的类加载器传给了catalina。。。用得着这么节省么，再catalina里边在新建一个也行呀，这样是不是省的在去创建了呢？节省内存？我也不知道，还需要再去好好深究一下，如果有人懂的话可以教教我，一起学习。
好了，回到bootstrap类里边，回到main方法，此时初始化结束，到了接下来一步，判断当前的状态

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhuwafw3j30e50logmo.jpg?tags=%5B%5D)

这里判断的是main函数传进来的参数个数，在这里我们看到，他处理五个参数
“startd”，“stopd”，“stop”，“start”，“configtest”
在捕获是1和2时，将数组中这个参数编程不带d的
startd和start的区别时多了一个load，让我们看看这个load里边有啥，这里边有个数组，数组在这里传来传去，我判定这个数组参数应该就只会穿一个参数进去，要不load不能实现里边的方法

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhw2l6juj30bx0e1t92.jpg?tags=%5B%5D)

load的代码
最后又进到了catalina里边的load，里边的load就是创建并执行了一个Diagester，这个类是啥，我又不知道了，有机会去细究
大致意思就是重新配置了一下配置文件，并加载，如果失败就抛出异常，一个是catalina配置失败，另一个是catalina权限不足

```js
在这里我又要说一下catalina，希望大家没忘记这个类是什么？我们现在在看什么？因为整个启动过程很庞大，这里才说的是刚开始，我们现在在启动catalina，经由bootstrap来启动，启动这个水桶！要不水（servlet）怎么进得来，没有水我们怎么和客户端交互，客户端的请求我们怎么处理。
```

回到刚刚的main方法对command判断的之中去。
然后stop和stopd的区别就是，stopd还要关闭一个Diagester，二stop不需要关闭这个。
然后终于到启动了！
我们这个catalina！这个关键的servlet容器就要开始了！就要干活了！有点小激动啊。。。

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vhykqpvoj30gy0ds0t4.jpg?tags=%5B%5D)

关键就是标红的那里，getServer().start()
让catalina里边的服务起来，这个server哪来的，是由接口server的实现类来实现啊！你忘了server的实现类是哪个？不慌！我来放图

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vha5nfb3j30c4093t8t.jpg?tags=%5B%5D)

调用了这个start，我就去standardServer里边找，没找到start啊！这时候我就想到了，动态调用！

```js
有一句话是：再写调用的时候我不知道在调用哪个，只有运行的时候才能知道。
```

standardServer，LifecycleMBeanBase和LifecycleBase都是lifecycle的实现类呀（start是在lifecycle里边定义的方法）
然后我找了这三个，只有lifecycBase里边又start方法啊，看到这里，原来很多不懂得开始茅塞顿开，真的原来不在意的细节再这些大项目中一一体现出来。
废话少说，上图！

![](https://wxt.sinaimg.cn/thumb300/006BeuLUly1g0vi0wui4hj30gb0nc0u7.jpg?tags=%5B%5D)


## 第三天









