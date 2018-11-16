---
title: git命令
date: 2018-11-15 21:21:33
categories: 技术工具
---
# <center>git常用命令</center>
* 以下是一些学习工作中常用的git操作，以供记录使用。

## 1.初始化仓库
```shell
git init
```
在当前目录下创建.git目录，同时当前目录成为一个Git仓库,意味着当前目录下的文件状态的改变，会被git记录

## 2.从远程仓库克隆

`git clone [git仓库url]`将http或ssh链接指向的Git仓库拷贝到本地，默认是master分支。

`git clone -b [分支名] [git仓库url]`则克隆的是该分支的仓库内容。

## 3.增加、注释操作
有很多的工具可以更方便的添加和注释，不必要非要使用代码。

## 4.当前状态
`git status`：显示当前仓库的最新状态。
```
位于分支 hexo
您的分支与上游分支 'origin/hexo' 一致。

尚未暂存以备提交的变更：
  （使用 "git add <文件>..." 更新要提交的内容）
  （使用 "git checkout -- <文件>..." 丢弃工作区的改动）

	修改：     db.json
	修改：     package-lock.json
	修改：     package.json

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）

	"source/_posts/git\345\221\275\344\273\244.md"

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

## 5.git日志
`git log`：显示从最近到最远的提交日志。包含每个提交的SHA1校验和、作者的名字和邮箱、提交时间以及提交说明等。
```
commit 67c8e0708bb198789df2896dfb47dcb27db7c564
Author: xiexuliunian <zzuzxd@126.com>
Date:   Fri Oct 26 21:14:49 2018 +0800

    添加CI

commit 918fa11a2a6f43736ea2716e5458b611d8998e22
Merge: e2cb954 bd6c67c
Author: xiexuliunian <zzuzxd@126.com>
Date:   Fri Oct 26 20:33:42 2018 +0800
```
## 6.拉取操作
`git pull`:拉取远程仓库最新提交，并合并到指定的本地分支上。

`git fetch`:拉取远程仓库最新提交，但不会自动合并分支。

## 7.推送操作
`git push`：将本地分支更改推送到远程仓库。

如果你是一个人这个操作，一般不会有问题，但是如果是多人协作一个项目，这一步很可能会有问题，原因是别人更新的新代码，你本地没有，因此下面操作是每次`push`前尽量遵循的操作。
```
git stash       //暂存你本地的所有更改，使本地相对与上次pull是一个干净的环境。
git pull        //拉取远程的仓库。
git stash pop   //在上次拉取的新的代码上合并暂存的代码。
git push        /现在你就可以放心的push代码了。
```
## 8.当你push出错的时候
当多人合作时，如果你push之前没有pull那么你很有可能会遇到
```
! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/dummymare/Hello-World.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
使用`git pull --rebase`：--rebase的作用是取消掉本地库中刚刚的commit，并把他们接到更新后的版本库之中。

常用的解决方案是：
```
git stash           
git pull --rebase   
git stash pop       
git push            
```
