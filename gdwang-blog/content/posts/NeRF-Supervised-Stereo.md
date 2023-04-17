---
title: "NeRF Supervised Stereo"
date: 2023-04-17T10:24:39+08:00
draft: false
---

# 1. NeRF
> * 神经辐射场，使用5D函数隐式表达三维场景，$F_\Omega(x, y, z, \theta, \phi)\rightarrow (r, g, b, \sigma)$， 不对场景进行显示建模；
> * 神经渲染，per-ray方式, 生成新视角下的2D图像， 可以渲染新视角下的图像，深度图，分割特征，法向量等；
> * 空间采样 & 编码； 



# 2. NeRF & Lidar
在计算深度图方面:
相同点：
> * Lidar 测距， ray cast;
> * NeRF 渲染深度图，ray cast；

不同点：
> * sparse， Lidar 的 ray cast， 发出光线，与粒子碰撞后反射，接收器接收，飞行时间法测距离；
> * dense， NeRF 的 ray cast， 发出光线 $r(t) = o + t * \vec{d}$，计算在光线上第一次碰撞到粒子的概率（不透明度），再沿光线积分；

优势：NeRF采用Image based 的方式获取场景深度，可以看成是一个软件工具。修改渲染时的粒子碰撞模型、空间采样模型等，可以处理
> * 无边界， 天空, ...
> * 反射， 水面，镜面, ...
> * 透射， 透明玻璃, ...
> * 水下， ... 

缺点：
> * 不能实时，需要提前拍摄场景，训练；
> * 速度慢；

# 3. NeRF supervised Stereo
使用手机拍摄真实场景图，基于NeRF构建虚拟视点下，左中右三目系统的 img, depth-gt数据集；本文作者使用手机拍摄270个场景，每个场景100张图片左右，生成的数据量大约为27000 对三元组triplets；
## 3.1 NeRF 渲染公式
* 渲染颜色
积分形式
$$
C(r) = \int_{t_n}^{t_f}T(t)\sigma(r(t))c(r(t), d)dt,
$$
where, $T(t) = \exp(-\int_{tn}^t\sigma(r(s))ds)$.

黎曼形式
$$
C(r) = \sum_{i=1}^{N}T_i\alpha_ic_i,
$$
where, $\alpha_i = 1-\exp(-\sigma_i\delta_i)$, $T_i=\prod_{j=1}^{i-1}(1-\alpha_j)$;

* 训练Loss
$$
L_{render} = \sum_r \Vert C(r) - \hat{C(r)} \Vert_2^2
$$

* 渲染深度
$$
z(r) = \sum_{i=1}^{N}T_i\alpha_it_i,
$$
视差， $d = \frac{fb}{z(r)\cos(\theta)}$, $\theta$指的是光线方向与相机主轴的夹角。

* 生成数据库
左中右虚拟相机pose，渲染图像与深度
中间相机
$$
E_k = R_k | t_k
$$
右相机
$$
E_k^r = R_k | (t_k + b)
$$
左相机
$$
E_k^l = R_k | (t_k - b)
$$

## 3.2 立体匹配
生成数据集后可以用任何的单目、双目深度估计网络架构作backbone，作者使用了PSM-Net, RAFT-Stereo, CF-Net做测试。

有左中右三个相机，作者借用了monoDepth2中的loss方法为数据集标注遮挡区域；
* 光度损失函数



光度误差
$$
L_\rho(I_c, I_c^r) = \beta \cdot \frac{1-\text{SSIM}(I_c, I_c^r)}{2} + (1-\beta)\cdot |I_c, I_c^r|
$$
where, $I_c^r$为右图warp到中间相机视角下的图像。

自监督误差
$$
L_{3\rho}(I_c^l, I_c, I_c^r) = \min(L_\rho(I_c^l, I_c), L_\rho(I_c, I_c^r)),
$$

NeRF监督误差
$$
L_{disp} = |d_c - \hat{d}_c|,
$$

NeRF Supervised 损失函数
$$
L_{NS} = \gamma_{disp} \cdot \eta_{disp} \cdot L_{disp} + \mu \cdot \gamma_{3\rho} \cdot (1 - \eta_{disp}) \cdot L_{3\rho}
$$

其中，$\mu$表示无纹理区域，计算方式参考monodepth2无纹理区域mask，
$$
\mu = [min L_{3\rho}(I_c^l, I_c, I_c^r) < L_{3\rho}(I_l, I_c, I_r)]
$$

其中，$\eta_{disp}$表示NeRF渲染深度的可信度，计算方式为
$$
\eta_{disp} = 0, (AO  < thr), \\
\eta_{disp} = AO, (AO \geq thr), \\
$$

where, AO范围是 $[0, 1]$
$$
AO = \sum_{i=1}^NT_i\alpha
$$