## 登录

* ```sql
  mysql -u root -p root
  ```

## 设置密码

* ```sql
  SET PASSWORD FOR root=PASSWORD('yourpassword');
  ```

## 创建用户授权

* ```sql
  # 创建用户
  CREATE USER 'user'@'%' IDENTIFIED BY '123456';
  # 查询权限
  SELECT * FROM mysql.user;
  SHOW GRANTS FOR 'user'@'%';
  # 授权
  GRANT ALL PRIVILEGES ON *.* TO 'user'@'%';
  # 刷新权限
  FLUSH PRIVILEGES;
  # 撤销用户权限
  REVOKE privilege ON databasename.tablename FROM 'username'@'host';
  # 删除用户
  DROP USER 'username'@'host';
  # 重启数据库
  service mysqld restart;
  ```
