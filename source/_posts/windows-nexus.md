---
title: Windows下搭建Maven仓库私服
date: 2016-08-19 13:38:00
updated: 2016-08-19 13:38:00
categories: 开发工具
tags: [Windows,Maven,Nexus]
---

#### Nexus和Maven是什么？
> Nexus和Maven就不多做解释了，如果不知道的基本也就没必要看如何搭建了。

#### Nexus安装
> 1. 下载
	Nexus下载地址：sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-latest-bundle.zip
<!--more-->
> 2. 安装
	下载后，解压，进入``` \nexus-2.11.3-01\bin\jsw ``` 目录
	然后根据要安装Nexus的操作系统版本进入相应目录，我是win7旗舰版64为所以我进入 \nexus-2.11.3-01\bin\jsw\windows-x86-64  目录
	![Nexus安装](/img/windows-nexus/1.png)
> 3. 启动
	如果执行 console-nexus.bat ，那么执行完，直接在浏览器打开：127.0.0.1:8081/nexus/  出来Nexus页面就代表ok。（启动过程可能会需要一段时间，启动完不要关闭窗口）
	如果执行 install-nexus.bat ， 那么执行完，要再进行一步：启动服务，可以再执行 start-nexus.bat 来启动服务
	如果喜欢装逼可以在window服务中找到
	![Nexus安装](/img/windows-nexus/2.png)
	来启动， 启动后参照上面在浏览器打开地址。（服务启动过程也需要一段时间，请耐心，千万不要以为死机了哦。）
	都说的这么直白了，那么 stop-nexus.bat 和uninstall-nexus.bat 就不唠叨了。wrapper.exe 这货是干嘛的还真不知道，也没再去网上搜，喜欢装逼的朋友可以自行搜索。
> 4. 关于相关配置
	网上有个地址：wrapper.tanukisoftware.com/doc/english/properties.html ，但是不是中文，懒得一点一点翻译，这里就只说几个，个人觉得爱装逼人可能会去该的东西吧。
	声明，Nexus是用的jetty做web服务。
	首先端口，为啥是8081呢，对啊，这是为什么呢？ 
	来请看这个目录：``` \nexus-2.11.3-01\conf ```  
	![Nexus安装](/img/windows-nexus/3.png)
	jetty.xml 里的配置：
	![Nexus安装](/img/windows-nexus/4.png)
	那么application-host和application-port 那来的呢？
	注意这个目录最后一个文件 nexus.properties，修改端口的时候，修改完，记得重启服务哦，端口不要已经被占用哦，否则会启动服务失败的，如果失败可以看下``` \nexus-2.11.3-01\logs\wrapper.log ```  里的错误信息。
	![Nexus安装](/img/windows-nexus/5.png)
	ok，你懂得！
	那么另一个装逼问题，window服务名咋来的？
	好，来请看这个目录：``` \nexus-2.11.3-01\bin\jsw\conf ```  下的 wrapper.conf  这个。由于里面配置项太多，只摘要几个显逼格的项。
	Nexus的仓库目录：
	![Nexus安装](/img/windows-nexus/6.png)
	也就是 和nexus-2.11.3-01同目录。
	安装为window服务的配置信息：
	![Nexus安装](/img/windows-nexus/7.png)

#### Nexus使用：
            