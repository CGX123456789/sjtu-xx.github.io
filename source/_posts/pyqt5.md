---
title: pyqt5
toc: true
date: 2020-06-29 21:24:56
tags:
categories:
- Python
- 第三方库
---

## PYQT5
<!--more-->
pyqt5下，dialog用exec_()方法，widget用show()方法;

#### 1.QDockWidget

[Dock嵌套布局](<https://blog.csdn.net/czyt1988/article/details/51209619>)

QDockWidget是一个可以停靠在QMainWindow内的窗口控件，它可以保持浮动状态或在指定位置作为子窗口附加到主窗口中。其常用方法有：
setWidget()，用于设置在DockWidget内显示的QWidget及其子类对象。
setFloating()，设置DockWidget是否可以浮动，参数为True或False。
setAllowedArea()，设置可以停靠的区域，可用参数为Qt.DockWidgetAreas{LeftDockWidgetArea, RightDockWidgetArea, TopDockWidgetArea, BottomDockWidgetArea, AllDockWidgetAreas, NoDockWidgetArea}。
setFeatures()，设置停靠窗口的功能属性（有些属性决定了显示哪些按钮），可用参数为QDockWidget.DockWidgetFeatures{DockWidgetClosable, DockWidgetMovable, DockWidgetFloatable, DockWidgetVerticalTitleBar, AllDockWidgetFeatures, NoDockWidgetFeatures}。
其常用信号有：
topLevelChanged(bool topLevel)：当浮动状态变化时发生，参数为True时为浮动状态。
void visibilityChanged(bool visible)：当显示状态变化时发生（一般是在以Tab方式显示时使用），参数为True时可见。

QMainWindow类中包含了有关DockWidget的一些函数方法，主要有：
addDockWidget(Qt.DockWidgetArea area, QDockWidget dockwidget)，将DockWidget添加到主窗口，第一个参数为停靠位置，第二个参数为要添加的DockWidget。
setDockOptions()，设置停靠属性，参数类型为QMainWindow.DockOptions { AnimatedDocks, AllowNestedDocks, AllowTabbedDocks, ForceTabbedDocks, VerticalTabs, GroupedDragging }
setTabPosition(Qt.DockWidgetAreas areas, QTabWidget.TabPosition position)，设置停靠和标题位置，第一个参数为停靠位置，第二个参数为标题位置，取值范围QtabWidget.TabPosition { North, South, West, East }。
tabifyDockWidget(QDockWidget first, QDockWidget second)，以Tab方式显示DockWidget，其中第二个窗体排在第一个之后。
setTabShape(QTabWidget.TabShape shape)，设置Tab标签样式，取值范围QTabWidget.TabShape { Rounded, Triangular }。

#### 2.右键菜单

```
       item_widget.setContextMenuPolicy(QtCore.Qt.CustomContextMenu)
       item_widget.customContextMenuRequested[QtCore.QPoint].connect(lambda pos: main_window.cad_item_right_click_menu(pos, item, item_widget))
```



**将菜单在当前位置显示**

```python
	方法一：
		self.TextContextMenu.move(QtGui.QCursor.pos()) 
        self.TextContextMenu.show()  
     方法二：
        #在当前坐标下显示，但是菜单停止不走动
        self.TextContextMenu.exec_(QtGui.QCursor.pos())        
        
        #将菜单在右上方显示
        self.TextContextMenu.move(self.pos() + pos)  
```

#### 3.自定义list item

<https://stackoverflow.com/questions/25187444/pyqt-qlistwidget-custom-items>

#### 4.Margin

(left, top, right, bottom)

#### 5.信号额外参数传递

<https://blog.csdn.net/fengyu09/article/details/39498777>

[传递额外参数但是不影响默认信号](<https://codeday.me/bug/20190326/831567.html>)

#### 6.拖放技术

[拖放技术](<https://www.jianshu.com/p/378ee9b63d48>)

使用拖放技术可实现在不同控件或应用之间的数据交换。使用该技术的典型示例是在Windows资源管理器中移动文件。要将文件移动到另一个目录，只需用鼠标左键单击文件图标差按住不放，将文件拖动到目标目录图标，然后释放鼠标按钮。如果要复制文件，而不是移动文件，则还要按住键。
**1.拖动**
(1) 在`mousePressEvent()`中，记录鼠标按下时的位置；
(2) 在`mouseMoveEvent ()`中，计算鼠标移动的距离，可用来防止无意的拖动。QApplication的下列静态方法可用来控制延迟量：

```
setstartDragDistance (Distance) - 拖动开始的最小移动量；
startDragDistance() - 返回拖动开始的最小移动量；
setstartDragTime (Time) -拖动开始的按压毫秒数；
startDragTime () - 返回拖动开始的按压毫秒数；
```

(3) 鼠标移动超过最小移动值或按压鼠标超过最小毫秒数，拖动操作开始。此时，创建一个QDrag对象，调用exce()方法进行相关操作。QDrag有以下方法：
```
exec( ) -  开始拖放操作,返回一个代表放置操作的枚举值。有两个格式：
exec(supportedActions = Qt.MoveAction)
exec(supportedActions, defaultDropAction)
详细用法参见http://doc.qt.io/qt-5/qdrag.html#exec
setMimeData (QMimeData qmd) - 设置拖放中传输的数据, 参数为QMimeData类型。传输文本的例子：
data = QtCore.QMimeData ()
data.setText ("Drag text")
drag = QtGui.QDrag (self)
drag.setMimeData (data)
mimeData () - 返回拖放过程中传输数据的QMimeData 对象；
setPixmap(QPixmap qpix) - 设定拖放过程鼠标显示的图像。如：
drag.setPixmap (QtGui.QPixmap ("dd representer.png"))
pixmap() - 返回拖放过程中鼠标显示图像的QPixmap对象；
setHotSpot (QPoint pt) - 设置图像的热点位置。
drag.setHotSpot (QtCore.QPoint (20, 20))
hotSpot() - 返回热点位置的QPoint对象；
setDragCursor (QPixmap pix, Qt.DropAction dact) - 设置拖放的鼠标形状。第一个参数为鼠标形状的QPixmap对象；第二参数为相关操作的枚举值，只能是 CopyAction, MoveAction 或LinkAction。drag.setDragCursor (QtGui.QPixmap ("move_cursor.png"), QtCore.Qt.MoveAction)
dragCursor ( Qt.DropAction dact) - 返回dact操作的鼠标形状的QPixmap对象;
source() - 返回源控件的引用；
target() - 返回目标控件的引用。如果拖放的目标对象为另一应用，返回值为None.
supportedActions () - 返回当前有效操作的组合值。
defaultAction () - 返回缺省的操作。
```
QDrag有两个信号：
`actionChanged (Qt.DropAction dact)` - 当拖放的行为被该变时，发射该信号；
`targetchanged (QWidget obj)` - 当目标控件被改变时，将会发射该信号；

**2.QMineData类**
拖放过过程中的MIME类型的数据传递通过将QMineData对象作为参数调用QDrag的setMimeData()函数实现。
创建QMimeData方法：
data=QtCore.QMimeData()
QMimeData类支持以下方法（详见http://doc.qt.io/qt-5/qmimedata.html):

```
setText(text) - 设置文本数据（MIME型文本/文本格式）:
data.setText("拖动文本")
text( ) - 返回文本数据;
hasText( ) - 如果对象包含文本数据，则返回True，否则返回False。
setHtml(HTML-text) - 以HTML格式设置文本数据（MIME型文本 / html格式）:
data.setHtml("拖动HTML文本</ b>")
html( ) - 返回HTML格式的文本数据;
hasHtml( ) - 如果对象包含HTML格式的文本数据，则返回True，否则为False.
setUrls( QList  url) - 设置地址列表，参数为QUrl类的列表；
data.setUrls ([QtCore.QUrl ("http://www.google.com/")])
urls () - 返回地址列表:
url = e.mimeData (). urls () [0] .toString ()
hasUrls () - 如果对象包Url地址，则返回True，否则为False.
setlmageData(QVarinat  img) - 设置图像数据，可以是QImage或QPixmap实例：
data.setlmageData (QtGui.Qlmage("pixmap.png"))
data.setlmageData (QtGui.QPixmap("pixmap.png"))
imageData( ) - 返回图像对象;
haslmage( ) - 如果对象包含图像数据，则返回True，否则为False.
setData(QString mimetype, QCore.QByteData data) 设置任意类型的MIME类型数据；参数1为QString类型，指定MIME类型；参数2为QByteData类实例。该方法可以用MIME不同类型，多次调用：
data.setData("text/plain",QtCore.QByteArray (bytes ("Data", "utf-8")))
data(QString mimetype) -  返回mimetype类型的QByteData数据对象;
hasFormat(QString mimetype) -  如果对象包含mimetype类型数据，则返回True，否则为False.
formats ( ) - 返回包括的mimetype类型列表；
removeFormat (QString mimetype) - 删除mimetype类型的数据;
clear ( ) - 删除所有数据。
```
如果要实现特殊类型数据的拖放，需要创建一个QMimeData子类，并重载retrieveData( ) 和 formats ( )方法。详细内容，请参考Qt文档。

**3.放置操作**
在处理拖放对象之前，必须告诉系统该组件可以处理这些事件。 为此，要在组件的构造函数中，调用继承自QWidget类的setAcceptDrops方法，参数值为True：
`self.setAcceptDrops（True）`
拖放对象的过程执行如下：
在控件的dragEnterEvent()方法中，检查拖动的MIME类型数据。如果该控件能处理此类型数据，则调用事件对象的acceptProposedAction( )方法。如果要变更操作，则将新事件传递给事件对象的setDropAction( )方法，然后调用accept( )方法，而不是调用acceptProposedAction( )方法。
如果要限制控件的特定区域才能放置操作，必须在dragMoveEvent( )方法中处理。即当拖放进入到特定的区域，调用accept( QRect rect)方法，参数rect为特定区域的QRect对象。
在控件的dropEvent()方法中，完成相关操作。

QWidget类的下列方法可以处理在拖放过程中发生的事件：

    dragEnterEvent (self,event) - 当拖动进入到控件区域时调用。通过event参数， 可取得QDragEnterEvent实例;
    dragLeaveEvent(self,event) - 当拖动离开控件区域时调用。通过event参数， 可取得QDragLeaveEvent实例;
    dragMoveEvent (self, event) -当拖动在控件区域移动时调用。通过event参数， 可取得QDragMoveEvent实例;
    dropEvent (self, event) - 当拖动在控件区域放开时调用。通过event参数， 可取得QDropEvent实例;

QDragLeaveEvent类继承自QEvent类，不附带任何其他信息。因为在该类中只要知道拖动的对象已经离开组件区域就够了。
相关类的继承顺序为：
QEvent - QDropEvent - QDragMoveEvent - QDragEnterEvent
QDragEnterEvent类中没有增加新方法。
QDropEvent类有下列方法：

```
mimeData( ) - 返回含有所传输数据和MIME类型信息的QMimeData类的实例；
pos( ) - 返回QPoint类的实例，为拖放对象放置的整数坐标;
posF( ) - 返回QPointF类的实例，为拖放对象放置的浮点坐标;
possibleAction( ) - 返回拖放可能的操作组合,如:
if e.possibleActions () & QtCore.Qt.MoveAction:
    print ("MoveAction")
if e.possibleActions () & QtCore.Qt.CopyAction:
    print ("CopyAction")
proposedAction( ) - 返回默认的放置操作；
acceptProposedAction( ) - 表示已准备好接受传输的数据和由proposalAction( )返回的默认操作。该方法(或者accept()方法)必须在dragEnterEvent()中调用，否则，dropEvent()不会被调用。
setDropAction (action) - 允许在放置时指定为action. 作了此设置后，必须调用accept( )方法, 而不是acceptProposedAction( )方法;
dropAction( ) - 返回放置时进行的操作。如果用setDropAction( )进行了更改，可能与proposedAction ()的返回值不一致。
keyboardModifiers( ) - 用于判断按下了哪些修饰键 (, , , 等.) ，可参考“PyQt5编程(18)：键盘事件”中的对应函数。
mouseButtons () - 用于判断在拖放过程中按下的鼠标键;
source( ) - 如果拖放是从另一个应用程来的，返回为None；否则，返回对拖放源控件的引用。
```
QDragMoveEvent类的方法有：
```
accept (QRect rect) - 表明允许后续的移动操作。 rect用来指定可接受拖放操作的区域；
ignore (QRect rect) - 表明不允许后续的移动操作。 rect用来指定不接受拖放操作的区域；;
answerRect () - 返回可接受拖放操作区域的QRect对象。
```
PyQt中的有些控件，如单行文本控件，已有拖放功能。因此，在自己实现拖放功能之前，请仔细阅读相关控件的文档。


#### 7.List和Tree之间的拖拽事件

[list和tree之间的拖拽](https://stackoverflow.com/questions/54837869/drag-items-between-qtreewidget-and-qlistwidget-in-pyqt5#comment96451219_54838206)

[listwidget中自定义widget的drag&drop](https://stackoverflow.com/questions/41595014/dragndrop-custom-widget-items-between-qlistwidgets)

[listview的使用](http://www.xdbcb8.com/archives/701.html)

[pyqt model/view drag&drop example](http://rowinggolfer.blogspot.com/2010/04/pyqt4-modelview-drag-drop-example.html)

#### 8. show和exec的区别

Pyqt中 QDialog  show和exec的区别

QDialog的显示有两个函数show()和exec()。他们的区别在参考文档上的解释如下：

**show():**
显示一个**非模式**对话框。控制权即刻返回给调用函数。
弹出窗口是否模式对话框，取决于modal属性的值。

原文：Shows the dialog as a modeless dialog. Control returns immediately to the calling code. 
The dialog will be modal or modeless according to the value of the modal property.

**exec():**
显示一个**模式**对话框，并且锁住程序直到用户关闭该对话框为止。函数返回一个DialogCode结果。
在对话框弹出期间，用户不可以切换同程序下的其它窗口，直到该对话框被关闭。
原文：Shows the dialog as a modal dialog, blocking until the user closes it. The function returns a DialogCode result. 
Users cannot interact with any other window in the same application until they close the dialog. 

 

**模式与非模式**

模式对话框，就是在弹出窗口的时候，整个程序就被锁定了，处于等待状态，直到对话框被关闭。这时往往是需要对话框的返回值进行下面的操作。如：确认窗口（选择“是”或“否”）。
非模式对话框，在调用弹出窗口之后，调用即刻返回，继续下面的操作。这里只是一个调用指令的发出，不等待也不做任何处理。如：查找框。

 

简单的理解：

**首先这两个方法返回值不同。exec()有返回值，show()没有返回值。**

**其次这两个方法的作用也不同。调用show()的作用仅仅是将widget及其上的内容都显示出来，控制权即刻返回给调用函数。而调用exec()后，调用线程将会被阻塞，锁住程序直到用户关闭该对话框，期间用户不可以切换同程序下的其它窗口直到Dialog关闭。**

#### 9.只接受数字&密文

```
self.kernel1_size_text = QtWidgets.QLineEdit()
self.kernel1_size_text.setPlaceholderText('k-size')
self.kernel1_size_text.setToolTip('kernel size(odd,odd)')
self.kernel1_size_text.setValidator(QtGui.QIntValidator())  QtGui.QDoubleValidata
self.kernel1_size_text.setEchoMode(QtWidgets.QLineEdit.Password)
self.kernel1_size_text.setText('3')
```



#### 10.自定义信号和槽

[自定义信号槽](https://blog.csdn.net/zhulove86/article/details/52563131)

[自定义信号必须放在类中函数的定义外](https://stackoverflow.com/questions/2970312/pyqt4-qtcore-pyqtsignal-object-has-no-attribute-connect)

```python
_signal = QtCore.pyqtSignal(str)  # 定义信号,定义参数为str类型
self._signal.emit("正在打印，请稍候...")
self._signal.connect(self.mySignal)
def mySignal(self, string):
    pass
```



