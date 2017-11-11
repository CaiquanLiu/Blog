---
title: Step by Step带你玩转DuerOS - OAuth2.0全面解析 (4)
date: 2017-11-11 21:42:32
tags: DuerOS
---
DuerOS的开发平台在创建产品时，有一步需要进行OAuth设置（<http://open.duer.baidu.com/doc/overall/console-guide_markdown>），
![DuerOS OAuth配置](http://upload-images.jianshu.io/upload_images/4905018-60bba90bdfea249e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
很多同学到这个步骤估计会很困惑，OAuth是什么？这里设置的URL有是什么呢？那我们这篇文章就好好讲一讲OAuth到底是什么？在DuerOS的接入过程中又是怎么使用的。

## 什么是OAuth
 其实OAuth在我们日常中是非常常见的，但可能你没有专门的关注过功能背后的实现原理。这里有一篇阮一峰老师专门介绍OAuth的文章《理解OAuth 2.0》(<http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html>)，这是我目前为止见得对OAuth讲解最浅显又是最全面的一篇（ps 阮一峰老师的文章质量都很高呀）。
## DuerOS是如何使用OAuth的
OAuth有4中授权模式，

![OAuth授权模式](http://upload-images.jianshu.io/upload_images/4905018-ef5c25357e5d5e5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而DuerOS使用的便是其中功能最完整、流程最严密的授权码模式。下面我们通过Python版本的DuerOS客户端来分析下DuerOS对OAuth的使用。

### 认证流程组成单元
授权码模式的认证流程如下所示，

![授权码模式认证流程](http://upload-images.jianshu.io/upload_images/4905018-ec351bc35c5ffe90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

主要涉及到4个基本单元：
* A-用户客户端
* B-用户端浏览器（完成URI重定向）
* C-用户客户端对应的后台服务器
* D-认证服务器

针对于DuerOS Python客户端，A、B、C、D分别对应：
* A-auth.sh（app/auth.py）
* B-系统浏览器
* C-app/auth.py中创建的Tornado WebServer
* D-百度的认证服务器
### OAuth认证流程
下面我们结合代码，一步一步的解析OAuth的整个过程
####1.用户客户端（A）对应的后台服务器（C）创建
运行，

    # ./auth.sh
Python客户端会创建一个本地的Tornado WebServer，

![Tornado WebServer创建（app/auth.py）](http://upload-images.jianshu.io/upload_images/4905018-2d971c3a33e20398.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个Tornado WebServer只是为了调试方便被部署在本地，实际项目应用中会作为独立的Web服务，部署在云端的。
#### 2. 客户端（A）吊起本地浏览器（B）访问客户端对应的后台服务器（C）
好像有些绕（汗。。。）

![app/auth.py](http://upload-images.jianshu.io/upload_images/4905018-011f163eefc61fc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，URL（http://127.0.0.1:3000）就是客户端对应的后台服务器（C）的地址。
#### 3. 客户端对应的后台服务器（C）通过重定向的方式让客户端浏览器（B）对百度认证服务器（D）发送认证请求
哎，好像更绕了（再汗。。。）

![app/auth.py](http://upload-images.jianshu.io/upload_images/4905018-c2c2be2650e19f54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4. 百度认证服务器（D）向客户端浏览器（B）返回用户登录页面
![用户登录页面](http://upload-images.jianshu.io/upload_images/4905018-2ba2989d06355468.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前面的步骤，用户客户端（A）、用户客户端对应的后台服务器（C）、百度认证服务器（D），三者之间的通信都需要客户端浏览器（B）进行重定向做请求转发。这样的过程是不是感觉很困惑呢？为啥非要通过浏览器（B）来做重定向请求转发呢？为啥不直接进行Http请求呢？
现在看到这个页面应该有答案了吧：需要由浏览器（B）来展示来自百度认证服务器（D）的认证页面，并完成用户身份认证的交互。
####5. 百度认证服务器（D）将授权码通过客户端浏览器（B）转发给客户端对应的后台服务器（C）
用户在第4步中输入账号、密码后，点击登录并授权。百度的认证服务器（D）会收到用户登录请求，然后将授权码通过客户端浏览器（B）重定向的方式发送给客户端对应的后台服务器（C）。

![app/auth.py](http://upload-images.jianshu.io/upload_images/4905018-ee62c52781a71782.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 6. 客户端后台服务器（C）通过授权码向百度认证服务器（D）请求Token
客户端后台服务器（C）通过上一步获得的授权码向百度认证服务器（D）请求授权，并获取Token，

![app/auth.py](http://upload-images.jianshu.io/upload_images/4905018-21b5df4a851d3fda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 7. 用户客户端（A）从客户端的后台服务器（C）中获取Token
由于Python DuerOS客户端中的后台服务器（Tornado WebServer）就在本地，所以，Token可以直接获得，
#### 8. token使用
千辛万苦通过OAuth流程获取的Token其实在DuerOS的后续请求中只是一个字段而已，

![请求中的token字段](http://upload-images.jianshu.io/upload_images/4905018-9f955b8fbdc3c4b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




















