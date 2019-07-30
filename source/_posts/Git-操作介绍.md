title: Git 操作介绍
author: Mentran
date: 2019-07-30 14:17:21
tags:
---
# Git

## 简介
Git是一个分布式版本控制系统，每个终端都是一个完整的版本库，保留了所有数据信息和修改记录。

## 配置
### 全局配置
设置以后该计算机上所有的git仓库都使用该用户
```bash
git config --global user.name "Name"
git config --global user.email "Email"
```
### 单独配置
1. 通过修改每个项目的.git/config文件进行设置
```
# 增加user配置
[user]
    name = username
    email = email@address.com
```
2. 在项目中重新指定用户
```
git config user.name "New user"
git config user.email "New email"
```

## 使用
```bash 
cd new_floder
git init                # 在 new_floder 下创建本地仓库，当前目录即为 工作区
git touch new_file      # 创建新文件
git add .               # 将该目录下文件加入到 暂存区
git status              # 查看修改信息
git commit -m "message" # 将暂存区的内容提交到 当前分支
```
Git管理的是文件的修改，需要将所有改变的文件通过 `git add` 加入到暂存区。`git commit`只负责提交暂存区中的内容到当前分支上。

## 撤销
```
git status                  # 查看当前暂存区的内容

git checkout -- file        # 将没有add的文件内容进行撤销，丢弃工作区的修改

git reset HEAD file         # 将没有commit的文件从暂存区丢弃，不会撤销文件的内容

git reset --hard HEAD^      # 将所有内容回退到上一个版本
git reset --hard commit_id  # 将所有内容回退到指定版本
```

## 远程仓库
### 提交
1. 首先通过Github建立一个远程仓库的repository
2. `git remote add origin git@github.com:xxx/xxx.git`
3. `git push origin master` # 将本地的(master)内容提交到远程库上

### 克隆
```
git clone git@github.com/xxx/xxx.git        # 默认克隆master分支的内容
git clone -b new_branch git@github.com/xxx/xxx.git      #克隆new_branch
```

## 分支管理
分支就是在当前记录下通过构建一个新的指针来创建一个支线。

创建新的分支并切换
```
git branch new_branch
git checkout new_branch
```

```
git checkout -b new_branch
```

```
git branch              # 查看分支情况
git branch -d new_branch # 删除分支
```

### merge
切换回master分支，并将new_branch分支进行合并
```
git checkout master
git merge new_branch
```

## stash保存现场
保存当前的工作区环境，处理其他branch事务；完成后再恢复现场。
```
git stash       # 保存现场
...             # 其他事务
git stash list  # 查看现场
git stash pop   # 恢复最近的现场
```