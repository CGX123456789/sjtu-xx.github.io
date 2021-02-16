---
title: mac内置python环境
toc: true
date: 2019-12-28 20:09:24
tags: ["python"]
categories:
- Python
- 应用
---

防止与anaconda中的环境冲突设置alias
在`~/.bash_profile`(如果使用zsh需要在`~/.zshrc`中)中添加`alias python_mac="/usr/local/Cellar/python/3.7.5/bin/python3"`
然后通过`python_mac`安装包
`python_mac -m pip install scikit-image`
