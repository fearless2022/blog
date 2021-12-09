## MySQL-"this is incompatible with sql_mode=only_full_group_by"问题

* ```sql
  show variables like 'sql_model';
  
  set sql_mode='';
  
  set sql_mode='no_engine_substitution,strict_trans_tables';
  
  select @@GLOBAL.sql_mode;
  
  set @@GLOBAL.sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
  ```

## 错误com.mysql.cj.exceptions.CJException: Access denied for user ‘root‘@‘%‘ to database ‘xdu_sxpp‘

* ```sql
  grant all on xxx.* to ‘root’@’%’ identified by ‘password’ with grant option;
  
  # 其中：xxx代表创建的数据库; password为用户密码，在此为root的密码。
  ```

## [ERR] 1273 - Unknown collation: 'utf8mb4_0900_ai_ci'

* ```sql
  # 打开sql文件，将文件中的所有
  # utf8mb4_0900_ai_ci替换为utf8_general_ci
  # utf8mb4替换为utf8
  # 保存后再次运行sql文件，运行成功
  ```

## Host 'XXX' is not allowed to connect to this MySQL server解决方案

* ```sql
  mysql -u root -p password
  
  mysql>use mysql;
  
  mysql>update user set host ='%'where user ='root' and host ='localhost';
  
  mysql>flush privileges;
  ```

## navicat连接docker-mysql8报错client does not support authentication

* ```bash
  # 进入mysql容器，执行以下命令
  mysql> alter user 'root'@'%' identified with mysql_native_password by '123456';
  mysql> flush privileges;
  ```
