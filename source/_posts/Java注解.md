---
title: Java注解
date: 2017-09-01 12:36:56
tags: [随笔,Java]
---
Java 提供了一种源程序中的元素关联任何信息和任何元数据的途径的方法。
<!-- more -->

## 注解分类

- 按运行时机制

1. 源码注解：注解只在源码中存在，编译成 .class 文件就不存在了。“SOURSE”
2. 编译时注解：注解在源码和 .class 中都存在（@Override/@Deprecated/@Suppvisewarnings）“CLASS”
3. 运行时注解：在运行阶段还起作用，甚至会影响运行逻辑的注解。（@Autowired）“RUNTIME”
- 按来源
1. JDK 的注解
2. 第三方注解
3. 自定义注解
元注解：对注解进行注解，注解的注解。

## 语法要求

1. 成员类型是受限的，合法类型包括原始数据类型、String、Class、Annotation、Enumeration
2. 如果注解只有一个成员，则成员必须取名为 value(),在使用时可以忽略成员名和赋值号（=）
3. 注解类可以没有成员，没有成员的注解称为标识注解。
使用注解的语法

@<注解名>(<成员名1>=<成员值1>,<成员名2>=<成员值2>,…)

@Description(desc=”I am Xa”,author=”Xa Xa”,age=18)

public void demo(){}

## 解析注解

通过反射获取类、函数或成员上的运行时注解信息，从而实现动态控制程序运行的逻辑。

## 使用举例

	```java
	package ann.xa;
		@Table("student")  
		public class Filter {  
		  
		    @Column("id")  
		    private int stuId;  
		    @Column("name")  
		    private String stuName;  
		    @Column("email")  
		    private String email;  
		      
		    public int getStuId() {  
		        return stuId;  
		    }  
		    public void setStuId(int stuId) {  
		        this.stuId = stuId;  
		    }  
		    public String getStuName() {  
		        return stuName;  
		    }  
		    public void setStuName(String stuName) {  
		        this.stuName = stuName;  
		    }  
		    public String getEmail() {  
		        return email;  
		    }  
		    public void setEmail(String email) {  
		        this.email = email;  
		    }  
		      
		}
	```
-

	```java
	package ann.xa;
	import java.lang.annotation.ElementType;
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	import java.lang.annotation.Target;
	@Target({ElementType.TYPE})  //作用在类或者接口
	@Retention(RetentionPolicy.RUNTIME)  
	public @interface Table {  
	  public String value();  
	}
	```
-

	```java
	package ann.xa;
	import java.lang.annotation.ElementType;
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	import java.lang.annotation.Target;
	@Target({ElementType.FIELD})  //用用在字段
	@Retention(RetentionPolicy.RUNTIME)  
	public @interface Column {  
	    String value();  
	}
	```
-

	```java
	package ann.xa;
	import java.lang.reflect.Field;
	import java.lang.reflect.Method;
	public class Test {
		public static void main(String[] args) {
			Filter f1 = new Filter();
			f1.setStuId(10); // 查询 ID 为 10 的用户
			Filter f2 = new Filter();
			f2.setStuId(10);
			f2.setStuName("XA"); // 用户名为 XA 的用户
			Filter f3 = new Filter();
			f3.setEmail("1223716098@qq.com,curiousby@163.com"); // 查询邮箱为其中之一的用户
			String sql1 = query(f1);
			String sql2 = query(f2);
			String sql3 = query(f3);
			System.out.println(sql1);
			System.out.println(sql2);
			System.out.println(sql3);
		}
		@SuppressWarnings("unchecked")
		private static String query(Filter f) {
			StringBuffer sb = new StringBuffer();
			// 1.获取到 class
			Class c = f.getClass();
			// 2.获取到 table 的名字
			boolean isExist = c.isAnnotationPresent(Table.class);
			if (!isExist) {
				return null;
			}
			Table t = (Table) c.getAnnotation(Table.class);
			String tableName = t.value();
			sb.append(" select * from ").append(tableName).append(" 1=1 ");
			// 3.遍历所有的字段
			Field[] fArray = c.getDeclaredFields();
			for (Field field : fArray) {
				// 4. 处理每个字段对应的 sql
				// 4.1 拿到字段名
				boolean fExist = field.isAnnotationPresent(Column.class);
				if (!fExist) {
					continue;
				}
				Column column = field.getAnnotation(Column.class);
				String columnName = column.value();
				String fieldName = field.getName();
				// 4.2 拿到字段值
				String getMethodName = "get"
						+ fieldName.substring(0, 1).toUpperCase()
						+ fieldName.substring(1);
				Object fieldValue = null;
				try {
					Method getMethod = c.getMethod(getMethodName);
					fieldValue = getMethod.invoke(f);
				} catch (Exception e) {
				}
				//4.3 拼装 sql
				if (fieldValue == null
						|| (fieldValue instanceof Integer && (Integer) fieldValue == 0)) {
					continue;
				}
				sb.append(" and ").append(columnName);
				if (fieldValue instanceof String) {
					if (((String) fieldValue).contains(",")) {
						String[] values = ((String) fieldValue).split(",");
						sb.append(" in ( ");
						for (String v : values) {
							sb.append("'").append(v).append("',");
						}
						sb.deleteCharAt(sb.length() - 1);
						sb.append(" )");
					} else {
						sb.append(" = '").append(fieldValue).append("' ");
					}
				} else if (fieldValue instanceof Integer) {
					sb.append(" = ").append(fieldValue).append(" ");
				}
			}
			return sb.toString();
		}
	}
	```
