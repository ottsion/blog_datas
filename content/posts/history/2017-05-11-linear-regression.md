---
date: 2017-05-11T23:59:59+00:00
draft: false
author: "caicai"
title: "线性回归"
categories: ["机器学习"]
tags: ["ML,regression"] 
---

链接:
[1. 线性回归总结](http://www.jianshu.com/p/25d650e5cb59)
[2. 正则化](http://www.jianshu.com/p/dda5eb64f425)
[3. 逻辑回归](http://www.jianshu.com/p/5ccc01385f40)
[4. Boosting](http://www.jianshu.com/p/7128dde2af6f)
[5. Adaboost算法](http://www.jianshu.com/p/b3f189767ad3)

-----

## 一. 模型介绍 ##
线性回归简而言之就是在平面中用一条直线去拟合一些点数据,在三维空间中就是用一个平面去拟合三维中的数据,而我们要做的就是寻找出一条最佳的线段或者平面去拟合数据,当然高维情况类似去寻找超平面。
初中的时候我们就学习过一元一次方程,那就是一个简单的拟合过程,只不过那个是完全可以拟合在一条线上,现在要做的是在有误差或者数据非线性排列的情况下,我们只能去尽力找出一条最佳的拟合线路:
```
import numpy as np
import matplotlib.pyplot as plt
x = np.arange(1,10,2).reshape(-1,1)
y = a*2 
plt.plot(x,y,'r-',linewidth=2, label=u"线性拟合")
plt.plot(x,y, 'bo')
plt.show()
```
![完全拟合,二元一次方程组求解;(数据线性情况下)](http://upload-images.jianshu.io/upload_images/1070582-cda61f7178421a18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression

x = np.arange(1,10,2).reshape(-1,1)
y = a*2 + np.random.randn(5)
linear_model = LinearRegression()
linear_model.fit(x.reshape(-1, 1),y)
y_pre = linear_model.predict(x)
plt.plot(x,y_pre,'r-',linewidth=2, label=u"线性拟合")
plt.plot(x,y, 'bo')
plt.show()
```

![线性回归;寻找最佳的拟合线段;(数据非线性情况下)](http://upload-images.jianshu.io/upload_images/1070582-29ac5417e759c672.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


定义一下一些符号表达，我们通常习惯用X=(x1,x2,...,xn)T∈ℝn×p表示数据矩阵，其中xi∈ℝp表示一个p维度长的数据样本；y=(y1,y2,...,yn)T∈ℝn表示数据的label，这里只考虑每个样本一类的情况。

线性回归的模型是这样的，对于一个样本xi，它的输出值是其特征的线性组合： 

![线性表达式](http://upload-images.jianshu.io/upload_images/1070582-b9b74033fc121d09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，w0称为截距，或者bias，通过设置X中X0=1,向量表达,简化了形式，因此实际上xi有p+1维度。
线性回归的目标是用预测结果尽可能地拟合目标label，

## 二. 损失函数 ##
从下图来直观理解一下线性回归优化的目标——图中线段距离（平方）的平均值，也就是最小化到分割面的距离和。
![三维中拟合后的情况](http://upload-images.jianshu.io/upload_images/1070582-5a0a77beed3e1c18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里我们要做的是在随机放置权值W的情况下不断的优化程式,以达到效果最佳的情况,这里的优化指的是尽量让各个点拟合在所求超平面,如果不能拟合,也要使得其距离超平面最近为好,也就是可以定义损失函数

![损失函数](http://upload-images.jianshu.io/upload_images/1070582-3af679fd12c075bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三. 求解过程 ##
由于上述损失函数图形如下,我们这里选用两种方式去求解
![J(θ)平面](http://upload-images.jianshu.io/upload_images/1070582-d60ef19ae4a024bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 1. 矩阵满秩：###
这种形式下我们可以通过求解导数为零的方式求解算法，可以直接得到最后的Ｗ权值．
![矩阵形式转换](http://upload-images.jianshu.io/upload_images/1070582-4137d5e50ef6eba9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![求解过程](http://upload-images.jianshu.io/upload_images/1070582-8227e6c4c7b1120b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 1. 矩阵不满秩：###
　　当矩阵不满秩，无法求得上述的矩阵逆解，这里采用梯度下降法进行求解，梯度下降法通常分为三种形式：整体批次梯度下降法，随机梯度下降法和批量梯度下降法．
　　梯度下降算法是一种求局部最优解的方法，对于F(**x**)，在a点的梯度是F(**x**)增长最快的方向，那么它的相反方向则是该点下降最快的方向，具体参考[wikipedia](http://en.wikipedia.org/wiki/Gradient_descent)。
　　 原理：将函数比作一座山，我们站在某个山坡上，往四周看，从哪个方向向下走一小步，能够下降的最快；注意：当变量之间大小相差很大时，应该先将他们做处理，使得他们的值在同一个范围，这样比较准确。
    1）首先对θ赋值，这个值可以是随机的，也可以让θ是一个全零的向量。
    2）改变θ的值，使得J(θ)按梯度下降的方向进行减少。
　描述一下梯度减少的过程，对于我们的函数J(θ)求偏导J： 
![](http://upload-images.jianshu.io/upload_images/1070582-185126423463544d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下图表达出解得最终结果与初始值相关：
![梯度下降方式](http://upload-images.jianshu.io/upload_images/1070582-3a38dff615044582.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里我们需要进行的是：
![迭代公式](http://upload-images.jianshu.io/upload_images/1070582-2d3453e9127d080f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　在批量梯度下降中，每一次参数更新都会遍历全部的训练数据{x1, y1}, {x2, y2}, ... , {xm,ym}对于线性回归问题，这种方法可以得到一个全局最优解，但是当样本数据量很大的时候，会非常耗时。

## 多项式回归 ##
我们可以通过设计高阶特征在线性回归的基础上实现多项式回归。对于线性回归的hypothesis,
![](http://upload-images.jianshu.io/upload_images/1070582-dc5bc59068a9dc44.latex?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
令$ x2=x1^2, x3=x1^3 $,实现了一个3次多项式的回归：
![](http://upload-images.jianshu.io/upload_images/1070582-c19a0861cca368f4.latex?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这也是用线性模型实现非线性的常用方法。这种方式能够拟合出非线性的超平面，有时候表现出很好的特性
```
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures
X_train = [[6], [8], [10], [14], [18]]
y_train = [[7], [9], [13], [17.5], [18]]
X_test = [[6], [8], [11], [16]]
y_test = [[8], [12], [15], [18]]

quadratic_featurizer = PolynomialFeatures(degree=2)
X_train_quadratic = quadratic_featurizer.fit_transform(X_train)
X_test_quadratic = quadratic_featurizer.transform(X_test)
xx = np.linspace(0, 26, 100)
regressor_quadratic = LinearRegression()
regressor_quadratic.fit(X_train_quadratic, y_train)
xx_quadratic = quadratic_featurizer.transform(xx.reshape(xx.shape[0], 1))
plt.plot(xx, regressor_quadratic.predict(xx_quadratic), 'r-')
plt.show()
```

![多项式回归与一般线性回归对比](http://upload-images.jianshu.io/upload_images/1070582-eab9f5bbe7b5ae18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[线性回归代码地址](https://github.com/ottsion/machine_learning/tree/master/LinearRegression)

参考:
[机器学习方法：回归（一）：线性回归Linear regression](http://blog.csdn.net/xbinworld/article/details/43919445)
[机器学习-----线性回归浅谈（Linear Regression）](http://www.cnblogs.com/GuoJiaSheng/p/3928160.html)
[Stanford机器学习笔记-1.线性回归](http://www.cnblogs.com/llhthinker/p/5248586.html)
[线性回归](http://www.jianshu.com/p/0396cb1a471c)
