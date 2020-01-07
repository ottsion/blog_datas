---
date: 2017-01-11T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第七部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期三, 11. 一月 2017 11:47上午 



> 这部分主要是用类处理彩色图像

---

## 像素与标量之间的差

```
#include "ColorDetector.h"
cv::Mat ColorDetector::process(const cv::Mat &image){
      cv::Mat output;
      cv::absdiff(image, cv::Scalar(target), output);
      std::vector<cv::Mat> images;
      cv::split(output, images);
      output=image[0]+images[1]+images[2];
      cv::threshold(
	output,
	output,
	maxDist,
	255,
	cv::THRESH_BINARY_INV
      );
      return output;
}
```

运行：

```
int main()
{
  ColorDetector cdetect ;
  cv::Mat image = cv::imread("/home/ottsion/Pictures/opencvImage/hua.jpg");
  if(image.empty())
    return 0;
  cdetect.setTargetColor(20,100,130);
  cv::namedWindow("image");
  cv::imshow("image",cdetect.process(image));
  cv::waitKey(0);
 
  return 0;
}
```

得出结果是：

![](../../../pic/mybolg_pic/opencv/2017-01-11 11:50:39屏幕截图.png) 

## 转换颜色空间

### CIE L*a*b色彩空间

L表示每个像素的亮度，ab表示颜色信息。

```
cv::Mat ColorDetector::process1(const cv::Mat &image){
  
  result.create(image.rows, image.cols, CV_8U);
  cv::cvtColor(image, converted, CV_BRG2LAB);
  cv::Mat_<cv::Vec3b>::iterator it = converted.begin<cv::Vec3b>();
  cv::Mat_<cv::Vec3b>::iterator itend = converted.end<cv::Vec3b>();
  cv::Mat_<uchar>::iterator itout = result.begin<cv::Vec3b>();
  for(; it!=itend; ++it, ++itout){
    ...
  }
}
```

### YCrCb空间

jpeg压缩使用的色彩空间
    
### CIE L*u*v空间 

是一种感知均匀的色彩空间

### CIE XYZ空间

标准色彩空间，RGB与XYZ之间的转换时线性的，Y通道对应灰度版本。

把彩色图转换为灰度图可以：
`cv::cvtColor(color, gray, CV::BGR2Gray)`

### 用色调饱和度亮度表示颜色

```
void test2(cv::Mat image){
  std::vector<cv::Mat> nchannels;
  cv::split(image, nchannels);
  //nchannels[0]   色调
  //nchannels[1]   饱和度
  //nchannels[2]   亮度
  cv::namedWindow("色调");
  cv::namedWindow("饱和度");
  cv::namedWindow("亮度");
  cv::imshow("色调", nchannels[0]);
  cv::imshow("饱和度", nchannels[1]);
  cv::imshow("亮度", nchannels[2]);
}
```

![](../../../pic/mybolg_pic/opencv/2017-01-11 12:26:58屏幕截图.png) 