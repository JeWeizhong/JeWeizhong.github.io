---

title: git极简入门
categories: 
  - git
tags:
  - git
date: 2018-11-23

---

## 1. 安装(略)

## 2.基础命令

`git config --global user.name "Your Name"`
`git config --global user.email "email@example.com"`

`git init [dir]` : 目录变成Git可以管理的仓库
`git add [file] [file]...` : 添加文件到仓库
`git commit -m "一些版本描述"` : 把(多个)文件交给仓库
`git log --pretty=oneline ` : 改动日志
` git reset --hard HEAD^` || ` git reset --hard HEAD^^` : 切换到上次修改，或者上上次修改
`git reset --hard [哈希值]` ： 切换到哈希值对应的版本号
` git status` : 查看工作区状态
`git diff ` : 比较文件
`git checkout -- [file]` ： 撤销工作区的修改，回到最近一次`git commit`或`git add`时的状态
`git rm [file]`  : 从版本库里删除文件
`git checkout [哈希值]` : 切换版本

## 添加远程仓库
```sh
git remote add origin https://github.com/JeWeizhong/Machine-learning-in-action-notes-and-code.git
git push -u origin master #第一次提交
git push origin master -f #强制覆盖已有分支
```
## 远程仓库同步到本地

１.　假如需要在另一个地方写项目可以用一下步骤：
```sh
git clone https://github.com/JeWeizhong/Machine-learning-in-action-notes-and-c # 克隆到本地
#在本地修改后
git add .
git commit  -m "creat DTs"
git push origin master
```

1. 假如本地已经有项目代码，需要同远程的项目合并：
```sh
# 从远程的origin的master主分支下载最新的版本到origin/master分支上
Git fetch origin master 
# 比较本地的master分支和origin/master分支的差别
git log -p master ..origin/master
git merge origin/master # 合并
```
第二种方式：
```sh
# 从远程下载到master下的tmp分支上
git fetch origin master:tmp 
# 比较
git diff tmp 
# 合并
git merge tmp
```
或者直接用`git pull origin master`，但是看不到细节

3. *目前还没学会分支*