## 拉取镜像

* ```bash
  docker pull jenkinsci/blueocean
  ```

## 启动容器

* ```bash
  docker run --env JAVA_OPTS=-Dhudson.model.DownloadService.noSignatureCheck=true -u root -d -p 9095:8080 -p 50000:50000 --name jenkins -v D:\File\ProjectFile\Resource\jenkins\jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
  ```

## 访问 jenkins

* ```bash
  # url
  localhost:9095
  # 容器启动日志查看 password
  *************************************************************
  *************************************************************
  *************************************************************
  
  Jenkins initial setup is required. An admin user has been created and a password generated.
  Please use the following password to proceed to installation:
  
  a96407ceb61f45298fae64148b9cfa1f
  
  This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
  
  *************************************************************
  *************************************************************
  *************************************************************
  # 挂载目录查看 password
  D:\File\ProjectFile\Resource\jenkins\jenkins_data\secrets\initialAdminPassword
  ```

## 配置 jenkins

### 安装插件

* ```bash
  # 如果安装插件失败，修改 hudson.model.UpdateCenter.xml 文件，替换镜像源地址，将 url 内容修改为
  https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json（清华大学官方镜像） 
  # 重启容器
  docker restart jenkins
  # 重启 jenkins ，访问地址
  localhost:9095/restart
  ```

### 创建管理员用户

### 配置 jenkins

### 构建流水线

* ```bash
  # 参照官方文档
  https://www.jenkins.io/zh/doc/tutorials/build-a-java-app-with-maven/
  # clone官方文档中示例项目，由于github访问慢，选择上传到gitee
  # 安装插件：Credentials Plugin
  # 配置gitee账号密码，在配置流水线 Credentials 添加
  # 流水线构建报错：WorkflowScript: 3: Invalid agent type "docker" specified，解决：安装Docker、Docker Pipeline插件
  # 流水线构建报错：Detected Maven Version: 3.5.0 is not in the allowed range [3.5.4,)，解决：修改pom.xml文件：[3.5.0,)
  # 终于构建成功！
  ```