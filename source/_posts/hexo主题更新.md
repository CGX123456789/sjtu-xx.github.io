---
title: hexo主题更新
toc: true
mathjax: true
date: 2020-07-22 15:21:06
tags:
categories:
- 其他工具
---

# hexo更新
<!--more-->
直接修改`package.json`中的版本号，执行`npm update`进行更新

# 主题更新
1. 主题下载
主题从github下载到`theme/icarus`目录
2. 安装
`hexo s`，按照提示通过npm安装对应的包。包的版本可以通过`package.json`中的版本号修改。所有包安装之后，再执行`hexo s`会在`icarus`目录生成对应的`_config.yml`文件


# 配置
1. 设置主页三栏，文章页两栏
将`_config.yml`复制到`_config.post.yml`，将`_config.post.yml`中的widget移动到一侧。
