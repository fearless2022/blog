## 创建用户

* ```bash
  db.createUser({ user:'root',pwd:'huafei',roles:[{ role:'readWrite', db: 'lilshop'}]})
  
  db.createUser({ user:'admin',pwd:'123456',roles:[{ role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]})
  ```

## 验证账号密码

* ```bash
  db.auth('admin', '123456')
  ```
