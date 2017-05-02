---
title: linux下搭建ActiveMQ
tags: [linux,activemq]
date: 2017-05-02 13:14:10
updated: 2017-05-02 13:14:10
categories: 开发工具
---

#### ActiveMQ是什么？
> ActiveMQ就不多做解释了，如果不知道的基本也就没必要看了，请执行百度Google一下。

#### ActiveMQ安装
> 1. 下载
	`` $wget http://archive.apache.org/dist/activemq/5.14.4/apache-activemq-5.14.4-bin.tar.gz ``
<!--more-->
> 2. 安装
	``` 
	$
	// 解压 
	$tar -xzf apache-activemq-5.14.4-bin.tar.gz
	```
> 3. 启动
	``` 
	$
	// 默认8061端口，默认用户admin/admin
	$./bin/activemq start
	// 查看启动状态
	$./bin/activemq status
	// 重启
	$./bin/activemq restart

	// 直接在浏览器打开：127.0.0.1:8061
	$
	```
> 4. 关于相关配置
	``` 
	$
	// 修改web后台页面的登录用户密码
	$vi conf/jetty-realm.properties
	// 修改倒数第二行，那里三个分别是用户名，密码，角色，其中admin角色是固定的
	$
	$ 修改java程序链接时开启密码验证（默认是不需要用户名密码直接链接的）
	$vi conf/activemq.xml
	// 在<broker>节点中加入以下内容
	<plugins>
             <simpleAuthenticationPlugin>
                 <users>
                     <authenticationUser username="${activemq.username}" password="${activemq.password}" groups="users,admins"/>
                 </users>
             </simpleAuthenticationPlugin>
         </plugins>

	 $vi conf/credentials.properties
	// 倒数二三行，修改用户名和密码
	$
	// 修改broker摘要信息
	$vi conf/activemq.xml
	// 修改broker节点的属性
	$

#### ActiveMQ使用：