## 创建用户

* ```bash
  db.createUser({ user:'root',pwd:'huafei',roles:[{ role:'readWrite', db: 'lilshop'}]})
  
  db.createUser({ user:'admin',pwd:'123456',roles:[{ role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]})
  ```

## 验证账号密码

* ```bash
  db.auth('admin', '123456')
  ```

## 查询

* ```bash
  db.getCollection('essays').find({"essayId":"623ada6f5769a77e47727804"})
  
  db.essays.find({"essayId":"623adb5e5769a77e4772c3c9"})
  ```
