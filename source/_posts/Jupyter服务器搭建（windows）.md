---
title: Jupyter服务器搭建（windows）
toc: true
date: 2020-02-04 15:29:04
tags: [python, jupyter]
categories:
- Python
- 第三方库
---

1. 创建配置文件：`jupyter-notebook --generate-config`
<!--more-->
  配置文件位于`/User/当前用户/.jupyter/*.py`
2. 修改登陆访问密码
  `jupyter-notebook password`
3. 修改远程访问配置
  修改可访问ip：
  在配置文件中添加`c.NotebookApp.ip = '*'`
  修改工作目录：
  在配置文件中添加`c.NotebookApp.notebook_dir = 'workdir'`
4. 在其他电脑上通过访问`ip:port`进行连接
