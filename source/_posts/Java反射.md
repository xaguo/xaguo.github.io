---
title: Java反射
date: 2017-09-02 12:44:49
tags: [随笔,Java]
---
反射是 Java 开发中一个非常重要的概念，掌握了反射的知识，才能更好的学习 Java。
<!-- more -->

## Class类的使用 ##

在面向对象的世界里，万事万物皆对象。（java语言中，静态的成员、普通数据类型除外)

类是对象，类是java.lang.Class类的实例对象

	```java
	package clazz.xa;
	public class ClassDemo1 {
		 public static void main(String[] args) {
			//Foo的实例对象如何表示
			Foo foo1 = new Foo();//foo1就表示出来了.
			//Foo这个类 也是一个实例对象，Class类的实例对象,如何表示呢
			//任何一个类都是Class的实例对象，这个实例对象有三种表示方式
			
			//第一种表示方式--->实际在告诉我们任何一个类都有一个隐含的静态成员变量class
			Class c1 = Foo.class;
			
			//第二种表达方式  已经知道该类的对象通过getClass方法
			Class c2 = foo1.getClass();
			
			/*官网 c1 ,c2 表示了Foo类的类类型(class type)
			 * 万事万物皆对象，
			 * 类也是对象，是Class类的实例对象
			 * 这个对象我们称为该类的类类型
			 */
			
			//不管c1  or c2都代表了Foo类的类类型，一个类只可能是 Class 类的一个实例对象
			System.out.println(c1 == c2);
			
			//第三种表达方式
			Class c3 = null;
			try {
				c3 = Class.forName("clazz.xa.Foo");
			} catch (ClassNotFoundException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			System.out.println(c2==c3);
			
			//我们完全可以通过类的类类型创建该类的对象实例---->通过c1 or c2 or c3创建Foo的实例对象
			try {
				Foo foo = (Foo)c1.newInstance();//需要有无参数的构造方法
				foo.print();
			} catch (InstantiationException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} catch (IllegalAccessException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
	class Foo{
		void print(){
			System.out.println("foo");
		}
	}
	```

## Java 动态加载类 ##

Class.forName(“类的全称”)不仅表示了，类的类类型，还代表了动态加载类（在运行时刻加载）【区分编译、运行】

编译时刻加载类是静态加载类、运行时刻加载类是动态加载类

new 对象创建对象 是静态加载类,在编译时刻就需要加载所有的可能使用到的类，通过动态加载类可以解决该问题。

动态加载类： Class c = Class.forName(“类的全称”)

通过类类型，创建该类的对象： c.newInstance();

Office 动态加载例子


	```java
	public interface OfficeAble {
		public abstract void start();
	}
	```
-

	```java
	public class Word implements OfficeAble {
		public void start() {
			System.out.println("Word start...");
		}
	}
	```
-

	```java
	public class Excel implements OfficeAble {
		public void start() {
			System.out.println("Excel start...");
		}
	}
	```
-

	```java
	class Office {
		public static void main(String[] args) {
			try {
				//动态加载类，在运行时刻加载
				Class c =Class.forName(args[0]);
				//通过类类型，创建该类对象
				OfficeAble oa = (OfficeAble)c.newInstance();
				oa.start();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	}
	```
## Java获取方法信息 ##

基本的数据类型 void关键字 都存在类类型

	```java
	package clazz.xa;
	public class ClassDemo2 {
		public static void main(String[] args) {
			
			Class c1 = int.class;//int 的类类型
			Class c2 = String.class;//String类的类类型   String类字节码（自己发明的)
			Class c3 = double.class;
			Class c4 = Double.class;
			Class c5 = void.class;
			
			System.out.println(c1.getName());
			System.out.println(c2.getName());
			System.out.println(c2.getSimpleName());//不包含包名的类的名称
			System.out.println(c5.getName());
		}
	}
	```

-

	```java
	package clazz.xa;
	import java.lang.reflect.Constructor;
	import java.lang.reflect.Field;
	import java.lang.reflect.Method;
	public class ClassUtil {
		/**
		 * 打印类的信息，包括类的成员函数、成员变量(只获取成员函数)
		 * @param obj 该对象所属类的信息
		 */
		public static void printClassMethodMessage(Object obj){
			//要获取类的信息  首先要获取类的类类型
			Class c = obj.getClass();//传递的是哪个子类的对象  c就是该子类的类类型
			//获取类的名称
			System.out.println("类的名称是:"+c.getName());
			/*
			 * Method类，方法对象
			 * 一个成员方法就是一个Method对象
			 * getMethods()方法获取的是所有的public的函数，包括父类继承而来的
			 * getDeclaredMethods()获取的是所有该类自己声明的方法，不问访问权限
			 */
			Method[] ms = c.getMethods();//c.getDeclaredMethods()
			for(int i = 0; i < ms.length;i++){
				//得到方法的返回值类型的类类型
				Class returnType = ms[i].getReturnType();
				System.out.print(returnType.getName()+" ");
				//得到方法的名称
				System.out.print(ms[i].getName()+"(");
				//获取参数类型--->得到的是参数列表的类型的类类型
				Class[] paramTypes = ms[i].getParameterTypes();
				for (Class class1 : paramTypes) {
					System.out.print(class1.getName()+",");
				}
				System.out.println(")");
			}
		}
	}
	```

-

	```java
	package clazz.xa;
	/**
	 * 获取java 中方法的信息
	 */
	public class ClassDemo3 {
		public static void main(String[] args) {
			String s = "hello";
			ClassUtil.printClassMethodMessage(s);
			
		    Float n1 = 1.0f;
		    ClassUtil.printClassMethodMessage(n1);
		}
	}
	```

## Java获取成员变量构造函数的信息 ##

	```java
	/**
	    * 获取成员变量的信息
	    * @param obj
	    */
	public static void printFieldMessage(Object obj) {
		Class c = obj.getClass();
		/*
		 * 成员变量也是对象
		 * java.lang.reflect.Field
		 * Field类封装了关于成员变量的操作
		 * getFields()方法获取的是所有的public的成员变量的信息
		 * getDeclaredFields获取的是该类自己声明的成员变量的信息
		 */
		//Field[] fs = c.getFields();
		Field[] fs = c.getDeclaredFields();
		for (Field field : fs) {
			//得到成员变量的类型的类类型
			Class fieldType = field.getType();
			String typeName = fieldType.getName();
			//得到成员变量的名称
			String fieldName = field.getName();
			System.out.println(typeName+" "+fieldName);
		}
	}
	/**
	 * 打印对象的构造函数的信息
	 * @param obj
	 */
	public static void printConMessage(Object obj){
		Class c = obj.getClass();
		/*
		 * 构造函数也是对象
		 * java.lang. Constructor中封装了构造函数的信息
		 * getConstructors获取所有的public的构造函数
		 * getDeclaredConstructors得到所有的构造函数
		 */
		//Constructor[] cs = c.getConstructors();
		Constructor[] cs = c.getDeclaredConstructors();
		for (Constructor constructor : cs) {
			System.out.print(constructor.getName()+"(");
			//获取构造函数的参数列表--->得到的是参数列表的类类型
			Class[] paramTypes = constructor.getParameterTypes();
			for (Class class1 : paramTypes) {
				System.out.print(class1.getName()+",");
			}
			System.out.println(")");
		}
	}
	```

## Java 方法反射的基本操作 ##

```java

```

- 方法的反射

**如何获取某个方法**:方法名称和方法的参数列表才能唯一确定某个方法

**方法反射操作**:method.invoke(对象，参数列表)

	```java
	package method.xa;
	import java.lang.reflect.Method;
	public class MethodDemo1 {
		public static void main(String[] args) {
		   //要获取print(int ,int )方法 
			//1.要获取一个方法就是获取类的信息，获取类的信息首先要获取类的类类型
			A a1 = new A();
			Class c = a1.getClass();
			/*
			 * 2.获取方法 名称和参数列表来决定  
			 * getMethod获取的是public的方法
			 * getDelcaredMethod自己声明的方法
			 */
		    try {
				//Method m =  c.getMethod("print", new Class[]{int.class,int.class});
		    	Method m = c.getMethod("print", int.class,int.class);
		    	
		    	//方法的反射操作  
		    	//a1.print(10, 20);方法的反射操作是用m对象来进行方法调用 和a1.print调用的效果完全相同
		        //方法如果没有返回值返回null,有返回值返回具体的返回值
		    	//Object o = m.invoke(a1,new Object[]{10,20});
		    	Object o = m.invoke(a1, 10,20);
		    	System.out.println("==================");
		    	//获取方法print(String,String)
	             Method m1 = c.getMethod("print",String.class,String.class);
	             //用方法进行反射操作
	             //a1.print("hello", "WORLD");
	             o = m1.invoke(a1, "hello","WORLD");
	             System.out.println("===================");
	           //  Method m2 = c.getMethod("print", new Class[]{});
	                Method m2 = c.getMethod("print");
	               // m2.invoke(a1, new Object[]{});
	                m2.invoke(a1);
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} 
	     
		}
	}
	class A{
		public void print(){
			System.out.println("helloworld");
		}
		public void print(int a,int b){
			System.out.println(a+b);
		}
		public void print(String a,String b){
			System.out.println(a.toUpperCase()+","+b.toLowerCase());
		}
	}
	```

## Java 反射了解集合泛型 ##


	```java
	package method.xa;
	import java.lang.reflect.Method;
	import java.util.ArrayList;
	public class MethodDemo4 {
		public static void main(String[] args) {
			ArrayList list = new ArrayList();
			
			ArrayList<String> list1 = new ArrayList<String>();
			list1.add("hello");
			//list1.add(20);错误的
			Class c1 = list.getClass();
			Class c2 = list1.getClass();
			System.out.println(c1 == c2);
			//反射的操作都是编译之后的操作
			
			/*
			 * c1==c2结果返回true说明编译之后集合的泛型是去泛型化的
			 * Java中集合的泛型，是防止错误输入的，只在编译阶段有效，
			 * 绕过编译就无效了
			 * 验证：我们可以通过方法的反射来操作，绕过编译
			 */
			try {
				Method m = c2.getMethod("add", Object.class);
				m.invoke(list1, 20);//绕过编译操作就绕过了泛型
				System.out.println(list1.size());
				System.out.println(list1);
				/*for (String string : list1) {
					System.out.println(string);
				}*///现在不能这样遍历
			} catch (Exception e) {
			  e.printStackTrace();
			}
		}
	}
	```
