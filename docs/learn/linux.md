## 搜索文件并列出完整目录

* ```bash
  find / -name *.jar -exec ls -l {} \;
  ```

## 在文件中搜索字符串

* ```bash
  grep -n 定时 eem.log
  
  -i ：忽略大小写的不同，所以大小写视为相同
  -n ：顺便输出行号
  -v ：反向选择，亦即显示出没有 ‘搜寻字符串’ 内容的那一行
  ```

## 查询起始日志

* ```bash
  head -4 test.c <行数> 显示的行数
  ```

## 查询最后100行实时日志

* ```bash
  tail -100f eem.log
  
  -f 循环读取
  -n <行数> 显示行数
  ```

## 查询日志

* ```bash
  less eem.log 
  
  -i 忽略搜索时的大小写
  -N 显示每行的行号
  /字符串：向下搜索“字符串”的功能
  ?字符串：向上搜索“字符串”的功能
  n：重复前一个搜索（与 / 或 ? 有关）
  N：反向重复前一个搜索（与 / 或 ? 有关）
  q:quit
  
  less 工具也是对文件或其它输出进行分页显示的工具，应该说是linux正统查看文件内容的工具，功能极其强大。
  less 的用法比起 more 更加的有弹性。在 more 的时候，我们并没有办法向前面翻， 只能往后面看但若使用了 less 时，就可以使用 [pageup][pagedown] 等按键的功能来往前往后翻看文件，更容易用来查看一个文件的内容。
  除此之外，在 less 里面拥有更多的搜索功能，不止可以向下搜，也可以向上搜
  ```

## 浮点数运算

* ```bash
  bc
  
  bc 1.06.95
  Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
  This is free software with ABSOLUTELY NO WARRANTY.
  For details type `warranty'.
  ```

## 查询时间

* ``` bash
  date
  date +%Y:%m:%d_%H:%M:%S 指定格式显示时间：yyyy-MM-dd_HH:mm:ss
  date +%s 显示时间戳
  date -d@1 显示1970年1月1日8时零分零秒
  
  %H : 小时(00…23)
  %M : 分钟(00…59)
  %S : 秒(00…61)
  %X : 相当于 %H:%M:%S
  %d : 日 (01…31)
  %m : 月份 (01…12)
  %Y : 完整年份 (0000…9999)
  %F : 相当于 %Y-%m-%d
  ```

## 查询日历

* ```bash
  cal 显示当月日历
  cal -3 显示系统前一个月，当前月，下一个月的月历
  cal -j 显示在当年中的第几天（一年日期按天算，从1月1号算起，默认显示当前月在一年中的天数）
  cal -y 显示当前年份的日历
  ```

## which/whoami命令

* ```bash
  whoami 查看当前的用户是root用户还是普通用户
  which +指令 查看当前指令怎么构成的
  ```

## zip/unzip命令

* ```bash
  zip copy.zip copy/*
  rm -rf copy
  unzip copy.zip -d copy
  
  -r 递归处理，将指定目录下的所有文件和子目录一并处理
  ```

## tar命令

* ```bash
  tar指令（重要）：打包/解包，不打开它，直接看内容
  
  -c ：建立一个压缩文件的参数指令(create 的意思)；
  -x ：解开一个压缩文件的参数指令！
  -t ：查看 tarfile 里面的文件！
  -z ：是否同时具有 gzip 的属性？亦即是否需要用 gzip 压缩？
  -j ：是否同时具有 bzip2 的属性？亦即是否需要用 bzip2 压缩？
  -v ：压缩的过程中显示文件！这个常用，但不建议用在背景执行过程！
  -f ：使用档名，请留意，在 f 之后要立即接档名喔！不要再加参数！
  -C ： 解压到指定目录
  ```

## uname 命令

* ```bash
  uname -r
  uname -a
  
  -a或–all 详细输出所有信息，依次为内核名称，主机名，内核版本号，内核版本，硬件名，处理器类型，硬件平台类型，操作系统名称
  ```

## shutdown命令

* ```bash
  shutdown
  
  -h ： 将系统的服务停掉后，立即关机。
  -r ： 在将系统的服务停掉之后就重新启动
  -t sec ： -t 后面加秒数，亦即『过几秒后关机』的意思
  ```

## scp命令

* ```bash
  scp -P 65535 admin@ip:/www/project/huafei_eemsys/dist.zip D:\Work\WorkSoftwareStartUp
  
  scp -P 29966 -r root@远程ssh服务器的ip:/home/wwwroot/.* /Users/ianMac/Desktop/
  
  -P ： 指定端口，大写字母P
  -r ： 递归上传指定目录
  ```

## ssh命令

* ```bash
  ssh -p 65535 admin@ip
  ```

## 防火墙相关

* ```bash
  # 查看防火墙状态
  
  systemctl status firewalld
  
  # 如果不是显示active状态，需要打开防火墙
  
  systemctl start firewalld
  
  # 查看所有已开放的临时端口（默认为空）
  
  firewall-cmd --list-ports
  
  # 查看所有永久开放的端口（默认为空）
  
  firewall-cmd --list-ports --permanent
  
  # 添加临时开放端口（例如：比如我修改ssh远程连接端口是223，则需要开放这个端口）
  
  firewall-cmd --add-port=223/tcp
  
  # 添加永久开放的端口（例如：223端口）
  
  firewall-cmd --add-port=223/tcp --permanent
  
  # 关闭临时端口
  
  firewall-cmd --remove-port=80/tcp
  
  # 关闭永久端口
  
  firewll-cmd --remove-port=80/tcp --permanent
  
  # 配置结束后需要输入重载命令并重启防火墙以生效配置
  
  firewall-cmd --reload
  
  systemctl restart firewalld
  ```
