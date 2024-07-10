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

## 集合相关

* ```java
  // list取子集
  private <T> List<T> getListByCount(Integer count, List<T> list) {
      if (list.size() > count) {
          return list.subList(0, count);
      } else {
          return list;
      }
  }
  // 利用org.apache.commons.collections4.CollectionUtils类中的方法求交/并/差集
  //intersection: 取交集
  CollectionUtils.intersection(list1,list2).stream().collect(Collectors.toList());
  //取并集（去重）
  CollectionUtils.union(list1,list2).stream().collect(Collectors.toList());
  //取差集
  CollectionUtils.subtract(list1,list2).stream().collect(Collectors.toList());
  //取两个集合的交集的补集(补集一般指绝对补集，即一般地，设S是一个集合，A是S的一个子集，由S中所有不属于A的元素组成的集合，叫做子集A在S中的绝对补集)
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

## stream流相关

* ```java
  // 注意Stream不会改变原对象，而会返回一个持有结果的新Stream
  // 手动分页
  List<DataVo> list = list.stream().skip((item.getPageNum() - 1) * item.getPageSize()).limit(item.getPageSize()).collect(Collectors.toList());
  // list按字段分组
  Map<LocalDate, List<AnalysisDailyRetain>> map = data.stream().collect(Collectors.groupingBy(AnalysisDailyRetain::getBeginDate));
  // list属性映射
  List<Long> userIds = projectUserList.stream().map(ProjectUser::getUserId).collect(Collectors.toList());
  // 排序
  List<DataVo> vos = vos.stream().sorted(Comparator.comparing(DataVo::getValue).reversed()).collect(Collectors.toList());
  // comparator排序
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
  /**
   * 交集
   * @param list1
   * @param list2
   * @return
   */
  private static List<String> intersect(List<String> list1, List<String> list2) {
      List<String> intersect = list1.stream().filter(item -> list2.contains(item)).collect(Collectors.toList());
      return intersect;
  }
  
  /**
   * 差集（list1-list2）
   * @param list1
   * @param list2
   * @return
   */
  private static List<String> minus(List<String> list1, List<String> list2) {
      List<String> minus = list1.stream().filter(item -> !list2.contains(item)).collect(Collectors.toList());
      return minus;
  }
  
  /**
   * 并集（不去重）
   * @param list1
   * @param list2
   * @return
   */
  private static List<String> unionAll(List<String> list1, List<String> list2) {
      list1.addAll(list2);
      return list1;
  }
  
  /**
   * 并集（去重）
   * @param list1
   * @param list2
   * @return
   */
  private static List<String> union(List<String> list1, List<String> list2) {
      list1.addAll(list2);
      return list1.stream().distinct().collect(Collectors.toList());
  }
  ```

## 保留x位小数

* ```java
  public static double round(double v, int scale) {
      if (scale < 0) {
          throw new IllegalArgumentException(
                  "The scale must be a positive integer or zero");
      }
      BigDecimal b = BigDecimal.valueOf(v);
      BigDecimal one = new BigDecimal("1");
      return b.divide(one, scale, BigDecimal.ROUND_HALF_UP).doubleValue();
  }
  ```

## 日期相关

* ```java
  // LocalDate转字符串-2024/06/25
  String dateStr = LocalDate.of(2024, 6, 25).format(DateTimeFormatter.ofPattern("yyyy/MM/dd"));
  // 字符串转LocalDate
  LocalDate date = LocalDate.parse("20240526", DateTimeFormatter.ofPattern("yyyyMMdd"));
  // 获取最近的周一日期
  LocalDate.now().with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY));
  // 获取本月1号
  LocalDate.now().with(TemporalAdjusters.firstDayOfMonth());
  // 周几-TUESDAY
  System.out.println(LocalDate.of(2024, 6, 25).getDayOfWeek());
  // 输出周几中文-星期二
  System.out.println(LocalDate.of(2024, 6, 25).getDayOfWeek().getDisplayName(TextStyle.FULL, Locale.CHINA));
  // 是否周五-false
  System.out.println(LocalDate.of(2024, 6, 25).getDayOfWeek() == DayOfWeek.FRIDAY);
  // 本月第几天-25
  System.out.println(LocalDate.of(2024, 6, 25).getDayOfMonth());
  // 获取两个日期之间的所有日期
  public static List<LocalDate> getDatesBetween(LocalDate startDate, LocalDate endDate) {
      List<LocalDate> dates = new ArrayList<>();
      long numOfDaysBetween = ChronoUnit.DAYS.between(startDate, endDate);
      for (int i = 0; i <= numOfDaysBetween; i++) {
          LocalDate date = startDate.plusDays(i);
          dates.add(date);
      }
      return dates;
  }
  // 获取两个日期之间的每周一
  public List<String> getMondayBetween(LocalDate startDate, LocalDate endDate) {
      List<String> mondays = new ArrayList<>();
      while (!startDate.isAfter(endDate)) {
          if (startDate.getDayOfWeek() == DayOfWeek.MONDAY) {
              mondays.add(startDate.format(DateTimeFormatter.ofPattern("yyyyMMdd")));
          }
          startDate = startDate.plusDays(1);
      }
      return mondays;
  }
  // 获取两个日期之间的每月1号
  public List<String> getMonthBetween(LocalDate startDate, LocalDate endDate) {
      List<String> months = new ArrayList<>();
      while (!startDate.isAfter(endDate)) {
          LocalDate monthInitial = startDate.withDayOfMonth(1);
          months.add(monthInitial.format(DateTimeFormatter.ofPattern("yyyyMMdd")));
          startDate = startDate.plus(1, ChronoUnit.MONTHS);
      }
      return months;
  }
  ```
