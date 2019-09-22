---
title: TensorFlow Servering C/S通信约束
date: 2018-09-21 17:30:10
tags: TensorFlow
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://zhuanlan.zhihu.com/p/45109527>

TensorFlow Serving以Server方式提供模型能力服务，作为服务的使用者（Client）可以通过gRPC和RESTfull API两种方式来获取模型能力。虽然TensorFlow对C/S的通信约束做了说明，但感觉介绍的并不是特别的清晰易用，需要自己根据使用示例，并结合文档进行梳理和总结。
# 官方参考示例
在文档中提到了两个参考示例，一个用于gRPC通信约束测试，一个用于RESTfull API通信约束测试。
## 1. gRPC示例
示例使用说明：<https://www.tensorflow.org/serving/serving_basic>
* 模型输出

minist_save_model.py
示例代码：<https://github.com/tensorflow/serving/blob/master/tensorflow_serving/example/mnist_saved_model.py>
* 客户端验证

mnist_client.py
示例代码：<https://github.com/tensorflow/serving/blob/master/tensorflow_serving/example/mnist_client.py>
## 2. RESTfull API示例
示例使用说明:<https://www.tensorflow.org/serving/api_rest>
* 模型输出

export_half_plus_two.py
示例代码：
<https://github.com/tensorflow/serving/blob/master/tensorflow_serving/servables/tensorflow/testdata/export_half_plus_two.py>
* 客户端验证

Http请求，通过curl命令完成
# 示例代码分析
虽然官网提供了两个示例，但实际上RESTfull API的示例过于简单，并且做了封装，实际的参考价值不大。下面主要结合gRPC的示例代码进行分析。

客户端同服务端进行通信交互的核心是几个标识，我们分别介绍下。
## 1. 模型标识
指定要使用哪一个模型。
参考mnist_client.py
![模型标识](https://upload-images.jianshu.io/upload_images/4905018-096c9d8c7179e212.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
模型标识在Serving的模型保存输出代码中并没有显示的声明。模型标识实际上是在指定模型输出路径，和设置TF Serving "target"时指定的。
![设置模型标识](https://upload-images.jianshu.io/upload_images/4905018-08d42983a3c994d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![设置模型标识](https://upload-images.jianshu.io/upload_images/4905018-ddbcb11c57d6b1a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考:<https://www.tensorflow.org/serving/serving_basic>
## 2. 签名标识
可以粗糙的理解为，用来指定需要调用模型中的哪个方法。
官网定义（参考：<https://www.tensorflow.org/serving/signature_defs>）
![签名标识](https://upload-images.jianshu.io/upload_images/4905018-f11fe8bf94deb985.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考mnist_client.py
![签名标识](https://upload-images.jianshu.io/upload_images/4905018-590604b058572893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
签名标识在Serving的模型保存输出代码中设置，
参考minist_save_model.py
![签名标识](https://upload-images.jianshu.io/upload_images/4905018-93b045830786fc5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 3. 输入标识
设置输入，传入待处理的数据。
官网定义（参考：<https://www.tensorflow.org/serving/signature_defs>）
![输入标识](https://upload-images.jianshu.io/upload_images/4905018-b2a8ffbd46651a52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考minist_save_model.py
![输入标识](https://upload-images.jianshu.io/upload_images/4905018-db79f2f06f1aab81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考mnist_client.py
![输入标识](https://upload-images.jianshu.io/upload_images/4905018-1ce83967311b7195.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 4. 输出标识
设置哪些数据作为返回值，只在Serving中进行设置，Client获取的返回值中，能够获取对应的标识。
官网定义（参考：<https://www.tensorflow.org/serving/signature_defs>）
![输出标识](https://upload-images.jianshu.io/upload_images/4905018-cdc8753f3d8a631e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考minist_save_model.py
![输出标识](https://upload-images.jianshu.io/upload_images/4905018-4fd54685229a5f5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考mnist_client.py
![输出标识](https://upload-images.jianshu.io/upload_images/4905018-c787b908032f977b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 总结
客户端向服务端发起请求时，指定了模型标识、签名标识、输入标识（包含数据），便可以从服务端获取处理结果，结果中包含输出标识。
gRPC和RESTfull API在通信约束上是一致的。













