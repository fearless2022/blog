## 拉取镜像

* ```bash
  docker pull mysql
  ```

## 准备数据存放目录

* ```bash
  mkdir /home/docker/mysql/data
  ```

## 启动

* ```bash
  docker run --name mysql -v /home/docker/mysql/data:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql -d mysql:latest
  ```

## 关闭

* ```bash
  docker stop mysql
  ```