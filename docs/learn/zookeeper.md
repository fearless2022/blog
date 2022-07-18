## **[Zookeeper是什么](https://www.roncoo.com/course/list.html?courseName=Zookeeper)**

* 分布式服务框架
* 解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等
* zookeeper=文件系统+监听通知机制

### 文件系统（znode）

* **PERSISTENT-持久化目录节点**
  * 客户端与zookeeper断开连接后，该节点依旧存在
* **PERSISTENT_SEQUENTIAL-持久化顺序编号目录节点**
  * 客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号

* **EPHEMERAL-临时目录节点**
  * 客户端与zookeeper断开连接后，该节点被删除
* **EPHEMERAL_SEQUENTIAL-临时顺序编号目录节点**
  * 客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号

### **监听通知机制**

* 客户端注册[监听](https://so.csdn.net/so/search?q=监听&spm=1001.2101.3001.7020)它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加删除）时，zookeeper会通知客户端

## **Zookeeper单机模式安装**

### 下载、解压zookeeper

* ```bash
  wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz
  tar -zxvf apache-zookeeper-3.7.0-bin.tar.gz
  ```

### 启动zookeeper

* ```bash
  ./bin/zkServer.sh start
  ```

### 检查zookeeper服务器状态

* ```bash
  ./bin/zkServer.sh status
  ```

### 连接zookeeper

* ```bash
  ./bin/zkCli.sh -server ip:port
  ```

### 停止zookeeper服务器

* ```bash
  ./bin/zkServer.sh stop
  ```

## Zookeeper使用

### **使用客户端命令操作zookeeper**

* ```bash
  # 查看节点
  ls /
  # 创建新节点
  create /zkNode zkData
  # 查看节点
  get /zkNode
  # 修改节点数据
  set /zkNode newZkData
  # 删除节点
  delete /zkNode
  # 退出zookeeper客户端
  quit
  ```

### **使用Java API操作zookeeper**

#### 导入依赖

* ```xml
  <!--zookeeper-->
  <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.5.9</version>
      <!--排除这个slf4j-log4j12-->
      <exclusions>
          <exclusion>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

* jar包冲突问题

* ```java
  // SLF4J: Class path contains multiple SLF4J bindings.
  // 需要排除zookeeper中slf4j依赖
  ```

#### java客户端

* ```java
  public class ZooKeeperProSync implements Watcher {
  
      private static CountDownLatch connectedSemaphore = new CountDownLatch(1);
      private static ZooKeeper zk = null;
      private static Stat stat = new Stat();
  
      @Override
      public void process(WatchedEvent watchedEvent) {
          // zk连接成功通知事件
          if (Event.KeeperState.SyncConnected == watchedEvent.getState()) {
              if (Event.EventType.None == watchedEvent.getType() && watchedEvent.getPath() == null) {
                  connectedSemaphore.countDown();
              } else if (Event.EventType.NodeDataChanged == watchedEvent.getType()) {
                  // zk目录节点数据变化通知事件
                  try {
                      System.out.println("配置已修改，新值为：" + new String(zk.getData(watchedEvent.getPath(), true, stat)));
                  } catch (InterruptedException | KeeperException e) {
                      e.printStackTrace();
                  }
              }
          }
      }
  
      public static void main(String[] args) throws IOException, InterruptedException, KeeperException {
          // zookeeper配置数据存放路径
          String path = "/username";
          // 连接zookeeper并且注册一个默认的监听器
          zk = new ZooKeeper("101.43.47.213", 5000, new ZooKeeperProSync());
          // 等待zk连接成功的通知
          connectedSemaphore.await();
          // 获取path目录节点的配置数据，并注册默认的监听器
          System.out.println(new String(zk.getData(path, true, stat)));
  
          Thread.sleep(Integer.MAX_VALUE);
      }
  }
  ```

## **Zookeeper集群模式安装**

### 配置集群

* ```bash
  # 修改配置文件
  cp conf/zoo_sample.cfg conf/zoo-1.cfg
  # 编辑
  vim conf/zoo-1.cfg
  dataDir=/tmp/zookeeper-1
  clientPort=2181
  server.1=127.0.0.1:2888:3888
  server.2=127.0.0.1:2889:3889
  server.3=127.0.0.1:2890:3890
  # 再从zoo-1.cfg复制两个配置文件zoo-2.cfg和zoo-3.cfg，只需修改dataDir和clientPort不同即可
  cp conf/zoo-1.cfg conf/zoo-2.cfg
  cp conf/zoo-1.cfg conf/zoo-3.cfg
  vim conf/zoo-2.cfg
  dataDir=/tmp/zookeeper-2
  clientPort=2182
  vim conf/zoo-2.cfg
  dataDir=/tmp/zookeeper-3
  clientPort=2183
  # 创建三个文件夹/tmp/zookeeper-1，/tmp/zookeeper-2，/tmp/zookeeper-2，在每个目录中创建文件myid 文件，写入当前实例的server id，即1.2.3
  cd /tmp/zookeeper-1
  vim myid
  1
  cd /tmp/zookeeper-2
  vim myid
  2
  cd /tmp/zookeeper-3
  vim myid
  3
  # 启动三个zookeeper实例
  ./bin/zkServer.sh start conf/zoo-1.cfg
  ./bin/zkServer.sh start conf/zoo-2.cfg
  ./bin/zkServer.sh start conf/zoo-3.cfg
  # 检测集群状态
  ./bin/zkServer.sh status conf/zoo_1.cfg
  ./bin/zkServer.sh status conf/zoo_2.cfg
  ./bin/zkServer.sh status conf/zoo_3.cfg
  # 连接客户端
  ./bin/zkCli.sh -server 127.0.0.1:2181
  ./bin/zkCli.sh -server 127.0.0.1:2182
  ./bin/zkCli.sh -server 127.0.0.1:2183
  ```

### 相关配置说明

- tickTime：这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳。
- initLimit：这个配置项是用来配置 Zookeeper 接受客户端（这里所说的客户端不是用户连接 Zookeeper 服务器的客户端，而是 Zookeeper 服务器集群中连接到 Leader 的 Follower 服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过 10个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 10*2000=20 秒
- syncLimit：这个配置项标识 Leader 与 Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是 5*2000=10秒
- dataDir：顾名思义就是 Zookeeper 保存数据的目录，默认情况下，Zookeeper 将写数据的日志文件也保存在这个目录里。
- clientPort：这个端口就是客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。
- server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器；B 是这个服务器的 ip 地址；C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信端口号不能一样，所以要给它们分配不同的端口号。