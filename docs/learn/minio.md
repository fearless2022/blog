## docker安装

### 拉取镜像

* ```bash
  docker pull minio/minio
  ```

### 启动镜像

* ```bash
  docker run -id -p 9000:9000 --name minio --privileged=true -e "MINIO_ROOT_USER=r" -e "MINIO_ROOT_PASSWORD=123456" -v /root/docker/minio/data:/data -v /root/docker/minio/config:/root/.minio minio/minio server /data --console-address ":9000" --address ":9090"
  
  docker run -p 9000:9000 --name minio minio/minio server /data --console-address ":9000" --address ":9090"
  ```

## 普通安装

* ```bash
  # 下载
  wget https://dl.min.io/server/minio/release/linux-amd64/minio
  # 赋予可执行权限
  chmod +x minio
  # 启动
  MINIO_ROOT_USER=admin MINIO_ROOT_PASSWORD=password ./minio server /mnt/data --console-address ":9001"
  ```

## 与springboot整合

### 引入依赖

* ```xml
  <dependency>
      <groupId>io.minio</groupId>
      <artifactId>minio</artifactId>
      <version>7.0.2</version>
  </dependency>
  ```

### yml配置

* ```yaml
  minio:
    endpoint: loc #部署时改为对象存储服务器的ip地址
    port: 9000
    accessKey: 9KVEBI97sQn0eIZs
    secretKey: 6iiergAB68wzHbb21GaECFNljp7UErzh
    secure: false
    bucketName: fearless
  ```

