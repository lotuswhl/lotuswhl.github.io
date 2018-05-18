---
layout:     post
title:      questions upon machine learning
subtitle:   思考问题有利于真正的成长
date:       2018-01-03
author:     HL
header-img: img/questions-upon-machine-learning.png
catalog: true
tags:
    - machine learning
---

## what is the difference between LR and SVM

* logistic regression 似乎在实用一个连续的函数匹配数据
* 并不总是单分类问题，所以LR可能会存在对P=0.5的情况难以分类
* SVM则试图使用一个超平面去将一组多维数据分割成两类
* SVM对于非线性可分的数据难以分类，或者如果两类数据之间的间隔不足，也会导致SVM难以分类。

## How are logistic regression and linear SVM's similiar?

事实上LR和Linear SVM很相似，都是在数据的特征向量之间建立一个超平面；其主要区别在于，LR使用的而是Logistic Loss function 而 SVM使用的是hinge loss。

* 对于SVM而言，其决策面只受support vector影响，除此之外的特征向量对他无贡献
* 对LR而言，则会考虑所有的点，只是离决策面(分割超平面)距离远的点，其贡献降低

LR和SVM都可以使用核方法(kernel methods)用于应对非线性分类问题，但是SVM与kernel methods配合的更好。另外一点：当加入新的样本点时，如果不影响支持向量，那么SVM不受影响，而LR则会根据样本点的位置进行微调因此可以很好的应用于在线学习。