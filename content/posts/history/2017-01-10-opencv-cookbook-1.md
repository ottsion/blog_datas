---
date: 2017-01-10T23:59:59+00:00
draft: false
author: "caicai"
title: "opencv视觉编程学习第一部分代码"
categories: ["常用库"]
tags: ["opencv,图像处理"] 
---

---

星期二, 10. 一月 2017 01:44下午 

> 这部分主要是读取并显示图像，并对图像进行基本操作，比如画图和写字，同时添加图片点击事件的操作

---

## 代码学习

``` C++
#include<iostream>
#include<string>
#include "opencv2/core/core.hpp"
#include "opencv2/highgui/highgui.hpp"
#include <boost/concept_check.hpp>
using namespace std;

//图片地址
const string imageAddr0 = "/home/ottsion/Documents/2.jpg";
const string imageAddr1 = "/home/ottsion/Pictures/opencvImage/hua.jpg";

/**
 * 根据图片信息显示图片
 * @param windowName 显示窗口名称
 * @param image opcv的图片信息
 */
void showImage(string windowName, cv::Mat image);
/**
 * 根据地址，读取图片信息
 * @param imageAddr 图片地址
 * @param image opcv的图片信息
 */
void readImage(string imageAddr, cv::Mat& image);
/**
 * 将图片进行水平或者垂直翻转
 * @param image opcv的图片信息
 */
void convertImage(cv::Mat& image);
/**
 * 读取时将图片灰度化或者三通道读取
 * @param image opcv的图片信息
 */
void getImageByTypes(cv::Mat& image);
/**
 * 鼠标点击事件响应
 * @param event 整数，表示鼠标触发事件的类型
 * @param x  鼠标事件出发时位置（像素坐标显示）
 * @param y  鼠标事件出发时位置
 * @param flags  表示事件发生时按下了鼠标的哪个键
 * @param param  执行任意对象的指针，作为附加参数发送给函数
 */
void onMouse(int event, int x, int y, int flags, void* param);
/**
 * 注册回调函数，本函数中将名为windowName的图像窗口与函数onMouse建立关联，同时把显示图像的地址作为附加参数传给函数
 * @param windowName   图像窗口名称
 * @param image  图像信息
 */
void callBackFunc(string windowName, cv::Mat& image);
/**
 * 在图像上绘画和写入文本
 * @param windowName 图像窗口名称
 * @param image  图像信息
 */
void drawAndWriteOnImage(string windowName, cv::Mat image);


void showImage(string windowName, cv::Mat image)
{
   //先定义窗口
  cv::namedWindow(windowName);
  //显示图片
  cv::imshow(windowName, image);
}

void readImage(string imageAddr, cv::Mat& image)
{
   //读取图片
  image = cv::imread(imageAddr);
  if(image.empty())
  {
     std::cout<<"显示没有图片或者读取错误"<< image.rows <<endl;
  }
  showImage("origin image", image);
}
void convertImage(cv::Mat& image)
{
  //翻转图像
  cv::Mat result;
  //正数表示水平，0表示垂直，负数表示水平和垂直
  cv::flip(image, result, 1);
  //就地处理
  //cv::flip(image, image, 1);
  showImage("convert image", result);

  //保存图片
  cv::imwrite("output.png", result);
}
void getImageByTypes(cv::Mat& image)
{
  /**
   * 读入图片将其转为灰度图片
   * 这样生成的图像由无符号字节构成，opencv中用定义的常量CV_8U表示
   */
  image = cv::imread(imageAddr1, CV_LOAD_IMAGE_GRAYSCALE);
  showImage("gray image", image);
  
  /**
   * 读入图片将其转为三通道图片
   * 这样生成的图像用opencv中用定义的常量CV_8UC3表示
   * 如果输入图像为灰度图像，三通道数值相同
   * 如果读入时图像选择本身格式，只需将第二个参数设置为负值\
   * 如果读入图像由整形（CV_16U,CV_32S）构成，图像每个像素将除以256，以便在256级灰度中显示
   * 如果读入图像由浮点数构成，图像每个像素将设置为从0.0到1.0，超出范围的部分显示为白色（大于1.0），黑色（小于0.0）
   */
  image = cv::imread(imageAddr1, CV_LOAD_IMAGE_COLOR);
  showImage("color image", image); 
}
void onMouse(int event, int x, int y, int flags, void* param)
{
  cv::Mat *im =reinterpret_cast<cv::Mat*>(param);
  switch(event)
  {
    /*
     * CV_EVENT_MOUSEMOVE      =0,
     * CV_EVENT_LBUTTONDOWN    =1,
     * CV_EVENT_RBUTTONDOWN    =2,
     * CV_EVENT_MBUTTONDOWN    =3,
     * CV_EVENT_LBUTTONUP      =4,
     * CV_EVENT_RBUTTONUP      =5,
     * CV_EVENT_MBUTTONUP      =6,
     * CV_EVENT_LBUTTONDBLCLK  =7,
     * CV_EVENT_RBUTTONDBLCLK  =8,
     * CV_EVENT_MBUTTONDBLCLK  =9
     */
    case CV_EVENT_LBUTTONDOWN:
      //第五部分
      cout<<"at ("<< x <<", "<< y << ") value is : "<< static_cast<int>(im->at<uchar>(cv::Point(x,y))) <<endl;
      //第六部分
      //drawAndWriteOnImage("color image", *im);
  }
}
void callBackFunc(string windowName, cv::Mat& image)
{
  image = cv::imread(imageAddr1, CV_LOAD_IMAGE_GRAYSCALE);
  showImage(windowName, image);
  cv::setMouseCallback(windowName, onMouse, reinterpret_cast<void*>(&image));
}
void drawAndWriteOnImage(string windowName, cv::Mat image)
{
    image = cv::imread(imageAddr1, CV_LOAD_IMAGE_GRAYSCALE);
    
    /* 基本绘图形状函数有circle、ellipse、line、rectangle等等
     * CV_EXPORTS_W void circle(CV_IN_OUT Mat& img, Point center, int radius,
     *                 const Scalar& color, int thickness=1,
     *                 int lineType=8, int shift=0);
     */
    cv::circle(
      image,                //目标图像
      cv::Point(155,110),   //中心点坐标
      65,		    //半径
      255,		    //颜色
      3   		    //厚度
    );
    
    //写入文本信息
    cv::putText(
      image,                   //目标图像
      "This is a text test",   //文本内容
      cv::Point(40,200),       //文本位置
      cv::FONT_HERSHEY_PLAIN,  //字体类型
      2.0,                     //字体大小
      255,                     //字体颜色
      2                        //文本厚度
    );
    showImage(windowName, image);
}



int main(int argc, char **argv)
{
  cv::Mat image;
  cout<<"this image is "<<image.size<<endl;
  cout<<"this image is "<<image.rows<<" x "<<image.cols<<endl;
 
  //第一部分，显示图片
  readImage(imageAddr1,image);
  cv::waitKey(0);      //0表示永远等待，正数表示秒
  
  //第二部分，翻转图像
  convertImage(image);
  cv::waitKey(0);
  
  //第三部分，读入读出三色变换
  getImageByTypes(image);
  cv::waitKey(0);
  
  //第四部分，查看图像通道数
  std::cout<<"this image has "<<image.channels()<<" channels."<<endl;
  
  //第五部分，回调函数
  callBackFunc("origin image", image);
  cv::waitKey();
  
  //第六部分，图像上绘图和写文字
  drawAndWriteOnImage("draw image", image);
  cv::waitKey();
  
  return 0;
}

```



