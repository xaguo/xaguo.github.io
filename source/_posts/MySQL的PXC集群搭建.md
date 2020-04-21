---
title: MySQL的PXC集群搭建
date: 2019-08-14 20:34:57
tags: [数据库,MySQL]
---

docker 中 MySQL PXC 高可用集群的搭建及常用指令、数据库热备份冷还原
<!-- more -->

## mysql PXC 集群 ##

<center>
	<img src="./structure.png" width="500px"></img>
</center>
<center>
图1 集群架构
</center>
- PXC 集群镜像

[**percona** https://hub.docker.com/r/percona/percona-xtradb-cluster](https://hub.docker.com/r/percona/percona-xtradb-cluster)

在线安装命令
`docker pull percona/percona-xtradb-cluster`

本地安装命令
`docker load < /home/soft/pxc.tar.gz`

修改镜像名字
`docker tag docker.io/percona/percona-xtradb-cluster pxc`

删除镜像
`docker rmi docker.io/percona/percona-xtradb-cluster`

创建 docker 内部网段（172.17.0.*）
`docker network create net1`

查看网段信息
`docker network inspect net1`

删除网段
`docker network rm net`

创建网段
`docker network create --subnet=172.18.0.0/24 net1`

创建 docker 卷
`docker volume create --name v1`

查看卷
`docker inspect v1`

删除卷
`docker volume rm v1`

创建PXC容器

创建卷

`docker volume create --name v1`

`docker volume create --name v2`
    
`docker volume create --name v3`
    
`docker volume create --name v4`
    
`docker volume create --name v5`
	
创建mysql节点容器	

`docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -v v1:/var/lib/mysql --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc`
	
`docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc`
    
`docker run -d -p 3308:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v3:/var/lib/mysql --privileged --name=node3 --net=net1 --ip 172.18.0.4 pxc`
    
`docker run -d -p 3309:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v4:/var/lib/mysql --privileged --name=node4 --net=net1 --ip 172.18.0.5 pxc`
    
`docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v5:/var/lib/mysql --privileged --name=node5 --net=net1 --ip 172.18.0.6 pxc`


> 错误
> docker exec -ti node1 bash
> 
> vi /etc/mysql/node.conf
> 
>    [mysqld]
>    
>    .# skip-grant-tables
>    
>    - ck
>mysql -uroot -p
>
>    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'abc123456' WITH GRANT OPTION;
>    
>exit
> 
>1.停止mysql数据库：systemctl stop mysqld
>
>2.用以下命令启动MySQL，以不检查权限的方式启动：mysqld --user=root --skip-grant-tables &
>
>3.登录mysql：mysql -uroot -p
>
>4.更新root密码允许任何客户端登录:GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'xa123456' WITH GRANT OPTION;
>
>5.刷新权限：flush privileges;
>
>6.退出mysql：exit


查看mysql主从同步状态
`SHOW SLAVE STATUS;`

停止从节点同步
`STOP SLAVE;`


- 安装 haproxy

在宿主机上，配置haproxy负载均衡

	```sh
    docker pull haproxy#下载镜像
	touch /home/soft/haproxy/haproxy.cfg# 创建配置文件
	docker run -it -d -p 4001:8888 -p 4002:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h1 --privileged --net=net1 --ip 172.18.0.7 haproxy#创建haproxy容器
	
	docker exec -it h1 bash#进入容器h1
	haproxy -f /usr/local/etc/haproxy/haproxy.cfg
	```

- 创建Mysql 用户

允许客户机登录数据库

	```sh
	flush privileges;
	CREATE USER 'haproxy'@'%' IDENTIFIED BY '';
	grant all privileges on *.* to root@'%'identified by 'password';
	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'abc123456' WITH GRANT OPTION; 
	```

- 安装 Keepalived

在Haproxy容器内

	```sh
    apt-get update
    apt-get install keepalived
	安装vim
	apt-get install vim
	创建keepalived配置文件
	touch /etc/keepalived/keepalived.conf
	vim /etc/keepalived/keepalived.conf
		vrrp_instance  VI_1 {
    	state  MASTER
    	interface  eth0
    	virtual_router_id  51
    	priority  100
    	advert_int  1
    	authentication {
        	auth_type  PASS
        	auth_pass  123456
    	}
    	virtual_ipaddress {
        	172.18.0.201
    	}
	}
	启动keepalived
	service keepalived start
	```

创建Haproxy容器(name=h2的原因是为了高可用)

	```sh
    docker run -it -d -p 4003:8888 -p 4004:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h2 --net=net1 --ip 172.18.0.8 --privileged haproxy

	docker exec -it h2 bash

	haproxy -f /usr/local/etc/haproxy/haproxy.cfg

	apt-get update
	apt-get install keepalived
	

	touch /etc/keepalived/keepalived.conf
	vim /etc/keepalived/keepalived.conf
			
		vrrp_instance  VI_1 {
		    state  MASTER  # Keepalived的身份（MASTER主服务要抢占IP，BACKUP备服务器不会抢占IP）。
		    interface  eth0    # docker网卡设备，虚拟IP所在
		    virtual_router_id  51  # 虚拟路由标识，MASTER和BACKUP的虚拟路由标识必须一致。从0～255
		    priority  100  # MASTER权重要高于BACKUP数字越大优先级越高
		    advert_int  1  # MASTER和BACKUP节点同步检查的时间间隔，单位为秒，主备之间必须一致
		    authentication {  # 主从服务器验证方式。主备必须使用相同的密码才能正常通信
		        auth_type  PASS
		        auth_pass  123456
		    }
		    virtual_ipaddress {  # 虚拟IP。可以设置多个虚拟IP地址，每行一个
		        172.18.0.201
		    }
		}
	```
	
- 暂停PXC集群的办法

	```sh
	vi /etc/sysctl.conf
	文件中添加net.ipv4.ip_forward=1这个配置
	systemctl restart network
	然后把虚拟机挂起
	
		.# vim /etc/keepalived/keepalived.conf
		vrrp_instance  VI_1 {
		    state  MASTER
		    interface  eth0
		    virtual_router_id  51
		    priority  100
		    advert_int  1
		    authentication {
		        auth_type  PASS
		        auth_pass  123456
		    }
		    virtual_ipaddress {
		        172.18.0.201
		    }
		}
	```
	
宿主机中 keepalived.conf
	
	```sh
	vrrp_instance VI_1 {
	    state MASTER
	#这里是宿主机的网卡，可以通过ip a查看当前自己电脑上用的网卡名是哪个
	    interface ens33
	    virtual_router_id 100
	    priority 100
	    advert_int 1
	    authentication {
	        auth_type PASS
	        auth_pass 1111
	    }
	    virtual_ipaddress {
	#这里是指定的一个宿主机上的虚拟ip，一定要和宿主机网卡在同一个网段，
	#我的宿主机网卡ip是192.168.63.130，所以指定虚拟ip是140
	           192.168.63.140
	    }
	}
	 
	#接受监听数据来源的端口，网页入口使用
	virtual_server 192.168.63.140 8888 {
	    delay_loop 3
	    lb_algo rr 
	    lb_kind NAT
	    persistence_timeout 50
	    protocol TCP
	#把接受到的数据转发给docker服务的网段及端口，由于是发给docker服务，所以和docker服务数据要一致
	    real_server 172.18.0.201 8888 {
	        weight 1
	    }
	}
	 
	#接受数据库数据端口，宿主机数据库端口是3306，所以这里也要和宿主机数据接受端口一致
	virtual_server 192.168.63.140 3306 {
	    delay_loop 3
	    lb_algo rr 
	    lb_kind NAT
	    persistence_timeout 50
	    protocol TCP
	#同理转发数据库给服务的端口和ip要求和docker服务中的数据一致
	    real_server 172.18.0.201 3306 {
	        weight 1
	    }
	}
	```

- 热备份数据

PXC全量备份

	```sh
    docker volume create backup
    
    docker stop node1
    docker rm node1# 数据库数据保存在Docker卷v1中，不会丢失
    .# 参数改变：
    .# 1. -e CLUSTER_JOIN=node2;原来其他节点是通过node1加入集群的，现在node1重新创建，需要选择一个其他节点加入集群
    .# 2. -v backup:/data;将Docker卷backup映射到容器的/data目录
    docker run -d -u root -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node2 -v v1:/var/lib/mysql -v backup:/data --network=net1 --ip 172.18.0.2 --name=node1 pxc
	docker exec -it node1 bash
	apt-get update
	apt-get install percona-xtrabackup-24
	mkdir /data/backup
	mkdir /data/backup/full
	.#不建议，已过时 innobackupex --backup -u root -p abc123456 --target-dir=/data/backup/full
	xtrabackup --backup -uroot -pabc123456 --target-dir=/data/backup/full
	```

PXC全量还原

	```sh
    1. 停止并删除PXC集群所有节点
    docker stop node1 node2 node3 node4 node5
    docker rm node1 node2 node3 node4 node5
    docker volume rm v1 v2 v3 v4 v5
    
    2. 按照之前的步骤重新创建node1容器，并进入容器，执行冷还原
    .# 创建卷
    docker volume create v1
    .# 创建容器
    docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -v v1:/var/lib/mysql -v backup:/data --name=node1 --network=net1 --ip 172.18.0.2 pxc
    .# 以root身份进入容器
    docker exec -it -uroot node1 bash
    .# 删除数据
    rm -rf /var/lib/mysql/*
    .# 准备阶段
    xtrabackup --prepare --target-dir=/data/backup/full/
    .# 执行冷还原
    xtrabackup --copy-back --target-dir=/data/backup/full/
    .# 更改还原后的数据库文件属主
    chown -R mysql:mysql /var/lib/mysql
    .# 退出容器后，重启容器
    docker stop node1
    docker start node1
	```