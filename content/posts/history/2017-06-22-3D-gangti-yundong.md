---
date: 2017-06-22T23:59:59+00:00
draft: false
author: "caicai"
title: "三维刚体运动表示及其运算"
categories: ["ROS"]
tags: ["slam"] 
---

## 三维刚体运动表示及其运算（李群李代数）

> 三维空间的刚体运动描述方式：旋转矩阵、变换矩阵、四元数和欧拉角。
>
> 编程EIgen、soupha

- 向量加减法

$a\pmb=\sum_{i=0}^{n}{(a_i+b_i)}$


- 向量内积 

$a\bullet b=a_Tb=\sum_{i=1}^{3}{a_ib_i}=|a||b|\cos\langle a,b\rangle​$

- 向量外积

-$a\times b=\begin{bmatrix}i&j&k\\a_1&a_2&a_3\\b_1&b_2&b_3\end{bmatrix}=\begin{bmatrix}a_2b_3-a_3b_2\\a_3b_1-a_1b_3\\a_1b_2-a_2b_1\end{bmatrix}=\begin{bmatrix}0&-a_3&a_2\\a_3&0&-a_1\\-a_2&a_1&0\end{bmatrix}b\triangleq\hat ab$

## 一. 旋转矩阵

设某坐标系$[e_1,e_2,e_3]$发生了一次旋转，变成了$[e_1^`,e_2^`,e_3^`]$,对于某个固定的向量a（向量不随坐标系旋转），它的坐标变换：

$[e_1,e_2,e_3]\begin{bmatrix}a_1\\a_2\\a_3\end{bmatrix}=[e_1^`,e_2^`,e_3^`]\begin{bmatrix}a_1^`\\a_2^`\\a_3^`\end{bmatrix}$

左乘$\begin{bmatrix}e_1^T\\e_2^T\\e_3^T\end{bmatrix}$得到：

$\begin{bmatrix}a_1\\a_2\\a_3\end{bmatrix}=\begin{bmatrix}e_1^Te_1^`&e_1^Te_2^`&e_1^Te_3^`\\e_2^Te_1^`&e_2^Te_2^`&e_2^Te_3^`\\e_3^Te_1^`&e_3^Te_2^`&e_3^Te_3^`\end{bmatrix}\begin{bmatrix}a_1^`\\a_2^`\\a_3^`\end{bmatrix}$

其中上式中中间矩阵即为旋转矩阵(为特殊正交群)：

$SO(n)=\left\{R\in R^{n\times n}|RR_T=I,det(R)=1\right\}​$



## 二. 变换矩阵

对旋转加上平移：$a^`=Ra+t$
这个表达式不适应于连乘，于是改写成下式：

$\begin{bmatrix}a^`\\1\end{bmatrix}=\begin{bmatrix}R&t\\0^T&1\end{bmatrix}\triangleq{T}\begin{bmatrix}a\\1\end{bmatrix}$

这种结构的好处是可以连乘：$\tilde{b}=T_1\tilde{a}$,$\tilde{c}=T_2\tilde{b}$
==>$\tilde{c}=T_2T_1\tilde{a}$

这种用四个数表达三维向量的做法称为齐次坐标引入齐次坐标后，旋转和平移可以放入同一个矩阵，称为变换矩阵称为特殊欧氏群（Special Euclidean Group）

$SE(3)=\left\{T=\begin{bmatrix}R&t\\0^T&1\end{bmatrix}\in R^{4\times4}|R\in SO(3),t\in R^3\right\}$

类似的定义反向变换：

$T^{-1}=\begin{bmatrix}R^T&-R^Tt\\0^T&1\end{bmatrix}$



## 三. 角轴/旋转向量

三维旋转：三自由度，用$R^3​$向量表示

方向为旋转轴、长度为转过的角度：$w=\theta n$

角轴与旋转矩阵的不同:

- 旋转矩阵：九个量，有正交性约束和行列式值约束
- 角轴：三个量，没有约束
- 注意它们只是表达方式的不同，但表达的东西可以是同一个
- 角轴也就是第四章要介绍的李代数

转换关系：

1. 轴角转旋转矩阵：罗德里格斯公式（Rodrigues's Formula ）：

$R=\cos\theta I+(1-\cos\theta)nn^T+\sin\theta\hat{n}$

2. 旋转矩阵转轴角：

角度：$\theta=\arccos(\frac{tr(R)-1}{2})$

轴：$Rn=n$-->就是求R的特征值为1的特征向量

## 四. 欧拉角（Euler Angles）

将旋转分解为三次不同轴上的转动，以便理解

•例如：按 Z-Y-X顺序转动

•轴可以是定轴或动轴，顺序亦可不同，因此存在许多种定义方式不同的欧拉角

常见的有 yaw-pitch-roll （偏航-俯仰-滚转）角等等。

```
1.绕物体的Z轴旋转，得到偏航角yaw；
2.绕旋转之后的Y轴旋转，得到俯仰角pitch；
3.绕旋转之后的X轴旋转，得到滚转角roll。
```

**万向锁问题导致无法应用到实际**

## 五. 四元数

结构：$q=q_0+q_1i+q_2j+q_3k$

它的性质：

$\begin{cases}i^2=j^2=k^2=-1\\ij=k,ji=-k\\jk=i,kj=-i\\ki=j,ik=-j\end{cases}$

同时一般表示为：$q=[s,\vec v]​$其中$s=q_0\in R,\vec v=[q_1,q_2,q_3]^T\in R^3​$

**与旋转向量的转换：**

假设某个旋转是绕单位向量$n=[n_x,n_y,n_z]^T$进行了角度为$\theta$的旋转，那么这个旋转四元数形式为：

$q=[\cos\frac{\theta}{2},n_x\sin\frac{\theta}{2},n_y\sin\frac{\theta}{2},n_z\sin\frac{\theta}{2}]^T​$

反之可以得出对应的旋转轴与夹角：

$\begin{cases}\theta=2\arccos q_0 \\ [n_x,n_y,n_z]^T=[q_1,q_2,q_3]^T/\sin{\frac{\theta}{2}}\end{cases}$

在四元数中，任意的旋转都可以由两个互为相反数的四元数表示。（转了一半的感觉）

四元数的运算：

1. 加减法

$q_a\pm q_b=[s_a\pm s_b,v_a\pm v_b]$

2. 乘法

每项都乘，最后相加。

$q_aq_b=s_as_b-x_ax_b-y_ay_b-z_az_b+(s_ax_b+x_as_b+y_az_b-z_az_b)i+(s_ay_b-x_az_b+y_as_b+z_ax_b)j+(s_az_b+x_ay_b-y_ax_b+z_as_b)k$

简化为：

$q_aq_b=[s_as_b-v_a^Tv_b,s_av_b+s_bv_a+v_a\times v_b]$

3. 共轭

虚部相反数即是共轭：$q_a^*=s_a-x_ai-y_aj-z_ak=[s_a,-v_a]$

4. 模长

$||q_a||=\sqrt{s_a^2+x_a^2+y_a^2+z_a^2}$

   两个四元数乘积的模即为模的乘积，保证了单位四元数相乘后仍是单位四元数：

$||q_aq_b||=||q_a||||q_b||$

5. 逆

$q^{-1}=q^*/||q||^2$

此时保证了$qq^{-1}=q^{-1}q=1$

   如果q为单位四元数，其逆和共轭是同一个变量，同时乘积的逆有和矩阵相似的性质：

$(q_aq_b)^{-1}=q_b^{-1}q_a^{-1}$

6. 数乘与点乘

四元数与数相乘：$kq=[ks,kv]$

点乘是指四元数每个位置分别相乘：$q_aq_b=s_as_b+x_ax_b+y_ay_b+z_az_b$







