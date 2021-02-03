---
title: pyinstaller打包
toc: true
mathjax: true
date: 2020-07-01 16:05:08
tags:
categories:
- Python
- 应用
---

pyinstaller用于windows下python代码的打包。
<!--more-->
## 参数说明
|参数|说明|
|----|----|
|-F|	指定打包后只生成一个 exe 格式的文件|
|-D|	–onedir 创建一个目录，包含 exe 文件，但会依赖很多文件（默认选项）|
|-c|	–console, –nowindowed 使用控制台，无界面 (默认)|
|-w|	去除控制台，建议在开发 gui 的时候使用|
|-i|	改变生成程序的 icon 图标|

测试文件
```python
from PySide2.QtCore import Slot
from PySide2.QtGui import QKeySequence
from PySide2.QtWidgets import QMainWindow, QAction,QApplication,QVBoxLayout,QLabel,QFrame
import sys
import numpy as np
import pandas as pd
from functools import reduce


class MainWindow(QMainWindow):
    def __init__(self):
        QMainWindow.__init__(self)
        self.setWindowTitle("Eartquakes information")

        # Menu
        self.menu = self.menuBar()
        self.file_menu = self.menu.addMenu("File")

        # Exit QAction
        exit_action = QAction("Exit", self)
        exit_action.setShortcut(QKeySequence.Quit)
        exit_action.triggered.connect(self.close)

        self.file_menu.addAction(exit_action)

        frame = QFrame()
        g_layout = QVBoxLayout()
        g_layout.addWidget(QLabel(','.join(map(str,np.arange(1,5).tolist()))))

        g_layout.addWidget(QLabel(reduce(lambda x,y:str(x)+","+str(y),pd.read_csv(r"C:\Users\10993\Desktop\1.csv", header=None).values.ravel().tolist())))
        frame.setLayout(g_layout)
        self.setCentralWidget(frame)

        # Status Bar
        self.status = self.statusBar()
        self.status.showMessage("Data loaded and plotted")

        # Window dimensions
        geometry = QApplication.desktop().availableGeometry(self)
        self.setFixedSize(int(geometry.width() * 0.8), int(geometry.height() * 0.7))

if __name__=="__main__":
    app = QApplication(sys.argv)
    wind = MainWindow()
    wind.show()
    sys.exit(app.exec_())
```
执行`pyinstaller -w --hidden-import=pkg_resources.py2_warn -F example.py`

## 出现的一些问题
1. 打包完成执行exe文件时，提示`Failed to execute script pyi_rth_pkgres`

可以通过增加hidden-import参数（Name an import not visible in the code of the script(s). This option can be used multiple times.）解决：`"pyinstaller --hidden-import=pkg_resources.py2_warn example.py"`
