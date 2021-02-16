---
title: TimeDistributed层（keras）
toc: true
date: 2020-03-11 10:28:36
tags:
categories:
- 深度学习
- 基础理论
---

# tf.keras.layers.TimeDistributed
<!--more-->
**This wrapper allows to apply a layer to every temporal slice of an input.**
输入至少应当有3个维度，第一个维度被函数默认为是一个临时的维度。
考虑一个具有32个样本的样本集合，每个样品都是由10个16维的向量组成。这个batch的输入shape就应当是(32,10,16),不包括样本个数维度的输入shape是(10,16)。

这个时候就可以使用TimeDistributed来对10个向量每个向量添加一个Dense层。此时输出是（32,10,8）维度的。
```python
model = Sequential()
model.add(TimeDistributed(Dense(8), input_shape=(10, 16)))
# now model.output_shape == (None, 10, 8)
```
对于一个子序列层，不需要输入input_shape参数
```python
model.add(TimeDistributed(Dense(32)))
# now model.output_shape == (None, 10, 32)
```
输出尺寸为(32, 10, 32).

同样TimeDistributed可以用于任何层,比如Conv2D layer:
```python
model = Sequential()
model.add(TimeDistributed(Conv2D(64, (3, 3)),
                          input_shape=(10, 299, 299, 3)))
```

