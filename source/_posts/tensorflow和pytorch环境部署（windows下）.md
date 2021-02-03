---
title: tensorflow和pytorch环境部署（windows下）
toc: true
date: 2020-03-14 18:31:25
tags:
categories:
- 深度学习
- 基础理论
---

由于现在tensorflow和pytorch更新都依赖cuda，更新比较麻烦，最近正好闲着，想着把电脑里面的环境更新一下。
<!--more-->

# cuda和cudnn的安装
## cuda的安装
直接从官网下载cuda。cuda直接运行安装。

## cudnn的安装
cudnn下载完成后，解压到cuda的安装目录即可。

# tensorflow的安装
## 安装
`conda install tensorflow-gpu==2.1.0`

## 测试代码
```python
import tensorflow as tf
import os

os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'  # 不显示等级2以下的提示信息
print('GPU', tf.test.is_gpu_available())

a = tf.constant(2.0)
b = tf.constant(4.0)
print(a + b)
```

# pytorch的安装
## 安装
`conda install pytorch torchvision cudatoolkit=10.1 -c pytorch`
## 测试
```python
import torch
print(torch.cuda.is_available())
```

# 其他软件包的安装
`conda install jupyter notebook scikit-learn opencv pillow lxml`
