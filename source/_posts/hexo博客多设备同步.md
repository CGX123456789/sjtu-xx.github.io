---
title: hexo博客多设备同步
toc: true
mathjax: true
date: 2021-02-04 00:09:35
tags:
- hexo
- 博客
categories:
- 其他工具
---



之前的hexo博客一直使用mac进行创建提交等。因为很多东西经常需要在windows上进行操作，如果发送到mac再进行提交就有些复杂，因此，尝试在两个操作系统上进行提交。

参考链接：https://www.jianshu.com/p/fceaf373d797

<!--more-->

github上的repo创建了两个分支，master保存静态页面，hexo用于保存网站的全部文件。

# 旧环境

1. 将旧环境中的文件上传到github的hexo分支：
   - github上切换到hexo分支，`git clone`仓库到本地。
   - 此时本地会多出一个`username.github.io`文件夹，命令行`cd`进去，删除除`.git`文件夹（如果你看不到这个文件夹，说明是隐藏了。windows下需要右击文件夹内空白处，点选'显示/隐藏 异常文件'，Mac下我就不知道了）外的其他文件夹。
   - 命令行`git add -A`把工作区的变化（包括已删除的文件）提交到暂存区（ps:`git add .`提交的变化不包括已删除的文件）。
   - 命令行`git commint -m "some description"`提交。
   - 命令行`git push origin hexo`推送到远程hexo分支。此时刷下github，如果正常操作，hexo分支应该已经被清空了。
   - 复制本地`username.github.io`文件夹中的`.git`文件夹到hexo项目根目录下。此时，hexo项目已经变成了和远程hexo分支关联的本地仓库了。而`username.github.io`文件夹的使命到此为止，你可以把它删掉，因为我们只是把它作为一个“中转站”的角色。以后每次发布新文章或修改网站样式文件时，`git add . & git commit -m "some description" & git push origin hexo`即可把环境文件推送到hexo分支。然后再`hexo g -d`发布网站并推送静态文件到master分支。

# 新环境

这部分应该要简单一点，如果你已经搭建过一个hexo博客的话。

- 新电脑上安装node.js和git。
- 安装hexo：`npm install -g hexo-cli`。
- clone远程仓库到本地 `git clone git@github.com:username/username.github.io.git`。
- 根据`packge.json`安装依赖`npm install`。
- 本地生成网站并开启博客服务器：`hexo g & hexo s`。如果一切正常，此时打开浏览器输入`http://localhost:4000/`已经可以看到博客正常运行了。

# 在两台电脑上的同步操作

至此，迁移工作已完成，在两台电脑之间的同步操作如下：

- `git pull`从远程hexo分支拉取最新的环境文件到本地，可以理解为svn的更新操作。比如在公司写了博客，回家在电脑上也要写需要先执行这一步操作。
- 文章写完，要发布时，需要先提交环境文件，再发布文章。按以下顺序执行命令：`git add .`、`git commit -m "some descrption"`、`git push origin hexo`、`hexo g -d`。