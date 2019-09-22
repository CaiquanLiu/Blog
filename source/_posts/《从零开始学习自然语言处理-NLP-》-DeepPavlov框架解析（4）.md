---
title: 《从零开始学习自然语言处理(NLP)》-DeepPavlov框架解析（4）
date: 2019-03-02 19:49:19
tags: 从零开始学习自然语言处理(NLP)
---
由于图片外链被禁止了，图片不能显示，完整文章看这里吧：<https://zhuanlan.zhihu.com/p/58133705>

## 写在最前面
在这个日新月异的信息时代，海量数据的积累，计算能力的不断提升，机器学习尤其是深度学习的蓬勃发展，使得人工智能技术在不同领域焕发出蓬勃的活力。自己经历了嵌入式开发，移动互联网开发，目前从事自然语言处理算法开发工作。从工程软件开发到自然语言处理算法开发，希望通过这个系列的文章，能够由浅入深，通俗易懂的介绍自然语言处理的领域知识，分享自己的成长，同大家一起进步。
## 问题描述
在上一篇《从零开始学习自然语言处理(NLP)-NLP Framework开源方案梳理(3)》中梳理了目前流行的NLP开源框架，这里重点介绍下DeepPavlov框架。DeepPavlov框架的模型实用性很强，对实际的生产开发有很大的借鉴意义。
## 框架组成
DeepPavlov是一个基于TensorFlow和Keras的，专门针对对话系统研究和实验部署的自然语言处理框架。
项目地址：http://docs.deeppavlov.ai/en/master/#
框架主要包括：
**常用的NLP模型（包括Pre-train模型）**
如词向量训练、分类、命名实体识别（NER）、相似度计算等；
**针对对话系统实现和评测的实验框架（Framework）**
基于Json文件进行开发流程和数据流pipeline配置；
**提供同第三方应用进行集成的工具**
如与Amazon Alexa和Microsoft Bot Framework的集成；
**为对话模型的评测提供Benchmark环境**
DeepPavlov的默认Pre-train模型和测评数据集主要基于英文和俄文，对于中文场景需要做适当的调整。
## 框架使用对象
**新模型开发者**
方便同已有Benchmark模型进行对比评测
**普通NLP任务处理者**
如针对内容审核任务，敏感信息增加掩码等任务，可以直接使用框架提供的分类和序列标注模型，完成业务服务的快速开发和测评；
**对话系统开发者**
DeepPavlov是为对话系统场景量身定制的。对话系统开发者，可以直接参考使用；
**对话系统应用开发者**
DeepPavlov框架为应用集成提供了专门的工具，可以直接与Amazon Alexa，Microsoft Bot Framework等平台进行对接。
## 框架使用
**规范开发流程**
框架将数据模型服务的开发和验证流程（如数据清洗、模型设计、模型训练、模型选优、模型评测），使用Json配置文件串联成pipeline。能够很好的规范开发流程；
**新模型对比评测**
为对话模型的评测提供Benchmark环境，方便新模型的对比评测。但环境主要基于英文和俄文，对于中文任务，需要重新训练对比；
**常用NLP模型使用**
框架内置了很多常用模型（以分类为例，就包含了cnn_model，bilstm_model，bilstm_attention_model，transformer_model等12种模型实现），在项目中可以直接使用；
同时框架提供了Pre-train模型，但主要是基于英文和俄文的，对于中文场景需要自己进行重新训练；
**对话系统开发**
DeepPavlov是为对话系统场景量身定制的，对话系统开发者，可以直接参考使用；
## DeepPavlov框架层次
DeepPavlov从整体到局部，可分为如下三个层次：
![DeepPavlov框架层次](https://upload-images.jianshu.io/upload_images/4905018-f4c0014e182c870b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下面我们从外层到内层，逐层介绍DeepPavlov的框架设计。
## DeepPavlov顶层框架
![DeepPavlov框架](https://upload-images.jianshu.io/upload_images/4905018-c0f6ca8cbb1f27c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Agent**
同用户直接交互的代理，直接接收用户输入的纯文本信息（raw text）
**Skill**
领域技能，如基于意图-词槽的任务型技能，基于Seq2Seq的闲聊技能，基于知识图谱的知识问答技能；
**Skill Manager**
确定用户query，选择使用哪些skill，并确定将哪一个skill的召回结果作为最终的回复；
**Component**
Skill实现的组成部分，如针对任务型技能，包括数据预处理component、意图识别component、slotfilling component等；
**Chainer**
Chainer以Json配置文件的形式，将某个skill相关的所有component串联起来；
**Data Storage**
框架本身包含的Pre-train模型和Benchmark评测数据集
## Skill框架
DeepPavlov内置的skill主要包括：
**任务型skill（Goal-Oriented Dialogue Bot）**
基于意图/词槽/对话管理等component实现的问答skill。
![任务型skill](https://upload-images.jianshu.io/upload_images/4905018-aa3877cb08461905.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**阅读理解skill（Open-Domain Question Answering）**
基于阅读理解实现问答skill。相对于阅读理解component（Context Question Answering），skill还包含在多个召回结果中进行排序的能力。
**规则型skill（Pattern Matching）**
基于自定义规则实现问答skill。
**Seq2Seq skill（Sequence-To-Sequence Dialogue Bot）**
基于Seq2Seq实现问答skill。
![Seq2Seq skill](https://upload-images.jianshu.io/upload_images/4905018-20ec116fa7907873.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**常见问题问答skill（Frequently Asked Questions Answering）**
先将句子嵌入为向量（使用词向量叠加），然后做分类处理（给每一个answer一个打分），选取打分最高的answer作为最终回复的skill。
**商品查询skill（eCommerce Bot）**
商品查询回复skill，支持多轮（添加过滤条件）。下面是场景示例，
![商品查询skill](https://upload-images.jianshu.io/upload_images/4905018-99c0ebb7de6cc02f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 基本能力框架
DeepPavlov内置的基本能力主要包括：
**数据预处理component（Data processors）**
主要提供包括分词、嵌入向量化等预处理能力（主要基于俄文和英文）。
**阅读理解component（Context Question Answering）**
相对于阅读理解skill（Open-Domain Question Answering），component不包含对多个召回结果进行排序（rank）的能力。具体的处理场景示例如下，
![阅读理解component](https://upload-images.jianshu.io/upload_images/4905018-b5316fe0884017ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**分类component（Classification）**
分类组件，可以用来做场景和意图的分类。
**Morphological Tagger component**
一种特殊的POS？
**命名实体识别component（Named Entity Recognition）**
NER能力组件。
**相似度计算component（Neural Ranking）**
通过基于孪生网络完成相似度计算，实现在标准问答库中标准答复的查找。
**词槽填充component（Slot filling）**
在NER的基础上，增加了词表限制。
***拼写纠错component（Spelling Correction）**
提供了两种纠错方法：
* levenshtein_corrector ：基于编辑距离
* brillmoore：基于统计模型

**TF-IDF排序component（TF-IDF Ranking）**
基于TF-IDF的文档召回排序。
**流行度排序component（Popularity Ranking）**
将TF-IDF打分和流行度打分作为特征，通过逻辑回归计算流行度，最终实现排序。
## Json配置文件解析
DeepPavlov通过Json配置文件实现开发流程控制和数据流pipeline的控制。
上面提到DeepPavlov主要分为Agent、Skill和Component三个层次。而Json配置文件主要应用在Skill和Component这两个层面。而对Agent的控制，框架通过直接的代码来实现，例如，
![agent](https://upload-images.jianshu.io/upload_images/4905018-35bbd3ed436a7545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其中，
**skills**
Agent支持的所有skill列表；
**skills_filter**
针对用户query，确定Agent使用哪些skill；
**skills_filter**
通过召回的不同skill结果，确定最后的回复内容给用户；
下面我们具体的介绍下Json配置文件的具体结构。
## Json配置文件结构
![Json配置文件结构](https://upload-images.jianshu.io/upload_images/4905018-6f83f4dcefee7713.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如上图所示，Json配置文件主要由如下五个部分组成（参考分类component）：
**dataset_reader**
主要负责数据的读取。
![dataset_reader](https://upload-images.jianshu.io/upload_images/4905018-ba0d381b6d858e97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**dataset_iterator**
数据迭代器，从dataset_reader中获得数据，然后按batch抽取数据，供后面的模型训练使用。
![dataset_iterator](https://upload-images.jianshu.io/upload_images/4905018-0e61711decf24e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**chainer**
配置文件的核心，将数据预处理、模型选优和模型预测输出，通过pipeline（"pipe"字段内进行约束）的形式串联起来。

    "chainer": {
    "in": [
      "x"
    ],
    "in_y": [
      "y"
    ],
    "pipe": [
      {
        "id": "classes_vocab",
        "class_name": "simple_vocab",
        "fit_on": [
          "y"
        ],
        "save_path": "{MODELS_PATH}/classifiers/{PROJECT_NAME}_{MODEL_NAME}/classes.dict",
        "load_path": "{MODELS_PATH}/classifiers/{PROJECT_NAME}_{MODEL_NAME}/classes.dict",
        "in": "y",
        "out": "y_ids"
      },
      {
        "in": "x",
        "out": "x_tok",
        "id": "my_tokenizer",
        "class_name": "char_tokenizer",
        "tokenizer": "char_tokenizer"
      },
      {
        "in": "x_tok",
        "out": "x_ids",
        "id": "seq_to_emb_ids",
        "class_name": "seq_to_emb_ids",
        "emb_file_path":"{EMBED_PATH}",
        "text_size":250
      },
      {
        "in": "y_ids",
        "out": "y_onehot",
        "class_name": "one_hotter",
        "id": "my_one_hotter",
        "single_vector": true,
        "depth": "#classes_vocab.len"
      },
      {
        "in": [
          "x_ids"
        ],
        "fit_on_batch_preprocess": [
          "x_ids",
          "y_onehot"
        ],
        "out": [
          "y_pred_probas"
        ],
        "main": true,
        "class_name": "keras_classifier_model",
        "graph_metrics": "multilabel_f1",
        "save_path": "{MODELS_PATH}/classifiers/{PROJECT_NAME}_{MODEL_NAME}/model",
        "load_path": "{MODELS_PATH}/classifiers/{PROJECT_NAME}_{MODEL_NAME}/model",
        "n_classes": "#classes_vocab.len",
        "train_emb": false,
        "kernel_sizes_cnn": [
          1,
          2,
          3,
          4,
          5
        ],
        "filters_cnn": 512,
        "optimizer": "Adam",
        "learning_rate": 0.001,
        "learning_rate_decay": 0.9,
        "loss": "categorical_crossentropy",
        "embedding_matrix": "#seq_to_emb_ids.matrix",
        "text_size": 250,
        "last_layer_activation": "softmax",
        "coef_reg_cnn": 0.0,
        "coef_reg_den": 0.0,
        "dropout_rate": 0.5,
        "dense_size": 30,
        "model_name": "cnn_model"
      },
      {
        "in": "y_pred_probas",
        "out": "y_pred_ids",
        "class_name": "proba2labels",
        "max_proba": true
      },
      {
        "in": "y_pred_ids",
        "out": "y_pred_onehot",
        "ref": "my_one_hotter"
      },
      {
        "in": "y_pred_ids",
        "out": "y_pred_labels",
        "ref": "classes_vocab"
      }
    ],
    "out": [
      "y_pred_ids",
      "y_pred_onehot",
      "y_pred_labels",
      "y_pred_probas"
    ]
    },

官方文档中，将“pipe”字段内的每一对花括号({})中的内容成为一个component（注意这里的component和上面提到的框架component是不同的。为了方便区分，我们将"pipe"中的component标识为pipe-component）。
**train**
模型训练、模型选优和评测配置。

    "train": {
    "epochs": 10,
    "batch_size": 256,
    "metrics": [
      {
        "name": "cal_confusion_matrix",
        "inputs": [
          "y",
          "y_pred_labels"
        ]
      },
      {
        "name": "f1_micro",
        "inputs": [
          "y",
          "y_pred_labels"
        ]
      },
      {
        "name": "recall_micro",
        "inputs": [
          "y_ids",
          "y_pred_ids"
        ]
      },
      {
        "name": "precision_micro",
        "inputs": [
          "y_onehot",
          "y_pred_onehot"
        ]
      },
      {
        "name": "f1_macro",
        "inputs": [
          "y_onehot",
          "y_pred_onehot"
        ]
      },
      {
        "name": "precision_macro",
        "inputs": [
          "y",
          "y_pred_labels"
        ]
      },
      {
        "name": "recall_macro",
        "inputs": [
          "y_ids",
          "y_pred_ids"
        ]
      },
      {
        "name": "recall_group",
        "inputs": [
          "y_onehot",
          "y_pred_onehot"
        ]
      },
      {
        "name": "precision_group",
        "inputs": [
          "y",
          "y_pred_labels"
        ]
      },
      {
        "name": "f1_group",
        "inputs": [
          "y_ids",
          "y_pred_ids"
        ]
      }
    ],
    "validation_patience": 5,
    "val_every_n_epochs": 1,
    "log_every_n_epochs": 1,
    "show_examples": true,
    "validate_best": true,
    "test_best": true,
    "report_path": "{MODELS_PATH}/classifiers/{PROJECT_NAME}_{MODEL_NAME}/report.xlsx"
    },

其中，
"metric"字段中排在最前面的指标，作为模型选优的标准。
**metadata**
相关相关的常量配置。
![metadata](https://upload-images.jianshu.io/upload_images/4905018-4b3fafd2b9128a17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其中，"imports"是DeepPavlov框架之外自定义实现。
## DeepPavlov存在的问题
**环境依赖**
DeepPavlov是基于TensorFlow和Keras实现的，不能继承其他计算框架的模型实现（如PyTorch）。
**语言支持**
Pre-train模型和评测数据集主要基于英文和俄文，不支持中文。
**生产环境部署**
DeepPavlov在运行时需要依赖整个框架源码，开发环境对框架修改后，生产环境需要更新整个框架。同时，也不能直接将功能Component作为服务独立导出，不适合在生产环境的部署和发布。











