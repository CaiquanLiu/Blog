---
title: 'Step by Step带你玩转DuerOS - Python DuerOS SDK[树莓派平台] (3)'
date: 2017-11-11 21:41:26
tags: DuerOS
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://www.jianshu.com/p/e689d770d99d>

在前一个帖子中，给大家带来了Python版本的DuerOS SDK。但DuerOS的测试环境却声明为Ubuntu，相信很多同学会疑惑：Python不是跨平台的吗？为什么要限制测试平台呢？真实的情况呢是这样的：
首先，Python跨平台这个没毛病。但问题在于DuerOS运行所需要的依赖环境确实跟平台相关的。比如DuerOS是基于Http2 ALPN的，但树莓派官方镜像的OpenSSL并不支持，而对应的Python库依赖于OpenSSL。为了在树莓派平台上支持Python的DuerOS SDK，专门交叉编译了OpenSSL和Python。
所以，这里限制了平台主要是方便同学能将DuerOS快速的Run起来。当然，如果想在MacOS、Windows平台运行DuerOS Python SDK也是没问题的，只是在依赖配置方面可能要多花些时间。
好了，废话不多说，直接上干货：
# 树莓派DuerOS Python 版本支持

##准备
按照个人版使用说明完成如下3步（<http://open.duer.baidu.com/doc/device-devkit/intro_markdown>）

- 镜像下载，烧录安装
- 配网
- 验证“小度小度”

## 开始
1. 停止现有小度功能，因为会占用MIC资源
		
		sudo systemctl disable duer
		sudo systemctl stop duer
2. 安装依赖包
	
		sudo apt-get update
		sudo apt-get install python-dateutil
		sudo apt-get install gir1.2-gstreamer-1.0
		sudo apt-get install python-pyaudio
		sudo apt-get install libatlas-base-dev
		sudo apt-get install python-dev		
		sudo pip install tornado
		sudo pip install hyper
hyper库用来支持http2.0 client, pyaudio用来支持录音，tornado用来完成oauth认证。

3. 下载编译好的openssl和Python安装包，并进行安装, **需要更新openssl才能支持python sdk的使用**。

	   *从如下地址下载openssl安装包*(链接: https://pan.baidu.com/s/1skAP6WH 密码: wknz)
	   *从如下地址下载python2.7.14安装包*(链接: https://pan.baidu.com/s/1o8MHkzK 密码: ngx4)

		tar -zxvf openssl1.1.tar.gz -C /usr
		tar -zxvf python2.7.14.tar.gz -C /usr/local/
		sudo rm -rf /usr/bin/python
		sudo ln -s /usr/local/python2.7.14/bin/python /usr/bin/python
4. 下载Python SDK和参考示例代码

		git clone https://github.com/MyDuerOS/DuerOS-Python-Client.git
	    cd DuerOS-Python-Client
		git checkout raspberry-dev
		

##运行和测试
1. 授权
				
		./auth.py
直接运行使用默认的client_id和client_secret，开发者可以替换成自己在DuerOS开放平台申请的client_id和client_secret，进而实现在控制台自定义的配置属性。

![app/auth.py](http://upload-images.jianshu.io/upload_images/4905018-b8e7c555be24d4fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，
需要在开放平台中“安全设置”的“授权回调页"，设置成  

    http://127.0.0.1:3000/authresponse

![授权回调页设置](http://upload-images.jianshu.io/upload_images/4905018-caca627041b10380.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 唤醒加识别
	
		./wakeup_trigger_start.sh
3. enter按键触发识别

		./enter_trigger_start.sh