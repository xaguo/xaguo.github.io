---
title: git常用操作
date: 2018-04-13 16:40:45
tags: 随笔
---

- 创建项目 git clone,git init
- 创建分支，推送分支，合并分支
- 删除分支、回退版本

<!-- more -->

		```sh
    	# 查看版本
    	git --version
    	# 从远程仓库下载项目
		git clone git@github.com:xaguo/higit.git
		# 查看状态
		git status
		# 查看本地分支
		git branch -a
		# 查看所在分支
		git branch
		# 添加文件
		git add .
		# 提交
		git commit -m '初次提交'
		# 查看远程
		git remote -v
		# 推送到远程的 master 分支
		git push origin master
		# 查看本地分支
		git branch -a
		# 创建开发分支 dev
		git checkout -b dev
		# 创建文件
		touch test1.txt
		git add test1.txt
		git commit -m 'dev 上的功能开发'
		# 把本地 dev 分支(当前分支)推送到远程的 dev 分支
		git push origin dev
		git branch -a
		# 切换到要合并到的分支
		git checkout master
		# 合并分支
		git merge dev
		# 把本地 master 提交到远程的 master 分支
		git push origin master
		# 删除本地 dev 分支
		git branch -D dev
		# 删除远程 dev 分支
		git push origin :dev
		# 退回到前一个版本
		git reset --hard head^
		# 查看日志
		git log
		git reflog
		# 版本回退
		git reset --hard HEAD@{2}
		git reset --hard 0de4a24
		```

