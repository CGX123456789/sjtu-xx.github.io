---
title: COMSOL学习笔记（传热）
toc: true
mathjax: true
date: 2020-08-06 20:48:07
tags: [COMSOL, 传热]
categories:
- 材料
---

COMSOL传热多物理场
<!--more-->
## 理论
### 能量守恒（广义传热方程）
$$\rho C_p \frac{\partial T}{\partial t}+\nabla\cdot(-k\nabla T)=Q-\rho C_p u\cdot\nabla T+\tau :S+\frac{T}{\rho}(\frac{\partial\rho}{\partial T})_p (\frac{\partial p_a}{\partial t}+u\cdot\nabla p_a)$$

> ctrl + / 变量输入提示
> 


