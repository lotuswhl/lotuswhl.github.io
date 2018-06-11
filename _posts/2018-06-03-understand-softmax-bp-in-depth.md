---
layout:     post
title:      Understanding Softmax loss function backpropagation In Depth
subtitle:   形象思考与数学推理
date:       2018-06-03
author:     HL
header-img: img/understanding/softmax-understand.jpeg
catalog: true
tags:
    - softmax
    - understanding
    - deep learning
---
# 问题引出
学习和研究机器学习和深度学习也有些时日了，或许是因为看得多了，或许是因为看的太久了，又或许当初在学习的时候就没有弄清楚很多问题的细节。希望自己在后续的研究过程中可以更加的注重细节并且及时的将自己的思考与理解整理出来，一方面增强逻辑理解，另一方面方便自己后续的查看。今天打算重新思考和推理一下softmax的反向传播过程。
## 为什么选择softmax？
因为softmax是在分类问题中应用最为广泛的损失函数。毕竟将向量转为概率分布输出是一件很有意义和价值的事情。另外一方面，softmax损失函数通常来说指的是使用交叉熵来构建目标损失函数。而交叉熵又是很有趣的一个东西：因为对数概率的存在。softmax损失函数作为反向传播的推导思考在难度以及实际使用上都是不错的选择。
## 为什么要重新思考反向传播？
事实上，在一开始学习深度学习的时候就开始接触方向传播的概念了。一般而言都会使用LR(logistic regression)的交叉熵损失函数来演示反向传播的过程。(LR的损失函数本身就是softmax的二分类问题表达)。实际上我们都可以通过简单的链式法则来进行推导，也就是推导损失L对我们需要调整或者学习的参数的导数，当然这中间还会计算必要的中间量的导数。
### 思考反向传播过程中损失函数对预测输出值的梯度？
通常对于N分类问题，我们的神经网络的输出层是N个节点，表达输入样本特征在这N个分类位置上的取值，通常来说值最大的那个也就是我们的预测分类。然后我们的损失函数构建于输出层之上，也就是会有一个目标分类向量，通常我们会使用onehot来编码目标分类。我们来看看交叉熵损失的公式:  

$$L = \sum_{j=1}^N{-t_j\log{o_j}}$$  

其中$t_j,o_j$分别是我们输出向量第$j$个的目标值和softmax输出值**注意$o_j$已经是概率了**。而我们在使用tensorflow这样的深度学习框架时其实根本看不到反向传播的轨迹，因为我们都是通过定义好损失函数之后，交给tensorflow内置的优化算法比如SGD，Adam等去优化，在训练时只要调用优化算法执行就可以实现反向传播，这很不利于我们的思考。而在pytorch当中似乎要好一些，至少在我们定义好损失函数之后，需要自己调用一下loss.backward()函数，将损失的梯度传递回去，然后使用自己的方法来按照梯度更新参数值或者使用pytorch的optim中提供的优化算法来帮助更新。当我们使用backward函数时，我们可以回传一个Tensor作为梯度，对于单个值也可以回传一个值作为梯度。那么问题来了，对于我们的N分类问题，我们是怎样传递一个损失的梯度向量回去的呢？毕竟我们知道损失函数的结果是一个值，仔细看一下上面的损失函数公式，实际上对于onehot编码的情况下，我们会将正确的标签位置的节点的输出的负对数$-\log{o_j}$作为梯度传播回去.然后loss.backward()默认传递的是一个值。**至少按照我一开始较为粗浅的想法应该传播回去一个梯度才对，一个更为直观的理解方式当然就是画图了，通过图示演算反向传播的过程便可以得到解答。**

# 推导
## 简单的二分类问题softmax forward示例
### forward
![softmax forward 实例](https://raw.githubusercontent.com/lotuswhl/lotuswhl.github.io/master/img/understanding/softmax-forward-demo.png)

> 图中假设h1,h2,h3为网络的输入，y1,y2表示的输出层的输出值，而o1,o2则是softmax函数的计算结果，最后的L是损失值。  


根据图示我们可以得到以下forward公式:  

$$L=-t_1\log{o_1}-t_2\log{o_2}$$  

$$o_1=\frac{e^{y_1}}{\sum_{i=1}^2{e^{y_i}}}$$   

$$o_2=\frac{e^{y_2}}{\sum_{i=1}^2{e^{y_i}}}$$  

$$y_1=w_{11}h_1+w_{21}h_2+w_{31}h_3$$  

$$y_2=w_{12}h_1+w_{22}h_2+w_{32}h_3$$    

### backward
假如我们要计算损失L对W21的梯度: 

![softmax backward](https://raw.githubusercontent.com/lotuswhl/lotuswhl.github.io/master/img/understanding/softmax-bp-demo.png)  

我们可以进行如下计算:  

$$\frac{\partial{L}}{\partial{o_1}}=-\frac{t_1}{o_1} $$  

$$\frac{\partial{L}}{\partial{o_2}}=-\frac{t_2}{o_2} $$  

$$\frac{\partial{o_1}}{\partial{y_1}}=\frac{e^{y_1}{\sum_{i=1}^2{e^{y_i}}}}{(\sum_{i=1}^2{e^{y_i}})^2}-\frac{e^{y_1}e^{y_1}}{(\sum_{i=1}^2{e^{y_i}})^2}=o_1-o_1^2$$  

$$\frac{\partial{o_2}}{\partial{y_1}}=-o_2o_1$$  

$$\frac{\partial{y_1}}{\partial{w_{21}}}=h_2$$  

最后就可以使用链式法则将他们拼接到一起了。具体的式子很简单这里就不列了。

# Last but not least
在这个post里面，我们主要说明几个问题:
* softmax 损失函数表达式
* 损失函数的梯度反向传播
* softmax的forward和backward公式推导示例

### 还有一个没有说明的问题：也就是为什么损失函数反向传播的梯度不是向量而是值？
实际上，我们在计算对向量的梯度时其本身也是计算对向量的某个成分的梯度也就是计算向量的某个位置上的值的梯度或者偏导数，然后组合起来就得到了向量的梯度。因此，对于这个问题而言，实际上是对梯度反向传播的理论与实现上的综合思考。因为我们在实践中为了追求计算的速度，往往会并行计算对向量的梯度值，因此看起来在中间的过程中是进行的向量的梯度传递，而在最后一层的损失函数值处，看起来只是传递了单个值作为梯度。但是其本质都是损失值L对输出向量y的每个值的梯度然后反向传播给前面的参数，进而进行参数的更新。  

以上。