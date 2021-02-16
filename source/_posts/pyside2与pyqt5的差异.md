---
title: pyside2与pyqt5的差异
toc: true
mathjax: true
date: 2020-07-01 15:36:25
tags:
categories:
- Python
- 第三方库
---

由于pyqt5的GPLv3协议要求，使用其的软件必须开源。而pyside2的LGPL协议允许闭源商用。
<!--more-->
1. 导入库的差异
`from PySide.QtCore import *`
2. pyside只支持pyqt中的api2
api1指的是使用qt中的QString,QVariant等类型
api2则是另外一种更加pythonic的接口，使用原生的Python的数据类型。
3. 信号和槽
pyside2:`QtCore.Signal()`和`QtCore.Slot()`
pyqt:`QtCore.pyqtSignal()`和`QtCore.pyqtSignal`
4. 属性
pyside2:`QtCore.Property`
pyqt:`QtCore.pyqtProperty`
5. 功能性差异
pyside中不提供qt4.5之前已经废弃函数的python接口
6. sender() 成员在 partial 或 lambda 函数内返回 None
7. 当继承一个类时，父类的构造函数总是需要被调用
8. 旧式风格的信号需要使用圆括号
`self.emit(SIGNAL ('text_changed_cb(QString)'), text)`

由于兼容性问题，pyside还存在一些问题
安装：
测试成功的办法
python3.7
pyside2 5.14.2
