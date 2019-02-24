---
title: 《TensorFlow内核剖析》笔记
date: 2019-02-24 19:40:33
tags: TensorFlow
---
市面上对TensorFlow使用介绍的书非常之多，但剖析原理的却很少。如果对TensorFlow的底层实现感兴趣，刘光聪的《TensorFlow内核剖析》是一个不错的选择。同时，这是一本开源技术书，可以免费获得。
## DistBelife框架
![DistBelife框架](https://upload-images.jianshu.io/upload_images/4905018-4353758491c42988.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## DistBelief缺点
![DistBelief缺点](https://upload-images.jianshu.io/upload_images/4905018-8e7217b17816c5f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## TensorFlow设计原则
![TensorFlow设计原则](https://upload-images.jianshu.io/upload_images/4905018-61167f0c483ebef2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## TensorFlow优势
![TensorFlow优势](https://upload-images.jianshu.io/upload_images/4905018-b5274b1e0a620542.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## TensorFlow里程碑
![TensorFlow里程碑](https://upload-images.jianshu.io/upload_images/4905018-5a1f4453b8c12fbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## TensorFlow技术栈
![TensorFlow技术栈](https://upload-images.jianshu.io/upload_images/4905018-fc46239d6f9e5447.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## TensorFlow系统架构
**TensorFlow系统架构**
![TensorFlow系统架构](https://upload-images.jianshu.io/upload_images/4905018-64be0bebce3b0fea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**说明**
![说明](https://upload-images.jianshu.io/upload_images/4905018-b9adaaea3bf849d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Client**
![Client](https://upload-images.jianshu.io/upload_images/4905018-9edbf4350c1eb435.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Master**
![Master](https://upload-images.jianshu.io/upload_images/4905018-3a4a8f95f10fbdd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Worker**
![Worker](https://upload-images.jianshu.io/upload_images/4905018-a9d3670a01be4c17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Kernel**
![Kernel](https://upload-images.jianshu.io/upload_images/4905018-0d571744e78612d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 集群组建
![集群组建](https://upload-images.jianshu.io/upload_images/4905018-fc9adc70dba88bf6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 本地模式
**本地模式**
![本地模式](https://upload-images.jianshu.io/upload_images/4905018-7fbc67ed7ad2c16e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**本地模式：图操作**
![本地模式：图操作](https://upload-images.jianshu.io/upload_images/4905018-19beefd44fa712ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 分布式模式
**分布式模式**
![分布式模式](https://upload-images.jianshu.io/upload_images/4905018-8421bbe8a067bf2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**分布式模式-图操作**
![分布式模式-图操作](https://upload-images.jianshu.io/upload_images/4905018-078e3463fda1933b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**PS与Worker**
![PS与Worker](https://upload-images.jianshu.io/upload_images/4905018-30a2d980b9f325ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**服务互联**
![服务互联](https://upload-images.jianshu.io/upload_images/4905018-889d5c03ed5237d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**单Client接入集群**
![单Client接入集群](https://upload-images.jianshu.io/upload_images/4905018-1cb2f9c97633ede4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**多Client接入集群**
![多Client接入集群](https://upload-images.jianshu.io/upload_images/4905018-2361f428ce897631.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**会话协同**
![会话协同](https://upload-images.jianshu.io/upload_images/4905018-533b782f7bbf85fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**会话控制领域模型**
![会话控制领域模型](https://upload-images.jianshu.io/upload_images/4905018-7a2cfa2712dc4900.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**获取远端设备集**
![获取远端设备集](https://upload-images.jianshu.io/upload_images/4905018-550f9faaa2881cdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
