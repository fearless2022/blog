## 预览

> [fearless's blog](https://blog.fearless233.fun/)

> [fearless's puzzle game](https://puzzle.fearless233.fun/)

> [fearless's github](https://github.com/fearless2022/blog)

> [el-admin](https://web.fearless233.fun/)

## 使用工具

> 一个神奇的文档网站生成器 [docsify](https://docsify.js.org/#/zh-cn/)

## 安装docsify

* ```bash
  npm i docsify-cli -g
  ```

## 初始化项目

* ```bash
  # docs：项目目录
  docsify init ./docs
  ```

## 项目结构

* `index.html` 入口文件
* `README.md` 会做为主页内容渲染
* `.nojekyll` 用于阻止 GitHub Pages 忽略掉下划线开头的文件
* 更多自定义内容，查阅官网：[docsify](https://docsify.js.org/#/zh-cn/)

## 本地预览

* ```bash
  # docs：项目目录
  docsify serve docs
  ```

## 插件安装

> 按照官网步骤安装：引入对应js，配置docsify对象

### 插件介绍，效果预览

#### [全文搜索 - Search](https://docsify.js.org/#/zh-cn/plugins?id=全文搜索-search)

* 文档左上角搜索框

* ```javascript
  // todo 搜索某些关键词，search.min.js报错
  search.min.js:1 Uncaught TypeError: Cannot read properties of undefined (reading 'substring')
  ```

* ![image-20211213112817714](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213112817714.png)

#### [谷歌统计 - Google Analytics](https://docsify.js.org/#/zh-cn/plugins?id=谷歌统计-google-analytics)

* 需要Google账号，可使用百度统计，参考博客：[百度统计使用教程](https://blog.csdn.net/u013791936/article/details/115462785)
* 统计网站的访问情况

#### [emoji](https://docsify.js.org/#/zh-cn/plugins?id=emoji)

* 解析 emoji

#### [外链脚本 - External Script](https://docsify.js.org/#/zh-cn/plugins?id=外链脚本-external-script)

* 执行外链脚本

#### [图片缩放 - Zoom image](https://docsify.js.org/#/zh-cn/plugins?id=图片缩放-zoom-image)

* 可放大图片

#### [在 Github 上编辑](https://docsify.js.org/#/zh-cn/plugins?id=在-github-上编辑)

* 文档右上角 Edit on github 图标，可配置链接到github地址
* ![image-20211213113652837](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213113652837.png)

#### [复制到剪贴板](https://docsify.js.org/#/zh-cn/plugins?id=复制到剪贴板)

* 鼠标移入代码区，显示 Copy to clipboard 按钮
* ![image-20211213113826502](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213113826502.png)

#### [Gitalk](https://docsify.js.org/#/zh-cn/plugins?id=gitalk)

* 基于Preact和Github Issue的评论系统
* 解决报错：Gitalk Error: Validation Failed. 442，原因是：文章URL过长，解决方法参考博客：[处理 Gitalk Validation Failed(422)](https://www.jianshu.com/p/5c062e7d5921)

* ![image-20211213114042650](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213114042650.png)

#### [Pagination](https://docsify.js.org/#/zh-cn/plugins?id=pagination)

* docsify的分页导航插件

* ![image-20211213114147992](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213114147992.png)

#### [字数统计](https://docsify.js.org/#/zh-cn/plugins?id=字数统计)

* 文章字数统计，文档右上角显示
* ![image-20211213114219634](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213114219634.png)

#### [Tabs](https://docsify.js.org/#/zh-cn/plugins?id=tabs)

* 在 Markdown 中显示选项卡
* ![image-20211213114345661](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213114345661.png)

#### [docsify-sidebar-collapse](https://github.com/iPeng6/docsify-sidebar-collapse)

* 侧边栏折叠插件，安装参考博客：[docsify 侧边栏目录扩展](https://www.cnblogs.com/baby123/p/14361402.html)
* ![image-20211213115025647](../file/image/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/image-20211213115025647.png)

## 部署到Github

### Create a new repository

### 将本地的docs项目push到新建的仓库

* 参考博客：[docsify搭建高逼格博客](https://segmentfault.com/a/1190000038320425)

### GitHub Pages 绑定二级域名

* 参考博客：[GitHub Pages 绑定二级域名](https://www.cnblogs.com/givebest/p/5607472.html)

## 参考博客

* [处理 Gitalk Validation Failed(422)](https://www.jianshu.com/p/5c062e7d5921)
* [docsify搭建高逼格博客](https://segmentfault.com/a/1190000038320425)
* [GitHub Pages 绑定二级域名](https://www.cnblogs.com/givebest/p/5607472.html)
* [docsify 侧边栏目录扩展](https://www.cnblogs.com/baby123/p/14361402.html)
* [百度统计使用教程一](https://blog.csdn.net/u013791936/article/details/115462785)
* [Docsify快速搭建个人博客](https://www.imooc.com/article/287154)
