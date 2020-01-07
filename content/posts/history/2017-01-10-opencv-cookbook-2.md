---
date: 2017-01-10T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第二部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期二, 10. 一月 2017 01:44下午 

> 这部分主要是对Mat的介绍，以及各种用法，包括读取和拷贝，注意深浅拷贝情况

---

## 代码学习

``` C++
#include<iostream>
#include<string>
#include<opencv2/core/core.hpp>
#include<opencv2/highgui/highgui.hpp>
const std::string imageAddr = "/home/ottsion/Pictures/opencvImage/hua.jpg";


//测试函数，创建一个图像
cv::Mat function(){
  cv::Mat ima(500, 500, CV_8U, 50);
  return ima;
}

int main()
{
  cv::namedWindow("Image 1");
  cv::namedWindow("Image 2");
  cv::namedWindow("Image 3");
  cv::namedWindow("Image 4");
  cv::namedWindow("Image 5");
  cv::namedWindow("Image");
  
  //创建一个240*320的新图像
  cv::Mat image1(240, 320, CV_8U, 100);
  cv::imshow("Image", image1);
  cv::waitKey(0);
  
  //重新分配一个新的图像
  image1.create(200, 200, CV_8U);
  image1 = 200;
  
  cv::imshow("Image", image1);
  cv::waitKey(0);
  
  //创建一个红色的图像，通道依次为BGR
  cv::Mat image2(240, 320, CV_8UC3, cv::Scalar(0, 0, 255));
  //或者
  //cv::Mat image2(cv::Size(320, 240), CV_8UC3);
  //image2 =  cv::Scalar(0, 0, 255)；
  cv::imshow("Image", image2);
  cv::waitKey(0);
  
  //读入一个图像
  cv::Mat image3 = cv::imread(imageAddr);
  
  //所有图像指向同一个数据块
  cv::Mat image4(image3);
  image1 = image3;
  
  //这些图像都是原图像的副本
  image3.copyTo(image2);
  cv::Mat image5 = image3.clone();
  
  //转换图像用来测试
  cv::flip(image3, image3, 1);
  
  //检查哪些图像在这个过程中受损
  cv::imshow("Image 3", image3);
  cv::imshow("Image 1", image1); 
  cv::imshow("Image 2", image2);
  cv::imshow("Image 4", image4);
  cv::imshow("Image 5", image5);
  cv::waitKey(0);
  
  //从函数中获取一个灰度图像
  cv::Mat gray = function();
  cv::imshow("Image", gray);
  cv::waitKey(0);
  
  //作为灰度图像读入
  image1 = cv::imread(imageAddr, CV_LOAD_IMAGE_GRAYSCALE);
  image1.convertTo(image2, CV_32F, 1/255.0, 0.0);
  cv::imshow("Image" ,image2);
  cv::waitKey(0);
  
  return 0;
}


```



