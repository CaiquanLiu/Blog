---
title: 《从零开始学习自然语言处理(NLP)》-NLP Framework开源方案梳理(3)
date: 2019-02-01 21:20:16
tags: 从零开始学习自然语言处理(NLP)
---
## 写在最前面
在这个日新月异的信息时代，海量数据的积累，计算能力的不断提升，机器学习尤其是深度学习的蓬勃发展，使得人工智能技术在不同领域焕发出蓬勃的活力。自己经历了嵌入式开发，移动互联网开发，目前从事自然语言处理算法开发工作。从工程软件开发到自然语言处理算法开发，希望通过这个系列的文章，能够由浅入深，通俗易懂的介绍自然语言处理的领域知识，分享自己的成长，同大家一起进步。
## 问题描述
新的项目开启时，一般会经历如下的流程：
* 业务需求梳理
* 算法方案选择
* 模型实现：选择开源方案，或自己实现
* 模型训练：数据清洗、模型训练（使用训练集）
* 最优模型选择：依据验证集结果，选择最优模型
* 模型评测：依据测试集结果，对模型进行评测
* 模型优化：本模型调优，或选择新的模型
* 模型服务上线

而在实际的项目开发中，存在如下的问题：
**1模型实现问题**
模型实现一般会选择开源方案，或自己编码实现。自己编码实现开发和调试成本高。直接选择开源方案，会面临开发环境不统一，编译问题，代码结果和可靠性难以保证等问题；
**2模型训练和评测问题**
该阶段会设计到数据清洗，输入读入，数据迭代器设计，数据预处理，词向量训练与准备，分词，文本转索引，
标签ont-hot表示，训练集/验证集/测试集划分，训练过程最优模型选择，模型评测(准确度A、精确度P、召回率R、F1值等)，数据分析（结果分析，混淆矩阵分析，ROC/AUC分析），Tensorboard结果查看等。由于该部分设计的流程和步骤较多，自己编码开发和调试工作量大，而开源方案很多步骤和组件并不全面。
**3 模型优化问题**
除了在现有模型的基础上进行局部优化外，还会选择新的模型。如果选择新的模型，则会重新自己编码实现或选择开源方案，会再次经历前面提到的问题1（模型实现问题）和问题2（模型训练和评测问题）。
**4 模型服务上线问题**
模型服务上线，同模型的训练和预测和类似，但又不完全相同。模型上线需要和模型训练一样的预处理，词向量，分词，文本转索引等步骤，但不需要模型的测评。同时，模型上线要求只能包含模型的训练文件，
而不能包括模型的代码。通常需要编写专门的代码用于模型服务上线。
一般来说，针对上面的问题，公司都会有一个统一的NLP算法框架，
**一方面，统一模型的实现、评测和上线流程**
**另一方面，框架自身包含很多现成的模型，便于业务在算法落地方面的快速验证**
大多数公司并不开源自己的NLP算法框架，但开源社区NLP开源框架还是蛮多的，这里做一个统一的梳理。
## 针对对话系统的Framework
### 1 DeepPavlov
运行依赖：TensorFlow/Keras
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-ffa015c3e2212104.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://github.com/deepmipt/DeepPavlov](https://github.com/deepmipt/DeepPavlov)
### 2 ParlAI
运行依赖：Pytorch
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-31a1018b9a6dc7bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：[https://github.com/facebookresearch/ParlAI](https://github.com/facebookresearch/ParlAI)
### 3 Rasa
运行依赖：Python
支持功能：纯任务型对话
项目地址：[https://www.rasa.com/](https://www.rasa.com/)
### 4 OpenDial
运行依赖：Java
支持功能：对话系统
描述：主要基于传统方法，深度网络较少涉及。
项目地址：[https://github.com/plison/opendial](https://github.com/plison/opendial)
## 通用NLP Framework
### 1 Intel NLP-Architect
运行依赖：TensorFlow
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-3869c6d90607bcf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：[http://nlp_architect.nervanasys.com/](http://nlp_architect.nervanasys.com/)
### 2 AllenNLP
运行依赖：Pytorch
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-19871ec0e38c2f3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://allennlp.org/](https://allennlp.org/)
### 3 PyText
运行依赖：Pytorch
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-658f4db3cb09b08d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://pytext-pytext.readthedocs-hosted.com/en/latest/#](https://pytext-pytext.readthedocs-hosted.com/en/latest/)
## NLP基础依赖组件项目（提供pre-train模型）
### 1 Stanford CoreNLP
运行依赖：Java
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-e62c769d3804da3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://stanfordnlp.github.io/CoreNLP/index.html](https://stanfordnlp.github.io/CoreNLP/index.html)
### 2 Stanford NLP
运行依赖：Pytorch
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-84ece0293bf62370.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://stanfordnlp.github.io/stanfordnlp/tokenize.html](https://stanfordnlp.github.io/stanfordnlp/tokenize.html)
### 3 spaCy
运行依赖：Python
支持功能：
![支持功能](https://upload-images.jianshu.io/upload_images/4905018-b05fe1a8edb066c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
项目地址：
[https://spacy.io/](https://spacy.io/)
## 小结
本文梳理了实际的业务算法落地开发过程，并提出了其中存在的问题，和解决方案。对当前开源的NLP框架做了整理，可以依据自身的需要开发自己的NLP算法框架，规范开发流程，提升业务落地效率。同时，也可以直接从开源方案中获取自己需求的算法实现模块。后续有新的开源框架方案，会持续整理进来。

