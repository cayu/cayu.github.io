---
title: "Nginx"
date: 2019-05-15T17:59:13-03:00
---

Nginx, es un servidor web open source, tiene muchos módulos como por ejemplo para implementar un proxy reverso, cache de HTTP, y balanceador de carga.

## Proxy Reverso

nginx.conf

```ini>
site.cayu.com.ar
<code ini>
server {
  listen 80;
  listen [::]:80;

  server_name	owncloud.cayu.com.ar;

  access_log /var/log/nginx/owncloud-access.log;
  error_log /var/log/nginx/owncloud-error.log;


  location / {
		proxy_pass http://10.1.1.222;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $host;
		proxy_set_header X-Forwarded-Proto $scheme;
  }
}

server {
    listen 443;
    listen [::]:443;

    server_name	owncloud.cayu.com.ar;

    ssl_certificate           /etc/nginx/ssl/cayu.crt;
    ssl_certificate_key       /etc/nginx/ssl/cayu.key;

    access_log /var/log/nginx/owncloud-ssl-access.log;
    error_log /var/log/nginx/owncloud-ssl-error.log;

    ssl on;
    ssl_session_cache  builtin:1000  shared:SSL:10m;
    ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

  location / {
		proxy_pass https://10.1.1.222;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $host;
		proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
