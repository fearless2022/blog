## 按年、月、日查询

* ```sql
  SELECT * FROM cms_book_statistics WHERE year(update_time ) = 2017 and month(update_time )= 09 and day(update_time ) = 27 and hour(update_time) = 12
  ```

* ```sql
  SELECT
  	DATE_FORMAT(alarm_at, '%Y-%m') months,
  	count(DISTINCT device_code) count
  FROM
  	alarm_record
  WHERE
  	logic_delete = 0
  AND group_id = 2
  AND YEAR (alarm_at) = 2020
  GROUP BY
  	months;
  
  SELECT
  	DATE_FORMAT(alarm_at, '%Y-%m-%d') days,
  	count(DISTINCT device_code) count
  FROM
  	alarm_record
  WHERE
  	logic_delete = 0
  AND group_id = 2
  AND YEAR (alarm_at) = 2020
  AND MONTH (alarm_at) = 12
  GROUP BY
  	days;
  
  SELECT
  	DATE_FORMAT(alarm_at, '%Y-%m-%d %H') hours,
  	count(DISTINCT device_code) count
  FROM
  	alarm_record
  WHERE
  	logic_delete = 0
  AND group_id = 2
  AND YEAR (alarm_at) = 2020
  AND MONTH (alarm_at) = 12
  AND DAY (alarm_at) = 31
  GROUP BY
  	hours;
  ```

## 常用命令

* ```sql
  # 查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次
  SHOW GLOBAL STATUS LIKE 'Com_______';
  # MySQL的慢查询日志默认没有开启
  SHOW VARIABLES LIKE 'slow_query_log';
  # 开启慢日志，在MySQL的配置文件（/etc/my.cnf）中配置
  # 开启MySQL慢日志查询开关
  slow_query_log=1
  # 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
  long_query_time=2
  # 重启
  systemctl restart mysqld
  ```

* **explain**

* ![explain](../file/image/mysql/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCR5aSp5YaN5YCfNTAw5bm0,size_20,color_FFFFFF,t_70,g_se,x_16.png)

## 查询数据库表结构

```sql
SELECT
	COLUMN_NAME 列名,
	COLUMN_TYPE 数据类型,
	IS_NULLABLE 是否允许为空,
	COLUMN_KEY 是否为主键,
	COLUMN_COMMENT 备注 
FROM
	INFORMATION_SCHEMA.COLUMNS 
	WHERE-- dj_admin为数据库名称，到时候只需要修改成你要导出表结构的数据库名称即可
	table_schema = 'nan_music' 
	AND -- user为表名，到时候换成你要导出的表的名称即可
-- 如果不写的话，默认会查询出所有表中的数据，这样可能就分不清到底哪些字段
	table_name = 'dj_admin'
-- 这个也就是你具体查看的那个表
```

