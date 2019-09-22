---
title: Step by Step带你玩转DuerOS - DuerOS能力初体验(2)
date: 2017-11-11 21:33:42
tags: DuerOS
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://www.jianshu.com/p/267334be6954>

有了前面两篇文章的介绍，相信大家已经跃跃欲试了，想最快的体验DuerOS的能力？那当然是下载DuerOS App啦，

![度秘App](http://upload-images.jianshu.io/upload_images/4905018-dc6b78232b08c78a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果想在硬件上体验，可以尝试树莓派版本，


![基于树莓派的个人开发套件](http://upload-images.jianshu.io/upload_images/4905018-fbd911a8fa84a6ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

项目地址：<http://open.duer.baidu.com/doc/device-devkit/intro_markdown>

但不论是手机端的App还是基于树莓派的个人开发套件，但都有一个开发者不能忍缺陷：只能“看”，不能“玩”。作为机智神勇的开发者，肯定需要能定制、能修改、能改造的。

# 新的选择
针对上面的情况，这里提供了一个Python版本的DuerOS。
项目地址：<https://github.com/MyDuerOS/DuerOS-Python-Client>
之所以选择Python主要出于以下方面的考量：
* 简单易学，即便是没有编程经验的同学也能快速上手
* 环境搭建简单，避免各种编译、链接问题
* AI时代必备技能，AI时代你还不会Python？

当前版本支持功能：
* 完成OAuth 2.0认证
* 通过[Enter]键触发唤醒状态
* 支持天气、百科等TTS播放功能
* 支持音乐播放功能
* 支持闹铃功能
* 云端下发Directive显示

后续会支持跟多的功能，比如，
* [小度，小度]语音唤醒
* 接入百度的技能平台，完成自定义功能

希望大家把使用过程中遇到的问题提出来，便于后续的优化

# DuerOS-Python-Client使用说明
## 运行依赖
* gstreamer1.0
* gstreamer1.0-plugins-good
* gstreamer1.0-plugins-ugly
* python-gi
* python-gst
* gir1.2-gstreamer-1.0
## 测试环境
* Ubuntu 16.04
* Python 2.7.12
## 使用说明
### 项目获取
通过git下载代码到本地

    # git clone https://github.com/MyDuerOS/DuerOS-Python-Client.git

### 认证授权
在DuerOS-Python-Client目录下执行
 
    # ./auth.sh

### 通过[Enter]键触发唤醒状态
在DuerOS-Python-Client目录下执行

    # ./enter_trigger_start.sh

然后，每次单击[Enter]键后进行语音输入
### 通过[小度小度]触发唤醒状态
在DuerOS-Python-Client目录下执行(暂时该功能还不支持)

    # ./wakeup_trigger_start.sh
然后，每次通过[小度小度]进行唤醒，然后，进行语音输入

 
## 代码结构

DuerOS-Python-Client代码结构如下图所示，


![代码结构 ](http://upload-images.jianshu.io/upload_images/4905018-159f48b317d7dcce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，

*DuerOS-Python-Client:项目根目录*

* DuerOS-Python-Client/auth.sh:认证授权脚本
* DuerOS-Python-Client/enter_trigger_start.sh:[Enter]按键触发唤醒脚本
* DuerOS-Python-Client/wakeup_tirgger_start.sh:[小度小度]触发唤醒脚本

*DuerOS-Python-Client/app:应用目录*

* DuerOS-Python-Client/app/auth.py:认证授权实现模块
* DuerOS-Python-Client/app/enter_trigger_main.py:[Enter]按键触发唤醒实现模块
* DuerOS-Python-Client/app/wakeup_tirgger_main.py:[小度小度]触发唤醒实现模块
* DuerOS-Python-Client/app/framework:平台相关目录
* DuerOS-Python-Client/app/framework/mic.py:录音模块(基于pyaudio)
* DuerOS-Python-Client/app/framework/player.py:播放模块(基于GStreamer)
* DuerOS-Python-Client/app/snowboy:snowboy唤醒引擎

*DuerOS-Python-Client/sdk:dueros sdk目录*

* DuerOS-Python-Client/sdk/auth.py:授权相关实现
* DuerOS-Python-Client/sdk/dueros_core.py:dueros交互实现
* DuerOS-Python-Client/sdk/interface:端能力接口实现

## SDK接口说明
### 授权模块(sdk/auth)
#### 授权接口
用户通过授权接口完成基于OAuth2.0的认证授权流程

    def auth_request(client_id=CLIENT_ID, client_secret=CLIENT_SECRET):
    '''
    发起认证
    :param client_id:开发者注册信息
    :param client_secret: 开发者注册信息
    :return:
    '''

### DuerOS核心模块(sdk/dueros_core)
#### directive监听注册
通过监听注册接口，用户可以获得云端下发的directive内容

        def set_directive_listener(self, listener):
        '''
        directive监听器设置
        :param listener: directive监听器
        :return:
        '''