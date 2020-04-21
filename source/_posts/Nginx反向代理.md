---
title: nginx常用操作
date: 2018-10-18 18:40:45
tags: 随笔
---

Nginx的安装和反向代理的配置
<!-- more -->

## Nginx反向代理

安装 nginx 
`sudo apt-get install nginx`

查看版本
`nginx -v`

`cd /etc/nginx/`

`cd conf.d`

`pwd`  #/etc/nginx/conf.d

创建配置文件

	```sh
	sudo vi xa-com-8081.conf`

	upstream xa{
		server 127.0.0.1:8081
	}
	
	server{
		listen 80;
		server_name 127.0.0.1
	
		location / {
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
			
			proxy_set_header Host $http_host;
			proxy_set_header X-Nginx-Proxy true;
			
			proxy_pass http://xa;
			proxy_redirect off;
			
		}
	}
	```


`pwd`  #/etc/nginx
`sudo vi nginx.conf`

隐藏 nginx 版本信息
`serrver_tokens off`

检查配置
`sudo nginx -t`

重启服务
`sudo nginx -s reload`






