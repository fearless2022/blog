## docker安装

### 拉取镜像

* ```bash
  docker pull minio/minio
  ```

### 启动镜像

* ```bash
  docker run -d  -p 9000:9000 -p 9001:9001 --name minio  -v D:\File\ProjectFile\Resource\minio\data:/data -e "MINIO_ROOT_USER=minioadmin" -e "MINIO_ROOT_PASSWORD=minioadmin"   minio/minio  server /data --console-address ":9001"
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
    endpoint: localhost #部署时改为对象存储服务器的ip地址
    port: 9000
    accessKey: PO8QYT2GX2QM6AC747QV
    secretKey: Bt2MyxyP+RXIHNF7xC9GLUq1JFqmLOyMlj+2Hd3Z
    secure: false
    bucketName: fearless
  ```

