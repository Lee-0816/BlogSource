---
title: Git常用命令总结
date: 2016-12-05 22:15:51
categories: 工具使用
tags: Git
description: Git常用命令使用总结。
---
## Local
# 一、初始化
<div align=center>
![](http://i4.buimg.com/567571/dd3793b2b627c019.png)
</div>
### （一）全局变量
1. `git config --global user.name "MrLrf"` 设置用户名
2. `git config --global user.email "liruifeng0718@163.com"` 设置邮箱
3. `git config --global color.ui "always"` 设置颜色

### （二）初始化版本库
1. `git init` 在当前根目录下生成.git目录

### （三）添加新文件到版本库
1. `git add somefile.text` 添加单个文件到暂存区
2. `git add *.txt` 添加所有txt文件到暂存区
3. `git add .; git add -u; git add -A` 添加所有文件暂存区（包括子目录，不包括空目录）
	区别：
		git add . ：他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。
		git add -u ：他仅监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区。add -u 不会提交新文件（untracked file）。（git add --update的缩写）
		git add -A ：是上面两个功能的合集（git add --all的缩写）

### （四）提交
1. `git commit -u "提交信息"` 提交代码

# 二、常用操作
<div align=center>
![](http://i1.piimg.com/567571/37ff9cdf2af2de5c.png)
</div>
### （一）提交
1. `git commit -m "some msg" -a` 提交所有修改
2. `git commit -m "some msg to readme.txt" readme.txt` 提交单个文件
3. `git commit -C head -a --amend` 增补提交，不会产生新的提交历史纪录

### （二）撤销修改
1. 撤销尚未提交的修改：
	1. `git checkout head readme.txt todo.txt` 撤销1、2个文件
	2. `git checkout head *.txt` 撤销所有txt文件
	3. `git checkout head .` 撤销所有文件
2. `git revert --no -commit head` 提交最近一次提交的反操作
3. `git reset head <filename>` 取消暂存
4. `git reset -- hard head^^` 复位到head之前的那个版本，不会在版本库中留下痕迹

### （三）分支
1. `git branch` 列出本地分支
2. `git branch -a` 列出所有分支
3. `git branch <branchname>` 基于当前分支的末梢创建新分支
4. `git checkout <branchname>` 检出分支（切换到新分支）
5. `git checkout -b <branchname>` 基于当前分支的末梢创建新分支并切换分支
6. 合并分支：
	1. `git merge <branchname>` 合并并提交（如果发生了冲突就不会自动提交，如果冲突很多，不想立即解决他们，可以直接使用git checkout head .撤销）
	2. `git merge --no -commit` 合并但不提交
7. 重命名分支：
	1. `git branch -m <branchname> <newname>` 重命名分支（不会覆盖已存在的同名分支）
	2. `git branch -M <branchname> <newname>` 重命名分支（会覆盖已存在的同名分支）
8. 删除分支：
	1. `git branch -d <branchname>` 删除分支（如果分支没有被合并会删除失败）
	2. `git branch -D <branchname>` 删除分支（即使分支没有被合并也照删不误）

### （四）解决冲突
1. 冲突很少时，直接编辑有冲突的文件然后提交即可
2. `git merge tool` 冲突比较复杂时，生成.backup，.base，.local，.remote四个文件，然后自动调用冲突解决工具，解决冲突之后手动删除.orig文件（冲突解决之前的文件备份），最后提交。

### （五）查看状态
1. `git status` 当前状态
2. `git log` 历史纪录
	注：git log指令后，输入q然后回车终止指令。
3. `git branch -v` 每个分支最后的提交
4. `git diff` 查看更改前后的区别

## Remote
# 一、初始化
<div align=center>
![](http://i4.buimg.com/567571/32f38e331a5edf03.png)
</div>
### （一）克隆版本库
1. `git clone <url>` 克隆url对应的版本库

### （二）添加远程仓库
1. `git remote add <别名(origin)> <远程版本库的url>` 将GitHub或其他设置为本地仓库的远程仓库
2. `git remote rm <别名(origin)>` 删除本地仓库的远程仓库及分支

### （三）创建一个无本地分支的库
1. `git init -bare` 当需要一个公用的中央库时，非常适合把它建成bare库

# 二、常用操作
<div align=center>
![](http://i2.muimg.com/567571/5ea022645ca04cac.png)
</div>
### （一）分支
1. `git branch -r` 列出远程分支
2. `git remote prune origin` 删除远程库中已经不存在的分支

### （二）从远程库获取
1. `git fetch <远程版本库>` 例：`git fetch origin` 获取但不合并
2. `git pull` 获取并合并到当前本地分支

### （三）推入远程库
1. `git push (origin master)`

# 三、http://github.com
<div align=center>
![](http://i1.piimg.com/567571/45e3577b951601b5.png)
</div>
GitHub上连接已有仓库时的认证，是通过使用了 SSH 的公开密钥
认证方式进行的。创建SSH Key：`ssh-keygen -t rsa -C "你的github邮箱"`。然后输入`cat ~/.ssh/id_rsa.pub`然后就复制到粘贴板。

