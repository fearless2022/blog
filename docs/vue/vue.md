## 安装node.js

* ```bash
  https://nodejs.org/zh-cn/
  
  # 安装淘宝镜像加速器，-g全局安装
  npm install cnpm -g
  
  # 检查版本
  node -v
  npm -v
  ```

## 安装vue-cli

* ```bash
  npm install vue-cli -g
  
  # 检查安装包
  vue list
  ```

##  创建目录

* ```bash
  # 进入目录
  vue init webpack my-vue
  
  # 安装依赖
  npm install
  
  # 运行
  npm run dev
  ```

## 安装vue-router

* ```bash
  npm install vue-router --save-dev
  
  # 导入
  import VueRouter from "vue-router"
  
  # 显示声明使用
  Vue.use(VueRouter)
  ```