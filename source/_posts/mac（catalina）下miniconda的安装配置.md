---
title: mac（catalina）下miniconda的安装配置
toc: true
date: 2020-01-26 13:50:41
tags: [anaconda,miniconda,python]
categories:
- Python
- 应用
---

# mac(catalina)下的miniconda环境配置
1. `brew install miniconda`
2. `conda init zsh`   或 `conda init shell`
3. `conda create env -f *.yml`  //yml文件通过`conda env export --file result.yml`进行备份

