---
date: 2017-01-05T23:59:59+00:00
draft: false
author: "caicai"
title: "Eigen矩阵库——代码演示"
categories: ["常用库"]
tags: ["eigen"]
---

星期四, 05. 一月 2017 02:13下午 

---

> 有关eigen库的一些基本使用方法

---

## 矩阵、向量初始化

```
#include <iostream>
#include "Eigen/Dense"
using namespace Eigen;
int main()
{
    MatrixXf m1(3,4);   //动态矩阵，建立3行4列。
    MatrixXf m2(4,3);   //4行3列，依此类推。
    MatrixXf m3(3,3);

    Vector3f v1;        //若是静态数组，则不用指定行或者列
    /* 初始化 */
    Matrix3d m = Matrix3d::Random();
    m1 = MatrixXf::Zero(3,4);       //用0矩阵初始化,要指定行列数
    m2 = MatrixXf::Zero(4,3);
    m3 = MatrixXf::Identity(3,3);   //用单位矩阵初始化
    v1 = Vector3f::Zero();          //同理，若是静态的，不用指定行列数

    m1 << 1,0,0,1,      //也可以以这种方式初始化
        1,5,0,1,
        0,0,9,1;
    m2 << 1,0,0,
        0,4,0,
        0,0,7,
        1,1,1;
    //向量初始化，与矩阵类似
    Vector3d v3(1,2,3);
    VectorXf vx(30);
}
```

## C++数组和矩阵转换

使用Map函数，可以实现Eigen的矩阵和c++中的数组直接转换,语法如下：

```
//@param MatrixType 矩阵类型
//@param MapOptions 可选参数，指的是指针是否对齐，Aligned, or Unaligned. The default is Unaligned.
//@param StrideType 可选参数，步长
/*
    Map<typename MatrixType,
        int MapOptions,
        typename StrideType>
*/
    int i;
    //数组转矩阵
    double *aMat = new double[20];
    for(i =0;i<20;i++)
    {
        aMat[i] = rand()%11;
    }
    //静态矩阵，编译时确定维数 Matrix<double,4,5> 
    Eigen:Map<Matrix<double,4,5> > staMat(aMat);


    //输出
    for (int i = 0; i < staMat.size(); i++)
        std::cout << *(staMat.data() + i) << " ";
    std::cout << std::endl << std::endl;


    //动态矩阵，运行时确定 MatrixXd
    Map<MatrixXd> dymMat(aMat,4,5);


    //输出，应该和上面一致
    for (int i = 0; i < dymMat.size(); i++)
        std::cout << *(dymMat.data() + i) << " ";
    std::cout << std::endl << std::endl;

    //Matrix中的数据存在一维数组中，默认是行优先的格式，即一行行的存
    //data()返回Matrix中的指针
    dymMat.data();
```

## 矩阵基础操作

eigen重载了基础的+ - * / += -= = /= 可以表示标量和矩阵或者矩阵和矩阵

```
#include <iostream>
#include <Eigen/Dense>
using namespace Eigen;
int main()
{
    //单个取值，单个赋值
    double value00 = staMat(0,0);
    double value10 = staMat(1,0);
    staMat(0,0) = 100;
    std::cout << value00 <<value10<<std::endl;
    std::cout <<staMat<<std::endl<<std::endl;
    //加减乘除示例 Matrix2d 等同于 Matrix<double,2,2>
    Matrix2d a;
     a << 1, 2,
     3, 4;
    MatrixXd b(2,2);
     b << 2, 3,
     1, 4;

    Matrix2d c = a + b;
    std::cout<< c<<std::endl<<std::endl;

    c = a - b;
    std::cout<<c<<std::endl<<std::endl;

    c = a * 2;
    std::cout<<c<<std::endl<<std::endl;

    c = 2.5 * a;
    std::cout<<c<<std::endl<<std::endl;

    c = a / 2;
    std::cout<<c<<std::endl<<std::endl;

    c = a * b;
    std::cout<<c<<std::endl<<std::endl;
```

## 点积和叉积

```
#include <iostream>
#include <Eigen/Dense>
using namespace Eigen;
using namespace std;
int main()
{
    //点积、叉积(针对向量的)
    Vector3d v(1,2,3);
    Vector3d w(0,1,2);
    std::cout<<v.dot(w)<<std::endl<<std::endl;
    std::cout<<w.cross(v)<<std::endl<<std::endl;
}
*/
```

## 转置、伴随、行列式、逆矩阵

小矩阵（4 * 4及以下）eigen会自动优化，默认采用LU分解，效率不高

```
#include <iostream>
#include <Eigen/Dense>
using namespace std;
using namespace Eigen;
int main()
{
    Matrix2d c;
     c << 1, 2,
     3, 4;
    //转置、伴随
    std::cout<<c<<std::endl<<std::endl;
    std::cout<<"转置\n"<<c.transpose()<<std::endl<<std::endl;
    std::cout<<"伴随\n"<<c.adjoint()<<std::endl<<std::endl;
    //逆矩阵、行列式
    std::cout << "行列式： " << c.determinant() << std::endl;
    std::cout << "逆矩阵\n" << c.inverse() << std::endl;
}
```

## 计算特征值和特征向量

```
#include <iostream>
#include <Eigen/Dense>
using namespace std;
using namespace Eigen;
int main()
{
    //特征向量、特征值
    std::cout << "Here is the matrix A:\n" << a << std::endl;
    SelfAdjointEigenSolver<Matrix2d> eigensolver(a);
    if (eigensolver.info() != Success) abort();
     std::cout << "特征值:\n" << eigensolver.eigenvalues() << std::endl;
     std::cout << "Here's a matrix whose columns are eigenvectors of A \n"
     << "corresponding to these eigenvalues:\n"
     << eigensolver.eigenvectors() << std::endl;
}
```

## 解线性方程

```
#include <iostream>
#include <Eigen/Dense>
using namespace std;
using namespace Eigen;
int main()
{
    //线性方程求解 Ax =B;
    Matrix4d A;
    A << 2,-1,-1,1,
        1,1,-2,1,
        4,-6,2,-2,
        3,6,-9,7;

    Vector4d B(2,4,4,9);

    Vector4d x = A.colPivHouseholderQr().solve(B);
    Vector4d x2 = A.llt().solve(B);
    Vector4d x3 = A.ldlt().solve(B);    


    std::cout << "The solution is:\n" << x <<"\n\n"<<x2<<"\n\n"<<x3 <<std::endl;
}
```

除了`colPivHouseholderQr`、`LLT`、`LDLT`，还有以下的函数可以求解线性方程组，请注意精度和速度： 解小矩阵（4*4）基本没有速度差别

## 最小二乘求解

最小二乘求解有两种方式，jacobiSvd或者colPivHouseholderQr，4*4以下的小矩阵速度没有区别，jacobiSvd可能更快，大矩阵最好用colPivHouseholderQr

```
#include <iostream>
#include <Eigen/Dense>
using namespace std;
using namespace Eigen;
int main()
{
    MatrixXf A1 = MatrixXf::Random(3, 2);
    std::cout << "Here is the matrix A:\n" << A1 << std::endl;
    VectorXf b1 = VectorXf::Random(3);
    std::cout << "Here is the right hand side b:\n" << b1 << std::endl;
    //jacobiSvd 方式:Slow (but fast for small matrices)
    std::cout << "The least-squares solution is:\n"
    << A1.jacobiSvd(ComputeThinU | ComputeThinV).solve(b1) << std::endl;
    //colPivHouseholderQr方法:fast
    std::cout << "The least-squares solution is:\n"
    << A1.colPivHouseholderQr().solve(b1) << std::endl;
}
```

## 稀疏矩阵

稀疏矩阵的头文件包括：

```
  #include <Eigen/SparseCore>
  #include <Eigen/SparseCholesky>
  #include <Eigen/IterativeLinearSolvers>
  #include <Eigen/Sparse>
```
  
初始化有两种方式： 
 1. 使用三元组插入

```
typedef Eigen::Triplet<double> T;
std::vector<T> tripletList;
triplets.reserve(estimation_of_entries); //estimation_of_entries是预估的条目
for(...)
{
    tripletList.push_back(T(i,j,v_ij));//第 i,j个有值的位置的值
}
SparseMatrixType mat(rows,cols);
mat.setFromTriplets(tripletList.begin(), tripletList.end());
// mat is ready to go!
```

 2. 直接将已知的非0值插入

```
SparseMatrix<double> mat(rows,cols);
mat.reserve(VectorXi::Constant(cols,6));
for(...)
{
    // i,j 个非零值 v_ij != 0
    mat.insert(i,j) = v_ij;
}
mat.makeCompressed(); // optional
```

稀疏矩阵支持大部分一元和二元运算:

```
sm1.real() sm1.imag() -sm1 0.5*sm1 
sm1+sm2 sm1-sm2 sm1.cwiseProduct(sm2) 
```

二元运算中，稀疏矩阵和普通矩阵可以混合使用

//dm表示普通矩阵 
`dm2 = sm1 + dm1; `
也支持计算转置矩阵和伴随矩阵


