---
title: 嵌入式开发环境搭建（包括Android NDK开发）
date: 2017-07-06 23:41:51
tags: Linux
---
## linux系统安装
系统要求：
Ubuntu 16.04 64bit 
linux-kernel 4.4
使用U盘安装Ubuntu系统：<http://www.linuxidc.com/Linux/2016-04/130520.htm>
## 修改更新源
检查 /etc/apt/sources.list更新源，如果是国外的源，先更新为国内源，否则后续的软件安装会很慢。
国内更新源：<http://wiki.ubuntu.org.cn/%E6%A8%A1%E6%9D%BF:16.04source>
## 屏幕分辨率调整
默认情况，如果系统安装在公司提供的台式机上，会出现屏幕分辨率低的情况（最高只有1360x768），需要手动增加新的分辨率，参考：<http://forum.ubuntu.org.cn/viewtopic.php?t=458723>
## Linux登录客户端
建议使用Putty：<http://www.putty.org/>
## Windows与Linux间的文件互传
建议通过SCP来进行Windows和Linux间的文件互传（Linux上的FTP服务配置相对麻烦）。
Linux端开启SCP，只需要：
		
		sudo apt-get install openssh-server
Windows端可使用WinSCP或Filezilla作为客户端
## 添加中文输入法
sougou的linux输入法是不错的选择：<http://pinyin.sogou.com/linux/>
## CLion安装（C/C++开发IDE）
习惯了AndroidStudio的同学都说好.
安装参考:<http://www.linuxdiyf.com/linux/25850.html>
## Android源码下载编译
下载Android源码需要较大的硬盘空间（下载最新的7.0源码和对应的依赖工具大概需要100G的容量）。
需要注意Android的编译使用的是OpenJDK，而平时的Android应用编译使用的是Oracle的JDK。
源码下载编译参考：<http://www.jianshu.com/p/367f0886e62b>
## NDK环境搭建
如果已经搭建了AndroidStudio环境，可以直接使用SDKManager进行下载，默认会下载到SDK/ndk-bundle目录下。
Android NDK下载：<https://developer.android.google.cn/ndk/downloads/index.html>