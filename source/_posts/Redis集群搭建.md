---
title: Redis集群搭建
date: 2019-08-20 22:01:59
tags: [数据库,Redis]
---

docker中redis集群中搭建步骤

<!-- more -->

## redis 集群
	
解除 docker 集群：`docker swarm leave -f`

查看 docker 网段：`docker network ls`
	
删除 docker 网段：`docker network rm netxx`
	
1 创建 docker 网段：`docker network create --subnet=172.19.0.0/16 net2`

2 下载redis镜像

`docker pull yyyyttttwwww/redis`

3 创建redis容器
	
`docker run -it -d --name r1 -p 5001:6379 --net=net2 --ip 172.19.0.2 redis bash`

4 进入容器
`docker exec -it r1 bash`

	```sh	
	#配置reids节点
	vi /usr/redis/redis.conf
		daemonize yes #以后台进程运行
		cluster-enabled yes #开启集群
		cluster-config-file nodes.conf #集群配置文件
		cluster-node-timeout 15000 #超时时间
		appendonly yew #开启AOF模式
	#启动redis
	cd /usr/redis/src
	./redis-server ../redis.conf
	```
	
5 重复第3、4步创建r2,r3,r4,r5,r6节点

6 创建集群

	```sh
	#进入容器r1
	docker exec -it r1 bash
	cd /usr/redis
	mkdir cluster
	cd src
	cp redis-trib-.rb ../cluster
	#创建redis集群
	./redis-trib-.rb create --replicas 1 172.19.0.2:6379 172.19.0.3:6379 172.19.0.4:6379 172.19.0.5:6379 172.19.0.6:6379 172.19.0.7:6379
	```
	
访问节点
	
`docker exec -it r1 bash`

	```sh
	/usr/redis/src/redis-cli -c
	>set a 10
	>get a
	```

查看节点状态
	
`/usr/redis/src/redis-cli -c`

`cluster nodes`
