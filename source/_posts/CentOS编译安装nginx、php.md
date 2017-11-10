---
title: CentOS编译安装nginx、php
date: 2017-11-03 22:11:12
tags: Centos编译安装ngins,php
---

安装服务器初始时系统级库
yum install gcc
yum install gcc-c++
yum install make
yum install cmake
yum install autoconf
yum install automake
yum install libtool
yum install re2c
yum install flex
yum install bison
## **安装nginx**
###**1：安装nginx需要的依赖：**
rewrite模块需要安装pcre库，yum install pcre pcre-devel
gzip模块需要安装zlib库，yum install zlib zlib-devel
ssl模块需要安装openssl库，yum install openssl openssl-devel
下载源码
http://nginx.org/en/download.html
./configure --prefix=/home/zhangsan/software/nginx --user=zhangsan --group=zhangsan --with-http_ssl_module
make
make install
###**2：配置nginx**
进入nginx配置目录cd /home/zhangsan/software/nginx/conf
修改nginx主配置文件加载自建目录下的所有配置文件vim nginx.conf
再http括号底部加上include 指定路径/具体文件或*;
配置文件示例：
server { 
    listen 80; 
    server_name www.test.com; 
    root /home/zhangsan/webroot/zhangsan; 
    if (!-e $request_filename) { 
        rewrite ^/(.*)$ /index.php last; 
    } 
    index index.php index.html index.htm; 
    location ~ \.php$ { 
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_index index.php; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; 
        include fastcgi_params; 
    } 
}
nginx参数优化（todo）
http{}内增加 client_max_body_size 8M;
启动nginx
sudo /home/zhangsan/software/nginx/sbin/nginx
重启nginx
##**安装php**
###**1：安装php需要的依赖：**
yum install libxml2 libxml2-devel
yum install libcurl libcurl-devel
yum install libpng libpng-devel
yum install libjpeg libjpeg-devel
yum install freetype freetype-devel
yum install libmcrypt libmcrypt-devel
yum install libicu libicu-devel
yum install openldap openldap-devel
注：安装libmcrypt libmcrypt-devel的时候可能出现找不到的情况，这时候需要添加额外源然后再安装
        yum install epel-release
        yum update
下载源码
http://php.net/downloads.php
./configure \
--prefix=/home/zhangsan/software/php \
--with-libdir=lib64 \
--enable-fpm \
--with-fpm-user=zhangsan \
--with-fpm-group=zhangsan \
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--enable-opcache \
--enable-pcntl \
--enable-mbstring \
--enable-sockets \
--enable-soap \
--enable-zip \
--enable-calendar \
--enable-bcmath \
--enable-intl \
--with-openssl \
--with-zlib \
--with-curl \
--with-gd \
--with-zlib-dir=/usr/lib64 \
--with-png-dir=/usr/lib64 \
--with-jpeg-dir=/usr/lib64 \
--with-gettext \
--with-xmlrpc \
--with-mhash \
--with-mcrypt \
--with-ldap
make
make install
cp php.ini-production /home/zhangsan/software/php/lib/php.ini
配置php-fpm
进到/home/zhangsan/software/php/etc
cp php-fpm.conf.default php-fpm.conf 复制所需的配置文件
进到/home/zhangsan/software/php/etc/php-fpm.d
cp www.conf.default www.conf    复制所需的配置文件
php参数优化（todo）
###**2：修改php.ini:**
cgi.fix_pathinfo=0    //如果文件不存在，则阻止 Nginx 将请求发送到后端的 PHP-FPM 模块， 以避免遭受恶意脚本注入的攻击
post_max_size = 8M
upload_max_filesize = 6M
启动php-fpm
sudo /home/zhangsan/software/php/sbin/php-fpm
关闭php-fpm
kill -INT 端口号
重启php-fpm
kill -USR2 端口号
