title: Git 操作介绍
author: Mentran
date: 2019-07-30 14:17:21
tags:
---
## Git简介
Git是一个分布式版本控制系统，每个终端都是一个完整的版本库，保留了所有数据信息和修改记录。

## 用户配置
### 全局配置
* 设置以后该计算机上所有的git仓库都使用该用户
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
***
## 工作区，暂存区，仓库

![upload successful](/images/pasted-0.png)

![upload successful](/images/pasted-1.png)

* Working Tree 当前的工作区域
* Index/Stage 暂存区域，和git stash命令暂存的地方不一样。使用git add xx，就可以将xx添加近Stage里面
* Repository 提交的历史，即使用git commit提交后的结果
***

## 常用命令
```bash 
cd new_floder
git init                # 在 new_floder 下创建本地仓库，当前目录即为 工作区
touch new_file      # 创建新文件
git add .               # 将该目录下文件加入到 暂存区
git status              # 查看修改信息
git commit -m "message" # 将暂存区的内容提交到 当前分支
```
Git管理的是文件的修改，需要将所有改变的文件通过 `git add` 加入到暂存区。`git commit`只负责提交暂存区中的内容到当前分支上。


### reset三种模式
reset就是让 **HEAD** 指针指向目标节点。
1. git reset **--hard**: 重置HEAD位置，并把工作区，暂存区和仓库内容都完全重置到Reset节点的内容
2. git reset **--soft**: 重置HEAD位置，保留工作区和暂存区的内容，只让仓库内容与Reset节点一致。原节点与Reset不一致的内容都放到暂存区中。
3. git reset **--mixed(默认)**: 重置HEAD位置，只保留工作区的内容，将暂存区和仓库内容更改与Reset节点保持一致。原节点与Reset节点不一致的内容都放到工作区。

### 撤销
```
git status                  # 查看当前暂存区的内容

git checkout -- file        # 将工作区中的内容修改进行撤销
# 如果暂存区有内容则使用暂存区内容
# 否则使用版本库中的内容

git reset --hard HEAD^      # 将所有内容回退到上一个版本
git reset --hard commit_id  # 将所有内容回退到指定版本
```
***
### 远程仓库
#### 提交
1. 首先通过Github建立一个远程仓库的repository
2. `git remote add origin git@github.com:xxx/xxx.git`
3. `git push origin master` # 将本地的(master)内容提交到远程库上

#### 克隆
```
git clone git@github.com/xxx/xxx.git                # 默认克隆master分支的内容
git clone -b new_branch git@github.com/xxx/xxx.git  # 克隆new_branch
```
***
### 分支管理
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
git branch                  # 查看分支情况
git branch -d new_branch    # 删除分支
```

#### merge
切换回master分支，并将new_branch分支进行合并
```
git checkout master
git merge new_branch
```
***
### stash保存现场
保存当前的工作区环境，处理其他branch事务；完成后再恢复现场。
```
git stash       # 保存现场
...             # 其他事务
git stash list  # 查看现场
git stash pop   # 恢复最近的现场
```