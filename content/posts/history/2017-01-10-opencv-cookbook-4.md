---
date: 2017-01-10T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第四部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期二, 10. 一月 2017 05:48下午 


> 这部分主要是操作像素

---


## Ma_的模板类
模板类可以直接申明类型，会多了一些使用方法，更加的简便，比如：

	cv::Mat_<uchar>  im2(image);
	im2(50, 100) = 0; //访问第50行100列的值

Mat与Mat_相互转换也是很简单的。

一般情况下Mat类读取像素：

	image.at<uchar>(i,j) = 255;

## 指针读取

除了一般的

```
    uchar* data = image.ptr<uchar>(i);
    for(int j=0;j<nc;j++)
    {
      data[j] = data[j]/div*div+div/2;
    }
```
我们还可以用以下语句执行：

```
*data = *data/div*div + div/2 ; data++
```

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

## 代码演示减色函数

```
void colorReduce(cv::Mat image,int div)
{
  int nl = image.rows;
  int nc = image.channels()*image.cols;
  for(int i=0;i<nl;i++)
  {
    uchar* data = image.ptr<uchar>(i);
    for(int j=0;j<nc;j++)
    {
      data[j] = data[j]/div*div+div/2;
    }
  }
  cv::namedWindow("image after colorReduce");
  cv::imshow("image after colorReduce", image);
}
```

![效果图](../../../pic/mybolg_pic/opencv/2017-01-10 17:46:00屏幕截图.png "xiaoguotu")

## 更有效的扫描方式：

```
void colorReduce6(cv::Mat image, int div=64) {

      int nl= image.rows; // number of lines
      int nc= image.cols * image.channels(); // total number of elements per line

      if (image.isContinuous())  {
          // then no padded pixels
          std::cout << "Image is continuous" << std::endl;
          nc= nc*nl;
          nl= 1;  // it is now a 1D array
       }

      int n= static_cast<int>(log(static_cast<double>(div))/log(2.0) + 0.5);
      // mask used to round the pixel value
      uchar mask= 0xFF<<n; // e.g. for div=16, mask= 0xF0

      for (int j=0; j<nl; j++) {

          uchar* data= image.ptr<uchar>(j);

          for (int i=0; i<nc; i++) {

            // process each pixel ---------------------

            *data &= mask;
            *data++ += div>>1;

            // end of pixel processing ----------------

            } // end of line
      }
}
```

```
void colorReduce7(cv::Mat image, int div=64) {

      if (image.isContinuous()) {
        // no padded pixels
        image.reshape(1,   // new number of channels
                      1) ; // new number of rows
      }
      // number of columns set accordingly

      int nl= image.rows; // number of lines
      int nc= image.cols*image.channels() ; // number of columns

      int n= static_cast<int>(log(static_cast<double>(div))/log(2.0) + 0.5);
      // mask used to round the pixel value
      uchar mask= 0xFF<<n; // e.g. for div=16, mask= 0xF0

      for (int j=0; j<nl; j++) {

          uchar* data= image.ptr<uchar>(j);

          for (int i=0; i<nc; i++) {

            // process each pixel ---------------------

            *data &= mask;
            *data++ += div>>1;

            // end of pixel processing ----------------

            } // end of line
      }
}

```


## 迭代器的遍历
共有两种方式：

	  cv::Mat_<cv::vec3b>::iterator it = image.begin<cv::Vec3b>();
	  
	  cv::MatIterator_<cv::Vec3b> it;




```
void colorReduceByIt(cv::Mat image, int div)
{
  cv::Mat_<cv::vec3b>::iterator it = image.begin<cv::Vec3b>();
  cv::Mat_<cv::Vec3b>::iterator itend = image.end<cv::Vec3b>();
  for(; it!=itend; it++)
  {
    (*it)[0] = (*it)[0]/div*div+div/2;
    (*it)[1] = (*it)[1]/div*div+div/2;
    (*it)[2] = (*it)[2]/div*div+div/2;
  }
  cv::namedWindow("image after colorReduceByIt");
  cv::imshow("image after colorReduceByIt", image);
}
```


## 锐化效果

```
void sharpen(cv::Mat &image, cv::Mat &result)
{
  result.create(image.size(), image.type());
  int nchannels = image.channels();
  
  for(int j=0;j<image.rows-1;j++)
  {
    const uchar* previous = image.ptr<const uchar>(j-1);
    const uchar* current = image.ptr<const uchar>(j);
    const uchar* next = image.ptr<const uchar>(j+1);
    
    uchar *output = result.ptr<uchar>(j);
    
    for(int i=nchannels; i<(image.cols-1)*nchannels; i++)
    {
      *output ++= cv::saturate_cast<uchar>(
	5*current[i] - current[i-nchannels]-current[i+nchannels]-previous[i]-next[i]
      );
    }
  }
  
  result.row(0).setTo(cv::Scalar(0));
  result.row(result.rows-1).setTo(cv::Scalar(0));
  result.col(0).setTo(cv::Scalar(0));
  result.col(result.cols-1).setTo(cv::Scalar(0));
  cv::namedWindow("image after sharpen");
  cv::imshow("image after sharpen", image);
}
```

![效果图](../../../pic/mybolg_pic/opencv/2017-01-10 19:29:11屏幕截图.png "xiaoguotu")

