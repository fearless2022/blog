## 进入项目主目录

## Maven-clean项目

## Maven-package项目

## 进入target目录

* ```bash
  cd target
  ```

## jar包上传

* ```bash
  scp eem-0.0.1.jar root@ip:/www/project/eemsys/
  ```

* ```bash
  scp dist.zip root@ip:/www/project/eemsys/
  ```

## 连接服务器

* ```bash
  ssh root@ip
  ```

## 进入项目目录

* ```bash
  cd /www/project/eemsys/
  ```

## 查询进程

* ```bash
  ps -ef|grep ee[m]
  ```

* ```bash
  root     20307     1  0 Apr08 ?        00:02:15 java -jar eem-0.0.1.jar --spring.profiles.active=test
  ```

## 关闭进程

* ```bash
  kill -9 20307
  ```

## 删除前端工程

* ```bash
  rm dist.zip
  ```

* ```bash
  rm -f dist.zip	(-f, --force                            强制删除。忽略不存在的文件，不提示确认)
  ```

* ```bash
  rm -r dist	( -r, -R, --recursive                递归删除目录及其内容)
  ```

* ```bash
  rm -rf dist
  ```

## 运行jar包

* ```bash
  nohup java -jar eem-0.0.1.jar --spring.profiles.active=test > eem.log &
  ```

## 解压前端工程

* ```bash
  unzip dist.zip
  ```
