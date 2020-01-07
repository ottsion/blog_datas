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

## 图像波浪化

```
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>

#include <math.h>

// remapping an image by creating wave effects
void wave(const cv::Mat &image, cv::Mat &result) {

	// the map functions
	cv::Mat srcX(image.rows,image.cols,CV_32F); // x-map
	cv::Mat srcY(image.rows,image.cols,CV_32F); // y-map

	// creating the mapping
	for (int i=0; i<image.rows; i++) {
		for (int j=0; j<image.cols; j++) {

			srcX.at<float>(i,j)= j;
			srcY.at<float>(i,j)= i+3*sin(j/6.0);

			// horizontal flipping
			// srcX.at<float>(i,j)= image.cols-j-1;
			// srcY.at<float>(i,j)= i;
		}
	}

	// applying the mapping
	cv::remap(image,  // source image
		      result, // destination image
			  srcX,   // x map
			  srcY,   // y map
			  cv::INTER_LINEAR); // interpolation method
}

int main()
{
	cv::Mat image= cv::imread("boldt.jpg",0);
	// image is resize for book printing
	cv::resize(image, image, cv::Size(), 0.6, 0.6);

	cv::namedWindow("Image");
	cv::imshow("Image",image);

	cv::Mat result;
	wave(image,result);

	cv::namedWindow("Remapped image");
	cv::imshow("Remapped image",result);

	cv::waitKey();
	return 0;
}
```
![](../../../pic/mybolg_pic/opencv/2017-01-10 20:23:54屏幕截图.png) 


