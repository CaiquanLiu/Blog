---
title: TensorFlow Serving入门
date: 2018-09-21 17:21:28
tags: TensorFlow
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://zhuanlan.zhihu.com/p/45109194>

大家习惯使用TensorFlow进行模型的训练、验证和预测，但模型完善之后的生产上线流程，就变得五花八门了。针对这种情况Google提供了TensorFlow Servering，可以将训练好的模型直接上线并提供服务。在2017年的TensorFlow开发者Summit上便提出了TensorFlow Serving。
传送门：<https://www.youtube.com/watch?v=q_IkJcPyNl0&list=PLOU2XLYxmsIKGc_NBoIhTn2Qhraji53cv&index=13>
但那时候客户端和服务端的通信只支持gRPC。在实际的生产环境中比较广泛使用的C/S通信手段是基于RESTfull API的，幸运的是从TF1.8以后，TF Serving也正式支持RESTfull API通信方式了。
## 服务框架
![TF Serving服务框架](https://upload-images.jianshu.io/upload_images/4905018-913e07a93c4821ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
基于TF Serving的持续集成框架还是挺简明的，基本分三个步骤：
* 模型训练
这是大家最熟悉的部分，主要包括数据的收集和清洗、模型的训练、评测和优化；
* 模型上线
前一个步骤训练好的模型在TF Server中上线；
* 服务使用

客户端通过gRPC和RESTfull API两种方式同TF Servering端进行通信，并获取服务；
# TF Serving工作流程
![TF Serving工作流程](https://upload-images.jianshu.io/upload_images/4905018-560bf34c3a9e5aca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
TF Serving的工作流程主要分为以下几个步骤：
* Source会针对需要进行加载的模型创建一个Loader，Loader中会包含要加载模型的全部信息；
* Source通知Manager有新的模型需要进行加载；
* Manager通过版本管理策略（Version Policy）来确定哪些模型需要被下架，哪些模型需要被加载；
* Manger在确认需要加载的模型符合加载策略，便通知Loader来加载最新的模型；
* 客户端像服务端请求模型结果时，可以指定模型的版本，也可以使用最新模型的结果；

# 简单示例
TF Serving客户端和服务端的通信方式有两种（gRPC和RESTfull API）
## 示例（一）：RESTfull API形式
### 1. 准备TF Serving的Docker环境
目前TF Serving有Docker、APT（二级制安装）和源码编译三种方式，但考虑实际的生产环境项目部署和简单性，推荐使用Docker方式。

		# docker pull tensorflow/serving
	 
### 2. 下载官方示例代码
示例代码中包含已训练好的模型和与服务端进行通信的客户端（RESTfull API形式不需要专门的客户端）

		# mkdir -p /tmp/tfserving
		# cd /tmp/tfserving
		# git clone https://github.com/tensorflow/serving

### 3. 运行TF Serving
    # docker run -p 8501:8501 \
      --mount type=bind,\
       source=/tmp/tfserving/serving/tensorflow_serving/servables/tensorflow/testdata/saved_model_half_plus_two_cpu,\
    target=/models/half_plus_two \
    -e MODEL_NAME=half_plus_two -t tensorflow/serving &
这里需要注意的是，较早的docker版本没有“--mount”选项，比如Ubuntu16.04默认安装的docker就没有（我的环境是Ubuntu 18.04）。
### 4.客户端验证
    # curl -d '{"instances": [1.0, 2.0, 5.0]}' \
      -X POST http://localhost:8501/v1/models/half_plus_two:predict 
返回结果，
     
    # { "predictions": [2.5, 3.0, 4.5] }

## 示例（二）：gRPC形式
### 1. 准备TF Serving的Docker环境
目前TF Serving有Docker、APT（二级制安装）和源码编译三种方式，但考虑实际的生产环境项目部署和简单性，推荐使用Docker方式。

     #  docker pull tensorflow/serving
### 2. 下载官方示例代码

    # mkdir -p /tmp/tfserving
    # cd /tmp/tfserving
    # git clone https://github.com/tensorflow/serving
### 3. 模型编译
    
    # python tensorflow_serving/example/mnist_saved_model.py models/mnist
### 4. 运行TF Serving

    # docker run -p 8500:8500 \
    --mount type=bind,source=$(pwd)/models/mnist,target=/models/mnist \
    -e MODEL_NAME=mnist -t tensorflow/serving
这里需要注意的是，较早的docker版本没有“--mount”选项，比如Ubuntu16.04默认安装的docker就没有（我的环境是Ubuntu 18.04）。
### 4.客户端验证

    # python tensorflow_serving/example/mnist_client.py --num_tests=1000 --server=127.0.0.1:8500
返回结果，
     
    #  Inference error rate: 11.13%
这里需要注意的是，直接运行mnist_client.py会出现找不到“tensorflow_serving”的问题，需要手动安装，

    # pip install tensorflow-serving-api

# 资料参考
TF Serving官方文档：<https://www.tensorflow.org/serving/>
































