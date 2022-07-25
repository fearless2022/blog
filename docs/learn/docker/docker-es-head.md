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

## 重启容器

* ```bash
  docker restart elasticsearch
  ```
