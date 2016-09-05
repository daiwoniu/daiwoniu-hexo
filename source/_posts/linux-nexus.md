---
title: linux下搭建Maven仓库私服
date: 2016-08-24 09:51:24
updated: 2016-08-24 09:51:24
categories: 开发工具
tags: [linux,Maven,Nexus]
---

#### Nexus和Maven是什么？
> Nexus和Maven就不多做解释了，如果不知道的基本也就没必要看如何搭建了。

#### Nexus安装
> 1. 下载
	`` $wget http://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-2.13.0-01-bundle.tar.gz ``
<!--more-->
> 2. 安装
	``` 
	$
	// 解压 
	$tar -xvzf nexus-2.13.0-01-bundle.tar.gz
	
	// 将nexus-2.13.0-01和sonatype-work放到同一个目录下(如创建的nexus-2.13目录)         
	$mkdir /opt/app/nexus-2.13
	$cp nexus-2.13.0-01 /opt/app/nexus-2.13/
	$cp sonatype-work /opt/app/nexus-2.13/

	// 拷贝启动脚本 
	$cp /opt/app/nexus-2.13/nexus-2.13.0-01/bin/nexus /etc/init.d/    

	// 修改启动脚本配置
	$vi /etc/init.d/nexus 
	NEXUS_HOME="/opt/app/nexus-2.13/nexus-2.13.0-01"  #Nexus安装目录
	RUN_AS_USER=nexus
	PIDDIR="${NEXUS_HOME}"
	// 保存 (按ESC后输入:wq!)  

	// 添加nexus用户
	$useradd nexus
	// 设置nexus目录用户权限 
	$chown -R nexus nexus-2.13
	// 设置nexus目录用户组权限
	$chgrp -R nexus nexus-2.13
	$
	```
> 3. 启动
	``` 
	$
	// 默认8081端口，默认用户admin/admin123
	$service nexus start
	// 查看启动状态和线程id 
	$service nexus status

	// 直接在浏览器打开：127.0.0.1:8081/nexus
	$
	```
> 4. 关于相关配置
	``` $vi /opt/app/nexus-2.13/nexus-2.13.0-01/conf/nexus.properties ```
	可以修改端口、ip、工作目录等
	![Nexus安装](/img/linux-nexus/1.png)
	本人的只是改了端口`` application-port=3000 ``

#### Nexus使用：
            