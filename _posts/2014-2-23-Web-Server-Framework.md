---
layout: post
time: 2014-02-23
title: Web Server 和 Web Framework
category: Web-Developing
keywords: Web Server,Web Framework,正向代理,反向代理
tags: Web Server,Web Framework
description: 虽然之前做过一些网络的开发，但仅仅停留在表面的，对于web server、web framework全是一知半解的，最近想要自己做一些小应用，本着"系统"学习的原则，在正式开发之前，对现存的各种web架构做了一下调研，理清了web server,web framework等概念,为了巩固知识，现整理成本文。
---

虽然之前做过一些网络的开发，但仅仅停留在表面的，对于web server、web framework全是一知半解的，最近想要自己做一些小应用，本着"系统"学习的原则，在正式开发之前，对现存的各种web架构做了一下调研，理清了web server,web framework等概念,为了巩固知识，现整理成本文。

# 代理

在正式说明web架构之前，首先需要谈的的是"代理"的概念，之前在调研的时候，总是看到"反向代理"这个词，一直不知道是什么意思。后来仔细查了一下，终于理解了，现在记录下来。

## 正向代理

所谓**正向代理**，其实就是我们常说的"代理"，简单的说，其实它就是一个跳板，利用这个跳板你可以访问一些你原来无法访问的网络地址。也就是说**正向代理**是指:**代理服务器替代访问方去访问目标服务器**{: style="color:red"}

正向代理最主要的应用场景就是在局域网内部访问外网的时候，有些组织内部的局域网是不能直接连接外网的，如果局域网内的用户想要连上外网的话，这就需要通过一台处于局域网内且能连通外网的服务器作为代理，利用这个代理服务器，局域网内的用户就能够连上外网了。

这个过程可以用下图来表示:

![](/assets/image/posts/2014-2-23-web-server-framework-0.png)

对于`Client A`来说，它就是使用了"代理"来连上Internet.

当然，正向代理的应用场景不止是这样，其他主要的应用场景主要有:

1. **访问无法访问的服务器**.比如某个服务器A是存在于某个局域网内部的，外网是无法访问，此时如果有另外一台服务器B，它处于和A同样的局域网内，但是却可以和Internet连接，如果B设置了代理服务的话，我们就能够利用代理服务器B从外网访问到A了。
2. **加速访问**.如果用户所在的路由线路的带宽比较低的话，利用一个代理服务器是可以加速网络访问的。但是现在的宽带发展迅猛，这种使用场景已经很少了。
3. **Cache**.代理服务器可以缓存一些之前别人已经浏览过的内容，当你再次访问的时候，就不需要"千里迢迢"地去访问原始地址了，代理服务器就能够把你需要的内容返回给你。
4. **隐藏自己**.现在大家在上网的时候，会留下很多的痕迹。服务器端会如实的记录下每一次访问的IP及其他客户端的信息，但是如果使用代理访问网站的话，那么网站记录的是代理服务器的信息，而不是自己本机的信息，这样就能够隐藏自己的"行踪"了。

## 反向代理

**反向代理**的概念和**正向代理**正好相反:客户端直接访问服务器S，但是服务器却把用户的请求转发给服务A，由A处理请求，然后再通过服务器S返回给用户。在这个过程中，用户完全不知道服务器A的存在。

如下图所示:

![](/assets/image/posts/2014-2-23-web-server-framework-1.png) 反向代理的主要场景:

1. **隐藏和保护原始资源服务器**.用户Client始终认为它访问的真正的资源服务器，但利用反向代理技术，它访问的只是反向代理的服务器，这样就很好的隐藏了真正的资源服务器，防止各种千奇百怪的访问请求损害资源服务器。
2. **负载均衡**.当访问连接数非常巨大的时候，单个资源服务器已经无法满足应用需求，那就需要配置成一个服务器集群，而此时反向代理服务器能够平衡各个资源服务器的负载量，优先将请求转发给负载较小的服务器。

## 总结

从上面的分析中，可以看到，所谓**正向代理**，是指在Client和目标Server之间加入一个代理服务器，这是Client的主动行为;而所谓**反向代理**，是指在目标Sever之后加入一个代理服务器，用户完全感知不到代理服务器的存在，Client完全是"被"代理的。


# Web架构

通常来说，web应用一般是三层结构:

{% highlight bash %}
web server --->web application ---> DB server
{% endhighlight %}

## Web Server

其中的**web server**就是我之前疑惑的概念之一，其实这里的web server是指通过某种协议使得计算机能够通过网络交换数据的软件系统，通常是主从结构的,也即由Client发起请求,web server接收请求并返回结果。这其中又包括了很多各种各样的协议，其中最常用的就是http协议了，就是我们所说的网站访问了。而**web server**通常就是负责处理接收到的请求的程序。其中比较常见的**web server**主要有:apahce,lighttpd,nginx,IIS,tornado等.

web server主要有以下三个功能:

1. **高效地处理静态文件**
2. **充当简易的防火墙**
3. **处理高并发短连接请求**

## Web Framework

**Web Server**是负责处理用户发起的请求的，而真正的逻辑处理是由上述的第二层结构**web application**处理的，**web Server**接收到用户发起的请求之后，它会将这个请求转交给**web application**，由**web application**处理数据，进行运算或逻辑处理，然后将计算结果交还给**web server**,**web server**再将数据发回给用户。

而所谓的**web framework**是指用来编写**web application**的工具包。

常见的Web Framework有很多很多,包括:

- Python的web框架: Django,Bottle,Flask
- Ruby的web框架: Rails
- java的web框架: Spring,JFinal
- PHP的web框架: Yii,ThinkPHP

## DB server

**DB server**就是指存储服务，在web开发中通常是mysql，但是现在随着数据量不断增大，很多非关系型的数据库越来越多，如MongDB等。


## 总结

其实这几个概念是非常浅显的，之前之所以有疑惑，是因为现在的很多**web framework**都自己集成了小型的**web server**，比如Django、Bottle等，安装完成之后，直接能开启web服务;而很多的**web server**又集成了**web framework**的功能，能够直接写**web application**，比如tornado等[^1]。因此，之前刚接触这些框架的时候，不管三七二一能够跑起来就行了，都没有深入去了解其中的原理。现在仔细调研和分析之后，整个web服务的结构就一清二楚了。

虽然现在的很多**web framework**都集成了**web server**(或者**web server**集成了**web framework**)，但是最好还是把这两个任务分开处理，这样能使它们"各司其职",不会出现性能的问题。

[^1]: 其实，可以这么说，**Bottle**是一个**web framework**，顺带了一个开发服务器;**tornado**是一个**web server**，顺带了一个mini框架。

# 参考资料

- [全面解读python web 程序的9种部署方式](http://lutaf.com/141.htm)
- [正向代理与反向代理的区别](http://bigc.at/reverse-proxy.orz)
- [图解正向代理、反向代理、透明代理](http://z00w00.blog.51cto.com/515114/1031287)
- [知乎:如何理解 Tornado ？从 web server、web 框架、和异步 I/O 模型的角度，分别与 Nginx、Django 和 Node.js 对比。](http://www.zhihu.com/question/20136991)
