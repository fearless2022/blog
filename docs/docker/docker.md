## 进入docker容器

* ```bash
  docker exec -it kafka /bin/bash
  ```

## docker启动redis并设置密码

* ```bash
  docker run -d --name redis -p 6379:6379 redis --requirepass "password"
  ```

## docker启动mongodb

* ```bash
  docker run -it -d --name mongo -p 27017:27017 mongo --auth
  ```

## docker启动logstash

* ```bash
  docker run -it -d --name logstash --link docker_elasticsearch_1 --net docker_default -v D:\File\ProjectFile\Resource\elk\logstash\pipeline:/usr/share/logstash/pipeline -v D:\File\ProjectFile\Resource\elk\logstash\config\logstash.yml:/usr/share/logstash/config/logstash.yml -v D:\Work\IdeaWorkSpace\learn\fearless-admin\log:/usr/share/logstash/logs -p 4560:4560 logstash:7.16.1
  ```

## 复制docker容器文件

* ```bash
  docker cp logstash:/usr/share/logstash D:/Work/WorkProject/shop/configuration/logstash
  ```

## 删除docker容器

* ```bash
  docker rm -f logstash
  ```

## 查看容器的Networks

* ```bash
  docker inspect [需要link的容器名称或ID]
  ```

## 查看所有容器的Networks信息

* ```bash
  docker network ls
  ```

## 删除docker网络

* ```bash
  docker network rm [需要删除的网络名称或ID]
  ```

## 运行docker容器

* ```bash
  docker run -it -v /宿主机目录:/容器目录 镜像名
  
  # -i: 以交互模式运行容器，通常与 -t 同时使用
  # -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用
  # -v: 将宿主机目录挂载到容器里
  ```

## docker容器与本机互传文件

* ```bash
  # docker容器->宿主机
  docker cp 4a2f08d2c1f8:/data1/configure.txt E:\PHP\configure.txt
  # 宿主机->docker容器
  docker cp E:\PHP\configure.txt 4a2f08d2c1f8:/data1/configure.txt
  ```

## docker启动mysql

* ```bash
  docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
  ```

## docker容器设置关闭开机自启动命令

* ```bash
  docker update --restart=no 容器id
  ```

## docker查询日志

* ```bash
  # 查看实时日志
  docker logs -f --tail 100 容器id
  # 导出时间段日志
  docker logs -t  --since='2024-03-21T15:30:00'  --until='2024-03-21T16:00:00' 容器id > log.txt
