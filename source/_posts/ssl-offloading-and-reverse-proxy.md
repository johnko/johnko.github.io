---
title: Using NGINX for SSL Offloading and Reverse Proxy
date: 2014-10-25
tags: [ ssl, openssl, nginx, proxy, reverse-proxy ]
---

Sources: [http://nginx.com/blog/nginx-ssl/][1] , [http://nginx.com/resources/admin-guide/reverse-proxy/][2]

[1]: http://nginx.com/blog/nginx-ssl/
[2]: http://nginx.com/resources/admin-guide/reverse-proxy/

I needed to redirect a path served by a web browser over SSL to another server.

For example: https://domain.local/oldpath/ should go to the old server and https://domain.local/newpath/ should go to the new server.

NGINX has both these capabilities.

Here's my stripped `nginx.conf`:

```
http {
    upstream oldserver {
        server 192.168.255.3:443;
    }
    upstream newserver {
        server 192.168.255.7:443;
    }
    server {
        listen 443 ssl;
        server_name         domain.local;
        ssl_certificate     bundle.crt;
        ssl_certificate_key private.key;
        location /oldpath/ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass https://oldserver/oldpath/;
        }
        location /newpath/ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass https://newserver/newpath/;
        }
    }
}
```

This is actually a little scary. Who knows how may SSL proxy servers are deployed that intercept traffic everyday? Great Firewall of China?
