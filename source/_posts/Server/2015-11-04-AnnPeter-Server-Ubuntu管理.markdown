---
layout: post
title: "AnnPeter-Server-Ubuntu管理"
date: 2015-11-04 13:50:30 +0530
comments: true
categories: Server
keywords: Server,Ubuntu,博客,AnnPeter's Blog
description: "AnnPeter-Server-Ubuntu管理"

---

本博文是我在阿里云服务器搭建的基础过程和相关配置，留作后用。
<!-- more -->

##Nginx,端口80
1. /var/logs/nginx  	日志文件
2. /www   				访问目录
3. /usr/local/nginx  	配置文件
```vim
user  www-data;
worker_processes  2;

error_log  /var/log/nginx/error.log;
error_log  /var/log/nginx/error.log  notice;
error_log  /var/log/nginx/error.log  info;

pid        /var/log/nginx/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    charset utf-8;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;

    keepalive_timeout  65;

    client_max_body_size 5m;

    gzip on; #开启gzip压缩输出
    gzip_min_length 1k; #最小压缩文件大小
    gzip_buffers 4 16k; #压缩缓冲区
    gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    gzip_comp_level 2; #压缩等级
    gzip_types text/plain application/x-javascript text/css application/xml;
    #压缩类型，默认就已经包含textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    gzip_vary on;

    server {
        listen		80;
        server_name dev.itpeter.cn;

        location / {
            root /www;
            autoindex on;
            autoindex_exact_size off;
            autoindex_localtime on;

            fancyindex on;
            fancyindex_header /.header.html;
            fancyindex_footer /.footer.html;

            auth_basic "Password";
            auth_basic_user_file /usr/local/nginx/conf/dev_htpasswd;
        }
    }

    server {
        listen 81;
        server_name www.itpeter.cn;

        location ^~ /static {
            root /www/chwlzzj;
        }

        location /{
            include uwsgi_params;
            uwsgi_pass 127.0.0.1:8888;
        }
    }

    server {
        listen       80;
        server_name  www.itpeter.cn;

        location ^~ /static {
            root /www/annpeter.cn;
        }

        location ^~ /upload {
            autoindex on;
            autoindex_exact_size on;
            autoindex_localtime on;
            root /www/annpeter.cn;
        }

        location  / {
            include uwsgi_params;
            uwsgi_pass			127.0.0.1:8800;
        }
    }


    server {
        listen       80;
        server_name  svn.itpeter.cn;

        location ^~ /mobilesafe {
            proxy_pass          http://localhost:8000;
            #proxy_set_header    Host             $host:80;
            proxy_set_header    X-Real-IP        $remote_addr;
            proxy_set_header    X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_set_header    Via              "nginx";
        }

        location ^~ /annpeter.cn {
            proxy_pass          http://localhost:9999;
            proxy_set_header    X-Real-IP        $remote_addr;
            proxy_set_header    X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_set_header    Via              "nginx";
        }
    }

}
```



##Apache，端口8000
> /etc/apache2/apache2.conf 	配置文件

##Django
>$ sudo apt-get install python-pip<br>
>$ sudo pip install Django==1.8.5<br>
>$ sudo apt-get install python-mysqldb<br>
><br>
>//Django搭建完成后，要启动供外网访问，启动方式<br>
>$ python manage.py runserver 0.0.0.0:8800<br>
><br>
><br>
>$ pip install uwsgi	//安装uwsgi<br>
>新建test.py文件，内容如下：<br>
>def application(env, start_response):<br>
>        start_response('200 OK', [('Content-Type','text/html')])<br>
>        return "Hello World"<br>
>$ apt-get install build-essential python-dev<br>
>$ uwsgi --http :8001 --wsgi-file test.py  //检查uwsgi是否安装成功<br>
><br>
><br>
>编写wsgi.py,放入Django项目根目录<br>
># -*-coding:utf-8 -*-<br>
>import os,sys<br>
>sys.path.append(os.path.dirname(os.path.dirname(__file__)))<br>
>os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings")<br>
>from django.core.wsgi import get_wsgi_application<br>
>application = get_wsgi_application()<br>
><br>
>编写uwsgi8800.xml，放入etc目录下<br>
><uwsgi><br>
>     <socket>:8800</socket><br>
>     <chdir>/www/annpeter.cn</chdir><br>
>     <module>wsgi</module><br>
>     <processes>4</processes> <!-- 进程数 --><br>
>     <daemonize>/var/log/uwsgi8800.log</daemonize><br>
></uwsgi><br>
><br>
>运行$ uwsgi -x /etc/uwsgi8800.xml启动web服务<br>
><br>
><br>
>然后再nginx.conf中配置转发<br>
>location  / {<br>
>	include uwsgi_params;<br>
>	uwsgi_pass          127.0.0.1:8800;<br>
>}<br>

##防火墙
>允许22、80、3306端口的数据<br>
>$ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT<br>
>$ sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT<br>
>$ sudo iptables -A INPUT -p tcp --dport 3306 -j ACCEPT<br>
><br>
>允许loopback数据<br>
>$ sudo iptables -I INPUT 1 -i lo -j ACCEPT<br>
><br>
>阻止其它数据<br>
>$ sudo iptables -P INPUT DROP<br>
><br>
>查看设置好的规则<br>
>$ sudo iptables -L --line-numbers<br>
