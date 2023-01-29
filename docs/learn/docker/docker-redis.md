## 拉取镜像

* ```bash
  docker pull redis
  ```

## 准备数据存放目录

* ```bash
  mkdir -p /home/docker/redis
  mkdir -p /home/docker/redis/data
  touch /home/docker/redis/redis.conf
  touch /home/docker/redis/redis.bash
  ```

## 启动

* ```bash
  docker run -p 6379:6379 --name redis -v /home/docker/redis/redis.conf:/etc/redis/redis.conf -v /home/docker/redis/data:/data -d redis redis-server /etc/redis/redis.conf --requirepass redis
  ```

## 关闭

* ```bash
  docker stop mysql
  ```