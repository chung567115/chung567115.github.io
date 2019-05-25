---
title: Git常用命令汇集
categories: 开发工具
tags:
  - Git
date: 2019-05-25 09:45:32
---

> 开发中常用到的Git命令大集合

<!-- more -->
# 一张图大致了解最基础的Git命令流程
![Git命令](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/Git命令.jpg)

&emsp;&emsp;图中名词解释
- Workspace: 工作区
- Stage: 暂存区
- Local: 本地仓库
- Remote: 远程仓库

# 最基础
```bash
# 初始化git仓库，将会创建.git文件夹
git init

# 克隆simaple项目
git clone  user@server:path/repo.git
git clone https://server/path/simpale

# 将指定文件文件加入本地仓库
git add simple-file

# 将当前目录所有文件加入本地仓库
git add

# 将所有文件加入本地仓库
git add -A
git add --all

# 从远仓库指定分支拉取并合并最新代码,默认为merge方式，可选用rebase参数
git pull [remote] [branch] [--rebase]

# 提交文件到本地仓库,并附加备注信息msg
git commit -m [msg]

# 提交文件到远程仓库指定分支,变基之后需要force参数才能正常提交
git push [remote] [branch] [--force]

# 从远程仓库更新代码
git fetch [remote]

# 合并远程或指定branch分支到当前分支
git merge [branch]

# 相比于merge，多人同分支开发时可用rebase避免多次merge commit
git rebase [branch]

# 解决冲突后继续处理
git rebase --continue

# 检出远程分支sample2到本地并命名为sample1
git checkout -b featrues/sample1 origin/featrues/sample2
```

# 其他基础

```bash
添加一个新的远程git仓库,此后便可以用字符串origin代替整个url
git remote add [origin] [url]

# 查看远程服务器
git remote [-v]

# 查看当前状态
git status

# 查看工作区和缓存区的不同
git diff

# 查看当前分支版本历史
git log

###########################撤销############################
# 撤回上一个commit操作,软删除,代码还在
git reset --soft HEAD~1

# 恢复[commit]修改前
git revert [commit]

############################分支###########################
# 新建本地分支
git branch [branch-name]

# 切换到分支
git checkout [branch-name]

# 查看本地分支
git branch

# 查看远程分支
git branch -r

# 查看所有分支
git branch -a

# 删除本地指定分支
git branch -d [branch]

# 删除远程指定分支
$ git push origin --delete [branch]
$ git branch -dr [remote/branch]

#######################从远程master新建分支##################
git checout master                   # 切换到本地master
git pull                             # 更新本地master
git checkout -b featrues/sample      # 新建并切换到分支sample
git push origin featrues/sample      # 提交sample分支到远程
############################################################
```

# 进阶
```bash
# 缓存工作区当前分支内容
git stash

# 查看所有缓存栈
git stash list

 # 选中版本n缓存出栈
git stash apply stash@{n}
```

<!-- more -->