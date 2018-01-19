---
title: 树莓派平台移植DLNA库（Platinum UPnP）
date: 2018-01-19 16:17:09
tags: 物联网(IOT)
---
## 背景介绍
DLNA 成立于2003 年6 月24 日,  其前身是DHWG （Digital Home Working Group 数字家庭工作组），由Sony、Intel、Microsoft等发起成立、旨在解决个人PC ，消费电器，移动设备在内的无线网络和有线网络的互联互通，使得数字媒体和内容服务的无限制的共享和增长成为可能。官方网址：https://www.dlna.org/。

2017年2月，DLNA委员会昨日正式对外宣布，该组织已于今年1月5日正式解散，并表示该组织解散的原因是旧的标准已经无法满足新设备的发展趋势，DLNA标准将来也不会再更新。DLNA委员会解散后，设备的认证和测试等工作将由DLNA高管在美国波特兰成立的SpireSpark公司接管，新的负责组织将在新的费用结构下，做包括HTML5在内的DLNA的DRM和认证。

## 设备组成
Home NetWork Device(HND)，这类设备指家庭设备，具有比较大的尺寸及较全面的功能，主要与移动设备区别开来，下属5类设备：

* DMS（Digital Media Server)
数字媒体服务器，提供媒体获取、记录、存储和输出功能。同时，内容保护功能是对DMS的强制要求。DMS总是包含DMP的功能，并且肯能包含其他智能功能，包括设备/用户服务的管理；丰富的用户界面；媒体管理/收集和分发功能。DMS的例子有PC、数字机顶盒（附带联网，存储功能）和摄像机等等。
* DMP
数字媒体播放器。能从DMS/M-DMS上查找并获取媒体内容并播放和渲染显示。比如智能电视、家庭影院等
* DMC
数字媒体控制器，查找DMS的内容并建立DMS与DMR之间的连接并控制媒体的播放。如遥控器。
* DMR
数字媒体渲染设备。通过其他设备配置后，可以播放从DMS上的内容。与DMP的区别在于DMR只有接受媒体和播放功能，而没查找有浏览媒体的功能。比如显示器、音箱等。
* DMPr
数字媒体打印机，提供打印服务。网络打印机，一体化打印机就属于DMPr。

## DLNA SDK选择
目前主流的DLNA SDK可以在OFC（Open Connectivity Foundation）官网上查询（https://openconnectivity.org/developer/specifications/upnp-resources/upnp-developer-resources#sdks）

![主流DLNA SDK](http://upload-images.jianshu.io/upload_images/4905018-3eb1b5067145ace6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


树莓派平台的验证测试采用了Platinum UPnP，官方网址：http://www.plutinosoft.com/platinum。

选择Platinum UPnP主要出于如下考虑：

* 使用广泛
目前已有较多的产品基于该SDK进行DLNA功能开发；
* 跨平台
采用C++编写，集成Neptune C++运行库。同时，编译框架也做了跨平台考虑，默认支持Linux、windows、Android等平台；
* 代码开源
代码完全开源；
* 示例丰富
内置FileMediaServerTest，MediaRendererTest等丰富参考样例，方便开发参考；

需要注意的是Platinum UPnP针对企业用户和开发者用户采用GPL和商用许可证两个license，
![双许可证](http://upload-images.jianshu.io/upload_images/4905018-bbf65bc0f7d69986.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 树莓派平台移植Platinum UPnP
下面将通过集成Platinum UPnP，使树莓派具备DMR功能，能够被DMS设备（如手机上的百度音乐App）发现，并接收来自DMS设备推送的资源URI，及播放状态信息。
### 安装cons
Platinum UPnP基于scons进行编译，所以，首先要安装scons。安装过程可以参考[《ubuntu下安装SCons》](http://blog.csdn.net/djy37010/article/details/51312975)

### Platinum UPnP下载

		# git clone https://github.com/plutinosoft/Platinum.git
### 更新依赖
Platinum UPnP依赖Neptune，

		# cd Platinum
		# git submodule update --init
### 增加树莓派平台支持
默认Platinum UPnP并不支持树莓派平台，需要我们手动添加。

####1. 添加树莓派平台目标目录
在Platinum/Build/Targets目录添加arm-raspberry-linux目录

![添加树莓派平台目录](http://upload-images.jianshu.io/upload_images/4905018-63f5fab7968c08f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2. 添加树莓派平台交叉编译配置文件
在Platinum/Build/Targets/arm-raspberry-linux目录下添加Config.scons文件，文件内容如下，
![交叉编译配置文件](http://upload-images.jianshu.io/upload_images/4905018-83de3f3248a5cd0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 增加MDR打印信息
修改Platinum/Source/Devices/MediaRenderer/PltMediaRenderer.cpp文件，
![增加MDR打印信息](http://upload-images.jianshu.io/upload_images/4905018-964cc7b291ef7d2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 编译输出
在Platinum目录运行，

		# scons target=arm-raspberry-linux build_config=Debug
编译生成的应用程序在Platinum/Targets/arm-raspberry-linux/Debug目录下，
![生成应用程序](http://upload-images.jianshu.io/upload_images/4905018-a1cb1c77e2aa218e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


编译生成的静态库在Platinum/Build/Targets/arm-raspberry-linux/Debug目录下，
![生成静态库](http://upload-images.jianshu.io/upload_images/4905018-2d08b5d78fc69c61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 测试验证
树莓派和安装百度音乐的手机处于同一局域网内，在树莓派端运行MediaRenderTest，启动树莓派端的DMR能力。

		# ./MediaRendererTest -f eddy-raspberry

#### 1.百度音乐[DMS]发现树莓派[DMR]设备
![MDS](http://upload-images.jianshu.io/upload_images/4905018-e1f4beb91f957047.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2.树莓派[DMR]接收来自百度音乐[DMS]的信息
![MDR](http://upload-images.jianshu.io/upload_images/4905018-cfaf64928b35a289.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 资料参考：
[《DLNA介绍》](http://blog.csdn.net/lancees/article/details/8230877)