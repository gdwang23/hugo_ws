---
title: "Basic"
date: 2023-03-02T14:45:07+08:00
draft: false
---

# Matrix
1.QR分解  
●定义：将列满秩矩阵Amn分解成正交矩阵Qmn和上三角矩阵Rnn;  
●列满秩：  
若列方向不满秩, 不存在一组完备的正交基矢，不能进行施密特正交化；  
●适应于矩阵特征值，逆，最小二乘问题；  
2.DP动态规划  
把求当前状态的问题转化为上一个状态的问题； 
3.SVD  

# Probability
1.卡尔曼滤波  
KF / EKF  
运动模型P(Xt | Xt-1)， 马尔可夫假设  
观测模型P(Et | Xt)   
先验信息P(X0)  


# CV image process
# Geometry
## 双目测距原理  
左右相机之间 旋转矩阵 R = 1, t = baseline  
点在左相机坐标系中 P1 = (X, Y, Z),  则在右相机坐标系中坐标为 P2 = （X-b, Y,  Z）  
左相机成像  
u1 = fx * X / Z + u0  
右相机成像  
u2 = fx * (X - b) / Z  +u0  
视差  
disp  = u1 - u2 = fx * b / Z  
深度  
Z = fx * b / disp  
	
## 单应性矩阵  
不严谨的定义：用 [无镜头畸变] 的相机从不同位置拍摄 [同一平面物体] 的图像之间存在单应性，可以用 [投影变换] 表示   
x‘ = H x  
3.Rectify  
4.变换矩阵  
●欧式变换  
T = [R, t;   
0, 1]  
●相似变换  
T = [sR, t;   
0, 1]  
●仿射变换  
T = [A, t;   
0, 1]  
●射影变换  
T = [A, t;   
a^T, v]  
欧式变换有6个自由度；相似变换有7个自由度；  
仿射变换有12个自由度；3D摄影变换有15个自由度，2D射影变换有8个自由度； 

## 相机位姿  
相对于世界坐标系下相机的旋转和平移， R, t  
R, SO(3)  
T, SE(3)  

李代数  
so(3), \phi, R = exp(-\pha^), 	映射， 3维向量到3维度矩阵；  
se(3)， \xi, T = , 	映射， 6维向量到4维矩阵；  


# 5.DL  
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

# 6.tools
1.git  
a.撤销git commit，撤销git add，保留编辑器改动代码  
git reset --soft HEAD^  
或者git reset --mixed HEAD^   
b.撤销git push  
git log 查看版本号  
git reset –-soft <版本号>  
git push origin 分支名 –-force  
c.commit 写错了，想修改一下注释  
git commit --amend  
d.mixed， soft， hard 区别  
--mixed, 不删除工作空间改动代码，撤销commit，撤销git add .   
--soft, 不删除工作空间改动代码，撤销commit，不撤销git add .  
--hard, 删除工作空间改动代码，撤销commit，撤销git add .  
e.把主分支合并到子分支  
git merge master, 在子分支下git merge  

2.  


Interview
c++ / 代码review / DL / KF / 休息
	





