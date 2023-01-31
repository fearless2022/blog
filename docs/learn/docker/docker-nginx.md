## 拉取镜像

* ```bash
  docker pull nginx
  ```

## 运行一个nginx容器，并映射本地相关文件目录

* ```bash
  # 本地创建相关目录,我这里放到D盘Docker文件下，方便我管理
  nginx   # 主目录
  nginx/logs # 创建日志目录
  nginx/html # 站点存放目录
  nginx/conf.d # 相关站点配置目录
  nginx/cert # 配置证书存储目录
   
  # 运行有一个容器
  docker run --name nginx-test  -p 80:80  -d nginx   # 运行容器
  # 进入到nginx-test容器中查看需要挂载的文件
  docker exec -it nginx-test /bin/bash
  # 拷贝一些常用的nginx文件到上面创建的目录中，与本地相映射
  docker cp nginx-test:/etc/nginx/nginx.conf D:\File\ProjectFile\Resource\nginx   # nginx 默认配置文件
  docker cp nginx-test:/etc/nginx/conf.d D:\File\ProjectFile\Resource\nginx    # nginx配置文件夹
  docker cp nginx-test:/usr/share/nginx/html D:\File\ProjectFile\Resource\nginx\html   # nginx的html文件
  ```

## 停止并删除nginx-test容器，重新运行一个新的nginx容器启动时，映射本地相关目录

* ```bash
  # 停止nginx-test容器
  docker stop nginx-test
   
  # 删除nginx-test容器
  docker rm nginx-test
   
  # 创建一个新 nginx 容器
  docker run --name nginx -d -p 80:80 -v D:\File\ProjectFile\Resource\nginx\nginx.conf:/etc/nginx/nginx.conf -v D:\File\ProjectFile\Resource\nginx\conf.d:/etc/nginx/conf.d -v D:\File\ProjectFile\Resource\nginx\html:/usr/share/nginx/html -v D:\File\ProjectFile\Resource\nginx\cert:/etc/nginx/cert -v D:\File\ProjectFile\Resource\nginx\logs:/var/log/nginx -e LANG=C.UTF-8 -e LC_ALL=C.UTF-8 --privileged=true nginx
  ```

## nginx.conf

* ```bash
  server
  {
  listen 80;
  server_name 47.108.248.203;
  index index.html;
  root  /usr/share/nginx/html/fearless-admin/dist;  #dist上传的路径
  
  # 避免访问出现 404 错误
  location / {
  try_files $uri $uri/ @router;
  index  index.html;
  }
  
  location @router {
  rewrite ^.*$ /index.html last;
  }
  
  # 接口
  location /api {
  proxy_pass http://47.108.248.203:8000;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_set_header X-Forwarded-Port $server_port;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  }
  
  # 授权接口													
  location /auth {
  proxy_pass http://47.108.248.203:8000;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_set_header X-Forwarded-Port $server_port;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  }
  
  # WebSocket 服务
  location /webSocket {
  proxy_redirect off;
  proxy_pass http://47.108.248.203:8000/webSocket;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  proxy_set_header Host $http_host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_connect_timeout 60s;
  proxy_read_timeout 86400s;
  proxy_send_timeout 60s;
  }
  
  # 头像
  location /avatar {
  proxy_pass http://47.108.248.203:8000;
  }
  
  # 文件
  location /file {
  proxy_pass http://47.108.248.203:8000;
  }
  
  # swagger
  location ~^/swagger/(.*)
  {
  proxy_redirect off;
  # proxy_set_header Host $host;
  proxy_set_header Host $host:$server_port; #添加:$server_port
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_pass http://47.108.248.203:8000/$1;
  }
  
  # 放行druid监控相关
  location ~ /druid/.*\.(html|htm|woff|ttf|svg|eot|gif|jpg|jpeg|bmp|png|ico|txt|js|css|xml)$ {
  proxy_pass          http://47.108.248.203:8000;
  proxy_redirect      off;
  proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header    X-Real-IP $remote_addr;
  proxy_set_header    Host $http_host;
  proxy_http_version  1.1;
  proxy_set_header    Connection "";
  }
  
  }
  ```