---
date: 2017-05-11T23:59:59+00:00
draft: false
author: "caicai"
title: "逻辑回归"
categories: ["机器学习"]
tags: ["ML, logistic"] 
---

链接:
[1. 线性回归总结](http://www.jianshu.com/p/25d650e5cb59)
[2. 正则化](http://www.jianshu.com/p/dda5eb64f425)
[3. 逻辑回归](http://www.jianshu.com/p/5ccc01385f40)
[4. Boosting](http://www.jianshu.com/p/7128dde2af6f)
[5. Adaboost算法](http://www.jianshu.com/p/b3f189767ad3)

-----
线性回归是通过拟合来达到目的，而逻辑回归呢侧重的是探寻概率，它不去寻找拟合的超平面，而是去寻找某个数据的类别归属问题。
## 一. 预测函数 ##
![Sigmoid函数](http://upload-images.jianshu.io/upload_images/1070582-5c59b3157c894162.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
怎么去预测？我们知道Sigmoid函数是如上这样的，它的表现是：将数据归纳在0-1之间，那么我们能不能通过去计算出的结果去拟合这样一个概率，使之成为一种分类的依据？答案是肯定的。凡事的概率都在0和1之间，拟合了概率，就是拟合了判定条件。
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1070582-c6bb6e213e44e023.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二. 具体做法 ##
我们知道线性回归是这样子的：
![](http://upload-images.jianshu.io/upload_images/1070582-565cc6d9fc3faee1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
将Sigmoid函数加载到这个结果上，不就是将结果0-1概率化了么：
![逻辑回归表达式](http://upload-images.jianshu.io/upload_images/1070582-ab8f313c8197ff8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
所以是这样子的：

![回归对比](http://upload-images.jianshu.io/upload_images/1070582-2d1fa60f7cf7603c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 三. 损失函数 ##
这样一来针对已有数据那就是0/1问题，要么概率为1的数据，要么概率为0的数据：

![样本评判](http://upload-images.jianshu.io/upload_images/1070582-1f60b3545160cf7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于我们来说概率可以简写合并成：
![](http://upload-images.jianshu.io/upload_images/1070582-51dee1961aa7c869.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
取其似然函数：
![](http://upload-images.jianshu.io/upload_images/1070582-c0f75e10e16945bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们要做的是在最大似然估计就是求使 L(θ )取最大值时的θ
![对数函数](http://upload-images.jianshu.io/upload_images/1070582-555314c1e307d924.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里可以自己做主，我选用下面作为损失函数，要是最大似然估计最大，就要使J(θ)函数最小，通过不断的优化θ使得J函数最小，进而L函数概率最大，完成任务。
![损失函数](http://upload-images.jianshu.io/upload_images/1070582-71dee2fb29891cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四. 求解过程 ##
### 1. 梯度下降法：###

![迭代函数](http://upload-images.jianshu.io/upload_images/1070582-01c8694cdd246ef6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![具体求解](http://upload-images.jianshu.io/upload_images/1070582-0d89fe43f219754b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 2. 矩阵法：###


![求解过程](http://upload-images.jianshu.io/upload_images/1070582-bce9b8bbf198b2f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![最终公式](http://upload-images.jianshu.io/upload_images/1070582-990451f1c8336281.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
通过依次求解A，E, θ得到最终解。（A为线性回归的θ*X）

参考：
[逻辑回归](http://blog.csdn.net/pakko/article/details/37878837)
[逻辑回归模型(Logistic Regression, LR)基础 - 文赛平](http://www.tuicool.com/articles/auQFju)
[机器学习—逻辑回归理论简介](http://www.mamicode.com/info-detail-501714.html)
