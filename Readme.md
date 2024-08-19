# Nginx Cache Purging with `ngx_cache_purge` Module

## Overview

This repository provides an example of how to set up Nginx with the `ngx_cache_purge` module to cache images and allow selective purging of cached files via a specific URL. The flow demonstrates how to configure Nginx to cache images and remove them from the cache on demand.

## Setup

### 1. Build and Start the Docker Containers

```sh
docker-compose up --build -d
```

### 2. Cache image 

To cache image you will need to make two sequential request from browser to cache image. As an indicator of caching will be 
```sh
x-cache-status
``` 
of response, HINT or MISS

```sh
curl -I "http://localhost/2.jpg" 

HTTP/1.1 200 OK
Server: nginx/1.27.0
Date: Mon, 19 Aug 2024 20:02:14 GMT
Content-Type: image/jpeg
Content-Length: 614880
Connection: keep-alive
Last-Modified: Mon, 19 Aug 2024 19:50:16 GMT
ETag: "66c3a1f8-961e0"
Accept-Ranges: bytes
X-Cache-Status: MISS
test: /2.jpg
```
On the third request we will get "HIT" of our cache status: 
```sh
curl -I "http://localhost/2.jpg"

HTTP/1.1 200 OK
Server: nginx/1.27.0
Date: Mon, 19 Aug 2024 20:03:41 GMT
Content-Type: image/jpeg
Content-Length: 614880
Connection: keep-alive
Last-Modified: Mon, 19 Aug 2024 19:50:16 GMT
ETag: "66c3a1f8-961e0"
X-Cache-Status: HIT
test: /2.jpg
Accept-Ranges: bytes
```

### 3. Purge image 

To purge exact image, we should make the request to '/purge/img.jpg' route 

```sh
curl -I "http://localhost/purge/2.jpg"

HTTP/1.1 200 OK
Server: nginx/1.27.0
Date: Mon, 19 Aug 2024 20:05:55 GMT
Content-Type: text/html
Content-Length: 147
Connection: keep-alive
test_purge: /2.jpg
```

as a result next request for image will get MISS cache-status in response, which means that our cache was successfully purged. Also additionally you can check the folder with the nginx cache. After succeeded purge we won't see any custom cached keys
```sh
docker exec -it custom_nginx /bin/sh
ls -R /var/cache/nginx
```



