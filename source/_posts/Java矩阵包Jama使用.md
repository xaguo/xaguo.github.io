---
title: Java矩阵包Jama使用
date: 2017-05-04 10:48:25
tags: [随笔,Java]
---

jama 中常用的矩阵计算，包括矩阵求逆、矩阵转置、矩阵加法、矩阵减法等基本的矩阵运算。
<!-- more -->

jama 介绍

[jama](http://math.nist.gov/javanumerics/jama/) 是一个基本的线性代数java包，它提供了实数非稀疏矩阵类，程序员可构造操控这些类。对于经常使用到矩阵运算的码农来说，即使不精通线性代数也没有关系，因为jama包提供的功能已经够用，调用方便，使用自然，而且易于理解。

jama 的 jar包下载地址：jama 如图所示

<center>
<img src="./jama.png" style="width=400px">
</center>

Demo代码

	```java
	package xa;
	import Jama.Matrix;  // 导入Jama包中的Matrix类 
	public class Demo {  
	   public static void main(String[] args) {  
	       System.out.println("调用jama包完成矩阵基本运算");  
	         
	       double [][] arrayA = {  
	               {-1, 1, 0},  
	               {-4, 3, 0},  
	               {1, 0, 2}  
	       };  
	       
	       double [][] arrayB={
		  {-1, 1, 0},  
	         {-4, 3, 0},  
	         {1, 0, 2}  
	       };
	        
	       //构造方法，传入一个 double[][] 数组
	       Matrix A = new Matrix(arrayA);
	       Matrix B= new Matrix(arrayB); 
	       //构造方法，Matrix(i,j) 传入一个矩阵的行列数
	       //例如 Matrix C=new Matrix(3,3);
	       
	       System.out.println("--------A的行数和列数---------");
	       int row=A.getRowDimension();
	       int col=A.getColumnDimension();
	       System.out.println(row+","+col);
	       
	       //print(i,j)方法：打印矩阵元素 i,j为矩阵的行数和列数
	       System.out.println("--------A---------");
	       A.print(A.getRowDimension(), A.getColumnDimension());
	       
	       //transpose()方法：打印矩阵元素
	       Matrix A_=A.transpose();
	       System.out.println("--------A的转置---------");
	       A_.print(A_.getRowDimension(), A_.getColumnDimension());
	       
	       //inverse()()方法：求矩阵的逆
	       System.out.println("--------A的行列式---------");  
	       double detNum = A.det();   // 行列式  
	       System.out.println(detNum+""); 
	       
	       //inverse()()方法：求矩阵的逆
	       Matrix A1=A.inverse();
	       System.out.println("--------A的逆---------");
	       A1.print(A1.getRowDimension(), A1.getColumnDimension());
	       
	      
	       //set方法：set(i, j, s)设置矩阵第 i 行第 j 列的元素值
	       B.set(2,2,2);
	       B.plus(B);
	       System.out.println("--------B---------");
	       B.print(B.getRowDimension(), B.getColumnDimension());
	       //get方法：gett(i, j, s)获取矩阵第 i 行第 j 列的元素值
	       double b= B.get(2, 2);
	       System.out.println("B(2,2)="+b);
	       
	       //矩阵减法
	       //C=A-B
	       System.out.println("--------A-B---------");
	       Matrix C=A.minus(B);
	       C.plus(B);
	       C.print(C.getRowDimension(), C.getColumnDimension());
	       
	       //矩阵加法
	       //C=A+B
	       System.out.println("--------A+B---------");
	       C=A.plus(B);
	       C.print(C.getRowDimension(), C.getColumnDimension());
	       
	       //数乘矩阵
	       double k=2;
	       //C=k*A
	       C=A.times(k);
	       System.out.println("--------k*A---------");
	       C.print(C.getRowDimension(), C.getColumnDimension());
	       
	       //矩阵乘法
	       //C=A*B
	       C=A.times(k);
	       System.out.println("--------A*B---------");
	       C.print(C.getRowDimension(), C.getColumnDimension());      
	   }   
	}
	```

[参考资料](http://www.cnblogs.com/ayan/archive/2012/04/17/2453471.html)