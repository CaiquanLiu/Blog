---
title: Step by Step带你玩转DuerOS - 唤醒词替换(5)
date: 2017-11-11 21:43:25
tags: DuerOS
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://www.jianshu.com/p/96284d314cbe>

当前Python版本的DuerOS只能通过[小度小度]进行唤醒。论坛里和群里很多同学问：“那如果我想通过[大白大白]，[叮当叮当]或者[你好]这样的自定义唤醒词来唤醒DuerOS改怎么做呢？”
自定义唤醒词其实超简单，下面我们就一步一步的来更换唤醒词。
## 1 在snowboy平台训练自己的唤醒词
先附上snowboy的官方地址：<https://snowboy.kitt.ai/>
### 登陆

![登陆入口](http://upload-images.jianshu.io/upload_images/4905018-58440cde7df17e4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 创建自定义的唤醒词

![自定义唤醒词创建](http://upload-images.jianshu.io/upload_images/4905018-cc4cd746d64f2f3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 下载唤醒模型
在线训练完成后，下载训练模型（本例中我的训练唤醒词为“小白”）

![唤醒模型下载](http://upload-images.jianshu.io/upload_images/4905018-dbc4a26ff06ca8dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载的模型为"小白.pmdl"
## 2 下载snowboy python接口代码
snowboy GitHub地址: <https://github.com/Kitt-AI/snowboy>
### Clone snowboy接口代码
    git clone https://github.com/Kitt-AI/snowboy.git

### 生成平台代码
在snowboy/swig/Python目录执行“make”命令，
![平台代码生成](http://upload-images.jianshu.io/upload_images/4905018-b5507aa8dd21c2f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![snowboy GitHub README.md](http://upload-images.jianshu.io/upload_images/4905018-d724412ee2566866.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 获得snowboy python接口组件
将snowboy/examples目录下的Python目录更名为snowboy

![image.png](http://upload-images.jianshu.io/upload_images/4905018-e2c29285cf7603ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3 Python DuerOS SDK代码更新
有了上面的定制唤醒模型（小白.pmdl）和snowboy python接口组件，下面就可以更新Python DuerOS SDK的代码了。
### Python DuerOS 中的app/snowboy目录内容更新
先删除Python DuerOS中的app/snowboy文件夹，然后再将步骤2中生成的snowboy文件夹拷贝到app/目录下，然后，将训练好的唤醒模型（小白.pmdl）拷贝到app/snowboy目录中。

![snowboy目录](http://upload-images.jianshu.io/upload_images/4905018-80b8c64b0d2fa0e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 修改app/snowboy/snowboydecoder.py
####[1]修改__ init __（）函数
注释掉self.audio和self.stream_in
![__init__](http://upload-images.jianshu.io/upload_images/4905018-a73a594a7cc51d03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### [2] 添加feed_data()方法

![添加feed_data()方法](http://upload-images.jianshu.io/upload_images/4905018-d8ce8225ea35022d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
####[3] 修改terminate()方法

![更新terminate()方法](http://upload-images.jianshu.io/upload_images/4905018-e144901202d0d27b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 修改app/wakeup_trigger_main.py
在main()方法中更新唤醒模型，

![唤醒模型更新](http://upload-images.jianshu.io/upload_images/4905018-0f557429b6e0b5c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 注意事项
经过上面的步骤，我们便能够更新成自己喜欢的唤醒词。但我们自定义的唤醒词还不是完美的。在测试中会发现唤醒率并不高。根本的原因在于我们自定义的唤醒词，训练语料条太少了（可能只有1组，3条）。要达到一个很好的唤醒率，需要进行大量丰富的语音样本训练才行。

![训练样本](http://upload-images.jianshu.io/upload_images/4905018-df186f9817c59151.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





