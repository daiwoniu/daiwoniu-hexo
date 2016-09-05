---
title: nginx随笔
tags:
  - nginx
  - 配置
  - 随笔
date: 2016-09-05 16:14:10
updated: 2016-09-05 16:14:10
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
