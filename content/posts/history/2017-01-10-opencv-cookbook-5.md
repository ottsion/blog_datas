---
date: 2017-01-10T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第五部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期二, 10. 一月 2017 07:57下午 


> 这部分主要是图像加减及各种运算

---


## 两图相加

主要有以下方法：

```
  //c[i] = a[i]+b[i]
  cv::add(imageA, imageB, resultC);
  //c[i] = a[i]+k
  cv::add(imageA, cv::Scalar(k), resultC);
  //c[i] = k1*a[i]+k2*b[i]+k3
  cv::addWeighted(imageA, k1, imageB, k2, k3, resultC);
  //c[i] = k*a[i]+b[i]
  cv::scaleAdd(imageA, k, imageB, resultC);
```

甚至于掩码：

```
  //if (mask[i]) c[i] = a[i] + b[i]
  cv::add(imageA, imageB, resultC, mask);
```

但有一点，所有这种处理必须进行`cv::Saturate_cast<>()`函数转换结果，以确保结果在0-256之间，避免溢出。

## 其他运算

以下括号内省略

###加减乘除

```
  cv::subtract();
  cv::absdiff();
  cv::multiply();
  cv::divide();
```

### 位运算符
```
  cv::bitwise_and();
  cv::bitwise_or();
  cv::bitwise_xor();
  cv::bitwise_not();
```

### 单输入运算

```
cv::sqrt();
cv::pow();
cv::abs();
cv::cuberoot();
cv::exp();
cv::log();
```

## 代码演示

```
#include <vector>
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>

int main()
{
	cv::Mat image1;
	cv::Mat image2;

	image1= cv::imread("boldt.jpg");
	image2= cv::imread("rain.jpg");
	if (!image1.data)
		return 0; 
	if (!image2.data)
		return 0; 

	// images ares resize for book printing
	cv::resize(image1, image1, cv::Size(), 0.6, 0.6);
	cv::resize(image2, image2, cv::Size(), 0.6, 0.6);

	cv::namedWindow("Image 1");
	cv::imshow("Image 1",image1);
	cv::namedWindow("Image 2");
	cv::imshow("Image 2",image2);

	cv::Mat result;
	// add two images
	cv::addWeighted(image1,0.7,image2,0.9,0.,result);

	cv::namedWindow("result");
	cv::imshow("result",result);

	// using overloaded operator
	result= 0.7*image1+0.9*image2;

	cv::namedWindow("result with operators");
	cv::imshow("result with operators",result);

	image2= cv::imread("rain.jpg",0);
	// images ares resize for book printing
	cv::resize(image2, image2, cv::Size(), 0.6, 0.6);

	// create vector of 3 images
	std::vector<cv::Mat> planes;
	// split 1 3-channel image into 3 1-channel images
	cv::split(image1,planes);
	// add to blue channel
	planes[0]+= image2;
	// merge the 3 1-channel images into 1 3-channel image
	cv::merge(planes,result);

	cv::namedWindow("Result on blue channel");
	cv::imshow("Result on blue channel",result);

	cv::waitKey();

	return 0;
}
```