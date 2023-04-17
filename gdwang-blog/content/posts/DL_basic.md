---
title: "DL_basic"
date: 2023-03-08T14:06:43+08:00
draft: false
---
# DL basic
## NN and BP
NN, neural network，可称为全连接的网络；也可以称为多层感知器MLP;

一层线性函数,  
$f = W x  $

两层线性函数，  
$f = W_2\max(0, W_1 x) $

三层  
$f = W_3\max(0, W_2\max(0, W_1 x)) $

为什么需要引入非线性？

如果没有线性的激活函数， 则$f = W_3W_2W_1x$， NN变成一个线性函数；NN的输入和输出仍然是线性关系，不能拟合非线性的问题。
引入非线性的函数，NN可以拟合任意的非线性函数。例如，非线性的分类问题，NN可以通过非线性的特征变换转化为线性可分类的问题；

### BP
需要计算loss对每一个权重的梯度信息；
local gradient, upstream gradient

### BN
nn.BatchNorm2D  
$x' = \alpha \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} + \beta
$



### 常用线性激活函数
sigmoid $\frac{1}{1+e^{-x}} $

ReLU $max(0, x)$

Leaky ReLU  $\max(0.1x, x)$

tanH $\tanh (x)$

ELU $x, x >= 0, \alpha (e^x-1), x < 0$



# torch 
## 函数
###
torch.clamp(input, min, max, out=None) → Tensor

clamp（）函数的功能将输入input张量每个元素的值压缩到区间 [min,max]，并返回结果到一个新张量

### DataSet, DataLoader
dataset = myDataSet(); Dataset负责建立索引到样本的映射;  

dateloader = DataLoader(); DataLoader负责以特定的方式从数据集中迭代的产生 一个个batch的样本集合;   

for idx, (img,label) in enumerate(dateloader); enumerate过程是dataloader按照其参数sampler规定的策略调用了其dataset的getitem方法;

# others  
把神经网络当成一个万能函数拟合器， 把卷积理解为滤波和特征提取，
全连接层理解为加权求和，反向传播理解为从最后一层向前逐步调整权重参数；
把设计模型当成搭积木；

## ResNet
### a.为什么提出ResNet，解决了什么问题  
深层网络存在退化，解决深层DL模型难训练的问题

在反向传播的过程中，由于误差本身就很小，如果层数很多，在经过激活函数时，误差消失，称为梯度消失；
### b.如何解决的

短路连接

### c.残差
y = ReLU(F(x) + x)    
梯度反向传播  
基于链式规则，反向传播中的梯度；  

loss/ x_L 表示损失函数到达L时的梯度；
1 表示shortcut的短路机制可以无损的传播梯度；  
残差梯度还需要经过带有weight的层；  
有1的存在不会导致梯度消失，残差学习更容易；  
###  d.res18 与 res50的区别  

3.局部与全局
瞳孔放大，聚焦局部， 瞳孔缩小，模糊轮廓原理
卷积操作，改变窗口大小，关注局部与整体；

1.L1, L2, 与smooth L1, Huber loss的区别； 
●L1, MAE, 平均绝对差，真实值和预测值的差，取平方；  
●L2, MSE 平均平方差，真实值和预测值差的平方，取平均；  
●smooth L1, 梯度随着损失值的减小而变化，loss越小，梯度越小，更容易收敛；  
0.5x^2 for |x| <1  
|x|-0.5 otherwise  
●Huber loss  
0.5x^2  for |x| < delta  
delta(|x|-0.5delta) otherwise  
●smooth L1， Huber loss都是由绝对差AE, 平方差SE的部分组成；预测值偏大，使用线性的误差AE，预测值偏小使用平方误差SE；区别在于Huber loss线性部分的斜率是可调的；smooth L1则固定斜率为1；  
●smooth L1 可以看成Huber loss线性部分斜率为1的一个特例；即当delta = 1, Huber退化成smooth L1；  
●优缺点  
L2赋予了异常数据更大的权重，容易被异常值破坏误差项，不稳定，容易产生梯度爆炸；  
L1梯度稳定，（如果不被非常大的异常值破坏， 比L2稍好），有稳定解；但L1在转折点处，对于小的变化项，跳跃很大；  
2.损失函数  
●L1， 鲁棒性差，稳定解；  
●L2， 鲁棒性强， 不稳定解；  
●交叉熵  
-sum[ yi  log(pi) + (1-yi) * log(1-pi)]  
yi 为分类label， pi为预测概率；  
3.激活函数  
●sigmoid  
f(x)  = 1/ (1+e^(-x))  
区间范围(0, 1)， 收敛缓慢， 可能导致梯度消失；幂函数计算开销太大；  
●LogSigmoid  
log[ 1/ (1+e^(-x))]  
●ReLU  
f(x) = max(0, x)  
仅在x > 0时候激活，x < 0时梯度为0。在反向传播过程中，部分神经元的权重和偏差没有更新，适用于图像输入；  
只有部分神经元被激活，计算效率要高；	 
●LeakyReLU  
max(0.01x, x)  
PReLU   
f(x) = max(ax, x)  
PReLU是对LeakyReLU的改进，a 不再固定0.01， 通过训练网络学习；  
RRelu函数也是对LeakyReLU的改进， 训练时，保持a在一定固定范围内；  
●softMax
f(xi) = e^(xi) / sum( e^(xi))  
将输出转化为概率的形式， 所有概率之和为1；常用于多分类最后一层，将输出转化为分类类别的概率形式；  
●tanh  
f(x)  = e^x-e^(-x)  /  e^x+e^(-x)  
输出均值为0， 收敛速度比sigmoid快，区间范围(-1, 1),  常适用于循环网络中；  

### FLOPs
FLOPs全名为floating point operations（浮点数的运算数），指模型前向传播的计算量、计算速度，用于衡量模型的复杂度；
全大写的FLOPS是指每秒浮点数的运算次数，用于衡量硬件性能。

计算一次卷积需要使用 $k_h * k_w * c_{in}$ 次乘法， $k_h * k_w * c_{in} - 1$次加法；  
如果bias，则为$k_h * k_w * c_{in} - 1 + 1$次加法， 总计算量 $2 * k_h * k_w * c_{in}$  

总计算量$2 * k_h * k_w * c_{in} * c_{out} * H_{out} * W_{out}$

例如，一次3x3卷积， in_planes = 64, out_planes = 64, H_out = 480, W_out = 640, 总浮点运算数约为$2e^10&, 20GFLOPs
