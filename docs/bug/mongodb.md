## mongo插入数据报错：java.lang.IllegalArgumentException: Prohibited character at position 0

* ```yaml
  # 要加单引号，这个数字会被识别成空字符串
  password: '123456' 
  ```
