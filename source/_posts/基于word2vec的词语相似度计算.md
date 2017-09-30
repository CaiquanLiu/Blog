---
title: 基于word2vec的词语相似度计算
date: 2017-09-30 16:37:28
tags: 机器学习/深度学习
---
# 应用场景

假设你有一个商品的数据库，比如：

商品名称 | 价格 
----|------
椅子 |   200元/个
香蕉 |  6元/斤
冰箱 | 2000元/台

现在通过用户的输入来检索商品的价格，最简单的方法就是通过字符串进行匹配，比如，
用户输入“椅子”，就用“椅子”作为关键字进行搜索，很容易找到椅子的价格就是200元/个。
但有时用户输入的是“凳子”，如果按照字符串匹配的方法，只能返回给用户，没有此商品。但实际上可以把“椅子”的结果返回给用户参考。这种泛化的能力，通过简单的字符串匹配是显然不能实现的。
# 词语相似度计算
在上面的例子中，“凳子”跟“椅子”的语意更相近，跟“香蕉”或“冰箱”的语意相对较远。在商品搜索的过程中，可以计算用户输入的关键字与数据库中商品名间的相似度，在商品数据库中找出相似度最大的商品，推荐给用户。这种相近的程度就是词语的相似度。在实际的工程开发中可以通过word2vec实现词语相似度的计算。

# 代码实现
    from sklearn.datasets import fetch_20newsgroups

    news = fetch_20newsgroups(subset='all')
    X, y = news.data, news.target

    from bs4 import BeautifulSoup
    import nltk, re


    # 把段落分解成由句子组成的list（每个句子又被分解成词语）
    def news_to_sentences(news):
        news_text = BeautifulSoup(news, 'lxml').get_text()
        tokenizer = nltk.data.load('tokenizers/punkt/english.pickle')
        raw_sentences = tokenizer.tokenize(news_text)

        # 对每个句子进行处理，分解成词语
        sentences = []
        for sent in raw_sentences:
            sentences.append(re.sub('[^a-zA-Z]', ' ', sent.lower().strip()).split())
        return sentences


    sentences = []

    for x in X:
        sentences += news_to_sentences(x)

    # import numpy
    # # 将预处理过的"词库"保存到文件中，便于调试
    # numpy_array = numpy.array(sentences)
    # numpy.save('sentences.npy', numpy_array)
    #
    # # 将预处理后的"词库"从文件中读出，便于调试
    # numpy_array = numpy.load('sentences.npy')
    # sentences = numpy_array.tolist()

    num_features = 300
    min_word_count = 20
    num_workers = 2
    context = 5
    downsampling = 1e-3

    from gensim.models import word2vec

    model = word2vec.Word2Vec(sentences, workers=num_workers,         size=num_features, min_count=min_word_count, window=context,
                          sample=downsampling)

    model.init_sims(replace=True)

    # 保存word2vec训练参数便于调试
    # model.wv.save_word2vec_format('word2vec_model.bin', binary=True)
    # model.wv.load_word2vec_format('word2vec_model.bin', binary=True)

    print '词语相似度计算：'
    print 'morning vs morning:'
    print model.n_similarity('morning', 'morning')
    print 'morning vs afternoon:'
    print model.n_similarity('morning', 'afternoon')
    print 'morning vs hello:'
    print model.n_similarity('morning', 'hellow')
    print 'morning vs shell:'
    print model.n_similarity('morning', 'shell')
# 运行结果
    /Users/liucaiquan/anaconda/bin/python /Users/liucaiquan/PycharmProjects/WordSimilarityCalculation/src/test.py
    词语相似度计算：
    morning vs morning:
    1.0
    morning vs afternoon:
    0.871482091583
    morning vs hello:
    0.731609166442
    morning vs shell:
    0.709714434122
# 调试技巧
在开发调试的过程中，会出现错误，需要重新运行程序。如果每次修改后，都从头开始执行，肯定会消耗很多无用的时间。比如，预处理后的文本结果和word2vec的训练参数，这些中间结果可以保持下来，当遇到问题时，就可以从文件中读取结果，而不需要每次都从头开始。
# 源码下载地址
<https://github.com/CaiquanLiu/MachineLearning>
# 代码参考
《Python机器学习及实践：从零开始通往Kaggle竞赛之路》
