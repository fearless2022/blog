## 自动填充

### 实现**MetaObjectHandle**接口

* ```java
  @Component
  public class MyMetaObjectHandler implements MetaObjectHandler {
      /**
       * 新增时候插入时间
       *
       * @param metaObject
       */
      @Override
      public void insertFill(MetaObject metaObject) {
          Long userId = null;
          try {
              userId = UserUtil.getUserId();
          } catch (UnavailableSecurityManagerException e) {
              userId = null;
          }
          this.setFieldValByName("createdBy", userId, metaObject);
          this.setFieldValByName("createdAt", LocalDateTime.now(), metaObject);
          this.setFieldValByName("updatedBy", userId, metaObject);
          this.setFieldValByName("updatedAt", LocalDateTime.now(), metaObject);
          this.setFieldValByName("deleted", 0, metaObject);
          this.setFieldValByName("reportRepairTime", LocalDateTime.now(), metaObject);
  
      }
  
      /**
       * 更新时更新时间
       *
       * @param metaObject
       */
      @Override
      public void updateFill(MetaObject metaObject) {
          Long userId = null;
          try {
              userId = UserUtil.getUserId();
          } catch (UnavailableSecurityManagerException e) {
              userId = null;
          }
          this.setFieldValByName("updatedBy", userId, metaObject);
          this.setFieldValByName("updatedAt", LocalDateTime.now(), metaObject);
      }
  }
  ```

### 填充字段加**@TableField**注解

* ```java
  @TableField(fill = FieldFill.INSERT)
  @TableField(fill = FieldFill.INSERT_UPDATE)
  ```

### 继承**BaseMapper<T>**

### 使用insert()、updateById()**内置方法**

### 自定义sql，**参数为实体类**，不能加@param注解，**sql语句必须写出填充字段**

## 查询

* ```java
  // 查询当天数据
  new LambdaQueryWrapper<>().apply("date_format(finished_time,'%Y-%m-%d')='" + LocalDate.now() + "'");
  aicNianBeastAtkRecordMapper.selectCount(new LambdaQueryWrapper<AicNianBeastAtkRecord>()
                  .eq(AicNianBeastAtkRecord::getMemberId, memberId)
                  .eq(AicNianBeastAtkRecord::getDefeated, 1)
                  .apply("date_format(now(), '%Y-%m-%d') = date_format(create_time, '%Y-%m-%d')"));
  // 查询字段
  aicNianBeastThemeMapper.selectCount(new QueryWrapper<AicNianBeastTheme>()
                  .select("DISTINCT entrance_id").lambda()
                  .eq(AicNianBeastTheme::getDelFlag, 0));
  // 随机查询一条数据
  aicNianBeastMessageMapper.selectOne(new LambdaQueryWrapper<AicNianBeastMessage>()
                  .eq(AicNianBeastMessage::getType, msgType)
                  .eq(AicNianBeastMessage::getDelFlag, 0)
                  .last("order by rand() limit 1")).getMessage();
  ```
