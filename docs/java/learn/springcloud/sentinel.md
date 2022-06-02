## 启动命令

* ```bash
  # -Dserver.port=8718 用于指定 Sentinel 控制台端口为 8718
  # 把dashboard自己也当成一个资源加入到了dashboard中来进行监控
  java -Dserver.port=8718 -Dcsp.sentinel.dashboard.server=localhost:8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar
  
  # 如果不想把dashboard自己加入控制台监控
  java -Dserver.port=8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar
  ```

## yml配置

* ```yaml
  spring:
    application:
      name: cloudalibaba-sentinel-service
      sentinel:
        transport: #dashboard地址
          dashboard: localhost:8718
          port: 8719  #默认端口，如果被占用则从8719依次+1扫描
  ```