---
title: Step by Step带你玩转DuerOS - DuerOS客户端架构设计(6)
date: 2017-11-11 21:44:23
tags: DuerOS
---
在前面的系列文章中，我们完整的分析了智能语音系统的系统组成，然后，我们提供了一个基于Python的DuerOS客户端供大家实际体验。这时候很多同学可能想：我能不能从零开始，完全的基于DuerOS提供的后台能力接口，用自己喜欢的语言（如C/C++、Java、ObjectC、Python、NodeJS），在自己熟悉的平台上（如Android、iOS、Linux、Windows、树莓派、单片机），自己独立完成DurOS的客户端开发呢？特别是对有产品化想法的同学，把DurOS的能力集成到自己的项目平台中。
这篇文章就跟大家一起来熟悉下DuerOS客户端实现的架构设计，主要是侧重对DuerOS客户端协议的解析（对于设备端开发过程中涉及到的更多细节，如AEC、采样率转换、音频焦点管理、混音处理），会在后面的文章中一一作出介绍和解析），希望这篇文章能对你的开发和工作有帮助。
## DuerOS客户端核心任务
简单的说，客户端的主要任务就是，将用户的语音数据通过网络传给DuerOS后台，然后，执行DuerOS后台经过语音处理后的结果。
当然了如果，真是这么简单就好了~
具体的说，客户端需要完成的工作主要有，
* 同DuerOS后台建立长链接，接收并处理DuerOS后台的推送命令（Directive）
* 上传用户录音数据到DuerOS后台服务器
* 执行上传录音后，DuerOS后台返回的控制命令（Directive）
* 上传设备事件[Event]到DuerOS后台服务端
* 通过Ping的方式保持链接
其中，
Directive：指DurOS服务端对客户端的控制命令，如播放一个语音，设置一个闹钟，播放一个音乐等等。
Event：指客户端上报当前发生的事件到DuerOS服务端，如音乐播放开始了，音乐播放结束了，闹铃开始响了，设备被唤醒并开始接受用户语音请求等等。在上传Event时，会附带设备当前的状态信息（ClientContext，如当前是否有音乐正在播放，播放到哪里了，设备端是否有设置闹铃，闹铃状态等等）。
## 代码实现
下面我们就结合具体场景和代码，逐个的来看各个部分是如何实现的。
分析场景:
* 用户语音输入“播放周杰伦的歌”
* 客户端上传用户的语音数据到DurOS后台服务器
* DuerOS后台服务判断用户输入结束，下发停止录音Directive
* 客户端响应停止录音的Directive
* 客户端响应录音通道，服务端返回的需要播放歌曲的Directive
* 客户端上报开始音乐播放的状态到DuerOS后台服务器
### 1. 同DuerOS后台建立长链接，接收并处理DuerOS后台的推送命令（Directive）
长链接主要实现DuerOS后台主动向客户端发送命令。比如，客户端一直向DuerOS后台发送数据，当DuerOS后台检测到用户输入结束时（VAD检测），DuerOS后台会主动通知客户端停止录音，并上传录音数据。
DuerOS的所有能力都是基于Http的（没有使用TCP/IP），为了实现长链接DuerOS的做法是：
* 客户端向DuerOS服务端发送get请求（设置较长的超时时间）
* DuerOS服务端收到get请求后，并不立即回复
* DuerOS服务端需要主动下发命令给客户端时，返回get结果
* 客户端通过接收get的返回内容，完成DuerOS服务端主动推动内容的接收
Python DuerOS客户端对应的代码实现(sdk/dueros_core.py)，
*长链接建立：*
![长链接建立](http://upload-images.jianshu.io/upload_images/4905018-4d9a1721507bebef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*DuerOS 下发Directive接收*

![DuerOS 下发Directive接收](http://upload-images.jianshu.io/upload_images/4905018-536eeee3e75eb23c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 2. 上传用户录音数据到DuerOS后台服务器
上传用户录音数据不单单是用户说话的音频流PCM数据，同时包括设备的状态信息（ClientContext），和开始录音的Event（ListenStarted）。状态和数据发送通过Post完成，Http的Body内容如下所示，

![用户录音数据上传报文格式](http://upload-images.jianshu.io/upload_images/4905018-a353f89c6f568c45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，ClientContext、Event、和音频流PCM通过multipart+chunk的Http Post方式进行上传（后面可能会添加一个帖子，专门介绍下Http 1.0、1.1、 2.0、multipart、chunk传输等内容）。
Python DuerOS客户端对应实现（sdk/dueros_core.py），
![用户录音数据上传](http://upload-images.jianshu.io/upload_images/4905018-b12be3fba76cf350.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3. 执行上传录音后，DuerOS后台返回的控制命令（Directive）
用户语音输入“播放周杰伦的音乐”后，语音上传通道会收到DuerOS后台服务器返回需要播放的音乐的Directive。

![录音上传后返回的Directive](http://upload-images.jianshu.io/upload_images/4905018-3a61465f85971cfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 4. 客户端上传音乐开始播放的Event
音乐开始播放后，客户端上传音乐开始播放的Event到DuerOS服务端（sdk/interface/audio_player.py）
![上传音乐开始播放的Event](http://upload-images.jianshu.io/upload_images/4905018-8fc5e96cec3343b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 5. Ping
Ping主要为了链接的维护。Http 1.0中每次Http 请求都需要进行TCP/IP的3次握手和4次挥手，Http1.1后支持连接复用，一次TCP/IP连接可以完成多次Http请求和回复。DuerOS使用Http2.0，为了避免链路上长时间没有数据传输而断开，使用Ping维持链接。
sdk/dureos_core.py
![Ping](http://upload-images.jianshu.io/upload_images/4905018-bff62564fccc0d81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 总结
### 通路总结
客户端同DuerOS服务端的通路一共有3个：
* 长链接通路

![长链接](http://upload-images.jianshu.io/upload_images/4905018-d7be5cba60305187.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双向
客户端发送get请求
服务端通过get response的形式推送消息（如停止录音Directive）
* 事件上报通路

![事件上报](http://upload-images.jianshu.io/upload_images/4905018-a8b2d06e62454112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双向
客户端发送post请求（包含开始录音的Event、当前设备状态ClientContext、录音数据PCM）
服务端返回语音请求的结果（如播放歌曲的Directive）
客户端单独上传事件到DurOS服务端（如开始播放音乐）
* Ping通路

![Ping](http://upload-images.jianshu.io/upload_images/4905018-4ce59769a9bd22dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单向
客户端通过get形式在链路没数据时发送Ping数据包
### 注意事项
* 事件（Event）和设备当前状态（ClientContext）一定是一起发送的
* Directive下发通道
长链接通道和事件上报通路（录音数据上传）都会下发Directive（如，长链接会下发停止录音的Directive，录音上传通道会下发包含音乐播放内容的音乐播放Directive）
* Event上传通道
录音数据上传（包括开始录音Event、当前设备状态ClientContext和录音数据PCM）和单独的事件上报（如音乐开始播放）共用同一通道
* Http技术细节
Http2.0特性
链接复用
MultiPart
Chunk
Ping连接维护
关于Http相关的技术细节后面专门写篇帖子吧







