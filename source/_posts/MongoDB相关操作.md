---
title: MongoDB常用操作
date: 2018-10-18 19:14:04
tags: [数据库,MongoDB]
---

MongoDB常用操作：安装配置，常用命令，数据导入导出等。
<!-- more -->

## 安装配置 MongoDB

### 安装

- 下载 MongoDB 3.6.0

`wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.6.0.tgz`

`http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/3.6/x86_64/`

- 解压

`tar -zxvf mongodb-linux-x86_64-3.6.0.tgz`

### 数据库配置

- 配置环境变量，`vim ~/.bash_profile` 写入 `:/root/mongodb-linux-x86_64-3.6.0/bin` 保存退出
- 创建文件夹，在 `mongodb-linux-x86_64-3.6.0` 目录下创建 `data/db` 和 `log/mongodb.log`
- 配置数据库，在 `mongodb-linux-x86_64-3.6.0/bin` 目录下创建 `vim mongodb.conf` 并写入

	```sh
	dbpath = /root/mongodb-linux-x86_64-3.6.0/data/db #数据文件存放目录
	logpath = /root/mongodb-linux-x86_64-3.6.0/logs/mongodb.log #日志文件存放目录
	port = 27017  #端口
	fork = true  #以守护程序的方式启用，即在后台运行
	`#nohttpinterface = true
	`#auth=true
	bind_ip=0.0.0.0
	```

- 启动数据库，`mongod -f /home/scalevge/vgehsr/mongodb-linux-x86_64-3.6.0/bin/mongodb.conf`

### mongbdb 数据导入导出

- mongodb 数据导出 `mongodump -h 127.0.0.1:27017 -d train-simulation -o train-simulation`

- mongodb 数据导入 `mongorestore -h 127.0.0.1:27017 -d train-simulation ./train-simulation`

- mongodb 单表导出 `mongoexport -d train-simulation -c users -q '{"name":{$ne:null}}' -o ./train-users.json`

- mongodb 单表导入 `mongoimport -h 127.0.0.1:27017 -d train-simulation -c users ./train-users.json`

- mongodb 数据更新 `mongo\ use dbName\ db.users.update({name:"XA"},{"$set":{role:51}})`

- 删除数据库 `mongo --host 127.0.0.1:27017 dbName --eval "db.dropDatabase()"`

### 先导入数据库数据，再配置数据库权限！！！

### mongbdb 数据库管理员用户配置

- `mongo --port 27017`
- 创建用户管理员：

	```sh
	//切换到 admin 数据库
	use admin
	//添加管理员，可以管理所有数据库
	db.createUser({user:"root",pwd:"root123",roles:[{role:"userAdminAnyDatabase",db:"admin"}]})
	//在admin数据库下对管理员进行授权。返回1说明授权成功
	db.auth('root','root123')
	
	use admin
	db.auth('root','root123')
	//切换到 demo 数据库
	use demo
	//添加demo数据库用户，可以读写demo数据库
	db.createUser({user:"demo",pwd:"root123",roles:[{role:"readWrite",db:"demo"}]})
	eg:db.createUser({user:"vs2018",pwd:"Scale123",roles:[{role:"readWrite",db:"virtual-survey"}]})
	//在admin数据库下对管理员进行授权。返回1说明授权成功
	db.auth('demo','root123')
	eg:db.auth("vs2018","Scale123")
	//添加demo数据库备份用户，只可以读demo数据库
	db.createUser({user:"demo1",pwd:"root123456",roles:[{role:"read",db:"demo"}]})
	```

- 授权后

	```sh
    mongo --port 20181
    user admin
    db.auth('root','root123')
    show dbs
	```

- 授权后，直接访问指定数据库

	```sh	
	mongo 127.0.0.1:20181/virtual-survey -u vs2018 -p Scale123
	show tables
	```

- 授权后，数据导入导出
	
- mongodb 数据导出 `mongodump -h 127.0.0.1:27017 -d train-simulation -u ts2018 -p Scale123 -o train-simulation`

- mongodb 单表导出 `mongoexport -d train-simulation -u ts2018 -p Scale123  -c users -q '{"name":{$ne:null}}' -o ./train-users.json`

- 打包 `tar zcvf fileName.tar.gz train-simulation`

	```sh
	mongo --port 20181
	use admin
	db.auth("root","root123")
	// 创建数据库
	use targetdb
	db.createUser({user:"demo",pwd:"root123",roles:[{role:"readWrite",db:"demo"}]})
	db.auth('demo','root123')
	```

- 切换数据库后要重新认证

- mongodb 数据导入 `mongorestore -h 127.0.0.1:27017 -d targetdb -u ts2018 -p Scale123 ./train-simulation`

- mongodb 单表导入 `mongoimport -h 127.0.0.1:27017 -d targetdb targetdb -u ts2018 -p Scale123 -c users ./train-users.json`



###数据库定时备份任务
- backup.sh

	```sh
	`#!/bin/sh
	backUpFolder=/home/train-simulation/data
	date_now =`date +%Y_%m_%d_%H%M`
	backFileName=train_$date_now

	cd $backUpFolder
	mkdir -p $backFileName

	mongodb 数据导出 mongodump -h 127.0.0.1:27017 -d train-simulation -u ts2018 -p Scale123 -o $backFileName

	tar zcvf $backFileName.tar.gz $backFileName

	rm -rf $backFileName
	```

- `sudo sh ./backup.sh`

- 定时任务

`crontab -e`

`13 00 * * * sh /home/*/backup.sh`
	

## 其他

- 查看 ip  `ip addr`

- 防火墙端口 `firewall-cmd --zone=public --add-port=3004/tcp --permanent`
- 重启防火墙 `firewall-cmd --reload`

- `firewall-cmd -add-forward-port=port=80:proto=tcp:toport=3004`

## Windows 配置

- 安装 MongoDB,写 .conf 配置文件 

	```sh
	dbpath = C:\path\MongoDB\data\db #数据文件存放目录
	logpath = C:\path\MongoDB\log\mongodb.log #日志文件存放目录
	port = 20181   #端口
	`#fork = true  #以守护程序的方式启用，即在后台运行
	`#nohttpinterface = true
	auth=false
	bind_ip=0.0.0.0
	```

- 配置环境变量

- 管理员身份打开 CMD

- 安装数据库服务

`mongod --config "c:\path\MongoDB\mongodb.conf" --install --serviceName "MongoDB"`

- 移除数据库服务

`mongod --remove --serviceName MongoDB`

- 启动服务

`net start MongoDB`

- 关闭服务

`net stop MongoDB`