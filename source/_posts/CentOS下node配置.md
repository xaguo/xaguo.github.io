---
title: CentOS下node配置
date: 2018-10-13 19:40:45
tags: 随笔
---

CentOS系统下node的安装配置
<!-- more -->

## 安装配置 nodejs

### 安装

- 下载nodejs

`wget https://npm.taobao.org/mirrors/node/v8.9.4/node-v8.9.4-linux-x64.tar.xz`

- 解压

`tar -xvf  node-v8.9.4-linux-x64.tar.xz`

- 测试是否安装成功

`cd node-v8.0.0-linux-x64/bin && ls`

`./node -v`

### 配置

- `vim ~/.bash_profile`

- 按【insert】

- 在 `PATH:$HOME/bin` 一行之后 加入 `:/root/node-v8.0.0-linux-x64/bin`

- 按【esc】

- :wq 保存退出

- 执行 `source ~/.bash_profile` 使设置生效

- 在任意目录下执行 `node -v` / `npm -v` 查看是否设置成功。