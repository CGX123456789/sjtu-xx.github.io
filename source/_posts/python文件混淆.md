---
title: python文件混淆
toc: true
mathjax: true
date: 2020-07-25 21:32:56
tags:
categories:
- Python
- 应用
---

选择了一种比较合适的加密方式对python文件进行加密。
<!--more-->
## 混淆
网址：https://pyob.oxyry.com/

该方法通过将变量名替换，实现加密效果。

该方法的缺点在于，只能对单个文件进行加密。无法对多个文件进行加密

## pyc
在混淆后对文件进行编译生成pyc文件，这样反解码出来的文件也是乱码。
### 单个pyc文件生成
`python -m foo.py`

`import py_compile   py_compile.compile('/path/to/foo.py')`
### 批量生成
`python -m compileall <dir>`

```python
import compileall
compileall.compile_dir(r'/path')
```



## 打包
还可以考虑将混淆文件打包成exe文件发布。
