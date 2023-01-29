## linux安装jdk

### **查看Linux系统是否有自带的jdk**

* ```bash
  # 查看java版本
  java -version
  # 检测jdk的安装包
  rpm -qa | grep java
  # 删除包
  rpm -e --nodeps 包名
  # 检查是否删除完
  rpm -qa | grep java
  ```

### 下载jdk压缩包

* ```bash
  # 官网地址
  https://www.oracle.com/java/technologies/downloads/
  ```

* ![image-20230129170501307](../file/image/java/image-20230129170501307.png)

### 解压

* ```bash
  # 上传安装包
  scp jdk-8u361-linux-x64.tar.gz root@ip:/opt
  # 解压
  tar -zxvf jdk-8u361-linux-x64.tar.gz
  ```

### 配置环境变量

* ```bash
  # 编辑配置文件
  vim /etc/profile
  # 文件追加配置
  JAVA_HOME=/opt/jdk1.8.0_361
  PATH=/opt/jdk1.8.0_361/bin:$PATH
  export JAVA_HOME PATH
  # 刷新配置
  source /etc/profile
  # 检查安装结果
  java -version
  java
  javac
  ```

## comparator排序

* ```java
  // 排序
  Comparator<AlarmRecordTempDto> comparator = (o1, o2) -> {
      // 按时间降序
      if (!o1.getAlarmAt().isEqual(o2.getAlarmAt())) {
          return o1.getAlarmAt().isBefore(o2.getAlarmAt()) ? 1 : -1;
      } else {
          // 按最高等级升序
          if (!o1.getMaxAlarmLevel().equals(o2.getMaxAlarmLevel())) {
              return o1.getMaxAlarmLevel() - o2.getMaxAlarmLevel() > 0 ? 1 : -1;
          } else {
              // 按总告警数降序
              if (!o1.getTotalCount().equals(o2.getTotalCount())) {
                  return o1.getTotalCount() - o2.getTotalCount() < 0 ? 1 : -1;
              }
          }
      }
      return 0;
  };
  List<AlarmRecordTempDto> dtoListBySort = dtoList.stream().sorted(comparator).collect(Collectors.toList());
  ```

* ```java
  vos = vos.stream().sorted(Comparator.comparing(DataVo::getValue).reversed()).collect(Collectors.toList());
  ```

## json转换

* ```java
  JSONObject jsonObject = JSONObject.parseObject(alarmTemplate.getExtCfg());
  JSONObject uv = jsonObject.getJSONObject("undervoltageAlarm");
  String lv1 = jsonObject.getString("threshold1");
  ```

## list取子集

* ```java
  private <T> List<T> getListByCount(Integer count, List<T> list) {
      if (list.size() > count) {
          return list.subList(0, count);
      } else {
          return list;
      }
  }
  ```

## list属性映射

* ```java
  List<Long> userIds = projectUserList.stream().map(ProjectUser::getUserId).collect(Collectors.toList());
  ```

## jvm参数配置

* ```bash
  -Xms1m -Xmx1m -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDetails
  ```

## jdbctemplate批量插入

* ```java
  jdbcTemplate.batchUpdate(
  
      "insert into log (item_id, operate_type, operate_cause, log_type, group_id, logic_delete, created_by, created_at, updated_by, updated_at) values (?, ?, ?, ?, ?, ?, ?, ?, ?, ?);",
  
      new BatchPreparedStatementSetter() {
          @Override
          public void setValues(PreparedStatement preparedStatement, int i) throws SQLException {
              preparedStatement.setLong(1, logList.get(i).getItemId());
              preparedStatement.setInt(2, logList.get(i).getOperateType());
              preparedStatement.setString(3, logList.get(i).getOperateCause());
              preparedStatement.setInt(4, logList.get(i).getLogType());
              preparedStatement.setLong(5, logList.get(i).getGroupId());
              preparedStatement.setBoolean(6, logList.get(i).getLogicDelete());
              preparedStatement.setLong(7, logList.get(i).getCreatedBy());
              preparedStatement.setTimestamp(8, new Timestamp(logList.get(i).getCreatedAt().toInstant(ZoneOffset.of("+8")).toEpochMilli()));
              preparedStatement.setLong(9, logList.get(i).getUpdatedBy());
              preparedStatement.setTimestamp(10, new Timestamp(logList.get(i).getUpdatedAt().toInstant(ZoneOffset.of("+8")).toEpochMilli()));
          }
  
          @Override
          public int getBatchSize() {
              return logList.size();
          }
      });
  ```

## AQS 公平锁和非公平锁

* 非公平锁在调用 lock 后，首先就会调用 CAS 进行一次抢锁，如果这个时候恰巧锁没有被占用，那么直接就获取到锁返回了
* 非公平锁在 CAS 失败后，和公平锁一样都会进入到 tryAcquire 方法，在 tryAcquire 方法中，如果发现锁这个时候被释放了（state == 0），非公平锁会直接 CAS 抢锁，但是公平锁会判断等待队列是否有线程处于等待状态，如果有则不去抢锁，乖乖排到后面
