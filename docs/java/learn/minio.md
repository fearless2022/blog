## 拉取镜像

* ```bash
  docker pull minio/minio
  ```

## 启动镜像

* ```bash
  docker run -id -p 9000:9000 --name minio --privileged=true -e "MINIO_ROOT_USER=r" -e "MINIO_ROOT_PASSWORD=123456" -v /root/docker/minio/data:/data -v /root/docker/minio/config:/root/.minio minio/minio server /data --console-address ":9000" --address ":9090"
  
  docker run -p 9000:9000 --name minio minio/minio server /data --console-address ":9000" --address ":9090"
  ```

