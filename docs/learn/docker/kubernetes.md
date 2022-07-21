## 容器（docker）

### docker engine配置阿里云镜像加速

* ```json
  {
    "builder": {
      "gc": {
        "defaultKeepStorage": "20GB",
        "enabled": true
      }
    },
    "experimental": false,
    "features": {
      "buildkit": true
    },
    "registry-mirrors": [
      "https://docker.mirrors.ustc.edu.cn",
      "http://hub-mirror.c.163.com",
      "https://registry.docker-cn.com"
    ]
  }
  ```

### docker-desktop转移镜像文件存储路径

* ```bash
  # 备份已有image数据
  wsl --export docker-desktop-data D:\File\ProjectFile\Resource\docker\backup\docker-desktop-data.tar
  wsl --export docker-desktop D:\File\ProjectFile\Resource\docker\backup\docker-desktop.tar
  # 注销当前的发行版
  wsl --unregister docker-desktop-data
  wsl --unregister docker-desktop
  # 重新导入备份的'docker-desktop-data'与'docker-desktop'
  wsl --import docker-desktop-data "D:\File\ProjectFile\Resource\docker\data" "D:\File\ProjectFile\Resource\docker\backup\docker-desktop-data.tar" --version 2
  wsl --import docker-desktop "D:\File\ProjectFile\Resource\docker\docker-desktop" "D:\File\ProjectFile\Resource\docker\backup\docker-desktop.tar" --version 2
  ```

### docker build 的工作原理

* ```bash
  # docker build 构建镜像的流程
  执行 docker build -t <imageName:imageTag> .
  Docker 客户端会将构建命令后面指定的路径(.)下的所有文件打包成一个 tar 包，发送给 Docker 服务端
  Docker 服务端收到客户端发送的 tar 包，然后解压，根据 Dockerfile 里面的指令进行镜像的分层构建
  # 正确理解 Docker 镜像构建上下文
  了解了 Docker 的架构和镜像构建的工作原理后，Docker 构建上下文也就容易理解了。Docker 构建上下文就是 Docker 客户端上传给服务端的 tar 文件解压后的内容，也即 docker build 命令行后面指定路径下的文件
  Docker 镜像的构建是在远程服务端进行的，所以客户端需要把构建所需要的文件传输给服务端。服务端以客户端发送的文件为上下文，也就是说 Dockerfile 中指令的工作目录就是服务端解压客户端传输的 tar 包的路径
  # 关于 docker build 指令的几点重要的说明
  如果构建镜像时没有明确指定 Dockerfile，那么 Docker 客户端默认在构建镜像时指定的上下文路径下找名字为 Dockerfile 的构建文件
  Dockerfile 可以不在构建上下文路径下，此时需要构建时通过 -f 参数明确指定使用哪个构建文件，并且名称可以自己任意命名
  ```

### Docker数据卷

* 卷技术，实际就是将容器内的目录，挂在到主机上

* 总结一句话：容器的持久化和同步操作！容器间也是可以数据共享

#### 使用数据卷

* ```bash
  方式一：命令行 -v
  docker run -it -v 主机目录:容器目录
  ```

#### 具名和匿名挂载

* 匿名挂载：-v 容器内路径 不指定容器外路径

* 具名挂载

* 区分匿名挂载和具名挂载

* ```bash
  -v 容器内路径         # 匿名挂载
  -v 卷名:容器内路径     # 具名挂载
  -v /主机路径:容器内路径 # 指定路径挂载
  ```

#### 数据卷容器

* 多个mysql实现数据共享

* ```bash
  # mysql docker01
  docker run -d -p 33061:3306 -v D:\docker\mnt\mysql\conf:/etc/conf.d -v D:\docker\mnt\mysql\data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
  
  # mysql docker02
  # 通过--volumes-form mysql01实现数据同步
  docker run -d -p 33061:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-form mysql01 mysql:5.7
  ```

* 容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用

* 但是一旦你持久化到了本地，这时候，本地的数据是不会删除的

### 初识DockerFile

* DockerFile就是用来构建docker镜像的构建文件！命令脚本！
* 通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个个的命令，每个命令都是一层

#### 基础知识

* 每个保留关键字（指令）都是必须大写字母
* 执行从上到下顺序执行
* 表示注释
* 每一个指令都会创建提交一个新的镜像层，并提交

#### 构建步骤

* 编写一个dockerfile文件
* docker build 构建成为一个镜像
* docker run 运行镜像
* docker push 发布镜像（DockerHub 阿里云镜像）

#### DockerFile的指令

* ```bash
  FROM       # 基础镜像，一切从这里开始构建
  MAINTAINER # 镜像是谁写的，姓名+邮箱
  RUN        # 镜像构建的时候需要运行的命令
  ADD        # 步骤,tomcat镜像，这是tomacat压缩包！添加内容
  WORKDIR    # 镜像的工作目录 
  VOLUME     # 挂载的目录
  EXPOSE     # 保留端口配置
  CMD        # 指定这个容器启动的时候要运行的命令
  ENTRYPOINT # 指定这个容器启动的时候要运行的命令，可以追加命令
  ONBUILD    # 当构建一个被继承 Dockerfile，这个时候就会运行onbuild
  COPY       # 类似add，将我们文件拷贝到镜像中
  ENV        # 构建的时候设置环境变量
  ```

#### 创建自己的镜像

* ```bash
  # 创建一个自己的镜像
  新建文件mydockerfile-centos
  
  # build镜像
  docker build -f mydockerfile-centos -t mycentos:0.1 .
  
  # docker history imagesID
  我们平时查看镜像是如何做的，通过上面的命令
  ```

#### 发布自己的镜像

* ```bash
  # DockerHub
  # 官网注册自己的账号
  # 确定这个账号可以登录
  docker login -u 用户名
  # 输入密码进入
  # 在我们服务器上提交自己的镜像
  docker push taohy/
  # 登录完就可以提交镜像
  ```

#### 在阿里云发布镜像

* 登录阿里云
* 找到容器镜像服务
* 创建命名空间
* 创建容器镜像
* 浏览镜像仓库信息

### Docker网络

#### 网络详解

* ```bash
  # 进入容器
  PS C:\Users\taohy> docker exec -it  98da3ab14625 /bin/bash
  
  # 查看容器的内部网络IP地址 172.17.0.3
  root@98da3ab14625:/usr/local/bin/ignition	# ifconfig
  eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 172.17.0.3  netmask 255.255.0.0  broadcast 172.17.255.255
          ether 02:42:ac:11:00:03  txqueuelen 0  (Ethernet)
          RX packets 16  bytes 1072 (1.0 KB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 9  bytes 602 (602.0 B)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  
  lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
          inet 127.0.0.1  netmask 255.0.0.0
          loop  txqueuelen 1000  (Local Loopback)
          RX packets 1394  bytes 354097 (354.0 KB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 1394  bytes 354097 (354.0 KB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
          
  # 再启动一个容器，查看ip地址 172.17.0.2 
  root@801e60036d61:/usr/local/bin/ignition	# ifconfig
  eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
          ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
          RX packets 472  bytes 373438 (373.4 KB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 430  bytes 33381 (33.3 KB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  
  lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
          inet 127.0.0.1  netmask 255.0.0.0
          loop  txqueuelen 1000  (Local Loopback)
          RX packets 708  bytes 173243 (173.2 KB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 708  bytes 173243 (173.2 KB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  
  # 思考，外机能否ping通主机容器内部？
  Linux能ping通
  winodws不能ping通
  
  # 容器如何访问到Windows？
  容器访问外机，默认的要用host.docker.internal
  
  # 容器之间能互相通吗？ 可以通 容器内：172.17.0.2 ping 172.17.0.3
  root@801e60036d61:/usr/local/bin/ignition	# ping 172.17.0.3
  PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
  64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.051 ms
  64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.037 ms
  64 bytes from 172.17.0.3: icmp_seq=3 ttl=64 time=0.052 ms
  
  # 查看网络命令 docker network --help 
  PS C:\Users\taohy> docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  075fff009f22   bridge    bridge    local
  efbaf94573b8   host      host      local
  29125732f2e0   none      null      local
  
  # 查看网络具体信息
  docker network inspect 075fff009f22
  
  # inspect 是查看具体信息，非常有用，不管是查看容器内部还是容器网络
  docker inspect 容器ID
  docker network inspect 网络ID
  ```

#### 查看所有的docker网络

* ```bash
  PS C:\Users\taohy> docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  075fff009f22   bridge    bridge    local
  efbaf94573b8   host      host      local
  29125732f2e0   none      null      local
  ```

#### 网络模式

* bridge ：桥接 docker（默认，如果自己创建也用bridge）
* none：不配置网络（用的少）
* host：和主机共享网络
* container：容器网络连接（用的少）

#### 网络测试

* ```bash
  # 创建自定义的网络bridge
  docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
  
  # 创建之后查看网络，mynet
  PS C:\Users\taohy> docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  075fff009f22   bridge    bridge    local
  efbaf94573b8   host      host      local
  6c3a287738b3   mynet     bridge    local
  29125732f2e0   none      null      local
  
  # 启动容器放在自定义网络 --net 
  docker  run -d -P --name tomcat-net-01 --net mynet tomcat
  
  # 假设要跨网络操作别人，就需要使用docker network connect 连通！
  ```

## 容器编排（学习环境）

### 安装Chocolatey

* ```bash
  # 输入此命令
  Get-ExecutionPolicy
  
  # 如果返回 Restricted ,运行下列命令
  Set-ExecutionPolicy AllSigned
  
  # 最后安装Chocolatey,执行此命令
  Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
  
  # 检测是否安装成功
  choco -
  
  # 安装软件
  choco install kubernetes-cli
  
  # 查看本地安装软件列表
  choco list -li
  
  # 卸载软装
  choco uninstall kubernetes-cli
  ```

### 安装kubectl

* ```bas
  choco install kubernetes-cli
  ```

### 安装minikube

* ```bash
  choco install minikube
  ```

#### 启动本地 Kubernetes 集群

* ```bash
  # 启动集群
  minikube start --vm-driver=hyperv --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
  # 检查集群的状态
  minikube status
  # 访问 minikube中的 k8s dashboard
  minikube dashboard
  # 检测kubectl
  kubectl get nodes
  # 停止集群
  minikube stop
  # 清理 minikube 的本地状态
  minikube delete
  # 删除本地所有集群和配置
  minikube delete --all
  ```

### 安装kind

* ```bash
  choco install kind
  ```

#### 创建集群

* ```bash
  # 创建集群，不指定集群名称默认为kind
  kind create cluster --config D:\File\ProjectFile\Resource\kind\kind.yaml --name kind-cluster
  # 查看集群
  kind get clusters
  # 删除某个集群
  kind delete cluster --name kind-cluster
  # 删除所有集群
  kind delete clusters –all
  # 查看所有可用的上下文（ *：表示目前设置的上下文）
  kubectl config get-contexts
  CURRENT   NAME                               CLUSTER                      
            kind-kind                          kind-kind            
  *         kind-kind-1.15.6                   kind-kind-1.15.6
  # 获取当前K8S上下文
  kubectl config current-context
  # 设置上下文
  kubectl config set-context kind-kind 
  Context "kind-kind" modified.
  # 检查设置的上下文
  kubectl get nodes
  NAME                        STATUS   ROLES    AGE     VERSION
  kind-1.18.2-control-plane   Ready    master   8m20s   v1.18.2
  ```
  
* kind.yaml

* ```yaml
  kind: Cluster
  apiVersion: kind.x-k8s.io/v1alpha4
  networking:
    # WARNING: It is _strongly_ recommended that you keep this the default
    # (127.0.0.1) for security reasons. However it is possible to change this.
    apiServerAddress: "0.0.0.0"
    # By default the API server listens on a random open port.
    # You may choose a specific port but probably don't need to in most cases.
    # Using a random port makes it easier to spin up multiple clusters.
    apiServerPort: 6443
  nodes:
    - role: control-plane
      extraPortMappings:
      - containerPort: 30001
        hostPort: 30001
        listenAddress: "0.0.0.0"
        protocol: tcp
      - containerPort: 30002
        hostPort: 30002
        listenAddress: "0.0.0.0"
        protocol: tcp
      - containerPort: 30003
        hostPort: 30003
        listenAddress: "0.0.0.0"
        protocol: tcp
      - containerPort: 30004
        hostPort: 30004
        listenAddress: "0.0.0.0"
        protocol: tcp
      - containerPort: 30005
        hostPort: 30005
        listenAddress: "0.0.0.0"
        protocol: tcp
  ```

#### 安装Kubernetes Dashboard

* ```bash
  # 安装
  kubectl apply -f D:\File\ProjectFile\Resource\kind\recommended.yaml
  # 查看deployment状态
  kubectl get deployments -n kubernetes-dashboard
  ```

* 修改recommended.yaml

* ![img](../../file/image/kubernetes/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAeHR6Li4uLi4u,size_20,color_FFFFFF,t_70,g_se,x_16.png)

* ![img](../../file/image/kubernetes/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAeHR6Li4uLi4u,size_20,color_FFFFFF,t_70,g_se,x_16-16582919979613.png)

#### 创建账号与授权

* ```bash
  # 创建账号与授权
  kubectl apply -f D:\File\ProjectFile\Resource\kind\dashboard-adminuser.yaml
  # 查看token
  kubectl -n kubernetes-dashboard create token admin-user
  # token
  eyJhbGciOiJSUzI1NiIsImtpZCI6Ilh3ZVU5TmVEX3dpVkZld0ExeVE5Z2hjVDF2aUt0VkhuTVBTT0dwWTM1aE0ifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjU4MzAzMzIxLCJpYXQiOjE2NTgyOTk3MjEsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiZGQwZjU2NDMtODc3OC00NGRiLWIxNGQtNDVjYjRjNzUzZDIwIn19LCJuYmYiOjE2NTgyOTk3MjEsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.Z85EQOuwrQwo1fz80rh4SIsaR8CgutFhXs7nxBwFV42RjWk5-XfPEfdHNNsexWC3z_nOZ9Me6yFu97vSevBRrjcV_QjofYdgZHCGQeiWQ-sKEGy8v8Cy1Juu6Mo2aJXrOWz--bGqSssXWOiLWATeWxEYuzr_LRO0fmCT1PZ-1Rk7SZok324pV2qdX1AkG4p5k6NhiMhQSHjHb4C37VKNatMbialIL6JO_YlgTSNWUHY33F8XqczqbtYSPSF51qyp3JPSWlztm5sFkhblmdg3_BYznEZjz4yieBiT5sxVgLI8MFT83rGS_UFy9R-UvEMD1gE4IrZu5fctsQ43H6GVOg
  # 删除账号和绑定关系
  kubectl -n kubernetes-dashboard delete serviceaccount admin-user
  kubectl -n kubernetes-dashboard delete clusterrolebinding admin-user
  ```
  
* dashboard-adminuser.yaml

* ```yaml
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: admin-user
    namespace: kubernetes-dashboard
  ---
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
    name: admin-user
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: cluster-admin
  subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
  ```

#### 访问dashboard图形化界面

* ```bash
  # 通过当前主机ip:nodePort访问
  https://localhost:30001
  ```

* ![image-20220720123834290](../../file/image/kubernetes/image-20220720123834290.png)

* ![image-20220720123816782](../../file/image/kubernetes/image-20220720123816782.png)

### 安装Helm

* ```bash
  choco install kubernetes-helm
  ```