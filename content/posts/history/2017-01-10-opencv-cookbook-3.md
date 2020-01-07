---
date: 2017-01-10T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第三部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期二, 10. 一月 2017 03:12下午 

> 这部分主要是对兴趣区域的介绍,以及对Ma_的模板类介绍

---

## 如何实现
第一步便是定义ROI，定以后可以把ROI当做普通Mat实例进行操作，关键在于ROI本身就是Mat对象，它与它的父图指向同一个数据缓冲区，并且有一个头部信息表示ROI的坐标，可用下面的方法插入小标志：

```
cv::Mat imageROI(
	image,
	cv:;Rect(
		image.cols-logo.cols, image.rows-logo.rows,     //ROI的坐标
                logo.cols, logo.rows                                                    //ROI的大小
	 )
);
logo.copyTo(imageROI);
```

同样的可以用以下方法:

```
imageROI = image(
	cv::Range(image.rows-logo.rows, image.rows),
	cv::Range(image.cols-logo.cols,  image.cols)
);
```

要定义图像中一些行组成的ROI：

	cv::Mat imageRIO = image.rowRange(start, end);

要定义图像中一些列组成的ROI：

	cv::Mat imageRIO = image.colRange(start, end);

## 图像掩码

```
  cv::Mat imageROI = image(cv::Rect(0,0,logo.cols, logo.rows));
  cv::Mat mask(logo);                //把标志作为掩码，必须是灰度图像
  logo.copyTo(imageROI,mask);    //插入标志，只复制掩码不为0的位置
```

## Ma_的模板类
模板类可以直接申明类型，会多了一些使用方法，更加的简便，比如：

	cv::Mat_<uchar>  im2(image);
	im2(50, 100) = 0; //访问第50行100列的值

Mat与Mat_相互转换也是很简单的。

## 代码演示(RIO)


```
#include<iostream>
#include<opencv2/core/core.hpp>
#include<opencv2/highgui/highgui.hpp>

const std::string imageAddr = "/home/ottsion/Pictures/opencvImage/hua.jpg";
const std::string logoAddr = "/home/ottsion/Pictures/opencvImage/logo1.jpg";


void ROItest1(cv::Mat image, cv::Mat logo)
{
  //在图像左上角放置一个logo
  //cv::Mat imageROI = image(cv::Rect(0,0,logo.cols, logo.rows));
  cv::Mat imageRIO(image, cv::Rect(0, 0,logo.rows,logo.cols));
  logo.copyTo(imageRIO);
  cv::namedWindow("copt to part zone");
  cv::imshow("copt to part zone", image);
}

void ROItest2(cv::Mat image, cv::Mat logo)
{
  cv::Mat imageROI = image(cv::Rect(0,0,logo.cols, logo.rows));
  cv::Mat mask(logo);
  logo.copyTo(imageROI,mask);
  cv::namedWindow("use mask");
  cv::imshow("use mask", image);
}
int main()
{
  cv::Mat image = cv::imread(imageAddr, CV_LOAD_IMAGE_GRAYSCALE);
  cv::Mat logo = cv::imread(logoAddr, CV_LOAD_IMAGE_GRAYSCALE);
  
  //矩形区域选择复制
  ROItest1(image, logo);
  cv::waitKey(0);
  
  //使用图像掩码
  ROItest2(image, logo);
  cv::waitKey(0);
  return 0;
}
```

![效果图](../../../pic/mybolg_pic/opencv/2017-01-10 16:02:07屏幕截图.png  "xiaoguotu")

## 代码演示(salt)

```
#include<iostream>
#include<opencv2/core/core.hpp>
#include<opencv2/highgui/highgui.hpp>

void salt(cv::Mat image, int n)
{
  int i,j;
  for(int k=0;k<n;k++)
  {
    i = std::rand()%image.rows;
    j = std::rand()%image.cols;
    if(image.type()==CV_8UC1)
      image.at<uchar>(i,j) = 255;
    else if(image.type()==CV_8UC3)
    {
      //cv::Vec3b表示三个8位的数值
      image.at<cv::Vec3b>(i,j)[0] = 255;
      image.at<cv::Vec3b>(i,j)[1] = 255;
      image.at<cv::Vec3b>(i,j)[2] = 255;
    }
  }
}

int main()
{
  cv::Mat image = cv::imread("/home/ottsion/Pictures/opencvImage/hua.jpg");
  salt(image, 3000);
  
  cv::namedWindow("image after salt");
  cv::imshow("image after salt", image);
  cv::waitKey(0);
  
}
```

![效果图](../../../pic/mybolg_pic/opencv/2017-01-10 17:10:51屏幕截图.png  "xiaoguotu")

