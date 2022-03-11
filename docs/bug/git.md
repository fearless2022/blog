## Git报错：'fatal:remote origin already exists'怎么处理？附上git常用操作以及说明。

* ```bash
  # 先删除
  git remote rm origin
  # 再次执行添加就可以了。
  ```

## Git上传项目提示Push rejected: Push to origin/master was rejected解决办法

* ```bash
  # 先从远程拉取master分支
  git pull origin master --allow-unrelated-histories
  # 然后推送即可
  git push -u origin master
  ```
