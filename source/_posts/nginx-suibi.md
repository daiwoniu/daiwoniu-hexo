---
title: nginx随笔
tags:
  - nginx
  - 配置
  - 随笔
date: 2016-09-05 16:14:10
updated: 2016-11-11 14:17:10
categories: 开发工具
---
### 2016-09-05 16:14:10
###### 命令
``` stylus
$
$cd /opt/app/nginx-1.10.1    // 进入安装目录
$sbin/nginx -t     // 检查配置文件ngnix.conf的正确性
$sbin/nginx    // 启动
$sbin/nginx -s reload    // 重新载入配置文件
$sbin/nginx -s reopen    // 重启 Nginx
$sbin/nginx -s stop     // 停止 Nginx
$
```

###### 配置
进入安装目录 `` $cd /opt/app/nginx-1.10.1 ``
nginx.conf 文件是nginx的主入口配置
<!--more-->
1.配置引入其他配置文件
```
$
$vi nginx.conf
http {
    ......


    include       conf.d/*.conf;     #导入配置文件
}
$
```

2.配置一个请求转发
在conf.d目录下创建test.conf 文件，内容为
```
$
$vi test.conf
server {
        listen       80;      #监听80端口
        server_name  test.com www.test.com;  #请求的域名， 多个用空格隔开

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_redirect off;
            proxy_set_header Host $host;    # 将请求的域名写入请求头
            proxy_set_header X-Real-IP $remote_addr;   # 将来源ip写入请求头
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    # 将来源ip写入请求头
            proxy_pass http://127.0.0.1:8080;    # 转发到目标地址
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
$
```

3.配置文件中的一些变量
```
$
$arg_PARAMETER      // 功能：如果在请求中设置了查询字符串，那么这个变量包含在查询字符串是GET请求PARAMETER中的值。
$args                // 功能：该变量的值是GET请求在请求行中的参数。
$binary_remote_addr  // 功能：二进制格式的客户端地址
$body_bytes_sent    // 功能：响应体的大小，即使发生了中断或者是放弃，也是一样的准确。
$content_length   // 功能：该变量的值等于请求头中的Content-length字段的值
$cookie_COOKIE   // 功能：该变量的值是cookie COOKIE的值
$document_root  // 功能：该变量的值为当前请求的location（http，server，location，location中的if）中root指令中指定的值。
$document_uri   // 功能：同$uri
$host           // 功能：该变量的值等于请求头中Host的值。如果Host无效时，那么就是处理该请求的server的名称。
                         在下列情况中，$host变量的取值不同于$http_host变量:
                            当请求头中的Host字段未指定（使用默认值）或者为空值，那么$host等于server_name指令指定的值。
                            当Host字段包含端口是，$host并不包含端口号。另外，从0.8.17之后的nginx中，$host的值总是小写。
$hostname              // 功能：有gethostname返回值设置机器名。
$http_HEADER           // 功能：该变量的值为HTTP 请求头HEADER，具体使用时会转换为小写，并且将“——”（破折号）转换为"_"(下划线)。
$is_args               // 功能： 如果设置了$args，那么值为“？”，否则为“”
$limit_rate            // 功能：该变量允许限制连接速率。
$nginx_version         // 功能： 当前运行的nginx的版本号
$query_string          // 功能：同$args
$remote_addr           // 功能：客户端的IP地址
$remote_user           // 功能： 该变量等于用户的名字，基本身份验证模块使用。
$remote_port           // 功能： 客户端连接端口
$request_filename      // 功能：该变量等于当前请求文件的路径，有指令root或者alias和URI构成。
$request_body          // 功能：该变量包含了请求体的主要信息。该变量与proxy_pass或者fastcgi_pass相关。
$request_body_file     // 功能：客户端请求体的临时文件。
$request_completion   // 功能：如果请求成功完成，那么显示“OK”。如果请求没有完成或者请求不是该请求系列的最后一部分，那么它的值为空。
$request_method     // 功能：该变量的值通常是GET或者POST。
$request_uri      // 功能：该变量的值等于原始的URI请求，就是说从客户端收到的参数包括了原始请求的URI，该值是不可以被修改的，不包含主机名，例如“/foo/bar.php?arg=baz”。
$scheme          // 功能：该变量表示HTTP scheme（例如HTTP，HTTPS），根据实际使用情况来决定，例如：rewrite  ^ $scheme://example.com$uri redirect;
$server_addr     // 功能：该变量的值等于服务器的地址。通常来说，在完成一次系统调用之后就会获取变量的值，为了避开系统钓鱼，那么必须在listen指令中使用bind参数。
$server_name     // 功能：该变量为server的名字。
$server)port     // 功能：该变量等于接收请求的端口。
$server_protocol // 功能：该变量的值为请求协议的值，通常是HTTP/1.0或者HTTP/1.1
$uri             // 功能：该变量的值等于当前请求中的URI（没有参数，不包括$args）的值。
                          它的值不同于request_uri，由浏览器客户端发送的request_uri的值。例如，可能会被内部重定向或者使用index。
			  另外需要注意：$uri不包含主机名，例如 "/foo/bar.html" 当前判断URL= $scheme://$server_name/$url
$
```
### 2016-11-11 14:17:10
4.配置一个负载均衡
在conf.d目录下创建test2.conf 文件，内容为
```
$
$vi test2.conf
    upstream test2{
      ip_hash;  #每个请求按访问ip地址的前三个段位信息的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
      #hash $request_uri;   #需要第三方插件，按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。 
      server 127.0.0.1:8080;
      server 127.0.0.1:8081 down;  #(down 表示单前的server暂时不参与负载) 
      server 127.0.0.1:8082 weight=2;  #(weight 默认为1.weight越大，负载的权重就越大)  
      server 127.0.0.1:8083 backup;  #(其它所有的非backup机器down或者忙的时候，请求backup机器) 
    }

    server {
        listen       80;      #监听80端口
        server_name  test2.com www.test2.com;  #请求的域名， 多个用空格隔开

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_redirect off;
            proxy_set_header Host $host;    # 将请求的域名写入请求头
            proxy_set_header X-Real-IP $remote_addr;   # 将来源ip写入请求头
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    # 将来源ip写入请求头
            proxy_pass http://test2;    # 转发到服务器组
        }

         location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|css)$ {
                expires 1d;   # 缓存一天
                proxy_pass http://test2; 
                #root  /opt/td-platform/current/webapp;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
$
```

5.配置php
在conf.d目录下创建test3.conf 文件，内容为
```
$
$vi test3.conf
    server {
        listen       80;
        server_name  test3.com www.test3.com;  #请求的域名， 多个用空格隔开
        root /home/php/htdocs; #php项目根目录，不是php的安装目录
        index           index.html index.htm index.php;   #  首页
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

       location / {
            if (!-e $request_filename){    #  出现必须地址/后必须跟index.php才能访问，这地方配置下转发，/自动转发到/index.php
                rewrite ^(.*)$ /index.php$1 last;
            }
       }

       location ~ .*\.php(\/.*)*$ {
            # root /home/php/htdocs; #php项目根目录，不是php的安装目录
            fastcgi_pass 127.0.0.1:9100;   #php-fpm.conf中listen的配置
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }

        # redirect server error pages to the static page /40x.html
        #
        error_page  404              /404.html;
        location = /40x.html {
        }

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
        }
   }
$
```

6.配置和优化
```
nginx指令中的优化（配置文件）

	worker_processes 8;
	　　nginx进程数，建议按照cpu数目来指定，一般为它的倍数。

	worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 10000000;
	　　为每个进程分配cpu，上例中将8个进程分配到8个cpu，当然可以写多个，或者将一个进程分配到多个cpu。

	worker_rlimit_nofile 102400;
	　　这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n的值保持一致。

	use epoll;
	　　使用epoll的I/O模型

	worker_connections 102400;
	　　每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为worker_processes*worker_connections。

	keepalive_timeout 60;
	　　keepalive超时时间。

	client_header_buffer_size 4k;
	　　客户端请求头部的缓冲区大小，这个可以根据你的系统分页大小来设置，一般一个请求的头部大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。分页大小可以用命令getconf PAGESIZE取得。

	open_file_cache max=102400 inactive=20s;
	　　这个将为打开文件指定缓存，默认是没有启用的，max指定缓存数量，建议和打开文件数一致，inactive是指经过多长时间文件没被请求后删除缓存。

	open_file_cache_valid 30s;
	　　这个是指多长时间检查一次缓存的有效信息。

	open_file_cache_min_uses 1;
	　　open_file_cache指令中的inactive参数时间内文件的最少使用次数，如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个文件在inactive时间内一次没被使用，它将被移除。
	内核参数的优化

	net.ipv4.tcp_max_tw_buckets = 6000
	　　timewait的数量，默认是180000。

	net.ipv4.ip_local_port_range = 1024    65000
	　　允许系统打开的端口范围。

	net.ipv4.tcp_tw_recycle = 1
	　　启用timewait快速回收。

	net.ipv4.tcp_tw_reuse = 1
	　　开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接。

	net.ipv4.tcp_syncookies = 1
	　　开启SYN Cookies，当出现SYN等待队列溢出时，启用cookies来处理。

	net.core.somaxconn = 262144
	web应用中listen函数的backlog默认会给我们内核参数的net.core.somaxconn限制到128，而nginx定义的NGX_LISTEN_BACKLOG默认为511，所以有必要调整这个值。
	也可以在nginx配置文件的linsten 80 后面加上这个backlog参数，但是不能超过内核里面设定的数值，如下：

	Linsten 80 backlog=65533;
	net.core.netdev_max_backlog = 262144
	　　每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许送到队列的数据包的最大数目。

	net.ipv4.tcp_max_orphans = 262144
	　　系统中最多有多少个TCP套接字不被关联到任何一个用户文件句柄上。如果超过这个数字，孤儿连接将即刻被复位并打印出警告信息。这个限制仅仅是为了防止简单的DoS攻击，不能过分依靠它或者人为地减小这个值，更应该增加这个值(如果增加了内存之后)。

	net.ipv4.tcp_max_syn_backlog = 262144
	　　记录的那些尚未收到客户端确认信息的连接请求的最大值。对于有128M内存的系统而言，缺省值是1024，小内存的系统则是128。

	net.ipv4.tcp_timestamps = 0
	　　时间戳可以避免序列号的卷绕。一个1Gbps的链路肯定会遇到以前用过的序列号。时间戳能够让内核接受这种”异常”的数据包。这里需要将其关掉。

	net.ipv4.tcp_synack_retries = 1
	　　为了打开对端的连接，内核需要发送一个SYN并附带一个回应前面一个SYN的ACK。也就是所谓三次握手中的第二次握手。这个设置决定了内核放弃连接之前发送SYN+ACK包的数量。

	net.ipv4.tcp_syn_retries = 1
	　　在内核放弃建立连接之前发送SYN包的数量。

	net.ipv4.tcp_fin_timeout = 1
	　　如果套接字由本端要求关闭，这个参数决定了它保持在FIN-WAIT-2状态的时间。对端可以出错并永远不关闭连接，甚至意外当机。缺省值是60秒。2.2 内核的通常值是180秒，你可以按这个设置，但要记住的是，即使你的机器是一个轻载的WEB服务器，也有因为大量的死套接字而内存溢出的风险，FIN- WAIT-2的危险性比FIN-WAIT-1要小，因为它最多只能吃掉1.5K内存，但是它们的生存期长些。

	net.ipv4.tcp_keepalive_time = 30
	　　当keepalive起用的时候，TCP发送keepalive消息的频度。缺省是2小时。

一个完整的内核优化配置

	net.ipv4.ip_forward = 0
	net.ipv4.conf.default.rp_filter = 1
	net.ipv4.conf.default.accept_source_route = 0
	kernel.sysrq = 0
	kernel.core_uses_pid = 1
	net.ipv4.tcp_syncookies = 1
	kernel.msgmnb = 65536
	kernel.msgmax = 65536
	kernel.shmmax = 68719476736
	kernel.shmall = 4294967296
	net.ipv4.tcp_max_tw_buckets = 6000
	net.ipv4.tcp_sack = 1
	net.ipv4.tcp_window_scaling = 1
	net.ipv4.tcp_rmem = 4096        87380   4194304
	net.ipv4.tcp_wmem = 4096        16384   4194304
	net.core.wmem_default = 8388608
	net.core.rmem_default = 8388608
	net.core.rmem_max = 16777216
	net.core.wmem_max = 16777216
	net.core.netdev_max_backlog = 262144
	net.core.somaxconn = 262144
	net.ipv4.tcp_max_orphans = 3276800
	net.ipv4.tcp_max_syn_backlog = 262144
	net.ipv4.tcp_timestamps = 0
	net.ipv4.tcp_synack_retries = 1
	net.ipv4.tcp_syn_retries = 1
	net.ipv4.tcp_tw_recycle = 1
	net.ipv4.tcp_tw_reuse = 1
	net.ipv4.tcp_mem = 94500000 915000000 927000000
	net.ipv4.tcp_fin_timeout = 1
	net.ipv4.tcp_keepalive_time = 30
	net.ipv4.ip_local_port_range = 1024    65000

一个简单的nginx优化配置文件

	user  www www;
	worker_processes 8;
	worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000;
	error_log  /www/log/nginx_error.log  crit;
	pid        /usr/local/nginx/nginx.pid;
	worker_rlimit_nofile 204800;

	events
	{
	  use epoll;
	  worker_connections 204800;
	}

	http
	{
	  include       mime.types;
	  default_type  application/octet-stream;

	  charset  utf-8;

	  server_names_hash_bucket_size 128;
	  client_header_buffer_size 2k;
	  large_client_header_buffers 4 4k;
	  client_max_body_size 8m;

	  sendfile on;
	  tcp_nopush     on;

	  keepalive_timeout 60;

	  fastcgi_cache_path /usr/local/nginx/fastcgi_cache levels=1:2
			keys_zone=TEST:10m
			inactive=5m;
	  fastcgi_connect_timeout 300;
	  fastcgi_send_timeout 300;
	  fastcgi_read_timeout 300;
	  fastcgi_buffer_size 16k;
	  fastcgi_buffers 16 16k;
	  fastcgi_busy_buffers_size 16k;
	  fastcgi_temp_file_write_size 16k;
	  fastcgi_cache TEST;
	  fastcgi_cache_valid 200 302 1h;
	  fastcgi_cache_valid 301 1d;
	  fastcgi_cache_valid any 1m;
	  fastcgi_cache_min_uses 1;
	  fastcgi_cache_use_stale error timeout invalid_header http_500;
	  
	  open_file_cache max=204800 inactive=20s;
	  open_file_cache_min_uses 1;
	  open_file_cache_valid 30s;
	  


	  tcp_nodelay on;
	  
	  gzip on;
	  gzip_min_length  1k;
	  gzip_buffers     4 16k;
	  gzip_http_version 1.0;
	  gzip_comp_level 2;
	  gzip_types       text/plain application/x-javascript text/css application/xml;
	  gzip_vary on;


	  server
	  {
	    listen       80 backlog=65533;
	    server_name  www.linuxyan.com;
	    index index.php index.htm;
	    root  /www/html/;

	    location /status
	    {
		stub_status on;
	    }

	    location ~ .*\.(php|php5)?$
	    {
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		include fcgi.conf;
	    }

	    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|css)$
	    {
	      expires      30d;
	    }

	    log_format  access  '$remote_addr - $remote_user [$time_local] "$request" '
		      '$status $body_bytes_sent "$http_referer" '
		      '"$http_user_agent" $http_x_forwarded_for';
	    access_log  /www/log/access.log  access;
	      }
	}

关于FastCGI的几个指令

	fastcgi_cache_path /usr/local/nginx/fastcgi_cache levels=1:2 keys_zone=TEST:10m inactive=5m;
	　　这个指令为FastCGI缓存指定一个路径，目录结构等级，关键字区域存储时间和非活动删除时间。

	fastcgi_connect_timeout 300;
	　　指定连接到后端FastCGI的超时时间。

	fastcgi_send_timeout 300;
	　　向FastCGI传送请求的超时时间，这个值是指已经完成两次握手后向FastCGI传送请求的超时时间。

	fastcgi_read_timeout 300;
	　　接收FastCGI应答的超时时间，这个值是指已经完成两次握手后接收FastCGI应答的超时时间。

	fastcgi_buffer_size 16k;
	　　指定读取FastCGI应答第一部分需要用多大的缓冲区，这里可以设置为fastcgi_buffers指令指定的缓冲区大小，上面的指令指定它将使用1个16k的缓冲区去读取应答的第一部分，即应答头，其实这个应答头一般情况下都很小（不会超过1k），但是你如果在fastcgi_buffers指令中指定了缓冲区的大小，那么它也会分配一个fastcgi_buffers指定的缓冲区大小去缓存。

	fastcgi_buffers 16 16k;
	　　指定本地需要用多少和多大的缓冲区来缓冲FastCGI的应答，如上所示，如果一个php脚本所产生的页面大小为256k，则会为其分配16个16k的缓冲区来缓存，如果大于256k，增大于256k的部分会缓存到fastcgi_temp指定的路径中，当然这对服务器负载来说是不明智的方案，因为内存中处理数据速度要快于硬盘，通常这个值的设置应该选择一个你的站点中的php脚本所产生的页面大小的中间值，比如你的站点大部分脚本所产生的页面大小为256k就可以把这个值设置为16 16k，或者4 64k 或者64 4k，但很显然，后两种并不是好的设置方法，因为如果产生的页面只有32k，如果用4 64k它会分配1个64k的缓冲区去缓存，而如果使用64 4k它会分配8个4k的缓冲区去缓存，而如果使用16 16k则它会分配2个16k去缓存页面，这样看起来似乎更加合理。

	fastcgi_busy_buffers_size 32k;
	　　这个指令我也不知道是做什么用，只知道默认值是fastcgi_buffers的两倍。

	fastcgi_temp_file_write_size 32k;
	　　在写入fastcgi_temp_path时将用多大的数据块，默认值是fastcgi_buffers的两倍。

	fastcgi_cache TEST
	　　开启FastCGI缓存并且为其制定一个名称。个人感觉开启缓存非常有用，可以有效降低CPU负载，并且防止502错误。但是这个缓存会引起很多问题，因为它缓存的是动态页面。具体使用还需根据自己的需求。

	fastcgi_cache_valid 200 302 1h;
	fastcgi_cache_valid 301 1d;
	fastcgi_cache_valid any 1m;
	　　为指定的应答代码指定缓存时间，如上例中将200，302应答缓存一小时，301应答缓存1天，其他为1分钟。

	fastcgi_cache_min_uses 1;
	　　缓存在fastcgi_cache_path指令inactive参数值时间内的最少使用次数，如上例，如果在5分钟内某文件1次也没有被使用，那么这个文件将被移除。

	fastcgi_cache_use_stale error timeout invalid_header http_500;
	　　不知道这个参数的作用，猜想应该是让nginx知道哪些类型的缓存是没用的。 以上为nginx中FastCGI相关参数，另外，FastCGI自身也有一些配置需要进行优化，如果你使用php-fpm来管理FastCGI，可以修改配置文件中的以下值：

	60
	　　同时处理的并发请求数，即它将开启最多60个子线程来处理并发连接。

	102400
	　　最多打开文件数。

	204800
	　　每个进程在重置之前能够执行的最多请求数。
```