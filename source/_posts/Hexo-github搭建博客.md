---
title: Hexo+github搭建个人博客并实现多终端管理
date: 2017-05-05 17:30:23
categories: 工具使用
tags: hexo
description: 在经历了无数次的折腾之后,终于完成了心目中的hexo个人博客,虽然有很多东西待完善(比如主题,Travis CI自动部署),但是还是要简单的总结一下的.
---
## 前言 ##
----------
前段时间在博客园写自己的第一篇博客被舍友看到,舍友说推荐一个个人博客搭建的方法叫hexo,从此我就踏上了这条不归路,至今已有几个月,终于大体完成了自己想要的功能,在这里记录一下.当然,未来还有很长的路要走...
使用hexo搭建个人博客大概可以分以下几个步骤:
1. 环境的搭建(基于Windows)
2. 安装Hexo并配置本地博客
3. 将本地博客同步到GitHub上
4. 管理个人博客
5. 管理博客主题(主题NexT的简单配置)
6. 多终端同时管理博客

## 环境的搭建(基于Windows) ##
----------
1. 下载并安装Node.js([Windows Installer 64-bit](https://nodejs.org/dist/v4.2.3/node-v4.2.3-x64.msi))
[点击打开安装教程](http://www.runoob.com/nodejs/nodejs-install-setup.html)
2. 下载并安装Git([Git-2.6.3-64-bit.exe](https://github-cloud.s3.amazonaws.com/releases/23216272/84b33b96-87f5-11e5-8f91-32080286239e.exe?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161210%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161210T033734Z&X-Amz-Expires=300&X-Amz-Signature=912c155bbe0fe970ca7b948f5f0d5e8c68c712b7fb8006062f53c8638c62c7b6&X-Amz-SignedHeaders=host&actor_id=14971673&response-content-disposition=attachment%3B%20filename%3DGit-2.6.3-64-bit.exe&response-content-type=application%2Foctet-stream))
[点击打开安装教程](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)
3. 注册GitHub账号

## 安装Hexo ##
----------
全局安装,在git bash下输入:`npm install hexo-cli -g`,执行完成之后输入:`npm install hexo --save`.安装完成之后输入`hexo -v`,出现版本信息则表明安装成功.
新建一个文件夹(随便什么地方),例如D:\hexo,在文件夹下打开git bash执行命令 `hexo init` 然后执行 `npm install`,之后npm将会自动安装你需要的组件，只需要等待npm操作完成即可。
继续在git bash中操作,执行命令 `hexo g`,完成之后执行 `hexo s`,控制台会提示
```
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
```
在浏览器中打开http://localhost:4000/ ，你将会看到：
![](http://opf32dlts.bkt.clouddn.com/17-5-5/86678797-file_1493993054937_a74a.jpg)

至此,本地博客已搭建成功.

> 注意: 若执行`hexo s`命令后在浏览器打开http://localhost:4000/ 没有反应,可能的原因是端口号被占用,可以执行`hexo s -p 端口号(5000)`,然后再浏览器打开http://localhost:5000/ 即可

## 将本地博客同步到GitHub上 ##
----------
1. 配置GitHub 首先要在本地生成ssh秘钥上传到注册的GitHub账户上,这里不再赘述,然后创建一个Repository,仓库名必须为[你的用户名.github.io].
2. 编辑_config.yml文件 用文本编辑器打开_config.yml文件,翻到最下面,改成下面的样子
```
deploy:
  type: git
  repository: https://github.com/MrLrf/MrLrf.github.io.git
  branch: master
```
3. 安装必要插件 进入本地hexo目录,在git bash下执行命令`npm install hexo-deployer-git --save`.
4. 重新部署 在git bash中依次执行`hexo clean`（清除旧的public生成文件）,`hexo generate`（或者`hexo g`生成静态文件）,`hexo server`（本地预览）,`hexo deploy`（部署到github上，第一次会有弹框提示，输入yes，点击ok）. 然后在浏览器中输入https://mrlrf.github.io/ 就可以访问了.

至此,个人博客搭建成功!

> 部署步骤 每次部署可以分为以下几步来进行
> `hexo clean`（清除旧的public生成文件）
> `hexo generate`（可以简写`hexo g`,生成静态文件）
> `hexo server`（可以简写`hexo s`,本地预览）
> `hexo deploy`（可以简写`hexo d`,部署到github上)

## 管理个人博客 ##
----------
1. 写一篇文章
输入命令`hexo new "hello"`生成一个.md文件,路径为hexo目录/source/_posts/
![](http://opf32dlts.bkt.clouddn.com/17-5-11/7938235-file_1494470636033_1460d.png)
用Markdown编辑器(Windows系统推荐MarkdownPad2)打开hello.md文件,然后编辑,完成后保存.然后重新部署之后就可以在博客中看到新写的博客了.
2. 配置信息
使用文本编辑器打开博客根目录下的_config.yml文件进行编辑.
```
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
# 博客名称
title: MyBlog
# 副标题
subtitle: 
# 简介
description: 在追寻真理的路上，我们要勇于犯错，只有犯错，我们才会离真理更进一步。
# 博客作者
author: Tristan
# 博客语言
language: zh-Hans
# 时区
timezone:

# 博客地址,与申请的GitHub一致
url: https://mrlrf.github.io/
root: /
# 博客链接格式
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# 日期格式
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination(分页)
## Set per_page to 0 to disable pagination(每页文章数量)
per_page: 10
pagination_dir: page

# Extensions(博客主题)
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/landscape
theme: hexo-theme-next

# Deployment(发布设置)
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: https://github.com/MrLrf/MrLrf.github.io.git
  branch: master
```

## 管理博客主题 ##
----------
博客的主题文件放在博客根目录themes文件夹下,修改主题的方法为:将主题文件夹下载放在themes文件夹下,然后在博客根目录_config.yml文件中修改theme为主题文件夹名,然后重新部署即可.
推荐几个我使用过的博客主题:

- [NexT](https://github.com/iissnan/hexo-theme-next)
简洁美观,目前Github上Star最高的Hexo主题，支持几种不同的风格。
![](http://opf32dlts.bkt.clouddn.com/17-5-11/26134374-file_1494474966697_129e1.jpg)
- [Yilia](https://github.com/litten/hexo-theme-yilia)
![](http://opf32dlts.bkt.clouddn.com/17-5-11/68196472-file_1494475636357_48a2.gif)
- [Yelee](https://github.com/MOxFIVE/hexo-theme-yelee)
该主题是作者基于Yilia修改而来，改变了大量的样式。
![](http://opf32dlts.bkt.clouddn.com/17-5-11/59189359-file_1494481082580_a630.jpg)

如果要修改主题的配置比如头像,友情链接之类的可以直接在主题文件夹下的_config.yml文件中修改,每个主题都有其对应的说明,都非常详细,这里就不多说了.

下面是本文最重要的部分,多终端管理博客.
## 多终端同时管理博客 ##
很多人都是公司一台电脑,家里一台电脑,像我,学校实验室还有一台电脑,我想去同时管理博客,达到备份博客主题、文章、配置的目的.接下来介绍一种可以解决此问题的方法.
**首先,将你的博客内容备份到GitHub.**
1. 配置.gitignore文件。进入博客根目录下，找到此文件，用文本编辑器打开，在最后增加两行内容/.deploy_git和/public.
2. 在博客根目录下,在git bash中执行`git init`和`git remote add origin <server>`命令,初始化仓库并绑定GitHub远程仓库.
3. 将博客内容同步到远程仓库,在git bash中执行一下命令
```
git add . #添加目录下所有文件
git commit -m "更新说明" #提交并添加更新说明
git push -u origin master #推送更新到远程仓库
```
**其他电脑pull远程仓库文件**
1. 在你的另一台电脑上先安装好git、node、hexo等,然后建好hexo文件夹并初始化,安装好插件,然后在博客根目录下git bash中执行以下命令:
```
git init
git remote add origin <server>
git fetch --all
git reset --hard origin/master
```
**发布博客后同步**
在一台电脑上发不完博客之后,记得将博客备份同步到远程仓库,在博客根目录下git bash中执行以下命令:
```
git add .
#可以用git master 查看更改内容
git commit -m "更新信息"
git push -u origin master #以后每次提交可以直接git push
```
**平时同步管理**
每次想写博客时，先执行`git pull`进行同步更新.发布完文章后同样按照上面的 发布博客后同步 同步到远程仓库。
如此就能解决多终端同时管理博客的问题了.

----------
转载请注明出处:https://mrlrf.github.io/2017/05/05/Hexo-github搭建博客/
作者博客:https://mrlrf.github.io