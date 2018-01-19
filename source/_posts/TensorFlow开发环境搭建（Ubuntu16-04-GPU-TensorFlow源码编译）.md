---
title: TensorFlow开发环境搭建（Ubuntu16.04+GPU+TensorFlow源码编译）
date: 2018-01-11 20:47:36
tags: 机器学习/深度学习
---
# 1 安装选择
## 1.1 平台
目前TensorFlow已支持Mac、Ubuntu和Windows三个主流平台（64位平台），
![https://www.tensorflow.org/install/](http://upload-images.jianshu.io/upload_images/4905018-d562d05d81808e9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 1.2 GPU vs CPU
在安装时可以选择安装版本是否支持GPU，
![https://www.tensorflow.org/install/install_linux](http://upload-images.jianshu.io/upload_images/4905018-63dce636960a5e42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 1.3 二进制安装 vs 源码安装
同时，安装时可以考虑采用二进制安装，还是源码安装，
![https://www.tensorflow.org/install/](http://upload-images.jianshu.io/upload_images/4905018-caff1336d1dd1238.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 1.4 二进制安装选择
二进制安装可以有多种选择，
![https://www.tensorflow.org/install/install_linux](http://upload-images.jianshu.io/upload_images/4905018-6d617b3d2e736011.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 1.5 实际使用选择
手头上有两台电脑，
* MacBook Pro (Retina, 13-inch, Early 2015)
处理器：2.7 GHz Intel Core i5
内存：8GB 1867MHz DDR3
显卡：Intel Iris Graphics 6100 1536MB
* 攀升兄弟组装台式机
处理器：英特尔 Core i7-6700 @3.40GHz 四核
主板：华硕 B150M-ET M2 SERIES
内存：8G（威刚DDR4 2801MHz）
硬盘：三星 MZ7TE256HMHP-00000（256GB/固态硬盘）
显卡：Nvidia GeForce GTX 950(2GB)

其中，
MacBook Pro采用基于Anaconda的二进制安装方式（仅支持CPU）。Anaconda是一个集成平台，包含大多数机器学习的常用工具，
* NumPy：科学运算包
* SciPy：在NumPy的基础上构建，功能更加强大的科学计算包
* Matplotlib：类似Matlab的绘图工具包
* Scikit-learn：经典机器学习工具包
* Pandas：数据处理和分析工具包（可用于数据读写、清洗、填充和分析等场景）
采用Anaconda安装方案，一方面MacBook Pro的开发环境很容易搭建（几句命令即可完成），另一方面，基于Anaconda的环境，也可以方便验证简单的机器学习算法。

对于台式机则采用了Ubuntu+GPU+TensorFlow源码编译的方式，进行TensorFlow环境搭建，下面我们着重介绍下这种安装方式。
# 2 Ubuntu16.04+TensorFlow(GPU)源码编译
## 2.1 Ubuntu系统安装
目前，大多数计算机，包括台式机已没有了光驱，同时，Ubuntu的系统安装盘也不易获得。针对这种情况，我们可以采用U盘来进行安装。具体步骤可以参考：[《Ubuntu 16.04 U盘安装图文教程》](http://www.linuxidc.com/Linux/2016-04/130520.htm)

## 2.2 禁用UEFI安全启动
对于华硕 B150M-ET主板，UEFI默认是开启的。而UEFI开启会导致第三方驱动安装失败（如显卡驱动，这是源码编译安装GPU支持的TensorFlow，遇到的第一个坑）。具体步骤可以参考：[《华硕主板禁用UEFI安全启动》](https://www.cnblogs.com/zhaofenqiang/p/6071385.html?utm_source=itdadao&utm_medium=referral)
## 2.3 安装NVIDIA驱动
### 2.3.1 安装方式
英伟达的显卡驱动有三种方式：
* apt-get+系统设置安装
* 安装CUDA时顺便安装
* 官方下载最新驱动并安装
参考：[《Ubuntu 16.04安装NVIDIA驱动》](http://blog.csdn.net/cosmoshua/article/details/76644029)
### 2.3.2 安装步骤
本文采用第一种方案：

      sudo apt-get install nvidia-367

然后进入：System Settings->Software&Updates->Additional Drivers->，选择NVIDIA Corporation[Using NVIDIA binary driver]
### 2.3.3 验证测试

    nvidia-smi #若列出GPU的信息列表，表示驱动安装成功
    nvidia-settings #若弹出设置对话框，亦表示驱动安装成功
## 2.4 安装CUDA
CUDA(Compute Unified Device Architecture)，是显卡厂商NVIDIA推出的运算平台。可以理解成基于GPU并行计算的应用层接口。
[CUDA8.0下载地址](https://developer.nvidia.com/cuda-80-download-archive)
![https://developer.nvidia.com/cuda-80-download-archive](http://upload-images.jianshu.io/upload_images/4905018-d71f88879413fe85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装Cuda的时候，需要关闭X服务。

    sudo service lightdm stop

这时，系统会出现黑屏。此时，同时按住[CTRL + ALT + F1]三个键进入命令行模式（如果不能进入命令行模式，可以参考[《alt+ctrl+F1黑屏 ，解决方案》](http://blog.csdn.net/u010733679/article/details/52022094)），
然后输入[账号]，[密码]后登陆。
在CUDA的下载目录运行，

    sudo sh cuda_xxx.run
需要注意的是，在询问是否安装“NVIDIA Accelerated Graphics Driver”可以选择“是”，

    Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 361.62?
    (y)es/(n)o/(q)uit: y

但在后续出现询问是否安装“X configuration”时，则需要选择“否”，否则之前安装的显卡驱动就白安装了。
CUDA安装结束后，则可以恢复到图形界面模式，

    sudo service lightdm start

到这里，CUDA的安装还不算结束，需要将CUDA相关的内容添加到系统环境变量中。安装过程中Summary提示，

    Please make sure that
    – PATH includes /usr/local/cuda-8.0/bin
    – LD_LIBRARY_PATH includes /usr/local/cuda-8.0/lib64, or, add /usr/local/cuda-8.0/lib64 to /etc/ld.so.conf and run ldconfig as root

CUDA相关的环境变量可以放在~/.bashrc中，

    vi ~/.bash_profile

在其中，增加如下两行，

    export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda-  8.0/lib64:/usr/local/cuda-8.0/extras/CUPTI/lib64"
    export CUDA_HOME=/usr/local/cuda-8.0

## 2.5 安装cuDNN
cuDNN（CUDA Deep Neural Network）相比标准的cuda，它在一些常用的神经网络操作上进行了性能的优化，比如卷积，pooling，归一化，以及激活层等等。
下载地址：<https://developer.nvidia.com/rdp/cudnn-download>
![https://developer.nvidia.com/rdp/cudnn-download](http://upload-images.jianshu.io/upload_images/4905018-1c28d7c5a2928ca9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载cuDNN后进行解压，并执行如下命令：

    sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include
    sudo cp -d cuda/lib64/libcudnn* /usr/local/cuda-8.0/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda-8.0/lib64/libcudnn*
## 2.6 gcc降低版本
网上说cuda8.0不支持5.0以上的编译器，因此需要降级，把编译器版本降到4.9，但我看了CUDA8.0的安装手册《NVIDIA CUDA INSTALLATION GUIDE FOR
LINUX》，并没有发现这一条，
![《NVIDIA CUDA INSTALLATION GUIDE FOR
LINUX》](http://upload-images.jianshu.io/upload_images/4905018-aee83a196d31033d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在实际使用中，还是将GCC做了降级（PS，在安装时可以先不降级，看看是否会出问题），GCC降级方法如下，

    sudo apt-get install g++-4.9
    sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 20
    sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 10
    sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 20
    sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 10
    sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 30
    sudo update-alternatives --set cc /usr/bin/gcc
    sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 30
    sudo update-alternatives --set c++ /usr/bin/g++

## 2.7 安装Bazel
Bazel是一个构建工具，即一个可以运行编译和测试来组装软件的工具，跟Make、Ant、Gradle、Buck、Pants和Maven一样。TensorFlow的编译是基于Bazel完成的。
Bazel官方地址：<https://docs.bazel.build/versions/master/install.html>
![https://docs.bazel.build/versions/master/install.html](http://upload-images.jianshu.io/upload_images/4905018-f5576ff594245afb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.7.1 安装准备
![https://docs.bazel.build/versions/master/install-ubuntu.html](http://upload-images.jianshu.io/upload_images/4905018-01725f875ea14762.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.7.2 安装
Bazel可以通过apt-get和下载安装两种方式完成，本文中，采用下载安装的方式实现，

    chmod +x PATH_TO_INSTALL.SH 
    ./PATH_TO_INSTALL.SH --user 

## 2.8 第三方库安装

    sudo apt-get install python-numpy swig python-dev python-wheel
    sudo apt-get install libcupti-dev
    sudo apt-get install git

## 2.9 TensorFlow源码编译
### 2.9.1 下载

    git clone https://github.com/tensorflow/tensorflow

### 2.9.2 编译配置

    cd ~/tensorflow
    ./configure

### 2.9.3 编译安装

    bazel build -c opt //tensorflow/tools/pip_package:build_pip_package
    bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
    sudo pip install /tmp/tensorflow_pkg/tensorflow-0.10.0-cp2-none-any.whl

至此，便完成了支持GPU的TensorFlow源码编译。

## 2.10 环境测试
可以通过如下代码进行测试，

    # Python
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
正常测试输出，

    Hello, TensorFlow!
# 3 资料参考
[《Ubuntu 16.04 U盘安装图文教程》](http://www.linuxidc.com/Linux/2016-04/130520.htm)
[《华硕主板禁用UEFI安全启动》](https://www.cnblogs.com/zhaofenqiang/p/6071385.html?utm_source=itdadao&utm_medium=referral)
[《Ubuntu 16.04安装NVIDIA驱动》](http://blog.csdn.net/cosmoshua/article/details/76644029)
[《alt+ctrl+F1黑屏 ，解决方案》](http://blog.csdn.net/u010733679/article/details/52022094)
[《Ubuntu16.04 下安装GPU版TensorFlow（包括Cuda和Cudnn）》](https://segmentfault.com/a/1190000008234390)
[《ubuntu16.04下安装TensorFlow(GPU加速)》](http://blog.csdn.net/zhaoyu106/article/details/52793183)

