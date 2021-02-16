---
title: COMSOL疲劳
toc: true
mathjax: true
date: 2020-07-31 16:07:19
tags: [COMSOL, 疲劳]
categories:
- 材料
---
# COMSOL仿真
<!--more-->
## COMSOL Multiphysics

## 结构疲劳与损伤仿真

视频地址：https://cn.comsol.com/video/simulation-of-structural-fatigue-and-damage-webinar-cn

### 基础

- S-N曲线

  环境、表面粗糙度、残余应力等都会影响材料的疲劳寿命

- $R=\frac{\sigma_{min}}{\sigma_{max}}$ 

  R=-1: 对称循环

  R=0：脉冲测试

- 分类

  - 高周疲劳：弹性变形、应力范围控制
  - 低周疲劳（几千次）：塑性变形、应变范围控制

- 裂纹

  - 疲劳裂纹通常出现在表面
  - 接触分析时通常出现在内部。

### 仿真方法

单个载荷周期的结果---》疲劳后处理

### 疲劳模型

**应力寿命**

![](1.png)



**应变寿命** 常用于低周疲劳

![](2.png)




