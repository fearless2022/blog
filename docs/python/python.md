## 环境搭建

### 安装miniconda

* ```python
  # 安装地址
  https://docs.conda.io/projects/miniconda/en/latest
  # 配置环境变量
  path:D:\Work\WorkSoftware\miniconda3\Scripts
  # 打开cmd检查
  conda --version
  ```

### 配置vscode

* ```python
  # 安装python插件
  # 安装jupyter插件
  # 安装jupyter库
  conda install jupyter
  # 打开jupyter前端页面
  jupyter lab
  ```

### 创建开发环境

* ```python
  # conda帮助文档
  conda -h
  # 环境创建
  conda create -n dev python=3.8
  # 查看已创建的环境
  conda env list
  # 激活环境
  conda activate dev
  # 进入、退出python解释器
  python
  exit()
  # 退出环境
  conda deactivate
  # 删除环境
  conda env remove -n dev
  ```
