---
title: "前端（Web）IO 吞吐"
description: "proxy_cache配置"
date: 2026-10-29T13:38:08+08:00
---
  Nginx's I/O throughput


# 一、安装
OS: Dianbian 12 (SSH Server + Standard system utilities)

## install
```
sudo apt update
sudo apt install nginx
```
default version:`1.22.1`
### upgrade
```
sudo apt update
sudo apt full-upgrade
```
You need to complete the following series of operations first.
#### Install the prerequisites
```
sudo apt install curl gnupg2 ca-certificates lsb-release debian-archive-keyring
```
#### official nginx signing key
- Fetch the key
```
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
```
- stable
```
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
https://nginx.org/packages/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

# 二、配置
The key configurations to remember:
- proxy_cache_path: Define cache storage, size limits, and shared memory zones
- proxy_cache_valid: Set TTL per HTTP status code
- proxy_ignore_headers: Override backend cache control for consistent caching
- proxy_cache_lock: Prevent thundering herd on cache misses
- proxy_cache_use_stale: Serve stale content during updates and failures
- proxy_cache_background_update: Update cache without user-visible latency

### startup
Once configuration is complete, start Nginx:
```
sudo systemctl enable --now nginx
```
config file: `/etc/nginx/nginx.conf`

### Defining the Cache Zone
The proxy_cache_path directive defines where NGINX stores cached content and how the cache is managed:
```
proxy_cache_path /var/cache/nginx/proxy_cache
    levels=1:2
    keys_zone=my_cache:10m
    max_size=1g
    inactive=60m
    use_temp_path=off;
```
- `/var/cache/nginx/proxy_cache`: The filesystem path where cached content is stored. Create this directory with proper permissions:
```
mkdir -p /var/cache/nginx/proxy_cache
chown nginx:nginx /var/cache/nginx/proxy_cache
```

### Complete Production Configuration
```
# Cache path definition (place in http context)
proxy_cache_path /var/cache/nginx/proxy_cache
    levels=1:2
    keys_zone=main_cache:100m
    max_size=10g
    inactive=24h
    use_temp_path=off;

# Microcache for API responses
proxy_cache_path /var/cache/nginx/api_cache
    levels=1:2
    keys_zone=api_cache:10m
    max_size=1g
    inactive=10m
    use_temp_path=off;

upstream backend {
    server 127.0.0.1:8080;
    keepalive 32;
}

server {
    listen 80;
    server_name example.com;

    # Static assets - long cache
    location /static {
        proxy_pass http://backend;
        proxy_cache main_cache;
        proxy_cache_valid 200 7d;
        proxy_cache_valid 404 1m;

        proxy_cache_use_stale error timeout http_500 http_502 http_503 http_504;

        add_header X-Cache-Status $upstream_cache_status always;
    }

    # API endpoints - microcaching
    location /api {
        proxy_pass http://backend;
        proxy_cache api_cache;
        proxy_cache_valid 200 1s;

        proxy_ignore_headers Cache-Control Expires Set-Cookie;
        proxy_cache_lock on;
        proxy_cache_lock_timeout 3s;

        # Bypass cache for authenticated requests
        proxy_cache_bypass $http_authorization;
        proxy_no_cache $http_authorization;

        add_header X-Cache-Status $upstream_cache_status always;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # Default - standard caching with stale-while-revalidate
    location / {
        proxy_pass http://backend;
        proxy_cache main_cache;
        proxy_cache_valid 200 5m;
        proxy_cache_valid 301 302 1h;
        proxy_cache_valid 404 1m;

        proxy_ignore_headers Cache-Control Expires;

        proxy_cache_use_stale updating error timeout http_500 http_502 http_503 http_504;
        proxy_cache_background_update on;
        proxy_cache_lock on;

        # Bypass for logged-in users
        proxy_cache_bypass $cookie_session;
        proxy_no_cache $cookie_session;

        add_header X-Cache-Status $upstream_cache_status always;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # Upstream connection keepalive
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }
}
```

## Scenarios
- Ignoring Backend Cache Headers
- Microcaching: Caching Dynamic Content
- Thundering Herd Prevention with proxy_cache_lock
- Stale-While-Revalidate Pattern
- Cache Bypass and Conditional Caching
- Bypassing Cache for Logged-In Users
- Cache Key Configuration
- Minimum Uses Before Caching

### Ignoring Backend Cache Headers
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 10m;

    # Ignore backend cache control headers
    proxy_ignore_headers Cache-Control Expires;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Microcaching: Caching Dynamic Content
```
location /api {
    proxy_pass http://backend;
    proxy_cache my_cache;

    # Cache for just 1 second
    proxy_cache_valid 200 1s;

    # Ignore backend cache headers (critical for microcaching)
    proxy_ignore_headers Cache-Control Expires Set-Cookie;

    # Prevent thundering herd
    proxy_cache_lock on;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Thundering Herd Prevention with proxy_cache_lock
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 10m;

    # Only one request fetches from backend
    proxy_cache_lock on;

    # How long to wait for lock before fetching anyway
    proxy_cache_lock_timeout 5s;

    # How long a request can hold the lock
    proxy_cache_lock_age 5s;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Stale-While-Revalidate Pattern
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 5m;

    # Serve stale content while updating
    proxy_cache_use_stale updating error timeout http_500 http_502 http_503 http_504;

    # Update cache in background subrequest
    proxy_cache_background_update on;

    # Prevent thundering herd
    proxy_cache_lock on;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Cache Bypass and Conditional Caching
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 10m;

    # Bypass cache for requests with nocache cookie or argument
    proxy_cache_bypass $cookie_nocache $arg_nocache;

    # Don't store response in cache for these requests
    proxy_no_cache $cookie_nocache $arg_nocache;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Bypassing Cache for Logged-In Users
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 10m;

    # Bypass cache if session cookie exists
    proxy_cache_bypass $cookie_session $cookie_PHPSESSID;
    proxy_no_cache $cookie_session $cookie_PHPSESSID;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Cache Key Configuration
```
location / {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 10m;

    # Default cache key
    proxy_cache_key $scheme$proxy_host$request_uri;

    add_header X-Cache-Status $upstream_cache_status always;
}
```
### Minimum Uses Before Caching
```
location /assets {
    proxy_pass http://backend;
    proxy_cache my_cache;
    proxy_cache_valid 200 1h;

    # Only cache after 3 requests
    proxy_cache_min_uses 3;

    add_header X-Cache-Status $upstream_cache_status always;
}
```

# Ref
- [nginx: Linux packages](https://nginx.org/en/linux_packages.html)
- [NGINX Proxy Cache & Microcaching: Complete Guide](https://www.getpagespeed.com/server-setup/nginx/nginx-proxy-cache-microcaching)
