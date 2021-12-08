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