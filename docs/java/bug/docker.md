## 基于WSL2 的 Docker Desktop 启动时 Failed to set version to docker-desktop: exit code: -1的解决方法

* ```bash
  # 管理员权限打开cmd，执行命令
  netsh winsock reset
  ```

## Docker容器做端口映射报错

* ```bash
  docker: Error response from daemon: driver failed programming external connectivity on endpoint lamp3 (46b7917c940f7358948e55ec2df69a4dec2c6c7071b002bd374e8dbf0d40022c): (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 86 -j DNAT --to-destination 172.17.0.2:80 ! -i docker0: iptables: No chain/target/match by that name.
  ```

* ```bash
  systemctl restart docker
  ```
