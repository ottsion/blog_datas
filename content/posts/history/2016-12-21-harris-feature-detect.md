---
date: 2016-12-21T23:59:59+00:00
draft: false
author: "caicai"
title: "Harris角点检测基础原理和效果"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---


> 学习opencv系列， feature2d 模块. 2D特征框架框架下面，Harris 角点检测子部分

## 图像特征及类型

在计算机视觉中，我们通常需要寻找两张图上的匹配关键点。为什么？因为一旦我们知道了两张图是相关联的，我 们就可以使用 *both 图像来提取它们中的信息。 **匹配关键点** 是指在场景中可以很容易识别出来的特性 . 这些特性就是这里所说的特征 。因此,特征应该有什么样的特性呢?应该具有可识别的 **独一无二** 性。

图像特征类型:

- 边缘
- 角点 (感兴趣关键点)
- 斑点(Blobs) (感兴趣区域)

这里**Harris 角点检测子**计算的是角点特征。

图像特征为什么可以使用角点，因为角点是两个边缘的连接点，它代表了两个边缘变化的方向上的点。图像梯度有很高的变化。这种变化是可以用来帮助检测角点的。

## 特征检测原理

由于角点代表了图像像素梯度变化，我们将寻找这个”变化”。
考虑到一个灰度图像I.划动窗口w(x,y)(with displacements u 在x方向和 v 方向)I计算像素灰度变化。

![公式](http://i.imgur.com/fAVd2FN.png)

其中:

1. w(x,y) is the window at position (x,y)
2. I(x,y) is the intensity at (x,y)
3. I(x+u,y+v) is the intensity at the moved window (x+u,y+v)

为了寻找带角点的窗口，我们搜索像素灰度变化较大的窗口。于是, 我们期望最大化以下式子:

![公式2](http://i.imgur.com/DY8qTHR.png)

使用 泰勒(Taylor)展开式:

![公式3](http://i.imgur.com/BN6HoH6.png)

式子可以展开为:

![公式4](http://i.imgur.com/oeXrVia.png)

一个举证表达式可以写为:

![公式5](http://i.imgur.com/02o30xI.png)

表示为:

![公式6](http://i.imgur.com/TWVrGlv.png)

因此我们有等式:

![公式7](http://i.imgur.com/RoMrrNw.png)

每个窗口中计算得到一个值。这个值决定了这个窗口中是否包含了角点:

![公式8](http://i.imgur.com/NY9zjwF.png)

其中:

1. det(M) = \lambda_{1}\lambda_{2}
2. trace(M) = \lambda_{1}+\lambda_{2}

一个窗口，它的分数 R 大于一个特定值，这个窗口就可以被认为是"角点"

## 效果演示代码


	#include "opencv2/highgui.hpp"
	#include "opencv2/imgproc/imgproc.hpp"
	#include <iostream>
	#include <stdio.h>
	#include <stdlib.h>
	
	using namespace std;
	using namespace cv;
	
	Mat src, src_gray;
	int thresh = 200;
	int max_thresh = 255;
	
	char* source_window = "Source image";
	char* corners_window = "Corners detected";
	
	void cornerHarris_demo( int , void* );
	
	int main(int argc, char** argv)
	{
		argv[1] = "C:/Users/SUNFC/Pictures/22.jpg";
		/// Load source image and convert it to gray
		src = imread( argv[1], 1);
		cvtColor( src, src_gray, CV_BGR2GRAY);
	
		/// Create a window and a trackbar
		namedWindow( source_window, CV_WINDOW_AUTOSIZE);
		createTrackbar("Threshold:", source_window, &thresh, max_thresh, cornerHarris_demo);
		imshow( source_window,src);
		cornerHarris_demo(0,0);
	
		waitKey(0);
		return 0;
	
	}
	
	void cornerHarris_demo(int, void*)
	{
		Mat dst, dst_norm, dst_norm_scaled;
		dst = Mat::zeros(src.size(), CV_32FC1);
	
		/// Detector parameters
		int blockSize = 2;
		int apertureSize = 3;
		double k = 0.04;
	
		/// Detecting corners
		cornerHarris( src_gray, dst, blockSize, apertureSize, k , BORDER_DEFAULT);
	
		/// Normalizing
		normalize(dst, dst_norm, 0, 255, NORM_MINMAX, CV_32FC1, Mat());
		convertScaleAbs(dst_norm, dst_norm_scaled);
	
		/// Drawing a circle around corners
		for (int j = 0; j < dst_norm.rows; j++)
		{
			for (int i = 0; i < dst_norm.cols; i++)
			{
				if ((int)dst_norm.at<float>(j, i) > thresh)
				{
					circle(dst_norm_scaled, Point(i, j), 5, Scalar(0), 2, 8, 0);
				}
			}
		}
		/// Showing the result
		namedWindow(corners_window, CV_WINDOW_AUTOSIZE);
		imshow(corners_window, dst_norm_scaled);
	}

结果：

![结果1](http://i.imgur.com/kqlq0CR.png)

![结果2](http://i.imgur.com/LKGaXrj.png)

后续慢慢加入详解....

学习来源：

opencv:[链接地址](http://www.opencv.org.cn/opencvdoc/2.3.2/html/doc/tutorials/features2d/trackingmotion/harris_detector/harris_detector.html#harris-detector) 

