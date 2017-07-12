---
title: Eddy的AI小助手-请求处理框架模块(15)
date: 2017-07-12 17:01:46
tags: Eddy的AI学习之旅
---
基于Python的MyTuringService主要用于进行数据处理，并响应来自Java微信服务器（MyWeChatService）的请求，数据流框图如下所示，

![数据流框图](Eddy的AI小助手-请求处理框架模块-15/数据流框图.png)

其中，
* 微信客户端：才权的AI小助手微信公众号客户端；
* 微信服务器：腾讯针对有微信的后台服务器；
* MyWeChatService：基于Tomcat的Java WebServer，用于与微信公众号进行绑定；
* MyTuringServcie：基于Tornado的Python WebServer，用于进行离线数据处理和响应MyWeChatService的请求；

MyTuringService WebServer入口，

![MyTuringService WebServer入口](Eddy的AI小助手-请求处理框架模块-15/MyTuringServiceWebServer入口.png)

其中，

* wechat_server.py：MyTuringService WebServer入口；
* http_client.py：模拟MyWeChatService的Http请求（用于测试）；

# 项目地址
Java代码：[http://github.com/CaiquanLiu/MyWeChatService.git](http://github.com/CaiquanLiu/MyWeChatService.git)
Python代码：[https://github.com/CaiquanLiu/MyTuringService](https://github.com/CaiquanLiu/MyTuringService)