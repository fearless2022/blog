## docker-mysql8.4.4-主从搭建

### 拉取mysql8.4.4镜像

```dockerfile
# 拉取镜像
docker pull swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/mysql:8.4.4
# 修改镜像名称（名称太长，按需要修改）
docker tag  swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/mysql:8.4.4  docker.io/mysql:8.4.4
```

* docker hub无法访问，[国内镜像源地址](https://docker.aityp.com/image/docker.io/mysql:8.4.4)

### 镜像打包/解压/上传

```bash
# 镜像打成tar包，上传至从库服务器
docker save -o /home/tyytadmin/mysql:8.4.4.tar docker.io/mysql:8.4.4
# 上传
scp mysql_8.4.4.tar 目标服务器ip:目标服务器指定目录
# 解压成镜像
docker load -i mysql_8.4.4.tar
```

* 目标服务器不能连接外网，无法直接拉取镜像

### 修改主库配置my.cnf

```bash
# 设置唯一server_id，区别从库
server-id=101
```

* 不设置`server_id`，后面查看主从关系时会报错：`Last_IO_Error: Fatal error: The replica I/O thread stops because source and replica have equal MySQL server ids; these ids must be different for replication to work (or the --replicate-same-server-id option must be used on replica but this does not always make sense; please check the manual before using it).`
* 无需指明`log bin`的值，因为它的默认值就是打开的
* 修改完重新启动容器

### 主库全量备份

#### 开启新终端

```bash
# 进入主mysql容器
docker exec -it 容器id/名 /bin/bash
# 连接mysql
mysql -u root -p
# 备份前最好锁定表，防止发生写操作导致状态改变
mysql> FLUSH TABLES WITH READ LOCK;
# 备份完解锁
mysql> UNLOCK TABLES;
```

#### 全量备份

```bash
# 进入容器执行mysqldump命令全量备份
docker exec -i 容器id/名 /usr/bin/mysqldump -u root --password=root密码 --all-databases --set-gtid-purged=OFF --single-transaction  --source-data=2 --triggers --routines --events > all_databases.dump
# 指定数据库备份
/usr/local/mysql/bin/mysqldump -u用户名 -p密码 --databases 数据库1 数据库2... > 保存路径/文件名.dump
# 备份完成，上传至从库服务器
scp all_databases.dump 目标服务器ip:目标服务器指定目录
```

* 参数说明
  `--all-databases`：全量备份
  `--set-gtid-purged=OFF`：默认开启，建议关闭gtid
  `--single-transaction`：一致性备份
  `--source-data=2`：备份点记录
  `--triggers --routines --events`：触发器/函数/存储过程备份

* `set-gtid-purged：ON`或未指定改参数（默认开启），导入dump可能出现错误：`ERROR 3546 (HY000) at line 24: @@GLOBAL.GTID_PURGED cannot be changed: the added gtid`，解决方法：[数据库备份恢复过程中的报错：ERROR 3546 (HY000) at line 24: @@GLOBAL.GTID_PURGED cannot be changed: the added gtid](https://blog.csdn.net/zsn200003/article/details/132466077)

### 查看主库同步状态信息

```sql
# 进入主mysql容器
docker exec -it 容器id/名 /bin/bash
# 连接mysql
mysql -u root -p
# 查看状态信息，同步前再次查询确认同步状态
mysql> SHOW BINARY LOG STATUS;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000002 |      158 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
```

### 主库创建从库复制账号，用于从库同步主库数据

```sql
# 为从库服务器 设置用户名和密码（表明从服务器的ip为任意,账号为slave 密码123456）
mysql> CREATE USER 'slave'@'%' IDENTIFIED BY '123456'; 
# 用户授权
mysql> grant replication slave, replication client on *.* to 'slave'@'%';
# 刷新权限
mysql> flush privileges;
```

### 从库启动容器

#### 准备挂载目录

```bash
# 挂载日志文件目录
mkdir -p /data/mysql_slave/log
# 挂载数据目录
mkdir -p /data/mysql_slave/data
# 挂载配置文件目录
mkdir -p /data/mysql_slave/conf
```

* -p:告诉 `mkdir` 命令，如果指定的目录的父目录不存在，那么 `mkdir` 应该创建它们。换句话说，它允许递归地创建目录结构

#### 创建从库配置文件

```bash
# 创建配置文件
touch /data/mysql_slave/conf/my.cnf
vim /data/mysql_slave/conf/my.cnf
# 设置唯一server_id，区别主库
server-id=102
```

* 注意文件权限，后面可能会出现忽略配置文件警告

#### 启动容器

```bash
# 启动命令
docker run -p 33060:3306 --name mysql_slave \
-v /data/mysql_slave/log:/var/log/mysql \
-v /data/mysql_slave/data:/var/lib/mysql \
-v /data/mysql_slave/conf:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=初始密码 \
--restart=always \
-d mysql:8.4.4
```

### 恢复数据

```bash
# 恢复数据
docker exec -i 72499e4d6187 mysql -u root -p密码 < all_databases.dump
```

* `set-gtid-purged：ON`或未指定改参数（默认开启），导入dump可能出现错误：`ERROR 3546 (HY000) at line 24: @@GLOBAL.GTID_PURGED cannot be changed: the added gtid`，解决方法：[数据库备份恢复过程中的报错：ERROR 3546 (HY000) at line 24: @@GLOBAL.GTID_PURGED cannot be changed: the added gtid](https://blog.csdn.net/zsn200003/article/details/132466077)

### 建立主从关系

```sql
# 进入从mysql容器
docker exec -it 容器id/名 /bin/bash
# 连接mysql
mysql -u root -p
# 建立主从关系
mysql> CHANGE REPLICATION SOURCE TO SOURCE_HOST = '主库ip',
SOURCE_PORT = 主库端口,	# 不指定，默认3306
SOURCE_USER = 'slave',	# 主库建立的同步账号
SOURCE_PASSWORD = '123456',	# 主库建立的同步账号密码
SOURCE_LOG_FILE = 'mysql-bin.000002',	# 主节点LOG STATUS
SOURCE_LOG_POS = 158,	# 主节点LOG STATUS
SOURCE_SSL = 1;
# 启动复制进程
mysql> START REPLICA;
# 查看复制状态，\G：格式化输出
mysql> SHOW REPLICA STATUS\G;
# 关键参数，都为YES表示配置成功
Replica_IO_Running: Yes
Replica_SQL_Running: Yes
# 停止复制
mysql> STOP REPLICA;
# 重置复制
mysql> RESET REPLICA ALL;
```

* `SOURCE_SSL = 1`，不指定出现错误：`Last_IO_Error: Error connecting to source 'slave@10.0.0.151:33060'. This was attempt 1/10, with a delay of 60 seconds between attempts. Message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection`.或者其他解决方式：[MySQL 8.0数据库主从Authentication plugin ‘caching_sha2_password‘ reported error: Authentication requi问题处理]([MySQL 8.0数据库主从Authentication plugin ‘caching_sha2_password‘ reported error: Authentication requi问题处理-CSDN博客](https://blog.csdn.net/gmaaa123/article/details/133178021))

* 关键参数，都为YES表示配置成功
  `Replica_IO_Running: Yes`
  `Replica_SQL_Running: Yes`
* 如果是connecting/no，查看异常信息`Last_IO_Error`/`Last_SQL_Error`

### 查看主从同步状态

```sql
mysql> SHOW PROCESSLIST;
*************************** 10. row ***************************
     Id: 101
   User: slave
   Host: 从库ip:53826
     db: NULL
Command: Binlog Dump
   Time: 147
  State: Source has sent all binlog to replica; waiting for more updates
   Info: NULL
```

## GTID方式主从

### [mysql官网GTID](https://dev.mysql.com/doc/refman/8.4/en/replication-gtids-howto.html)

### 修改主/从库配置文件my.cnf

```bash
gtid_mode=ON
enforce-gtid-consistency=ON
```

### 建立主从关系

```sql
mysql> CHANGE REPLICATION SOURCE TO
     >     SOURCE_HOST = host,
     >     SOURCE_PORT = port,
     >     SOURCE_USER = user,
     >     SOURCE_PASSWORD = password,
     >     SOURCE_AUTO_POSITION = 1;	# 从服务器在启动复制时自动读取主服务器的二进制日志文件和位置，而不是手动指定
```

* 查看主从状态报错，`Last_IO_Error: Got fatal error 1236 from source when reading data from binary log: 'Cannot replicate because the source purged required binary logs. Replicate the missing transactions from elsewhere, or provision a new replica from backup. Consider increasing the source's binary log expiration period. The GTID set sent by the replica is '2cc6f94d-73cc-11ee-b42a-0242ac110002:1-41858222,
  b0c2e9f6-02d4-11f0-9b4e-0242ac110602:1-6062,
  b76cd84c-f48e-11ee-bcaa-0242ac110402:29541', and the missing transactions are 'b76cd84c-f48e-11ee-bcaa-0242ac110402:4063-29540''`，暂未找到解决方案

## 参考博客

* [mysql8.4:搭建主从复制，mysql8.x版本命令差异](https://www.cnblogs.com/architectforest/p/18429705)

* [数据库备份恢复过程中的报错：ERROR 3546 (HY000) at line 24: @@GLOBAL.GTID_PURGED cannot be changed: the added gtid](https://blog.csdn.net/zsn200003/article/details/132466077)

* [MySQL 8.0数据库主从Authentication plugin ‘caching_sha2_password‘ reported error: Authentication requi问题处理](https://blog.csdn.net/gmaaa123/article/details/133178021)

* [对Docker部署的MySQL中的数据进行备份恢复](https://blog.csdn.net/web18296061989/article/details/145970329)

* [Mysql8.4基于GTID主从部署以及故障修复](https://blog.csdn.net/qq_34692500/article/details/142589092)

* [Mysql 8.4.0 结合 Docker 搭建GTID主从复制，以及传统主从复制](https://www.cnblogs.com/yfeil/p/18237529)

