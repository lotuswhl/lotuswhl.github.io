---
layout:     post
title:      how to choose a machine learning classifier
subtitle:   简单的思考，来源于国外的一篇文章
date:       2017-12-13
author:     HL
header-img: img/machine-learning-classifier.jpeg
catalog: true
tags:
    - machine learning
    - classifier
    - experience conclusion

---

# 如何选择一个机器学习分类器

你是如何为你的分类问题选择机器学习算法的呢？当然，如果你很关注accuracy，那么最佳方案便是测试一组不同的算法（当然，相应的也要确保对每个算法进行不同参数的尝试），然后通过交叉验证选择其中最好的一个算法。但是，如果你只是想找一个“足够好”的算法，或者寻起初步方案，那么这里有一些不错的通用的方案可供借鉴。

## 你的训练数据集有多大？

如果你的数据集比较小，那么high bias/low variance classifiers(比如朴素贝叶斯) 比 low bias / high variance classifiers(比如kNN)要好，因为后者会过拟合；但是low bias / high variance classifiers 随着训练集的增加而变得更好，因为high bias classifiers 并不能提供准确的模型。

## 一些特别的算法的优势

### 朴素贝叶斯 : 

​	很简单，你只是做了一些计数而已；如果朴素贝叶斯的条件独立性假设真正成立，那么朴素贝叶斯分类器将会比其他的判别模型，比如，logistic regression 更快的收敛，于是你便需要更少的训练数据。此外，即便NB  的假设不成立，在实际中，朴素贝叶斯分类器通常也能取得不错的效果；如果你想要一个快速简单而表现的很好的方法，那么这是一个很好的赌注！

​	朴素贝叶斯最大的缺陷在于，他不会学习特征之间的交互关系（比如，他不能学习到你喜欢辣椒和糖果，但是你却讨厌将这两者结合起来）

### logistic regression : 

​	现在有很多方法可以用来正则化你的模型，你不必像使用朴素贝叶斯方法那样过于担心特征之间的相关性；使用logistic regression，你可以获得更好的概率解释，而不像决策树或者支持向量机，你可以很容易的用新的数据更新你的模型（比如，可以使用在线的梯度下降的方法），再一次，决策树和SVM做不到这样；

​	如果你想要一个概率框架，那么可以使用他（比如，你可以很轻松的调整分类的阈值，当你不确定或者想要获取置信区间时）；或者，如果你希望在将来能够获取到更多的数据时可以快速将之并入到你的模型。

### 决策树 :

​	决策树比较容易解释，他们可以很容易的处理特征交互关系并且他们是non-parametric (非参数型的，也就是没有固定的模型参数)，所以，你无需关系极值问题或者或者数据是否线性可分。

​	决策树的一个缺点在于他们不支持在线学习，也就是说当你有新的样本时必须要重新建立树；另外一个缺点是他们很容易过拟合，但是有一些集成学习方法可以解决这个问题，比如随机森林，（随机森林快速，可扩展，通常来说不必担心参数调整问题，而SVM 则需要）

###  SVM :

​	支持向量机具备高准确率，以及很好的移除过拟合的理论保证，即便你的数据不是线性可分，只要给他一个合适的内核（kernel）他就能工作的很好；

​	决策树在文本分类问题中特别流行（高维特征都是高斯的）；然而他是内存导向性的，难以解释并且令人恼火的难以运行和微调。

# But ...

Recall, though, that better data often beats better algorithms, and designing good features goes a long way. And if you have a huge dataset, then whichever classification algorithm you use might not matter so much in terms of classification performance (so choose your algorithm based on speed or ease of use instead).

And to reiterate what I said above, if you really care about accuracy, you should definitely try a bunch of different classifiers and select the best one by cross-validation. Or, to take a lesson from the Netflix Prize (and Middle Earth), just use an ensemble method to choose them all.





