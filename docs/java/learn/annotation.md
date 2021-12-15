## 注解

* Java 注解（Annotation）又称 Java 标注，是 JDK5.0 引入的一种注释机制
* **可以被其他程序(比如:编译器等)读取，与反射配合实现各种功能**

## 自定义注解

### 声明注解

* ```java
  // 只能作用于类上
  @Target(ElementType.TYPE)
  // 保存到运行时
  @Retention(RetentionPolicy.RUNTIME)
  public @interface DbTable {	
      String name() default "";
  }
  ```

* ```java
  @Target(ElementType.FIELD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface SqlInteger {
      // 该字段对应数据库表名
      String name() default "";
      // 嵌套注解
      Constraints constraint() default @Constraints;
  }
  ```

* ```java
  @Target(ElementType.FIELD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface SqlString {
      String name() default "";
      int value() default 0;
      Constraints constraint() default @Constraints;
  }
  ```

* ```java
  @Target(ElementType.FIELD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface Constraints {
      // 是否为主键
      boolean primaryKey() default false;
      // 是否允许为null
      boolean allowNull() default false;
      // 是否唯一
      boolean unique() default false;
  }
  ```

### 实体类

* ```java
  @Data
  @DbTable(name = "member")
  public class Member {
  
      @SqlString(name = "id", value = 50, constraint = @Constraints(primaryKey = true))
      private String id;
  
      @SqlString(name = "name", value = 30)
      private String name;
  
      @SqlInteger(name = "age")
      private int age;
  
      @SqlString(name = "description", value = 50, constraint = @Constraints(allowNull = true))
      private String description;
  }
  ```

### 注解解释器

* ```java
  public class TableCreator {
  
      public static String createTableSql(String className) throws ClassNotFoundException {
          String tableCreate = "";
          Class<?> cl = Class.forName(className);
          DbTable dbTable = cl.getAnnotation(DbTable.class);
          // 如果没有表注解，直接返回
          if (dbTable == null) {
              System.out.println("no db table annotations in class " + className);
              return null;
          }
          String tableName = dbTable.name();
          // if the name is empty, use the Class name
          if (tableName.length() < 1) {
              tableName = cl.getName().toLowerCase();
          }
          List<String> columnDefs = new ArrayList<>();
          // 通过Class类API获取到所有成员字段
          for (Field field : cl.getDeclaredFields()) {
              String columnName = null;
              // 获取字段上的注解
              Annotation[] annotations = field.getDeclaredAnnotations();
              if (annotations.length < 1) {
                  // not a db table column
                  continue;
              }
              // 判断注解类型
              if (annotations[0] instanceof SqlInteger) {
                  SqlInteger sqlInteger = (SqlInteger) annotations[0];
                  // 获取字段对应列名称，如果没有就使用字段名称
                  if (sqlInteger.name().length() < 1) {
                      columnName = field.getName().toLowerCase();
                  } else {
                      columnName = sqlInteger.name();
                  }
                  // 构建语句
                  columnDefs.add(columnName + " INT" + getConstraints(sqlInteger.constraint()));
              }
              // 判断String类型
              if (annotations[0] instanceof SqlString) {
                  SqlString sqlString = (SqlString) annotations[0];
                  // use field name if name not specified
                  if (sqlString.name().length() < 1) {
                      columnName = field.getName().toLowerCase();
                  } else {
                      columnName = sqlString.name();
                  }
                  columnDefs.add(columnName + " VARCHAR(" + sqlString.value() + ")" + getConstraints(sqlString.constraint()));
              }
              // 数据库表构建语句
              StringBuilder createCommand = new StringBuilder("CREATE TABLE " + tableName + "(");
              for (String columnDef : columnDefs) {
                  createCommand.append("\n    ").append(columnDef).append(",");
              }
              // remove trailing command
              tableCreate = createCommand.substring(0, createCommand.length() - 1) + ");";
          }
          return tableCreate;
      }
  
      private static String getConstraints(Constraints constraint) {
          String constraints = "";
          if (!constraint.allowNull()) {
              constraints += " NOT NULL";
          }
          if (constraint.primaryKey()) {
              constraints += " PRIMARY KEY";
          }
          if (constraint.unique()) {
              constraints += " UNIQUE";
  
              return constraints;
          }
  
          public static void main(String[] args) throws ClassNotFoundException {
              String[] arg = {"com.test.annotation.Member"};
              for (String className : arg) {
                  System.out.println("table creation sql for " + className + " is: \n" + createTableSql(className));
              }
          }
      }
  ```

### 测试结果

* ```sql
  table creation sql for com.test.annotation.Member is: 
  CREATE TABLE member(
      id VARCHAR(50) NOT NULL PRIMARY KEY,
      name VARCHAR(30) NOT NULL,
      age INT NOT NULL,
      description VARCHAR(50));
  ```

## 参考博客

* [深入理解Java注解类型(@Annotation)](https://blog.csdn.net/javazejian/article/details/71860633?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163955313216780271969210%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163955313216780271969210&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-71860633.first_rank_v2_pc_rank_v29&utm_term=%E6%B3%A8%E8%A7%A3&spm=1018.2226.3001.4187)
