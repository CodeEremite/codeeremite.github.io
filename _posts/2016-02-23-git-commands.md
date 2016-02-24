---
layout:      default
title:       Git命令清单 
keywords:    git
description: git常用命令清单
categories:  [git]
tags:        git
---

# Git命令清单

## 1.Git基本命令

    # 查看Git版本
    $ git --version

    # 查看Git命令帮助,以git config为例,三种方法
    $ git help config
    $ git config --help
    $ man git-config

## 2.Git配置

Git的全局配置文件`--global`为`~/.gitconfig`，项目配置文件在项目目录`{workpath}/.git/config`下。

    # 显示当前的Git配置 
    $ git config --list

    # 编辑Git配置文件
    $ git config -e [--global]

    # 基本设置
    $ git config [--global] user.name "<name>"
    $ git config [--global] user.email "<email address>"
    
项目中添加忽略文件方法:

+ `{workpath}/.gitignore` 所有人都需要忽略的文件，会随本地版本库其他内容一起传播 

+ `{workpath}/.git/info/exlude` 本地版本库会忽略，但不会传播

## 3.新建代码库

    # 在当前目录新建
    $ git init

    # 新建一个名为project_name的目录，并将其初始化为Git代码库
    $ git init <project_name>

    # 下载远程项目及其整个代码历史
    $ git clone [url] [localname]

## 4.增加/删除文件

    # 添加文件到暂存区
    $ git add [file1] [file2] ...
    $ git add [dir]
    $ git add .

    # 删除工作区文件，并将此次删除放入暂存区
    $ git rm [file1] [file2] ...

    # 停止追踪指定文件，但文件会保留在工作区
    $ git rm --cached [file]    

    # 重命名工作区文件，并将其放入暂存区
    $ git mv [file-oldname] [file-newname]

## 5.提交

    # 提交暂存区到仓库区
    $ git commit -m "<message>"
    $ git commit [file1] [file2] ... -m "<message>"

    # 直接将已跟踪文件的变动提交到仓库区 
    $ git commit -am "<message>"

    # 使用一次新的提交，替代上一次提交
    $ git commit --amend -m "<message>"

## 6.查看信息    

HEAD : 指当前所在分支末梢的最新提交，即最新版本

    # 显示有变更的文件
    $ git status 

    # 比较工作区和暂存区之间的区别
    $ git diff

    # 比较暂存区和仓库区间的区别
    $ git diff --cached

    # 比较工作区与仓库区的差别
    $ git diff HEAD

    $ 比较当前工作目录树与<SHA>版本
    # git diff <SHA>

    $ 比较两个版本间的差异
    # git diff <SHA1> <SHA2>

    # 查看提交日志
    $ git log 

    # 显示最近n个提交
    $ git log -n

    # 显示从提交<SHA>到现在的全部提交 
    $ git log <SHA>

    # 利用时间范围查找提交日志
    $ git log --since "5 hours"
    $ git log --before "5 hours"

    # 显示两版本间的提交
    $ git log <SHA-old> <SHA-new>

    # 显示指定文件什么人什么时间修改过
    $ git blame <file>

## 7.分支

分支多用于三个场景:

+ 支持不同版本
+ 开发新功能
+ bug修复

常用分支命令:

    # 列出所有本地分支 [远程分支|全部分支]
    $ git branch [-r | -a]

    # 新建分支,但仍然停留在当前分支
    git branch <branch-name> 

    # 新建分支，并切换到该分支
    git checkout -b <branch-name> 

    # 新建分支，指向某个tag
    git checkout -b <branch-name> <tag-name>

    # 切换到指定分支，并更新工作区
    git checkout <branch-name>

    # 重命名分支 
    git branch -m <old-branch> <new-branch>

    # 删除分支(见后注)
    git branch [-d | -D] <branch-name>

    # 删除远程分支
    $ git push origin --delete <branch-name>
    $ git branch -dr <remote/branch>

    # 直接合并
    # 合并指定分支到当前分支
    $ git merge <branch-name>

    # 压合全并
    # 将一个分支若干提交压合成一个，提交到当前分支末梢(压合合并),添加到暂存区未提交
    $ git merge --squash <branch-name> 
    
    # 拣选合并
    # 拣选另一条分支上某提交，将其合并入当前分支
    $ git cherry-pick [-n] <commit-SHA>

注:

+ 删除分支一般发生在三种场景中。

  + 分支已打标签并已发布(打标签后，Git会保留至此标签的历史轨迹)
  + 试验分支成功完成工作或中途废弃
  + 合并回当前分支后

    **只有当要删除的分支已成功合并回当前分支时，用选项-d删除分支的操作才能成功**  
    **如确定无须合并，则用选项-D**  

+ 冲突总是发生在对不同分支上的同一文件的同一文本块以不同的方式修改，并试图合并的时候。

  + 简单的冲突，手工编辑并解决冲突即可，然后保存修改、暂存、提交
  + 复杂的冲突用mergetool (`git mergetool`可以查看本地merge.tool的值)

## 8.标签

标签(里程碑)记录版本库一个特定点，用来标识难读难记的内部版本号

    # 列出所有标签
    $ git tag 

    # 新建一个tag在当前commit [指定commit | 指定分支的最后一个commit]
    $ git tag <tag-name> [<commit> | <branch>]

    # 删除本地tag
    $ git tag -d <tag-name>

    # 查看tag信息
    $ git show <tag-name>

## 9. 撤销

    # 重置HEAD至指定状态，即恢复前面的修改
    $ git reset

