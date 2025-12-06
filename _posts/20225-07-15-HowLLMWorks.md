## 神经网络是个什么东西？
![safsdf.png](/image/llm/neuron.png) 
![safsdf.png](/image/llm/neuron1.png) 

## 激活函数：是干啥的，没太理解neuron
![safsdf.png](/image/llm/Activation_Function.png) 
![safsdf.png](/image/llm/Activation_Function1.png) 
![safsdf.png](/image/llm/Activation_Function2.png) 
![safsdf.png](/image/llm/Activation_Function3.png) 
![safsdf.png](/image/llm/Activation_Function4.png) 
![safsdf.png](/image/llm/Activation_Function5.png) 


## 
## y=wx + b怎么理解

## 神经元是啥

## softmax是干啥的


## 矩阵相乘

行 列 要想等 4行2列* 2行6列 最后是 4行6列
单个 token embedding 是向量，多 token 的 embedding 堆起来就是矩阵。

##  Q K 怎么区分的， 毕竟都是矩阵，感觉相对有意义，单个没啥意义。

让模型可以学习不同“视角”的表示。

## ywx+b是线性变换，全联接层
## relu是 激活函数

## Q K V 为啥改Q *K 计算attention的，为啥Q的梯度更高，如何区分的。

改 Q，会改变整行（I 关注 love 程度变了）
改 K，只是改变某列（love 被所有人关注的程度变了）

Q K 是向量，不是矩阵WQ WK是矩阵，
x* WQ= Q
![safsdf.png](/image/LLM2/QKV2.png) 
![safsdf.png](/image/LLM2/QKV.png) 




attention 只是关系矩阵，不是预测矩阵
预测下一个单词要跟预测矩阵相乘，才能知道谁最大概率是下一个。

但是预测之前还要有个 可学习矩阵，其实也就是格式化下才能被预测矩阵使用。


## 那模型是不是pytorch写的？

## 模型的本质是啥

## attention 矩阵最后如何变为对应的具体token


## langraph 
![safsdf.png](/image/LLM2/LangChain.png) 


# 视觉

## 同一物体的各种形态”，从而学会“语义不变性”。

## 遮挡与鲁棒性
图像部分遮挡时，模型仍要能识别整体。

## 多尺度特征（


## 池化是干啥的。

##
梯度 是指损失函数相对于权重的变化率，也可以看作是权重调整的方向和步幅。我们通过计算梯度，了解每个权重需要增加还是减少。

反向传播 是一种计算梯度的算法，它通过链式法则逐层计算梯度，从输出层反向传播到输入层。


## dsp 乘 加运算


## 卷积核矩阵的作用

## 池化的作用


## 激活层的作用
输入和激活层的结果才能更好拟合某件事情

## 如何最后输入为相似度的

跟多头注意力最后有点像


## 阴阳

孤阳不生，孤阴不长
一个东西是不能单独存在的，只有相对，才有存在。

你说白的是白色，那是因为有黑色

同样的，你说这两个词关系近，那就要有这个词，和另一个词的向量，关系近就是向量的点积数值大。

同样的，你说卷积里面，特征图跟某个特征相似。被卷积之后复合某个特征。

特征图1， 特征权重，相似， 所有向量相乘的和 最大，那就是相似。


