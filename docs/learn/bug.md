## Caused by: org.yaml.snakeyaml.scanner.ScannerException:  while scanning for the next token found character '@' that cannot start any token. (Do not use @ for indentation)

* ```bash
  尝试 maven reimport ，问题解决
  ```


## Idea运行报错Error running ‘Application‘: Command line is too long的解决方法

* ```xml
  找到项目里面的.idea/workspace.xml文件
  找到<component name="PropertiesComponent"></component >
  在component 标签里加一行 <property name="dynamic.classpath" value="true" />
  ```

## Caused by: java.lang.IllegalStateException: Method has too many Body parameters

* ```tex
  在方法的参数上加上@RequestParam注解（同时可以用多个）即可
  这里有两个参数，加上两个@RequestParam注解。如果有一个对象参数是要用@RequestBody注解的（这个注解同时只能用一个）
  ```

## 支付宝调用接口保存：cert check fail: ALIPAY_CERT_SN is Empty!

* ```bash
  调用方法设置错误，一般是由于普通公钥签名，但response使用了公钥证书的方法
  公钥和公钥证书签名服务端response的方法不同，详见RSA2和公钥证书签名验签的区别
  
  java语言需将公钥证书的certificateExecute（）修改为RSA2密钥的execute（）
  java服务端请求，如当面付//RSA2密钥请求response = alipayClient.execute(request)
  公钥证书请求请求response = alipayClient.certificateExecute(request)
  php服务端请求，如当面付//RSA2普通公钥和公钥证书请求方法一致。$response = $aop->execute($request)
  .net服务端请求，如当面付//RSA2密钥请求response = client.Execute(request)
  公钥证书请求请求response = client.CertificateExecute(request)
  ```

## Error:(1, 1) java: 非法字符: ‘\ufeff’  Error:(1, 10) java: 需要class, interface或enum

* ```tex
  Eclipse可以智能的把UTF-8+BOM文件转为普通的UTF-8文件，IDEA没有这个智能转换
  用IDEA转换，先转换为GBK，再转回UTF-8
  ```

## 基于WSL2 的 Docker Desktop 启动时 Failed to set version to docker-desktop: exit code: -1的解决方法

* ```bash
  # 管理员权限打开cmd，执行命令
  netsh winsock reset
  ```

## Docker容器做端口映射报错

* ```bash
  docker: Error response from daemon: driver failed programming external connectivity on endpoint lamp3 (46b7917c940f7358948e55ec2df69a4dec2c6c7071b002bd374e8dbf0d40022c): (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 86 -j DNAT --to-destination 172.17.0.2:80 ! -i docker0: iptables: No chain/target/match by that name.
  
  systemctl restart docker
  ```

## mongo插入数据报错：java.lang.IllegalArgumentException: Prohibited character at position 0

* ```yaml
  # 要加单引号，这个数字会被识别成空字符串
  password: '123456' 
  ```

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

## [解决java.sql.SQLNonTransientConnectionException: Public Key Retrieval is not allowed](https://www.cnblogs.com/xing-29391/p/13322843.html)

* ```yaml
  # mysql8.0在连接数据库的url中，加上allowPublicKeyRetrieval=true
  jdbc:mysql://localhost:3306/user?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
  ```

## Public Key Retrieval is not allowed

* ```yaml
  # 数据库配置连接
  allowPublicKeyRetrieval=true
  ```
