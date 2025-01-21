---
title: nginx 反向代理 onlyoffice
published: 2025-01-21
description: 解决onlyoffice 在项目中的跨域问题
tags: [docker]
category: Linux
draft: false
---

.

```yaml
---
title: nginx 反向代理 onlyoffice window的docker desktop环境下
published: 2025-01-21

---

```

### docker 启动 nginx
```docker
一开始挂载你没配置文件，需起个临时的的nging 把里面的配置文件拿出来
docker run --name nginx --restart=always -d -p 80:80 -d -p 443:443 -v D:\learn\nginx\static:/usr/share/nginx/html -v D:\learn\nginx\logs:/var/log/nginx/ nginx:latest

docker nginx2:/etc/nginx D:\learn\nginx\conf

docker rm nginx2

挂载启动nginx
docker run --name nginx --restart=always -d -p 80:80 -d -p 443:443 -v D:\learn\nginx\static:/usr/share/nginx/html -v D:\learn\nginx\conf:/etc/nginx -v D:\learn\nginx\logs:/var/log/nginx/ nginx:latest

检查是否启动正常，访问http://ip:80

如有问题，检查挂载配置是否齐全，具体查看日志
docker logs -f nginx

如果没提示 检查 var/log/nginx下日志
docker exec -it nginx
```
### 修改nginx.conf
```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;  # 监听端口
        server_name  ip地址或域名;  # 服务器名称

        

        # 默认 Location 块
        
        # 错误页面配置
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }

        location /onlyoffice/ {
            proxy_pass http://ip:8180/; #注意不要welcome 会影响nacos
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # WebSocket 支持
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            # 添加 CORS 头
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
            add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';

            # 处理预检请求
            if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain; charset=utf-8';
                add_header 'Content-Length' 0;
                return 204;
            }
        }
    }

}

```

### 启动 onlyoffice
```
docker run -i -t -d -p 8180:80 -v D:\learn\onlyoffice\logs:/var/log/onlyoffice -v D:\learn\onlyoffice\data:/var/www/onlyoffice/Data --restart=always onlyoffice/documentserver

```

### 修改onlyoffice default.json 去除过滤
```
vim /etc/onlyoffice/documentserver/default.json

supervisorctl start all #启动所有服务包括example

```

### 检查是否onlyoffice启动正常 http://ip:8180 
### 检查nginx代理是否成功 http:ip/onlyoffice/welcome 是否正常 

### nacos配置 不具备通用性
```yaml
# openOffice:
#   openHomeWindows: C:\\Program Files (x86)\\OpenOffice 4
#   openHomeLinux: /opt/openoffice4
#   htmlHome: D:\\pdf2htmlEX\\pdf2htmlEX.exe
#   #filetype: doc,docx,ppt,pptx,pdf,xls,xlsx,txt
#   filetype: xls,xlsx
#   portNumber: 8100
# aspose-word:
#   #容器内中文字体包路径
#   zhPath: /home/go/fonts/chinese
libreOffice:
  portNumber: 8180
  go:
    path: /home/upload/resources/
  serverUrl: tcp://ip:2375
  dockerName: libreoffice
  timeout: 600
  filetype: doc,docx,ppt,pptx,xls,xlsx
onlyOffice:
  internet:
    urls:
      - "http://ip/onlyoffice" #nginx映射地址 前端通过网关服务 找这个地址，解决跨域
  gateway:
    ip: ip
    port: 8081
    http: http
  intranet:
    urls: 
      - "http://ip1/onlyoffice"
      - "http://ip2/onlyoffice" 
```



