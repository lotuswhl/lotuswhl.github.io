---
layout:     post
title:      Attention Mechanism 初探
subtitle:   关于意图机制的探索
date:       2018-05-13
author:     HL
header-img: img/attention-mechanism-1.jpg
catalog: true
tags:
    - deep learning
    - rnn
    - lstm
    - attention
---
# 意图机制的起源
当我们人在分析某个问题的时候,我们通常会阅读问题的背景知识,然后根据具体的问题去寻找蛛丝马迹.也就是我们带着目的或者意图去寻找可供利用的知识.那么在深度学习及其火热的应用领域自然语言处理和计算机视觉问题上,我们又是如何利用意图机制去解决问题的呢.  
实际上在很早的时候就已经有研究学者将意图机制应用于各种神经网络模型中,特别是在视觉问题上,因为意图本身就与人的视觉观察聚焦点有着很大关联.近几年,意图机制被广泛的运用于RNN模型用于解决自然语言处理问题,而后续又渐渐的应用于视觉问题上,比如图像的分类或者生成图像的描述等等.
# 意图机制用于解决什么样的问题
一个机制的存在或者一个方法的存在必然是用于解决某类或者某些类问题的.一个非常典型的应用就是Neural Machine Translation(NMT).  
在早期的机器翻译系统中,通常加入了大量的人工特征工程,也就是加入了很多的静态统计信息.这样的系统泛化能力往往比较差,也就是对于新的数据的适应能力比较差.而神经机器翻译NMT却有着更好的泛化能力.一个典型的NMT通常是由RNN或者LSTM等循环神经网络将一条语句进行编码为一个输出向量和一个解码器(通常也是RNN)构成.基于对编码器可以编码整条语句信息的假设,我们先将语句转为embedding然后在从这个embedding中提取信息,进行解码,解码的过程也就是翻译的过程.典型示图如下:
![NMT sample](https://github.com/lotuswhl/lotuswhl.github.io/blob/master/img/attention/NMT-Sample.png)
