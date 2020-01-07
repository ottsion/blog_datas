---
date: 2017-05-11T23:59:59+00:00
draft: false
author: "caicai"
title: "tensorflow--tensorboard"
categories: ["深度学习"]
tags: ["DL, tensorboard"] 
---



来源：[Tensorflow 自带可视化Tensorboard使用方法 附项目代码](http://blog.csdn.net/jerry81333/article/details/53004903)
Tensorboard：
如何更直观的观察数据在神经网络中的变化，或是已经构建的神经网络的结构。上一篇文章说到，可以使用matplotlib第三方可视化，来进行一定程度上的可视化。然而Tensorflow也自带了可视化模块Tensorboard，并且能更直观的看见整个神经网络的结构。
![](http://upload-images.jianshu.io/upload_images/1070582-2e4c64e474929abb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面的结构图甚至可以展开，变成：
![](http://upload-images.jianshu.io/upload_images/1070582-3c0ff9b872625bcb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用：
**结构图：**

     with tensorflow .name_scope(layer_name):
直接使用以上代码生成一个带可展开符号的一个域，并且支持嵌套操作：

    with tf.name_scope(layer_name):  
        with tf.name_scope('weights'): 
节点一般是变量或常量，需要加一个“name=‘’”参数，才会展示和命名，如：

  with tf.name_scope('weights'):  
      Weights = tf.Variable(tf.random_normal([in_size,out_size])) 


![](http://upload-images.jianshu.io/upload_images/1070582-99ca84e75e677afb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
结构图符号及意义：
![](http://upload-images.jianshu.io/upload_images/1070582-2822b92bbab742d5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

****
**变量：**
变量则可使用Tensorflow.histogram_summary()方法：

    tf.histogram_summary(layer_name+"/weights",Weights) #name命名，Weights赋值 

![](http://upload-images.jianshu.io/upload_images/1070582-bc758ad2b640bcc8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**常量：**
常量则可使用Tensorflow.scalar_summary()方法：

    tf.scalar_summary('loss',loss) #命名和赋值  

![](http://upload-images.jianshu.io/upload_images/1070582-81ab0aaf6737534e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**展示：**
最后需要整合和存储SummaryWriter：

    #合并到Summary中  
    merged = tf.merge_all_summaries()  
    #选定可视化存储目录  
    writer = tf.train.SummaryWriter("/目录",sess.graph)  

merged也是需要run的，因此还需要：

    result = sess.run(merged) #merged也是需要run的  
        writer.add_summary(result,i)  

**执行：**
运行后，会在相应的目录里生成一个文件，执行：

    tensorboard --logdir="/目录"  

会给出一段网址：
![](http://upload-images.jianshu.io/upload_images/1070582-1705f1e4c97413e3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
浏览器中打开这个网址即可，因为有兼容问题，firefox并不能很好的兼容，建议使用Chrome。

![](http://upload-images.jianshu.io/upload_images/1070582-9d96ce9238708f7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

常量在Event中，结构图在Graphs中，变量在最后两个Tag中。

附项目代码：
```
import tensorflow as tf  
import numpy as np  
  
def add_layer(inputs,in_size,out_size,n_layer,activation_function=None): #activation_function=None线性函数  
    layer_name="layer%s" % n_layer  
    with tf.name_scope(layer_name):  
        with tf.name_scope('weights'):  
            Weights = tf.Variable(tf.random_normal([in_size,out_size])) #Weight中都是随机变量  
            tf.histogram_summary(layer_name+"/weights",Weights) #可视化观看变量  
        with tf.name_scope('biases'):  
            biases = tf.Variable(tf.zeros([1,out_size])+0.1) #biases推荐初始值不为0  
            tf.histogram_summary(layer_name+"/biases",biases) #可视化观看变量  
        with tf.name_scope('Wx_plus_b'):  
            Wx_plus_b = tf.matmul(inputs,Weights)+biases #inputs*Weight+biases  
            tf.histogram_summary(layer_name+"/Wx_plus_b",Wx_plus_b) #可视化观看变量  
        if activation_function is None:  
            outputs = Wx_plus_b  
        else:  
            outputs = activation_function(Wx_plus_b)  
        tf.histogram_summary(layer_name+"/outputs",outputs) #可视化观看变量  
        return outputs  
  
#创建数据x_data，y_data  
x_data = np.linspace(-1,1,300)[:,np.newaxis] #[-1,1]区间，300个单位，np.newaxis增加维度  
noise = np.random.normal(0,0.05,x_data.shape) #噪点  
y_data = np.square(x_data)-0.5+noise  
  
with tf.name_scope('inputs'): #结构化  
    xs = tf.placeholder(tf.float32,[None,1],name='x_input')  
    ys = tf.placeholder(tf.float32,[None,1],name='y_input')  
  
#三层神经，输入层（1个神经元），隐藏层（10神经元），输出层（1个神经元）  
l1 = add_layer(xs,1,10,n_layer=1,activation_function=tf.nn.relu) #隐藏层  
prediction = add_layer(l1,10,1,n_layer=2,activation_function=None) #输出层  
  
#predition值与y_data差别  
with tf.name_scope('loss'):  
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys-prediction),reduction_indices=[1])) #square()平方,sum()求和,mean()平均值  
    tf.scalar_summary('loss',loss) #可视化观看常量  
with tf.name_scope('train'):  
    train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss) #0.1学习效率,minimize(loss)减小loss误差  
  
init = tf.initialize_all_variables()  
sess = tf.Session()  
#合并到Summary中  
merged = tf.merge_all_summaries()  
#选定可视化存储目录  
writer = tf.train.SummaryWriter("Desktop/",sess.graph)  
sess.run(init) #先执行init  
  
#训练1k次  
for i in range(1000):  
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})  
    if i%50==0:  
        result = sess.run(merged,feed_dict={xs:x_data,ys:y_data}) #merged也是需要run的  
        writer.add_summary(result,i) #result是summary类型的，需要放入writer中，i步数（x轴）
```
