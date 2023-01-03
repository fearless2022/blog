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
  new LambdaQueryWrapper<>().apply("date_format(finished_time,'%Y-%m-%d')='" + LocalDate.now() + "'")
  ```
