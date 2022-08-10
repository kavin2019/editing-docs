# 转发配置的Demo


/etc/nginx/nginx.conf
```javascript
user nobody;
worker_processes 8;

events {
    worker_connections  1024;
}

http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;
#    server {                                                                      
#        listen 80;                                                                
#        server_name localhost;                                                    
#        location / {                                                              
#            root html;                                                            
#            index index.html index.htm;                                           
#        }                                                                         
#        error_page 500 502 503 504 /50x.html;                                     
#        location = /50x.html {                                                    
#            root html;                                                            
#        }                                                                         
#    }                                                                             
    include /etc/nginx/conf.d/*.conf;
}

```
/etc/nginx/conf.d/000.conf
```javascript
server {
  listen 80 default_server;
  server_name yaboivip.com;

  location / {
     proxy_set_header Host $http_host;               
     proxy_set_header X-Real-Ip $remote_addr;
     proxy_set_header REMOTE-HOST $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     #proxy_set_header Upgrade $http upgrade; #这两句是支持websocket时需要开启的
     #proxy_set_header Connection "upgrade"; 
     proxy_pass http://127.0.0.1:7001;
  }
}

```
