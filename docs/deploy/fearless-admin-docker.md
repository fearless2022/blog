## 上传项目jar包

## jar包同级目录创建dockerfile文件

* ```bash
  FROM openjdk:8
  ARG JAR_FILE=./*.jar
  COPY ${JAR_FILE} app.jar
  ENV TZ=Asia/Shanghai
  ENTRYPOINT ["java","-jar","/app.jar"]
  ```

## 构建镜像

* ```bash
  docker build -t fearless-admin-docker .
  ```

## 启动java容器

* ```bash
  docker run -d --name fearless-admin-docker -p 8000:8000 -e "TZ=Asia/Shanghai" -e DB_HOST=172.17.0.1 -e DB_PWD=mysql -e REDIS_HOST=172.17.0.1 -e REDIS_PWD=redis -v /home/docker/project/fearless-admin/data/:/home/lijin/project/fearless-admin/ -v /home/docker/project/fearless-admin/log/:/log/ fearless-admin-docker
  ```

* **这里使用 Nginx 反向代理访问后端服务，由于容器内部通信，因此需要使用容器的 IP，也就是 172.17.0.1**