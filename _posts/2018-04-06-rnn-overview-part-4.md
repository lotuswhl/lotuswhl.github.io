---
layout:     post
title:      循环神经网络概览系列之 - part 4
subtitle:   RNN overview 系列之(LSTM)
date:       2018-04-06
author:     HL
header-img: img/rnn-lstm.png
catalog: true
tags:
    - deep learning
    - rnn
    - lstm
---

# 介绍
首先简单的介绍一下LSTM的背景及其特点。在RNN刚提出之时，由于他强大的处理序列数据能力，很快就得到了业界的重视，但是因为RNN本身的特点，以及当时的时代局限性（比如激活函数的选择，比如大规模计算能力的不可获得），其发展受到一定的限制。其主要原因在于RNN的训练存在着梯度弥散的问题，使得它难以处理长期依赖问题(虽说其初衷就是为了解决长期依赖)，关于其原因，前面的章节也有说明。随后LSTM关注于解决梯度弥散的问题，其中最为经典的设计在于他的门！并且，他将节点循环的部分的权重设为单元权重也就是1.**这里值得注意的是原始的LSTM并没有Forget gate，因此，其内部状态Cell State与前一个状态之间的系数为1.**  
关于LSTM的精彩介绍，请看<a href="https://lotuswhl.wordpress.com/2018/05/17/%e7%90%86%e8%a7%a3lstm/" target="_blank">我的wordpress博客-LSTM</a>，这里面有着非常丰富图示，可以很好的帮助你理解LSTM。

# LSTM再探
再贴一下LSTM的一个很好的图示:  

![LSTM-Beautiful picture](https://raw.githubusercontent.com/lotuswhl/lotuswhl.github.io/master/img/RNN/LSTM-diagram.png)  

所以，从上图可以看出LSTM的几大特点:
* 当前时间的输入$x_t$与上一个时间步骤的隐层输出$h_{t-1}$
* 三个门一个信息输入，输入门input gate，遗忘门forget gate，输出门output gate，以及信息输入input。他们都有着同样的输入$x_t$ and $h_{t-1}$.
* 一个内部状态Cell State ($\bf S$).

关于LSTM的构成公式这里不再列出，欢迎查看<a href="https://lotuswhl.wordpress.com/2018/05/17/%e7%90%86%e8%a7%a3lstm/" target="_blank">我的wordpress博客-LSTM</a>博客。

## LSTM内容补充
实际上我们会发现，如果输入门和输出门都关闭的话，那么会发生什么？信息将会保存在内部状态Cell State中！然后，这些信息不会丢失，也不会对当前的输出造成影响！(**注意，这里的保留信息或者门的关闭，指的是局部的信息！也可能是全部，所以门的闭合可以针对特定部分的数据。**)。
> 那么，反向传播的时候回发生什么？显然，因为内部状态的Cell的内部循环的权重为1，所以不会影响梯度的回传，也就是BPTT，随着反向传播的进行，这些门就会学会什么时候关闭什么时候打开，也就是什么时候允许新的数据流入，什么时候允许旧的数据输出。

## 值得关注的BRNNs
因为很多时候当前时间的目标输出并不仅仅依赖于当前时间之前的数据信息，还有可能依赖于其后面的序列信息。因此，引入双向的RNN就很有意义了，其实很简单，也就是堆叠两条RNN。当然，BRNN需要针对固定的时间长度，因为其需要前向输入序列也需要反向输入序列。然后当前时间的输出依赖于两个隐层的输出，反向传播也是朝着不同的方向进行。
> 局限性：很明显的BRNNs，因为其需要固定或者确定的时间长度用以反向传播，主要是因为需要反向输入序列。因此，就没有那么灵活，比如，无法**在线学习**。