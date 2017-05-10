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
使用hexo搭建个人博客大概可以分一下几个步骤:
1. 环境的搭建(基于Windows)
2. 安装Hexo并配置本地博客
3. 将本地博客同步到GitHub上
4. 管理个人博客
5. 主题 配置
6. 主题NexT的简单配置
7. 多终端同时管理博客

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
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```
在浏览器中打开http://localhost:4000/，你将会看到：
<div align=center>
![](http://opf32dlts.bkt.clouddn.com/17-5-5/86678797-file_1493993054937_a74a.jpg)
</div>
至此,本地博客已搭建成功.

> 注意: 若执行`hexo s`命令后在浏览器打开http://localhost:4000/没有反应,可能的原因是端口号被占用,可以执行`hexo s -p 端口号(5000)`,然后再浏览器打开http://localhost:5000/即可

## 将本地博客同步到GitHub上 ##
----------
