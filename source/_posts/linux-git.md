---
title: linux下安装git
date: 2016-08-24 10:42:46
updated: 2016-08-24 10:42:46
categories: 开发工具
tags: [linux,git]
---

#### git是什么？
> git就不多做解释了，如果不知道的基本也就没必要看如何安装了。

#### 安装
> 1. 下载
	`` $wget https://www.kernel.org/pub/software/scm/git/git-2.1.2.tar.gz ``
<!--more-->
> 2. 安装
	``` 
	$
	// 移除旧的安装
	$yum remove git
	// 安装依赖
	$yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel     
	$yum install  gcc perl-ExtUtils-MakeMaker

	// 解压 
	$tar -xvzf git-2.1.2.tar.gz
	// 创建安装目录
	$mkdir /opt/app/git-2.1.2
	// 安装
	$make prefix=/opt/app/git-2.1.2 all
	$make prefix=/opt/app/git-2.1.2 install

	// 修改环境变量 
	$vi /etc/profile 
	export GIT_HOME=/opt/app/git-2.1.2  #git安装目录
	export PATH=$PATH:$GIT_HOME/bin
	// 保存 (按ESC后输入:wq!)  

	// 使修改生效，.后面有空格
	$. /etc/profile 

	// 查看git版本
	$git --version


	// 其它方式安装
	$yum install git #此方法安装的版本比较低
	$
	```

#### git使用：
> 请查看 [git随笔](/posts/develop-tool/2016-08-17-git-suibi.html)
            