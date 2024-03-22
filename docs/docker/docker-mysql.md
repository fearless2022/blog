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

## 启动容器

* ```bash
  # 配置文件 my.cnf
  [mysqld]
  
  #lower_case_table_names=1
  binlog-format=MIXED
  #expire_logs_days=15
  #修改时区
  default-time_zone='+8:00'
  
  sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
  # 启动容器
  docker run -p 33070:3306 --name mysql \
  -v /data/mysql/log:/var/log/mysql \
  -v /data/mysql/data:/var/lib/mysql \
  -v /data/mysql/conf:/etc/mysql/conf.d \
  -e MYSQL_ROOT_PASSWORD=password@#2022 \
  --restart=always \
  -d mysql:8.1.0
  # 创建用户
  create user test identified by 'test@2023';
  
  GRANT ALL ON test.* TO 'test'@'%';
  
  flush privileges; 
  ```