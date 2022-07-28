## 下载ik

* ```bash
  # 下载地址
  https://github.com/medcl/elasticsearch-analysis-ik/releases
  # 选择的版本要与es版本一致
  ```

## 安装

* ```bash
  # 启动elasticsearch容器，进入plugins目录，创建ik文件夹
  docker exec -it elasticsearch /bin/bash
  cd plugins
  mkdir ik
  cd ik
  # 把刚才下载的zip上传到ik目录
  docker cp elasticsearch-analysis-ik-7.9.2.zip elasticsearch:/usr/share/elasticsearch/plugins/ik
  # 进入es容器ik目录，解压zip
  unzip elasticsearch-analysis-ik-7.6.1.zip
  # 删除zip文件
  rm -f elasticsearch-analysis-ik-7.6.1.zip
  # 退出es容器，并重新启动即可
  docker restart elasticsearch
  ```
