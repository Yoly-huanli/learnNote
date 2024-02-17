[toc]



## github没有提交记录显示

需要绑定github的账号邮箱

 git config user.email XXX

## github把node_modules上传上去了

新建.gitignore文件，配置上传文件

```
node_modules/   表示过滤这个文件夹
```

## git本地修改想换到另一个分支

修改时，直接在本地的master分支上进行了修改，没有提交，想新建一个分支，把修改内容迁移过去

+ Git stash 新增的文件无法监听到

  ===》 直接git checkout -b xxx即可，不需要做任何操作

# git rebase

+ 整理提交记录

  ```
  # 将最近的三个提交合并为一个新的提交
  git rebase -i HEAD~3
  ```

+ 合并代码，解决冲突
