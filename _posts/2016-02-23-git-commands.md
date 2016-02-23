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


## 7.分支

    # 获取本地分支
    $ git branch
    # 获取远程分支
    $ git branch -r 
    # 获取所有分支，包括远程分支
    $ git branch -a





