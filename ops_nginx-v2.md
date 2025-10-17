---
title: "前端（Web）负载均衡"
description: "本系列包括架设Real Server和Keepalived Server、Keepalive安装配置"
date: 2022-10-06T08:38:08+08:00
---
  Nginx's load balancing


# 一、安装
OS: RHEL Community Version(Rocky8/Alma8) minimal（安装时的选项）

## 0. prepare
```bash
#dnf -y install gcc gcc-c++ automake make pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

see [update openssl](https://tdtc-hrb.github.io/cnblogs/post/ops_openssl/)

### Library devel
```bash
$sudo dnf install gd-devel libxml2-devel libxslt-devel
```

#### GeoIP
  Download latest [atomic-release](http://www6.atomicorp.com/channels/atomic/centos/8/x86_64/RPMS/)    
Install atomic-release rpm:
```bash
$sudo rpm -Uvh http://www6.atomicorp.com/channels/atomic/centos/8/x86_64/RPMS/atomic-release-1.0-23.el8.art.noarch.rpm
```
Install GeoIP-devel rpm package:
```bash
$sudo dnf install GeoIP-devel
```

### allow fw
```bash
$sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
$sudo firewall-cmd --reload
```


## installation
First, install Nginx of centos [repo]((https://nginx.org/en/linux_packages.html#RHEL));    
Then, replace the nginx program file.    
Note: The replaced program file version must be the same as the repo!

/etc/yum.repos.d/nginx.repo:
```xml
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```
install:
```bash
$sudo dnf install -y nginx
```


### 1) down
```bash
$curl --progress https://nginx.org/download/nginx-1.22.0.tar.gz | tar xz
```

### 2) setup module
see arguments:
```bash
$nginx -V
```

- GD lib
```bash
--with-http_image_filter_module=dynamic
```
- libxml2/libxslt
```bash
--with-http_xslt_module=dynamic
```
- GeoIP library
```bash
--with-http_geoip_module=dynamic
```

#### param list
```bash
./configure --prefix=/etc/nginx \
 --sbin-path=/usr/sbin/nginx \
 --modules-path=/usr/lib64/nginx/modules \
 --conf-path=/etc/nginx/nginx.conf \
 --error-log-path=/var/log/nginx/error.log \
 --http-log-path=/var/log/nginx/access.log \
 --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock \
 --http-client-body-temp-path=/var/cache/nginx/client_temp \
 --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
 --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
 --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
 --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
 --user=nginx \
 --group=nginx \
 --with-compat \
 --with-file-aio \
 --with-threads \
 --with-http_addition_module \
 --with-http_auth_request_module \
 --with-http_dav_module \
 --with-http_flv_module \
 --with-http_gunzip_module \
 --with-http_gzip_static_module \
 --with-http_mp4_module \
 --with-http_random_index_module \
 --with-http_realip_module \
 --with-http_secure_link_module \
 --with-http_slice_module \
 --with-http_ssl_module \
 --with-http_stub_status_module \
 --with-http_sub_module \
 --with-http_v2_module \
 --with-mail \
 --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-http_image_filter_module=dynamic --with-http_xslt_module=dynamic --with-http_geoip_module=dynamic --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'
```

### 3) Gen program
```bash
$make
```

Copy nginx and all .so files in the objs folder.
#### main program
```bash
$sudo cp -rfp objs/nginx /usr/sbin/
```

#### all so file
```bash
$sudo cp objs/*.so /usr/lib64/nginx/modules/
```


## run
- start
```bash
$sudo /usr/sbin/nginx
```

- restart
```bash
$sudo /usr/sbin/nginx -s reload
```


# 二、配置
libs path:
```
/usr/lib64/nginx/
```

## 0. load lib
> /etc/nginx/nginx.conf
add add below in the main context:
```bash
load_module "modules/ngx_http_geoip_module.so";
load_module "modules/ngx_http_image_filter_module.so";
load_module "modules/ngx_http_xslt_filter_module.so";
```

## 1. load balancing
Official website recommended [configuration](https://nginx.org/en/docs/http/load_balancing.html) changes
### upstream
> /etc/nginx/nginx.conf
```bash
http {
    upstream localhost {
        server 192.168.0.122:8080 weight=6; ## local
        server 192.168.0.127:8080 weight=3;
    }
}
```

### proxy
> /etc/nginx/conf.d/default.conf
```bash
server {
    listen 80;

    location / {
        proxy_connect_timeout 3;
        proxy_send_timeout 30;
        proxy_read_timeout 30;
        proxy_pass http://localhost;
    }
}
```


# Reference
- [nginx - load balancing](https://tdtc-hrb.github.io/csdn/post/ops_nginx/)
