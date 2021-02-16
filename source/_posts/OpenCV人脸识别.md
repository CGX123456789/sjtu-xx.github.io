---
title: OpenCV人脸检测
toc: true
date: 2020-02-16 11:01:36
tags: [OpenCV,人脸检测]
categories:
- 深度学习
- 目标检测
---

[转载链接](https://www.jiqizhixin.com/articles/2019-05-30-3)
<!--more-->
本教程将介绍如何使用 OpenCV 和 Dlib 在 Python 中创建和运行人脸检测算法。同时还将添加一些功能，以同时检测多个面部的眼睛和嘴巴。本文介绍了人脸检测的最基本实现，包括级联分类器、HOG 窗口和深度学习 CNN。

我们将通过以下方法实现人脸检测：

- 使用 OpenCV 的 Haar 级联分类器
- 使用 Dlib 的方向梯度直方图
- 使用 Dlib 的卷积神经网络

本文代码的 Github 库（以及作者其他博客的代码）链接：

https://github.com/maelfabien/Machine_Learning_Tutorials 

我们将使用用于计算机视觉的开源库 OpenCV，它用 C/C++编写，有 C++、Python 和 Java 接口。同时支持 Windows、Linux、MacOS、iOS 和 Android 系统。同时我们还需要工具包 Dlib，它是一个包含机器学习算法和创建复杂软件的 C++工具包。

**步骤**

第一步是安装 OpenCV 和 Dlib。运行以下命令：

```
pip install opencv-python
pip install dlib
```

文件生成的路径如下（版本不同，路径会稍有差别）：

```
/usr/local/lib/python3.7/site-packages/cv2
```

如果在使用 Dlib 时出现问题，请参见文章：https://www.pyimagesearch.com/2018/01/22/install-dlib-easy-complete-guide/

**导入工具包和模型路径**

创建一个新的 Jupyter notebook/Python 文件，从以下代码开始：

```
import cv2
import matplotlib.pyplot as plt
import dlib
from imutils import face_utils
font = cv2.FONT_HERSHEY_SIMPLEX
```

**级联分类器**

首先研究级联分类器。

**理论**

级联分类器，即使用类 Haar 特征工作的级联增强分类器，是集成学习的一种特殊情况，称为 boost。它通常依赖于 Adaboost 分类器（以及其他模型，如 Real Adaboost、Gentle Adaboost 或 Logitboost）。

级联分类器在包含检测目标的几百个样本图像以及不包含检测目标的其他图像上进行训练。

我们如何检测图上是否有人脸呢？有一种名为 Viola-Jones 的[目标检测](https://mp.weixin.qq.com/cgi-bin/appmsg?t=media/appmsg_edit&action=edit&type=10&appmsgid=503279302&isMul=1&token=815003832&lang=zh_CN)框架的算法，包括了实时人脸检测所需的所有步骤：

- 提取 Haar 特征，特征来自 Haar 小波
- 创建图像
- Adaboost 训练
- 级联分类器

**Haar 特征选择**

人脸上最常见的一些共同特征如下：

- 与脸颊相比，眼部颜色较深
- 与眼睛相比，鼻梁区域较为明亮
- 眼睛、嘴巴、鼻子的位置较为固定......

这些特征称为 Haar 特征。[特征提取](https://mp.weixin.qq.com/cgi-bin/appmsg?t=media/appmsg_edit&action=edit&type=10&appmsgid=503279302&isMul=1&token=815003832&lang=zh_CN)过程如下所示：

![img](https://image.jiqizhixin.com/uploads/editor/7245f5c9-5da5-4edf-bff6-186719d5fef6/640.jpeg)

*Haar 特征*

在上图中，第一个特征测量眼部和上脸颊之间的强度差异。特征值计算的方法很简单，对黑色区域中的像素求和再减去白色区域中的像素即可。

然后，将这个矩形作为卷积核作用到整个图像。为了不产生遗漏，我们需要用到每个卷积核的所有的维度和位置。简单的 24 * 24 的图像可能会产生超过 160000 个特征，每个特征由像素值的和/差组成。这样在计算上无法实现实时人脸检测。那么，该如何加快这个过程呢？

一旦通过矩形框识别到有用区域，则在与之完全不同的区域上就无需再做计算了。这一点可以通过 Adaboost 实现。

使用积分图像原理计算矩形框特征的方法更快。我们将在下一节介绍这一点。

![img](https://image.jiqizhixin.com/uploads/editor/53407211-6e2b-4c88-8cfb-ad9af35e04d4/640.jpeg)

原始论文中提到几种可用于 Haar 特征提取的矩形框：

- 双矩形特征计算的是两个矩形区域内像素和的差，主要用于检测边缘 (a,b)
- 三矩形特征计算的是中心矩形和减去两个外部矩形和的差，主要用于检测线 (c,d)
- 四矩形特征计算的是矩形对角线对之间的差 (e)

![img](https://image.jiqizhixin.com/uploads/editor/887c30d9-bceb-479b-b57e-4b3be0a24a90/640.png)

*Haar 矩形*

特征提取完成后，使用 Adaboost 分类器将它们应用于训练集，该分类器结合了一组弱分类器来创建准确的集成模型。只需 200 个特征（最初是 16 万个），实现了 95％的准确率。该论文的作者提取了 6000 个特征。

**积分图像**

以卷积核的形式计算特征需要花费很长时间。出于这个原因，作者 Viola 和 Jones 提出了图像的中间表示：积分图像。积分图像的作用是仅使用四个值简单地计算矩形和。我们来看看它是如何工作的！

假设我们想要确定一个坐标为 (x,y) 的给定像素的矩形特征。然后，像素的积分图像是给定像素的上方和左侧的像素之和。

![img](https://image.jiqizhixin.com/uploads/editor/2a219eb2-16c0-4342-a28e-48a18401baca/640.png)

其中 ii(x,y) 是积分图像，i(x,y) 是原始图像。

当计算整个积分图像时，有一种只需要遍历一次原始图像的递归方法。实际上，我们可以定义以下一对递归形式：

![img](https://image.jiqizhixin.com/uploads/editor/957c420e-6646-4dcc-ba55-c16eec2b0c80/640.png)

其中 s(x,y) 是累积行和，而 s(x−1)=0, ii(−1,y)=0。

这是怎么实现的呢？假设我们想要估算区域 D 的像素总和。我们已经定义了 3 个其他区域：A，B 和 C。

- 点 1 处的积分图像的值是矩形 A 中的像素的总和。
- 点 2 处的值为 A + B。
- 点 3 处的值为 A + C。
- 点 4 处的值是 A + B + C + D。

因此，区域 D 中的像素之和可以简单地计算为： 4+1−(2+3)。

这样我们仅使用 4 个数组值就计算出了矩形 D 的值。

![img](https://image.jiqizhixin.com/uploads/editor/05ac2da5-0df3-4e6c-9613-9dbfc2fe15b3/640.jpeg)

人们应该知道矩形在实际中是非常简单的特征，但对于人脸检测已经足够了。当涉及复杂问题时，可调滤波器往往更灵活多变。

![img](https://image.jiqizhixin.com/uploads/editor/e92412f5-225a-4c1f-bba4-63b93194bc5d/640.png)

*可调滤波器*

**使用 Adaboost 学习分类函数**

给定一组带标签的训练图像（正负样本均有），Adaboost 用于：

- 提取一小部分特征
- 训练分类器

由于 16 万个特征中的大多数特征与之极不相关，因此我们设计一个增强模型的弱学习算法，用来提取单个矩形特征，将最好的正负样本区分开。

**级联分类器**

虽然上述过程非常有效，但仍存在一个重大问题。在图像中，大部分图像为非面部区域。对图像的每个区域给予等同的注意力是没有意义的，因为我们应该主要关注最有可能包含人脸的区域。Viola 和 Jone 使用级联分类器在减少了计算时间的同时，实现了更高的检测率。

关键思想是在识别人脸区域时排除不含人脸的子窗口。由于任务是正确识别人脸，我们希望假阴率最小，即包含人脸却未被识别的子窗口最少。

每个子窗口都使用一系列分类器。这些分类器是简单的决策树：

- 如果第一个分类器检测为正样本，继续用第二个
- 如果第二个分类器检测是正样本，继续用第三个
- 以此类推

虽然有时可能包含人脸的图被认成负样本被子窗口漏检。但初级分类器以较低的计算成本筛除了大多数负样本，下图的分类器可额外消除更多的负样本，但需要更多的计算量。

![img](https://image.jiqizhixin.com/uploads/editor/c014f487-359d-4f0e-9f9a-c9bf6720073f/640.jpeg)

使用 Adaboost 训练分类器，并调整阈值使错误率降到最低。在训练该模型时，变量如下：

- 每个阶段分类器数量
- 每个阶段的特征数量
- 每个阶段的阈值

幸运的是，在 OpenCV 中，整个模型已经经过预训练，可直接用于人脸检测。

如果想了解有关 Boosting 技术的更多信息，欢迎查看作者关于 Adaboost 的文章：

https://maelfabien.github.io/machinelearning/adaboost

**输入**

下一步是找到预训练的权重。我们将使用默认的预训练模型来检测人脸、眼睛和嘴巴。文件应位于此路径（python 版本不同，路径略有不同）：

```
/usr/local/lib/python3.7/site-packages/cv2/data
```

确定路径后，以此方式声明级联分类器：

```
cascPath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_frontalface_default.xml"
eyePath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_eye.xml"
smilePath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_smile.xml"
faceCascade = cv2.CascadeClassifier(cascPath)
eyeCascade = cv2.CascadeClassifier(eyePath)
smileCascade = cv2.CascadeClassifier(smilePath)
```

**检测图像中的人脸**

在实现实时人脸检测算法之前，让我们先尝试在图像上简单检测一下。从加载测试图像开始:

```
# Load the image
gray = cv2.imread('face_detect_test.jpeg', 0)
plt.figure(figsize=(12,8))
plt.imshow(gray, cmap='gray')
plt.show()
```

![img](https://image.jiqizhixin.com/uploads/editor/ee639060-e22b-43cc-8914-9fcd5ada0869/640.jpeg)

*测试图像*

然后开始检测人脸，并将检测到的人脸框起来。

```
# Detect faces
faces = faceCascade.detectMultiScale(
gray,
scaleFactor=1.1,
minNeighbors=5,
flags=cv2.CASCADE_SCALE_IMAGE
)
# For each face
for (x, y, w, h) in faces: 
    # Draw rectangle around the face
    cv2.rectangle(gray, (x, y), (x+w, y+h), (255, 255, 255), 3)
```

以下是 detectMultiScale 函数常见的参数列表：

- scaleFactor：确定每个图像缩放比例大小。
- minNeighbors：确定每个候选矩形应保留多少个相邻框。
- minSize：最小目标的大小。小于该值的目标将被忽略。
- maxSize：最大目标的大小。大于该值的目标将被忽略。

最后，显示结果：

```
plt.figure(figsize=(12,8))
plt.imshow(gray, cmap='gray')
plt.show()
```

![img](https://image.jiqizhixin.com/uploads/editor/3bd5ead0-5197-4aa4-8151-52a2c1c11f52/640.jpeg)

在测试图像上成功检测到人脸。现在开始实时检测！

**实时人脸检测**

下面继续进行实时人脸检测的 Python 实现。第一步是启动摄像头，并拍摄视频。然后，将图像转换为灰度图。这用于减小输入图像的维数。实际上，我们应用了一个简单的线性变换，而不是每个像素用三个点来描述红、绿、蓝。

![img](https://image.jiqizhixin.com/uploads/editor/9d27ae45-4222-4f77-9fcc-58d70cf832e4/640.png)

这在 OpenCV 中是默认实现的。

```
video_capture = cv2.VideoCapture(0)
while True:
    # Capture frame-by-frame
    ret, frame = video_capture.read()
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
```

现在我们使用上述定义的 faceCascade 变量，它包含一个预训练算法，现在将其用于灰度图。

```
faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor=1.1,
        minNeighbors=5,
        minSize=(30, 30),
        flags=cv2.CASCADE_SCALE_IMAGE
        )
```

对于检测到的每个人脸，都加上一个矩形框：

```
for (x, y, w, h) in faces:
        if w > 250 :
            cv2.rectangle(frame, (x, y), (x+w, y+h), (255, 0, 0), 3)
            roi_gray = gray[y:y+h, x:x+w]
            roi_color = frame[y:y+h, x:x+w]
```

对于检测到的每张嘴，都加上一个矩形框：

```
smile = smileCascade.detectMultiScale(
        roi_gray,
        scaleFactor= 1.16,
        minNeighbors=35,
        minSize=(25, 25),
        flags=cv2.CASCADE_SCALE_IMAGE
    )
    for (sx, sy, sw, sh) in smile:
        cv2.rectangle(roi_color, (sh, sy), (sx+sw, sy+sh), (255, 0, 0), 2)
        cv2.putText(frame,'Smile',(x + sx,y + sy), 1, 1, (0, 255, 0), 1)
```

对于检测到的每双眼睛，都加上一个矩形框：

```
eyes = eyeCascade.detectMultiScale(roi_gray)
    for (ex,ey,ew,eh) in eyes:
        cv2.rectangle(roi_color,(ex,ey),(ex+ew,ey+eh),(0,255,0),2)
        cv2.putText(frame,'Eye',(x + ex,y + ey), 1, 1, (0, 255, 0), 1)
```

然后计算人脸总数，显示整体图像：

```
cv2.putText(frame,'Number of Faces : ' + str(len(faces)),(40, 40), font, 1,(255,0,0),2)      
    # Display the resulting frame
    cv2.imshow('Video', frame)
```

当按下 q 键时，执行退出选项。

```
if cv2.waitKey(1) & 0xFF == ord('q'):
        break
```

最后当所有操作完成后，关闭所有窗口。在 Mac 上关闭窗口存在一些问题，可能需要通过活动管理器退出 Python。

```
video_capture.release()
cv2.destroyAllWindows()
```

**封装**

```
import cv2

cascPath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_frontalface_default.xml"
eyePath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_eye.xml"
smilePath = "/usr/local/lib/python3.7/site-packages/cv2/data/haarcascade_smile.xml"

faceCascade = cv2.CascadeClassifier(cascPath)
eyeCascade = cv2.CascadeClassifier(eyePath)
smileCascade = cv2.CascadeClassifier(smilePath)

font = cv2.FONT_HERSHEY_SIMPLEX
video_capture = cv2.VideoCapture(0)

while True:
    # Capture frame-by-frame
    ret, frame = video_capture.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor=1.1,
        minNeighbors=5,
        minSize=(200, 200),
        flags=cv2.CASCADE_SCALE_IMAGE
    )

    # Draw a rectangle around the faces
    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x+w, y+h), (255, 0, 0), 3)
            roi_gray = gray[y:y+h, x:x+w]
            roi_color = frame[y:y+h, x:x+w]
            cv2.putText(frame,'Face',(x, y), font, 2,(255,0,0),5)

    smile = smileCascade.detectMultiScale(
        roi_gray,
        scaleFactor= 1.16,
        minNeighbors=35,
        minSize=(25, 25),
        flags=cv2.CASCADE_SCALE_IMAGE
    )

    for (sx, sy, sw, sh) in smile:
        cv2.rectangle(roi_color, (sh, sy), (sx+sw, sy+sh), (255, 0, 0), 2)
        cv2.putText(frame,'Smile',(x + sx,y + sy), 1, 1, (0, 255, 0), 1)

    eyes = eyeCascade.detectMultiScale(roi_gray)
    for (ex,ey,ew,eh) in eyes:
        cv2.rectangle(roi_color,(ex,ey),(ex+ew,ey+eh),(0,255,0),2)
        cv2.putText(frame,'Eye',(x + ex,y + ey), 1, 1, (0, 255, 0), 1)

    cv2.putText(frame,'Number of Faces : ' + str(len(faces)),(40, 40), font, 1,(255,0,0),2)      
    # Display the resulting frame
    cv2.imshow('Video', frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
      break

# When everything is done, release the capture
video_capture.release()
cv2.destroyAllWindows()
```

**结果**

我已经制作了人脸检测算法的 YouTube 视频演示：



**Dlib 的方向梯度直方图（HOG）**

第二种常用的人脸检测工具由 Dlib 提供，它使用了方向梯度直方图（HOG）的概念。论文《Histograms of Oriented Gradients for Human Detection》实现这一方案。

**理论**

HOG 背后的想法是将特征提取到一个向量中，并将其输入到分类算法中，例如支持向量机，它将评估人脸（或实际想识别的任何对象）是否存在于某个区域中。

提取的特征是图像梯度（方向梯度）方向的分布（直方图）。梯度通常在边缘和角落周围较大，并允许我们检测这些区域。

在原始论文中，该算法用于人体检测，检测过程如下：

**预处理**

首先，输入图像必须尺寸相同（可通过裁剪和缩放）。图像长宽比要求为 1:2，因此输入图像的尺寸可能为 64x128 或 100x200。

**计算梯度图像**

第一步是通过以下卷积核计算图像的水平梯度和垂直梯度：

![img](https://image.jiqizhixin.com/uploads/editor/629050c3-86c7-4b27-9903-bf953f27895c/640.png)

*计算梯度的卷积核*

图像的梯度通常会消除非必要信息。

上面图像的梯度可以通过下面的 python 语句找到：

```
gray = cv2.imread('images/face_detect_test.jpeg', 0)
im = np.float32(gray) / 255.0
# Calculate gradient 
gx = cv2.Sobel(im, cv2.CV_32F, 1, 0, ksize=1)
gy = cv2.Sobel(im, cv2.CV_32F, 0, 1, ksize=1)
mag, angle = cv2.cartToPolar(gx, gy, angleInDegrees=True)
```

绘制图片：

```
plt.figure(figsize=(12,8))
plt.imshow(mag)
plt.show()
```

![img](https://image.jiqizhixin.com/uploads/editor/b7b2438b-1f98-4992-bdf8-7b94e4dbde7f/640.jpeg)

我们之前没有预处理图像。

**计算 HOG**

首先将图像分成 8x8 个单元来提供紧凑表示，使 HOG 对噪声更鲁棒。然后，计算每个单元的 HOG。

为了估计区域内的梯度方向，我们只需在每个区域内的 64 个梯度方向值（8x8）及其大小（另外 64 个值）之间构建直方图。直方图的类别对应梯度的角度，从 0 到 180°。总共 9 类：0°，20°，40°...... 160°。

上面的代码给了我们 2 个信息：

- 梯度方向
- 梯度大小

当我们构建 HOG 时，有 3 种情况：

- 角度小于 160°，且不介于两类之间。在这种情况下，角度将添加到 HOG 的正确类中。
- 角度小于 160°，恰好在两类之间。在这种情况下，像素被均分到左右两侧类中。
- 角度大于 160°。在这种情况下，我们认为像素与 160°和 0°成比例。

![img](https://image.jiqizhixin.com/uploads/editor/d944bc9f-9bdf-4a60-ba76-e5f53ba6af5b/640.jpeg)

![img](https://image.jiqizhixin.com/uploads/editor/841dab46-ac57-43e1-9656-40c3b3738003/640.jpeg)

每个 8x8 单元的 HOG 如下所示：

![img](https://image.jiqizhixin.com/uploads/editor/8b5ba4f7-a2f8-48a3-8b3c-ab03ce87366d/640.jpeg)

*HOG*

**模块归一化**

最后，可以用 16×16 的模块对图像进行归一化，并使其对光照不变。这可以通过将大小为 8x8 的 HOG 的每个值除以包含它的 16x16 模块的 HOG 的 L2 范数来实现，这个模块实际上是长度为 9*4 = 36 的简单向量。

**模块归一化**

最后，将所有 36x1 向量连接成一个大向量。OK！现在有了特征向量，我们可以在上面训练一个软 SVM 分类器（C=0.01）。

**检测图像上的人脸**

实现非常简单：

```
face_detect = dlib.get_frontal_face_detector()
rects = face_detect(gray, 1)
for (i, rect) in enumerate(rects):
(x, y, w, h) = face_utils.rect_to_bb(rect)
    cv2.rectangle(gray, (x, y), (x + w, y + h), (255, 255, 255), 3)

plt.figure(figsize=(12,8))
plt.imshow(gray, cmap='gray')
plt.show()
```

![img](https://image.jiqizhixin.com/uploads/editor/0f5b21c6-a073-44e8-9fac-06ddbc85b0b5/640.jpeg)

**实时人脸检测**

如前所述，该算法非常容易实现。我们还实现了一个更轻量的版本，只用来识别人脸。Dlib 让人脸关键点的检测更加容易，但这是另一个话题。

```
video_capture = cv2.VideoCapture(0)
flag = 0

while True:

    ret, frame = video_capture.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    rects = face_detect(gray, 1)

    for (i, rect) in enumerate(rects):

        (x, y, w, h) = face_utils.rect_to_bb(rect)

        cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)

        cv2.imshow('Video', frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

video_capture.release()
cv2.destroyAllWindows()
```

**Dlib 中的卷积神经网络**

最后一种方法基于卷积神经网络。为了增强结果，它还实现了最大边缘目标检测（MMOD）。

**理论**

卷积神经网络是主要用于计算机视觉的前馈神经网络。它们提供自动图像预处理以及密集的神经网络部分。CNN 还是用来处理带有网格状拓扑的数据的特殊神经网络。它的架构灵感来自动物视觉皮层。

以前的方法中，很大一部分工作是选择滤波器来创建特征，以便尽从图像中可能多地提取信息。随着深度学习和计算能力的提高，这项工作现在可以实现自动化。CNN 的名称就来自我们用一组滤波器卷积初始图像输入的事实。需要选择的参数仍是需要应用的滤波器数量以及尺寸。滤波器的尺寸称为步幅。一般步幅设置在 2 到 5 之间。

![img](https://image.jiqizhixin.com/uploads/editor/b2ddc028-d6a9-4d24-91aa-82a4bdf216c7/640.jpeg)

在这种特定情况下，CNN 的输出是二分类，如果有人脸，则取值 1，否则取 0。

**检测图像上的人脸**

一些元素在实现中会发生变化。

第一步是下载预训练模型：https://github.com/davisking/dlib-models/blob/master/mmod_human_face_detector.dat.bz2

 将下载后的权重放到文件夹中，并定义 dnnDaceDetector：

```
dnnFaceDetector = dlib.cnn_face_detection_model_v1（"mmod_human_face_detector.dat"）
```

然后，与之前做的相同：

```
rects = dnnFaceDetector(gray, 1)
for (i, rect) in enumerate(rects):
    x1 = rect.rect.left()
    y1 = rect.rect.top()
    x2 = rect.rect.right()
    y2 = rect.rect.bottom()
    # Rectangle around the face
    cv2.rectangle(gray, (x1, y1), (x2, y2), (255, 255, 255), 3)
plt.figure(figsize=(12,8))
plt.imshow(gray, cmap='gray')
plt.show()
```

![img](https://image.jiqizhixin.com/uploads/editor/4f7f86e8-d304-466f-a130-16e04efc1d06/640.jpeg)

**实时人脸检测**

最后，实现实时 CNN 人脸检测：

```
video_capture = cv2.VideoCapture(0)
flag = 0

while True:
    # Capture frame-by-frame
    ret, frame = video_capture.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    rects = dnnFaceDetector(gray, 1)

    for (i, rect) in enumerate(rects):

        x1 = rect.rect.left()
        y1 = rect.rect.top()
        x2 = rect.rect.right()
        y2 = rect.rect.bottom()

        # Rectangle around the face
        cv2.rectangle(frame, (x1, y1), (x2, y2), (0, 255, 0), 2)

    # Display the video output
    cv2.imshow('Video', frame)

    # Quit video by typing Q
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

video_capture.release()
cv2.destroyAllWindows()
```

**如何选择模型**

这是一个很难回答的问题，但我们只讨论两个重要指标：

- 计算时间
- 准确率

在速度方面，HOG 是最快的算法，其次是 Haar 级联分类器和 CNN。

但是，Dlib 中的 CNN 是准确率最高的算法。HOG 表现也很好，但在识别较小的人脸时会有一些问题。Haar 级联分类器的整体表现与 HOG 相似。

考虑到实时人脸检测的速度，我在个人项目中使用了 HOG。

希望这个关于 OpenCV 和 Dlib 的人脸检测的快速教程能对你有所帮助。
