---
title: docker常用操作
date: 2019-08-12 09:34:57
tags: [随笔,docker]
---

docker安装配置及常用的命令
<!-- more -->

## CentOS 安装 docker ##

`yum -y update`

`yum install -y docker`

在docker里禁用selinux，--selinux-enabled=false.
重新编辑docker配置文件：
`vi /etc/sysconfig/docker`
改为：`--selinux-enabled=false`


- docker 启动、关闭与重启

`service docker start`

`service docker stop`

`service docker restart`

- docker 命令

<center>
	<img src="./docker.png" width="400px"></img>
</center>

- DaoCloud 镜像仓库加速器

`curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io`

`vi /etc/docker/docker.json`

- 在线安装 java 镜像

查找镜像
`docker search java`

下载安装镜像
`docker pull docker.io/java`

查看镜像
`docker images`

导出镜像
`docker save java > /home/java.tar.gz`

导入镜像
`docker load < /home/java.tar.gz`

移除镜像
`docker rmi java`

创建并启动容器
`docker run -it --name myjava bash`

端口映射（宿主机端口:容器端口）
`docker run -it --name myjava -p 9000:8080 -p 9001:8085 java bash`

文件映射(宿主机目录:容器目录 --读写权限)
`docker run -it --name myjava -v /home/project:/soft --privileged java bash`

`ocker run -it --name myjava -p 9000:8080 -p 9001:8085 -v /home/myproject:/soft --privileged docker.io/java bash`

查看容器信息
`docker ps -a`

暂停和停止容器

`docker pasue myjava`

`docker unpause myjava`

`docker stop myjava`

`docker start -i myjava`

>拷贝文件 `docker cp a.txt [containId]://usr/share/a.txt`
>提交保存 `docker commit  -m 'fun' [containId] [ImgName]`
>删除镜像 `docker rmi ImageName`
>查看运行中的容器 `docker ps`
>查看所有容器 `docker ps -a`
>删除容器 `docker rm ContainName/[ContainId]`