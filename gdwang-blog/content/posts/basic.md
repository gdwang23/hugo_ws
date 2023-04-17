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
## SSIM
$
ssim(x, y) = \frac{(2\mu_x\mu_y+c_1)(2\sigma_x\sigma_y+c_2)}{(\mu_x^2+\mu_y^2+c_1)(\sigma_x^2+\sigma_y^2+c_2)}
$


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
	





