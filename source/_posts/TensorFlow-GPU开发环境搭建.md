---
title: TensorFlow GPU开发环境搭建
date: 2019-03-29 23:27:14
tags: TensorFlow 
---
TensorFlow有CPU版本和GPU版本之分，CPU版本安装相对简单，按着TensorFlow的官方文档进行安装即可。但CPU版本只能使用CPU进行计算，计算效率低。对于简单的模型计算可以使用CPU模式，但对于复杂的模型训练就需要GPU的支持了。
## GPU版本安装方式
TensorFlow的GPU版本有两种安装方式：
**源码编译安装**
这种方式灵活性最强，但这种方式不但会涉及TensorFlow GPU版本计算框架的依赖，还会牵扯到源码编译的工具依赖和库依赖。如果不是有特别的需要，不建议采用这种安装方式。
**二进制安装包安装**
针对大多数的使用场景，直接使用编译好的二进制安装包进行安装即可，操作相对简单，依赖也比较少。本文主要针对二进制安装包的方式进行安装。
## 二进制安装包安装方式选择
**参考TensorFlow官方文档**
TensorFlow官方文档提供了基于命令方式的安装模式。但实际上，文档提供的安装命令更多是一种参考，而不是针对所有显卡和硬件环境都可以适用的，傻瓜式解决方案。或者说，直接按照TensorFlow的官网提供命令进行安装，多半会出现问题。
**自定义安装**
TensorFlow CPU版本和GPU版本最大的不同在于它们依赖的硬件平台，CPU版本的硬件依赖比较低，甚至可以粗糙的认为对硬件没有特别的要求（桌面或工作站处理器，大多是基于X86计算架构的）。GPU则不同，即便大家目前使用的GPU卡大多是Nvidia的，但Nvidia不同的显卡系列的计算能力和对TensorFlow的支持也是不一样的。所以有必要针对自己实际的GPU显卡，配置与之相对应的运行环境，最终满足TensorFlow GPU版本的运行要求。
## TensorFlow GPU版本的运行依赖
GPU版本的TensorFlow运行依赖并不复杂，从底层到上层，依赖关系如下图所示：
![运行依赖](https://upload-images.jianshu.io/upload_images/4905018-e64598fdad6c2325.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**显卡**
目前深度学习使用的主流GPU大多是Nvidia的显卡。而大家常用的是GeForce系列和Tesla系列。从Nvidia的角度，GeForce系列定位在游戏市场，Tesla系列定位在数据中心。但实际上从经济考虑，很多公司都使用GeForce做模型训练，然后使用Tesla做线上服务（Nvidia禁止公司使用Tesla系列之外的显卡作为数据中心）。
**驱动**
驱动运行在系统的内核态，是操作系统的一部分，直接跟显卡硬件打交道，是应用层和硬件打交道的门户。不同的显卡需要不同的驱动。
**CUDA**
CUDA运行在系统的应用层，对通用GPU计算操作做了封装，方便其他组件调用。
**cuDNN**
cuDNN依赖CUDA库，封装了常用的深度学习方法，供更上层的组件调用。
**TensorFlow GPU版本要求**
不同版本的TensorFlow，对运行环境是有不同的要求。驱动版本的选择，主要依赖于显卡型号。而CUDA、cuDNN、Python版本的选择，则需要依赖TensorFlow GPU版本的选择：
![依赖版本](https://upload-images.jianshu.io/upload_images/4905018-726584ec5bee3647.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考：https://www.tensorflow.org/install/source

在有了显卡之后，后面需要做的，就是从下到上，安装对应版本的驱动、CUDA库、cuDNN库、Python和TensorFlow了。
## 具体环境搭建步骤
下面我们以超微的GPU工作站为例，逐步搭建TensorFlow GPU开发环境。
**系统环境**
* GTX 1080TI 四卡
* Ubuntu 16.04
* 目标：TensorFlow GPU 1.10.0版本

**（一）驱动安装**
驱动选择地址：https://www.nvidia.com/Download/index.aspx?lang=en-us
![驱动选择](https://upload-images.jianshu.io/upload_images/4905018-7d32ed5c819f8dc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
最终，会下载得到xxx.run的安装文件，直接使用shell安装，

        # sudo sh xxx.run

**（二）CUDA安装**
下载地址：https://developer.nvidia.com/cuda-zone
![最新CUDA](https://upload-images.jianshu.io/upload_images/4905018-4af061cba4cfd665.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们需要安装的TensorFlow GPU版本为1.10.0，所以CUDA的版本是9。但直接打开是最新版本的CUDA。可以选择上面的“Legacy Releases”，然后，选择需要的版本，
![对应版本CUDA](https://upload-images.jianshu.io/upload_images/4905018-1dcf34269d5c370f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
最终，会下载得到xxx.run的安装文件，直接使用shell命令进行安装，

        # sudo sh xxx.run

**（三）CUDNN安装**
下载地址（需要注册）：https://developer.nvidia.com/cudnn
cuDNN主要和CUDA版本对齐，
![cuDNN](https://upload-images.jianshu.io/upload_images/4905018-b0030c20943724b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
cuDNN主要下载两个文件，
![两个文件](https://upload-images.jianshu.io/upload_images/4905018-6933e5f4e092925b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下载的两个xxx.deb文件，通过dpkg -i命令进行安装，

        # dpkg -i xxx.deb

**（四）Python安装**
Python可以采用Anaconda环境，具体的安装步骤就不详细展开了，工作中选择了Python3.6版本。只提两点：
**全局环境变量设置**
Anaconda默认会将环境变量设置到~/.bashrc文件中。如果想全局可见，可以手动在/etc/profile文件中添加环境变量。
**使用清华的下载链接**
Anaconda的官方下载，国内下载速度很慢，可以直接使用清华的源。
地址：https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/
**（五）TensorFlow GPU安装**
TensorFlow安装先对简单，直接使用pip命令进行安装即可，但低于1.13.0版本的TensorFlow GPU版本，不能直接使用“pip install tensorflow-gup===xxx”来安装。需要手动下载安装文件，然后进行安装。
TensorFlow GPU历史版本下载地址：https://pypi.org/project/tensorflow-gpu/
![历史版本](https://upload-images.jianshu.io/upload_images/4905018-8e91236a94f8f8a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
TensorFlow GPU也可以使用清华的源，但源更新的可能不够及时：
清华TensorFlow GPU地址：https://mirrors.tuna.tsinghua.edu.cn/help/tensorflow/
![清华地址](https://upload-images.jianshu.io/upload_images/4905018-8d5ccc9b25aba2f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 小结
TensorFlow的GPU版本安装，本身并不复杂。关键是要梳理清楚不同组件的依赖关系。不同组件之间没有特别的强依赖，如果发现其中的某个组件有问题，单独卸载和重装即可。

