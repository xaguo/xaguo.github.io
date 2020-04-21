---
title: Python操作MySQL
date: 2017-09-26 22:45:24
tags: [Python,数据库,MySQL]
---

使用 PyMySQL 操作 MySQL 数据库

<!-- more -->

安装PyMySQL： `pip install PyMySQL`

## DB API

- connection : 建立数据库连接
- cursor : 执行 SQL、获取数据

connection 的使用

	```py
	# -*- coding: UTF-8 -*-
	import pymysql
	conn = pymysql.connect(
	   host='127.0.0.1',
	   user='root',
	   passwd='root',
	   db='master',
	   charset='utf8'
	)
	cursor=conn.cursor()
	print conn
	print cursor
	cursor.close()
	conn.close()
	```

cursor 的使用
	
	```py
	# -*- coding: UTF-8 -*-
	import pymysql
	conn = pymysql.connect(
		host='127.0.0.1',
		user='root',
		passwd='root',
		db='db',
		charset='utf8'
	)
	cursor=conn.cursor()
	sql="select * from users"
	cursor.execute(sql)
	print cursor.rowcount
	# for row in cursor:
	#     print row
	# 获取一条
	rs=cursor.fetchone()
	print rs
	# 获取多条
	rs = cursor.fetchmany(3)
	print rs
	# 获取剩下的全部
	rs=cursor.fetchall()
	print rs
	cursor.close()
	conn.close()
	```

select 查询

```py
	# -*- coding: UTF-8 -*-
	import pymysql
	conn = pymysql.connect(
	    host='127.0.0.1',
	    user='root',
	    passwd='root',
	    db='db',
	    charset='utf8'
	)
	cursor=conn.cursor()
	sql="select * from users"
	cursor.execute(sql)
	print cursor.rowcount
	rs=cursor.fetchall()
	for row in rs:
		print  "userid=%d,email=%s,password=%s" % row
	cursor.close()
	conn.close()
	```
增、删、改、事务的使用

	```py
	# -*- coding: UTF-8 -*-
	import pymysql
	conn = pymysql.connect(
	    host='127.0.0.1',
	    user='root',
	    passwd='root',
	    db='db',
	    charset='utf8'
	)
	cursor=conn.cursor()
	sql_insert = "insert into users(id,email,password) value(6,'xc@163.com','xc123456')"
	sql_update = "update users set email='xccc@163.com' where email='xc@163.com'"
	sql_delete = "delete from users  where d<3"
	try:
	    # 执行语句
	    cursor.execute(sql_insert)
	    print cursor.rowcount
	    cursor.execute(sql_update)
	    print cursor.rowcount
	    cursor.execute(sql_delete)
	    print cursor.rowcount
	    # 提交语句
	    conn.commit()
	except Exception as e:
	    print e
	    # 回滚
	    conn.rollback()
	cursor.close()
	conn.close()
	```

## 开发数据库程序的流程

1. 创建 connection 对象，获取 cursor
2. 使用 cursor 执行 SQL 语句
3. 使用 cursor 获取数据、判断执行状态
4. 提交事务或者回滚事务
5. 关闭 cursor 、关闭 connection

事务：访问和更新数据库的一个程序执行单元

- 原子性：事务中包括的诸操作要么都做，要么都不做
- 一致性：事务必须使数据库从一致性状态转变到另一个一致性状态
- 隔离性：一个事务的执行不能被其他事务干扰
- 持久性：事务一旦提交，它对数据库的改变就是永久的

开发中怎么使用事务？

- 关闭自动 commit ：设置 `conn.autocommit(false)`
- 正常结束事务： `conn.commit()`
- 异常结束事务： `conn.rollback()`
银行转账实例：

开始事务->检查账号 A 和账户 B 是否可用->检查账户 A 是否有 100 元
->账户 A 减去 100 元账户 B 加上 100 -> 提交事务/如果出现异常则回滚事务

银行转账实例：

开始事务->检查账号 A 和账户 B 是否可用->检查账户 A 是否有 100 元
->账户 A 减去 100 元账户 B 加上 100 -> 提交事务/如果出现异常则回滚事务

银行转账实例编码实现

	```py
	# -*- coding: UTF-8 -*-
	import sys
	import pymysql
	# 转账类
	class TransferMoney(object):
	    def __init__(self,conn):
	        self.conn=conn
	    # 检查账号是否可用
	    def check_acctavailable(self,acctid):
	        cursor = self.conn.cursor()
	        try:
	            sql = "select * from account where acctid=%s"%acctid
	            print "check_acctavailable："+sql
	            cursor.execute(sql)
	            rs=cursor.fetchall()
	            if len(rs)!=1:
	                raise Exception("账号%s不存在"%acctid)
	        finally:
	            cursor.close()
	    # 检查账号余额
	    def has_enough_money(self,acctid,money):
	        cursor = self.conn.cursor()
	        try:
	            sql = "select * from account where acctid=%s and money>%s" % (acctid,money)
	            print "has_enough_money：" + sql
	            cursor.execute(sql)
	            rs = cursor.fetchall()
	            if len(rs) != 1:
	                raise Exception("账号%s余额不足" % acctid)
	        finally:
	            cursor.close()
	    # 减款操作
	    def reduce_money(self,acctid,money):
	        cursor = self.conn.cursor()
	        try:
	            sql = "update account set money=money-%s where acctid=%s" % (money,acctid)
	            print "reduce_money：" + sql
	            cursor.execute(sql)
	            if cursor.rowcount != 1:
	                raise Exception("账号%s减款失败" % acctid)
	        finally:
	            cursor.close()
	    # 加款操作
	    def add_money(self,acctid,money):
	        cursor = self.conn.cursor()
	        try:
	            sql = "update account set money=money+%s where acctid=%s" % (money, acctid)
	            print "add_money：" + sql
	            cursor.execute(sql)
	            if cursor.rowcount != 1:
	                raise Exception("账号%s加款失败" % acctid)
	        finally:
	            cursor.close()
	    # 转账
	    def transfer(self,source_acctid,target_acctid,money):
	        try:
	            self.check_acctavailable(source_acctid)
	            self.check_acctavailable(target_acctid)
	            self.has_enough_money(source_acctid,money)
	            self.reduce_money(source_acctid,money)
	            self.add_money(target_acctid,money)
	            conn.commit()
	        except Exception as e:
	            self.conn.rollback()
	            raise e
	if __name__=="__main__":
	    source_acctid = 3
	    target_acctid = 2
	    money = 1000
	    conn = pymysql.connect(host='127.0.0.1',user='root',passwd='root',
	                           db='db',charset='utf8'
	                           )
	    tr_money = TransferMoney(conn)
	    try:
	        tr_money.transfer(source_acctid,target_acctid,money)
	    except Exception as e:
	        print "出现问题："+str(e)
	    finally:
	        conn.close()
	```