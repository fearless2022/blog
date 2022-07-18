## docker-kafka环境搭建

### 拉取镜像

#### 下载zookeeper

* ```bash
  docker pull wurstmeister/zookeeper
  ```

#### 下载kafka

* ```bash
  docker pull wurstmeister/kafka
  ```

### 启动镜像

#### 启动zookeeper

* ```bash
  docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper
  ```

#### 启动kafka

* ```bash
  docker run -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=192.168.3.200:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.3.200:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -t wurstmeister/kafka
  ```

* **其中ip地址需换成宿主机的ip地址**

### 测试kafka

#### 进入kafka容器

* ```bash
  docker exec -it kafka /bin/bash
  ```

#### 进入kafka所在目录

* ```bash
  cd /opt/kafka/
  ```

#### 再开一个xshell客户端，一个作为生产者，一个作为消费者，按上面操作进入kafka目录

#### 发送消息

* ```bash
  ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic kafka
  ```

#### 接收消息

* ```bash
  ./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kafka --from-beginning
  ```

#### 查看topic

* ```bash
  ./bin/kafka-topics.sh --zookeeper 192.168.3.200:2181 --list
  ```

## springboot-kafka简单demo

### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <!--spring-kafka-->
  <dependency>
      <groupId>org.springframework.kafka</groupId>
      <artifactId>spring-kafka</artifactId>
      <version>2.4.4.RELEASE</version>
  </dependency>
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
  </dependency>
  ```

#### 问题记录

* ```java
  org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.boot.autoconfigure.kafka.KafkaAnnotationDrivenConfiguration': Unexpected exception during bean creation; nested exception is java.lang.TypeNotPresentException: Type org.springframework.kafka.listener.RecordInterceptor not present
      at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:544) ~[spring-beans-5.3.2.jar:5.3.2]
      at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.2.jar:5.3.2]
      at org.springframework.beans.factory.support.AbstractBeanFactory$$Lambda$197/106557175.getObject(Unknown Source) ~[na:na]
  ```

* 问题原因：springboot版本与spring-kafka版本不对应，项目中使用的springboot版本是：`2.4.1`，经过测试，spring-kafka的版本需要在`2.4.4.RELEASE`以上，否则会报上面的异常
* springboot版本与spring-kafka版本的对应关系,可以查看[官网](https://spring.io/projects/spring-kafka#overview)
* 下面是官网截图：
* ![官网截图](../../file/image/docker-kafka/image-20201221173020696.png)

### application.yml

* ```yaml
  spring:
    kafka:
      producer:
        bootstrap-servers: 192.168.3.200:9092
      consumer:
        bootstrap-servers: 192.168.3.200:9092
        group-id: 0
  ```

### KafkaController.java

* ```java
  @RestController
  public class KafkaController {
  
      @Resource
      private KafkaProducer kafkaProducer;
  
      @GetMapping("/sendMessage")
      public String sendMessage(String message) {
          kafkaProducer.sendMessage(message);
          return "success";
      }
  }
  ```

### KafkaProducer.java

* ```java
  @Slf4j
  @Component
  public class KafkaProducer {
  
      @Resource
      private KafkaTemplate<String, String> kafkaTemplate;
  
      public void sendMessage(String message) {
          ListenableFuture<SendResult<String, String>> future = kafkaTemplate.send("kafka", message);
          future.addCallback(o -> log.info("kafka-消息发送成功：" + message), throwable -> log.info("kafka-消息发送失败：" + message));
      }
  }
  ```

### KafkaConsumer.java

* ```java
  @Slf4j
  @Component
  public class KafkaConsumer {
  
      @KafkaListener(topics = {"kafka"})
      public void listener(String message){
          log.info("kafka-收到消息："+message);
      }
  }
  ```

* 此时已经可以启动项目，访问：[http://localhost:8080/sendMessage?message=abc](http://localhost:8080/sendMessage?message=abc) 进行测试

### 整合swagger-ui

#### pom.xml

* ```xml
  <!--swagger-ui-->
          <dependency>
              <groupId>io.springfox</groupId>
              <artifactId>springfox-swagger2</artifactId>
              <version>2.9.2</version>
          </dependency>
  		<dependency>
              <groupId>io.springfox</groupId>
              <artifactId>springfox-swagger-ui</artifactId>
              <version>2.9.2</version>
          </dependency>
          <!--增强swagger-ui-->
          <dependency>
              <groupId>com.github.xiaoymin</groupId>
              <artifactId>knife4j-spring-boot-starter</artifactId>
              <version>2.0.4</version>
          </dependency>
  ```

#### Swagger2Config.java

* ```java
  @Configuration
  @EnableSwagger2
  @EnableKnife4j
  public class Swagger2Config {
  
      @Bean
      public Docket createRestApi() {
          return new Docket(DocumentationType.SWAGGER_2)
                  .apiInfo(apiInfo())
                  .select()
                  .apis(RequestHandlerSelectors.basePackage("com.example.kafka"))
                  .paths(PathSelectors.any())
                  .build();
      }
  
      private ApiInfo apiInfo() {
          return new ApiInfoBuilder().title("DEMO--API接口文档")
                  .description("接口返回数据格式为json。其基本结构为{'code': 200, 'msg':'', 'data':{...} }。其中code为错误代码，我们采用"
                          + "标准的http错误代码，比如200代表成功，400代表参数错误，401代表没有登录，500表示服务器内部错误无法完成请求"
                          + "。msg为错误消息，大部分时候为空，当code代码不为200时会是具体的错误描述。data" + "是返回的数据，data的具体内容请参考api。")
                  .termsOfServiceUrl("http://www.xxx.com/")
                  .version("1.0")
                  .build();
      }
  }
  ```

#### 测试地址

* 原生swagger-ui：[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)
* 增强swagger-ui：[http://localhost:8080/doc.html](http://localhost:8080/doc.html)
* 增强swagger-ui界面比原生好看一些（还有一些新增功能，可以百度了解），使用也比较简单，只需引入`knife4j-spring-boot-starter`依赖，在swagger配置类上加`@EnableKnife4j`注解就可以了

## [kafka相关概念学习](https://blog.csdn.net/lzb348110175/category_9309572.html)

### topic-partition

* Topic 是一个存储消息的逻辑概念，可以理解为是一个消息的集合
* Partition，分区，提高了Kafka的并发，也解决了Topic中数据的负载均衡
* **Kafka 只保证在同一个分区内的消息是有序的**

### consumer group

* 消费者组，由多个 consumer 组成， **消费者组内每个消费者负** **责消费不同分区的数据，一个分区只能由一个组内的某一个消费者消费；消费者组之间互不影响**
* **消费者组是逻辑上的一个订阅者**

### producer消息分发策略

* 在 Kafka 中，一条消息由 **key** 和 **value** 两部分组成
* producer 则会根据 key 和 partition 机制，来判断当前这条消息应该发送并存储到哪个 partition 中
* Kafka 采用的是 hash 取模的分区算法
* 如果 key 为 null 的话，则会随机的分配一个分区，这个随机是在这个参数 "metadata.max.age.ms"的时间范围内随机选择一个

### 消费者如何消费指定分区消息

* ```java
  /**
   * 1.使用 spring-kafka.jar包中的 KafkaTemplate 类型
   *   使用 @KafkaListener 注解方式
   *   如下：说明消费的是名称为test的topic下,分区 1 中的消息
   */
  @KafkaListener(topicPartitions = {@TopicPartition(topic = "test",partitions = {"1"})})
   
  /**
   * 2.使用kafka-clients.jar包中的 KafkaConsumer 类型
   * 如下：说明消费的是名称为test的topic下,分区 1 中的消息
   */
  TopicPartition topicPartition = new TopicPartition("test" , 1);
  KafkaConsumer consumer = new KafkaConsumer(props);
  consumer.assign(Arrays.asList(topicPartition));
  ```

### 分区分配策略

#### **多个Partitions有什么好处**

* 多个 partition ，能够对 broker 上的数据进行分片，通过减少消息容量来提升 IO 性能
* 为了提高消费端的消费能力，一般情况下会通过多个 conusmer 去消费 同一个 topic 中的消息，即实现消费端的负载均衡

#### **针对多个Partition，消费者该消费哪个分区的消息**

* **RangeAssignor 分配策略(范围分区)**
  * **默认采用 Range 范围分区**
  * 通过 **partitions数/consumer数** 来决定每个消费者应该消费几个分区
* **RoundRobinAssignor分配策略(轮询分区)**

#### 什么时候触发分区分配策略（**Kafka 消费者端的 Rebalance 操作**）

* 同一个 consumer 消费者组 group.id 中，新增了消费者进来，会执行 Rebalance 操作
* 消费者离开当期所属的 consumer group组。比如 **主动停机** 或者 **宕机**
* 分区数量发生变化时(即 topic 的分区数量发生变化时)
* 消费者主动取消订阅

### Rebalance，即对 [Kafka](https://so.csdn.net/so/search?q=Kafka&spm=1001.2101.3001.7020) 中的分区进行重新分配的过程

* Rebalance，即对 [Kafka](https://so.csdn.net/so/search?q=Kafka&spm=1001.2101.3001.7020) 中的分区进行重新分配的过程

#### **确定 Coordinator**

* 当消费者向 kafka 集群中的任意一个 broker 发送一个 GroupCoordinatorRequest 请求，Kafka Server 服务端会返回一个当前负载最小的 broker 节点的 id，并将该 id 所对应的的 broker 节点设置为当前 consumer group 的 Coordinator

#### **JoinGroup 过程**

* 当确定了 Coordinator 之后，所有的 Consumer 都会向 Coordinator 发送一个 JoinGroup 请求（只要启动，所有消费者都会发送该请求），此时 Coordinator 会从 Consumer group 中选取一个 consumer 担任 leader 角色，并把组成员信息和订阅的消息发送给所有的消费组

#### **Synchronizing Group State 阶段** 

* **该阶段主要完成 leader 将消费者对应的 partition 分配方案同步给consumer group 中的所有 consumer**
* 每个消费者，都会向 Coordinator 发送 SyncGroupRequest 请求
* 每个消费者，还会向 Coordinator 发送 SyncGroup 请求，不过只有 leader 节点会发送分配方案
* 当 leader 把方案发给 Coordinator 以后，Coordinator 会把结果设置到 SyncGroupResponse 中，这样所有成员都知道自己应该消费哪个分区

### 零拷贝

* 使用DMA技术，通过网卡直接去访问系统的内存
* 去掉那些没必要的数据复制操作， 同时也会减少上下文切换次数
* Java 提供了访问这个系统调用的方法：`FileChannel.transferTo API`
* linux 系统调用`sendfile()`
* 使用 `sendfile()` ，只需要一次拷贝就行，允许操作系统将数据直接从页缓存发送到网络上，所以在这个优化的路径中， 只有最后一步将数据拷贝到网卡缓存中是需要的

#### java零拷贝实现

* ```java
  File file = new File("demo.zip");
  RandomAccessFile raf = new RandomAccessFile(file, "rw");
  FileChannel fileChannel = raf.getChannel();
  SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("", 1234));
  // 直接使用了transferTo()进行通道间的数据传输
  fileChannel.transferTo(0, fileChannel.size(), socketChannel);
  ```

## [学习链接](https://blog.csdn.net/lzb348110175/category_9309572.html)
