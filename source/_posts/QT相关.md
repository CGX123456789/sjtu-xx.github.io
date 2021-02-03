---
title: qt相关
toc: true
date: 2020-05-06 17:02:31
tags:
categories:
- C++
- 基础
---

# clion插件
<!--more-->
External tools
In "File -> Settings -> Tools -> External Tools", add 4 external tools:

Qt Creator:
```
Program:   "PATH_TO_QT/QT_VERSION/QT_ARCH/Tools/QtCreator/bin/qtcreator"
Arguments: $FilePath$
```
UI Designer:
```
Program:   "PATH_TO_QT/QT_VERSION/QT_ARCH/lib/bin/designer")
Arguments: $FilePath$
```
LUpdate:
```
Program:   "PATH_TO_QT/QT_VERSION/QT_ARCH/lib/bin/lupdate")
Arguments: $FilePath$ -ts $FileNameWithoutExtension$.ts
```
Linguist:
```
Program:   "PATH_TO_QT/QT_VERSION/QT_ARCH/lib/bin/linguist")
Arguments: $FilePath$
```
Now you can right click these file types and under the external tool:
For .ui select Qt Creator/Designer and start UI designing
For .qml select Qt Creator and design UI in QML editor
For .qrc select Qt Creator and use resource editor
For .cpp/.ui select LUpdate to create its translation file
For .ts select Linguist and start the translating

# qt资源文件
手动修改和编写qrc文件的方法。
test.qrc文件：
```xml
<!DOCTYPE RCC><RCC version="1.0">

<qresource>

    <file>images/copy.png</file>

    <file>images/cut.png</file>

    <file>images/document.png</file>

    <file>images/icon.png</file>

    <file>images/new.png</file>

    <file>images/open.png</file>

    <file>images/paste.png</file>

    <file>images/save.png</file>

</qresource>

</RCC>
```
.qrc 文件中列出的 资源文件 是程序的源码树的一部分。指定的路径是 .qrc 文件所在目录的相对路径。 注意，列出的资源文件必须位于 .qrc 文件所在目录或者其子目录下。
程序中，可以用资源在源码树中的名称加一个  `:/` 前缀 来访问它。例如，在程序的源码树中是 `images/cut.png` 的文件可以通过`:/images/cut.png` 来访问。

但也可以用file标签中的alias属性来指定：
```xml
<file alias="cut-img.png">images/cut.png</file>
```
这时该文件可以通过  `:/cut-img.png`  来访问。

也可以在 .qrc 文件中用 qresource 标签的 prefix 属性：
它可以为 .qrc 文件中所有文件指定一个前缀：
```xml
<qresource prefix="/myresources">

<file alias="cut-img.png">images/cut.png</file>

</qresource>
```
这时该文件可以用  :/myresources/cut-img.png  访问。

# QT路径
比如我们有一个程序在：`C:/Qt/examples/tools/regexp/regexp.exe`

1. 程序所在目录
　　`QString QCoreApplication::applicationDirPath()`
　　那么 `qApp->applicationDirPath()` 的结果是：
　　输出：`C:/Qt/examples/tools/regexp`　

2. 程序的完整名称。那么可以这么写：
　　`qApp->applicationFilePath()`
　　输出：`C:/Qt/examples/tools/regexp/regexp.exe`

3. 当前工作目录
   　QDir 提供了一个静态函数 `currentPath()` 可以获取当前工作目录
　　如果我们是双击一个程序运行的，那么程序的工作目录就是程序所在目录。
　　如果是在命令行下运行一个程序，那么运行程序时在命令行的哪个目录，那个目录就是当前目录。

4. 用户目录路径

　　Qt 5 中引入的方法
 　`QStandardPaths::writableLocation(QStandardPaths::HomeLocation);`
　　`QStandardPaths::standardLocations(QStandardPaths::HomeLocation);`
　　这两个方法的区别是 `standardLocations()` 返回值是 QStringList。当然对于 HomeLocation 来说这个 QStringList 中只有一个 QString。

　　还有另外一种方法，利用 QDir 类的一个静态函数：
　　`QDir::homePath();`

5. 我的文档路径

　　 Qt 5 中引入的方法。

　　`QStandardPaths::writableLocation(QStandardPaths::DocumentsLocation);`

　　`QStandardPaths::standardLocations(QStandardPaths::DocumentsLocation);`

6. 桌面路径

　　`QStandardPaths::writableLocation(QStandardPaths::DesktopLocation);`

　　`QStandardPaths::standardLocations(QStandardPaths::DesktopLocation);`

7. 程序数据存放路径

　　通常我们会将程序所需的一些数据存入注册表。但是有时需要存储的数据太多，放在注册表中就不适合了。这时我们就要找个专门的地方来放数据。以前我喜欢将数据直接放到程序所在目录，但是后来发现我的程序运行时经常没有权限对这个目录下的文件进行写操作。后来发现其实 Qt 早就替我们考虑过这些问题了。

　　Qt 5 中引入的方法。

　　`QStandardPaths::writableLocation(QStandardPaths::AppDataLocation);`

　　`QStandardPaths::standardLocations(QStandardPaths::AppDataLocation);`

　　Qt 5.5 中引入了另一种方法：　　

　　`QStandardPaths::writableLocation(QStandardPaths::AppConfigLocation);`

　　`QStandardPaths::standardLocations(QStandardPaths::AppConfigLocation);`

　　这个方法一般来说和上面的方法得到的结果是相同的。按照 Qt 帮助文档的解释，这个方法可以确保返回的路径非空。所以我认为应该优先选用这个方法。

8. 临时文件路径

　　Qt 5 中引入的方法。

　　`QStandardPaths::writableLocation(QStandardPaths::TempLocation);`

   `QStandardPaths::standardLocations(QStandardPaths::TempLocation);`

　　更传统的方法是利用 QDir 的一个静态函数 `tempPath()`。

　　`QDir::tempPath();`

　　在这个目录下生成临时文件和临时目录需要用到另外两个类： QTemporaryFile 和 QTemporaryDir。就不展开介绍了，大家可以参考 qt 的帮助文档。

至此，常用的各种特殊路径就介绍的差不多了。剩下还有些不常用的，可以参考 QStandardPaths 类的介绍。

# 事件过滤器
QT事件模块一个真正强大的特性是可以设置一个QObject的实例去监测另外一个QObject实例的事件,在被监测的实例see之前.
返回true告诉Qt我们已经处理了这个事件. 如果我们返回false, Qt会发送这个event到它原来的目的地.
1. 安装事件过滤器
`installEventFilter(parent);`
2. 事件过滤器
```c++
#include <QEvent>
#include <QObject>

class MyButton : public QPushButton {
Q_OBJECT
public:
    MyButton(QWidget *parent) : QPushButton(parent) {
        installEventFilter(parent);
    };
};

class MyWindow : public QMainWindow {
Q_OBJECT
public:
    MyWindow() {
        this->setWindowTitle("321");
        button = new MyButton(this);
    };

    bool eventFilter(QObject *obj, QEvent *event) override {
      # 过滤事件
        if (button == obj) {
            if (event->type() == QEvent::Enter) {
                this->setWindowTitle("123");
                return true;
            }
        }else{
            return false;
        }
    };
private:
    MyButton *button;
};
```
# QString to string

```c++
QString qs;
std::string utf8_text = qs.toUtf8().constData();
```

# 带参数的信号与槽函数的绑定
qt5中的用法：https://www.devbean.net/2012/09/qt-study-road-2-deep-qt5-signals-slots-syntax/

## 一般情况
```c++
    QObject::connect(&a, &Counter::valueChanged,
                     &b, &Counter::setValue);

```
## 有重载的信号
```c++
//1
void (Newspaper:: *newPaperNameDate)(const QString &, const QDate &) = &Newspaper::newPaper;
QObject::connect(&newspaper, newPaperNameDate,
                 &reader,    &Reader::receiveNewspaper);

//2 不推荐
// 使用的是 C 风格的强制类型转换。此时，如果你改变了信号的类型，那么你就会有一个潜在的运行时错误。例如，如果我们把(const QString &, const QDate &)两个参数修改成(const QDate &, const QString &)，C 风格的强制类型转换就会失败，并且这个错误只能在运行时发现。
QObject::connect(&newspaper,
                 (void (Newspaper:: *)(const QString &, const QDate &))&Newspaper::newPaper,
                 &reader,
                 &Reader::receiveNewspaper);

//3  C++ 推荐的风格，当参数类型改变时，编译器会检测到这个错误。
QObject::connect(&newspaper,
                 static_cast<void (Newspaper:: *)(const QString &, const QDate &)>(&Newspaper::newPaper),
                 &reader,
                 &Reader::receiveNewspaper);
```
## 参数个数不一致
```c++
QObject::connect(&newspaper, static_cast<void (Newspaper:: *)
(const QString &)>(&Newspaper::newPaper),
[=](const QString &name) 
{ /* Your code here. */ }
);
```

# 拖放
https://www.devbean.net/2013/05/qt-study-road-2-dnd/
https://www.devbean.net/2013/05/qt-study-road-2-dnd-data/
https://openhome.cc/Gossip/Qt4Gossip/DragExecAccept.html

drag方需要重写：
```c++
    void dragMoveEvent(QDragMoveEvent *e) override;

    void mousePressEvent(QMouseEvent *event) override;

    void mouseMoveEvent(QMouseEvent *event) override;
```

```c++

void CustomListWidget::mousePressEvent(QMouseEvent *event) {
    if (event->button() == Qt::LeftButton) {
        startPoint = event->pos();
    }
    QListWidget::mousePressEvent(event);
}

void CustomListWidget::mouseMoveEvent(QMouseEvent *event) {
    if (event->buttons() & Qt::LeftButton) {
        if ((event->pos() - startPoint).manhattanLength() >= QApplication::startDragDistance()) {
            performDrag();
        }
    }
    QListWidget::mouseMoveEvent(event);
}

void CustomListWidget::performDrag() {
    QListWidgetItem *item = currentItem();
    if (item) {
        auto mimeData = new QMimeData;
        mimeData->setText(item->text());
        mimeData->setImageData(item->icon());
        auto drag = new QDrag(this);
        drag->setMimeData(mimeData);
        drag->setPixmap(item->icon().pixmap(QSize(22, 22)));
        drag->exec(Qt::CopyAction);
    }
}
void CustomListWidget::dragMoveEvent(QDragMoveEvent *e) {}
```

drop方需要重写：
```c++
    void dragEnterEvent(QDragEnterEvent *e) override;

    void dragMoveEvent(QDragMoveEvent *e) override;

    void dropEvent(QDropEvent *e) override;
```

```c++

void CustomTreeWidget::dropEvent(QDropEvent *e) {
    qDebug() << e->type();
    qDebug() << e->source();
    qDebug() << e->mimeData()->text();
    qDebug() << e->mimeData()->imageData();
    qDebug() << e->source()->objectName();
//    CustomListWidget *source = qobject_cast<CustomListWidget *>(e->source());
    QIcon icon = e->mimeData()->imageData().value<QIcon>();
    QString text = e->mimeData()->text();
    auto item = new QTreeWidgetItem();
    item->setIcon(0, icon);
    item->setText(1, text);
    addTopLevelItem(item);
    e->accept();
    QTreeWidget::dropEvent(e);
}

void CustomTreeWidget::dragEnterEvent(QDragEnterEvent *e) {
    e->setDropAction(Qt::CopyAction);
    e->accept();
}

void CustomTreeWidget::dragMoveEvent(QDragMoveEvent *event) {}
```