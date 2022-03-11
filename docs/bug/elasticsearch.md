## es-head连接不上es

* ```yaml
  # 进入es容器
  # cd config
  # vi elasticsearch.yml
  # 新增下面的配置
  http.cors.enabled: true
  http.cors.allow-origin: "*"
  ```
