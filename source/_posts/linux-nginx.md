---
title: linux下安装nginx
date: 2016-09-05 15:59:10
updated: 2016-09-05 15:59:10
categories: 开发工具
tags: [linux,nginx]
---

#### nginx是什么？
> nginx就不多做解释了，如果不知道的基本也就没必要看如何安装了。

#### nginx安装
> 1. 下载
	PCRE 作用是让 Ngnix 支持 Rewrite 功能（*<font color=#FFFF00>非必须</font>*）。
	`` $wget http://downloads.sourceforge.net/project/pcre/pcre/8.39/pcre-8.39.tar.gz ``
	`` $wget http://nginx.org/download/nginx-1.10.1.tar.gz ``
<!--more-->
> 2. 安装
	``` 
	$
	// 解压 
	$tar -zxvf nginx-1.10.1.tar.gz
	
	// 创建安装目录
	$mkdir /opt/app/nginx-1.10.1
	// 配置安装选项（如果需要--with-pcre请将目录指向下载的pcre-8.39.tar.gz解压后的文件夹目录）
	$./configure --prefix=/opt/app/nginx-1.10.1 --with-http_stub_status_module --with-http_ssl_module --with-pcre=/opt/install/pcre-8.39
	// 安装
	$make
	$make install

	// 创建 Nginx 运行使用的组和用户(非必须)
	$groupadd www
	$useradd -g www www
	$
	```
> 3. 启动
	``` 
	$
	$cd /opt/app/nginx-1.10.1
	$sbin/nginx
	// 直接在浏览器打开：127.0.0.1:80
	$
	```

#### nginx使用：
> 请查看 [nginx随笔](/posts/develop-tool/2016-09-05-nginx-suibi.html)
            