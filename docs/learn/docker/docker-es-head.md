## 拉取镜像

* ```bash
  docker pull mobz/elasticsearch-head:5
  ```

## 运行

* ```bash
  docker run -d --name elasticsearch_head -p 9100:9100 docker.io/mobz/elasticsearch-head:5
  ```

## 修改es配置文件

* ```bash
  # 进入es容器
  docker exec -it elasticsearch /bin/bash
  cd config
  vi elasticsearch.yml
  ```

* elasticsearch.yml

* ```yaml
  # 新增下面的配置
  http.cors.enabled: true
  http.cors.allow-origin: "*"
  ```

## 索引数据浏览无数据

* ```bash
  # 原因
  elasticsearch 6增加了请求头严格校验的原因，并且返回结果是
  
  {
  "error" : "Content-Type header [application/x-www-form-urlencoded] is not supported",
  "status" : 406
  }
  # 解决
  # 进入容器
  docker exec -it elasticsearch /bin/bash
  # vi/vim vendor.js
  vi /usr/src/app/_site/vendor.js
  6886行
  contentType: "application/x-www-form-urlencoded"
  改成
  contentType: "application/json;charset=UTF-8"
  7573行
  var inspectData = s.contentType === "application/x-www-form-urlencoded" &&
  改成
  var inspectData = s.contentType === "application/json;charset=UTF-8" &&
  # 显示行号为
  :set nu
  # 跳转到指定行为
  :行号
  ```

## 重启容器

* ```bash
  docker restart elasticsearch
  ```
