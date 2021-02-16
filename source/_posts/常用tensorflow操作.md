---
title: 一些常用的tensorflow操作
toc: true
date: 2020-03-07 21:43:52
tags:
categories:
- 深度学习
- 基础理论
---

# 特征层or图像处理
<!--more-->
## （1）tf.image.crop_and_resize
对特征层或图像进行裁剪，裁剪后再进行resize
```python
tf.image.crop_and_resize(
    image,
    boxes,
    box_ind,
    crop_size,
    method='bilinear',
    extrapolation_value=0,
    name=None
)
```
参数：
- image：一个Tensor,必须是下列类型之一：uint8, uint16, int8, int16, int32, int64, half, float32, float64, 一个形状为[batch, image_height, image_width, depth]的四维张量,image_height和image_width需要为正值.
- boxes：一个类型为float32的Tensor，是形状为[num_boxes, 4]的二维张量。张量的第i行指定box_ind[i]图像中框的坐标，并且在标准化坐标中指定[y1, x1, y2, x2]，四个坐标均是标准归一化后的结果；标准化的坐标值y被映射到图像坐标y * (image_height - 1)处，从而标准化图像高度的[0, 1]间隔被映射到[0, image_height - 1]的图像高度坐标中。我们允许y1>y2,在这种情况下,采样的裁剪是原始图像的上下翻转版本，宽度维度的处理方式类似。[0,1]范围之外的标准化坐标是允许的,在这种情况下，我们使用extrapolation_value外推输入图像值。
- box_ind：一个int32类型的Tensor；形状为[num_boxes]的1维张量,在[0, batch)中具有int32值，该box_ind[i]值指定第i个方框要引用的图像。
- crop_size：一个int32类型的Tensor；一个2个元素的一维张量,size = [crop_height, crop_width]。所有裁剪的图像修补程序都调整为此大小.图像内容的宽高比不被保留；crop_height和crop_width需要为正值。
- method：可选的string,其来自：“bilinear”；默认为"bilinear"；指定插值方法的字符串.现在只支持“双线性(bilinear)”.
- extrapolation_value：可选的float,默认为0,用于推断的值(如果适用). name：操作的名称(可选).
返回值：
tf.image.crop_and_resize函数返回一个类型为float32的Tensor. 形状为[num_boxes, ,crop_height, crop_width, depth]
测试代码：
```python
import tensorflow as tf
from PIL import Image
import numpy as np
img = np.array(Image.open("img/street.jpg"))
shape = img.shape
img = img.reshape([1,shape[0], shape[1], shape[2]])
a = tf.image.crop_and_resize(img,[[0.5,0.6,0.9,0.8],[0.2,0.6,1.3,0.9]],box_ind=[0,0],crop_size=(100,100))
sess = tf.Session()
b = a.eval(session = sess)
Image.fromarray(np.uint8(b[0])).show()
```

## （2）tf.image.crop_and_resize
这是在对图像、特征层处理经常用到的函数，可以对特征层或者图像resize。
```python
tf.image.resize_images(
    images,
    size,
    method=ResizeMethod.BILINEAR,
    align_corners=False
)
```
使用指定的method调整images为size。
调整大小的图像将失真，如果他们的原始纵横比与size不一样。

- images：形状为[batch, height, width, channels]的4-D张量或形状为[height, width, channels]的3-D张量。
- size：2个元素(new_height, new_width)的1维int32张量,表示图像的新大小。
- method：ResizeMethod,默认为ResizeMethod.BILINEAR。
- align_corners：布尔型,如果为True,则输入和输出张量的4个拐角像素的中心对齐,并且保留角落像素处的值；默认为False。
如果images是四维,则返回一个形状为[batch, new_height, new_width, channels]的四维浮动张量；如果images是三维,则返回一个形状为[new_height, new_width, channels]的三维浮动张量.

## （3）tf.transpose
可以对输入进行转置。
```python
tf.transpose(
    a,
    perm=None,
    name='transpose',
    conjugate=False
)
```

- a：一个 Tensor。
- perm：a 的维数的排列。
- name：操作的名称(可选)。

# 获得特定位置的内容
## (1) tf.gather
```python
tf.gather(
    params,
    indices,
    validate_indices=None,
    name=None,
    axis=0
)
```
- params：一个张量，这个张量是用来收集数值的.该张量的秩必须至少是 axis + 1。
- indices：一个张量.必须是以下类型之一：int32,int64.索引张量必须在 [0, params.shape[axis]) 范围内。
- axis：一个张量.必须是以下类型之一：int32，int64。在参数轴从中收集索引。默认为第一个维度.支持负索引。
- name：操作的名称(可选)。

测试代码:
```python
import tensorflow as tf
 
a = tf.Variable([[1,2,3,4,5], [6,7,8,9,10], [11,12,13,14,15]])
index_a = tf.Variable([0,1,2,3])
 
b = tf.Variable([1,2,3,4,5,6,7,8,9,10])
index_b = tf.Variable([2,4,6,8])
 
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(sess.run(tf.gather(a, index_a)))
    print(sess.run(tf.gather(a, index_a,axis = 1)))
    print(sess.run(tf.gather(b, index_b)))
```
输出：
```python
[[ 1  2  3  4  5]
 [ 6  7  8  9 10]
 [11 12 13 14 15]
 [ 0  0  0  0  0]]
[[ 1  2  3  4]
 [ 6  7  8  9]
 [11 12 13 14]]
[3 5 7 9]
```

## (2) tf.gather_nd
获得特定位置的内容。
```python
tf.gather_nd(
    params,
    indices,
    name=None
)
```
- params：张量，这个张量是用来收集数值的。
- indices：张量，必须是以下类型之一：int32，int64；索引张量。
- name：操作的名称(可选)。

测试代码:
```python
import tensorflow as tf
 
a = tf.Variable([[1,2,3,4,5], [6,7,8,9,10], [11,12,13,14,15]])
index_a = tf.Variable([[0,1],[0,0],[1,3],[2,4]])

b = tf.Variable([[[1,2,3],[4,5,6]], [[6,7,8],[9,10,11]], [[11,12,13],[14,15,16]]])
index_b = tf.Variable([[0,1],[0,0],[1,1],[2,2]])
index_c = tf.Variable([[0,1,1],[0,0,0],[1,1,1],[2,2,2]])
 
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(sess.run(tf.gather_nd(a, index_a)))
    print(sess.run(tf.gather_nd(b, index_b)))
    print(sess.run(tf.gather_nd(b, index_c)))
```
输出：
```python
[ 2  1  9 15]
[[ 4  5  6]
 [ 1  2  3]
 [ 9 10 11]
 [ 0  0  0]]
[ 5  1 10  0]
```

## (3) tf.where
判断哪些位置符合这个表达式。
```python
tf.where(
    condition,
    x=None,
    y=None,
    name=None
)
```

根据condition返回x或y中的元素。

如果x和y都为None,则该操作将返回condition中true元素的坐标，坐标以二维张量返回，其中第一维(行)表示真实元素的数量，第二维(列)表示真实元素的坐标。

如果两者都不是None。则x和y必须具有相同的形状。如果x和y是标量，则condition张量必须是标量，如果x和y是更高级别的矢量，则condition必须是大小与x的第一维度相匹配的矢量，或者必须具有与x相同的形状。

condition张量作为一个可以选择的掩码(mask)，它根据每个元素的值来判断输出中的相应元素/行是否应从 x (如果为 true) 或 y (如果为 false)中选择。

如果condition是向量，则x和y是更高级别的矩阵，那么它选择从x和y复制哪个行(外部维度)。如果condition与x和y具有相同的形状，那么它将选择从x和y复制哪个元素。

测试代码:
```python
import tensorflow as tf
import numpy as np
sess=tf.Session()

#-------------------#
#   用法1：
#   x，y没值的时候
#-------------------#
a=[[[True, False],
    [True, False]],
   [[False, True],
    [False, True]],
   [[False, False],
    [False, True]]]
print(sess.run(tf.where(a)))
#-------------------#
#   用法2：
#   x，y有值的时候
#-------------------#
a=np.array([[1,0,0],[0,1,1]])
a1=np.array([[3,2,3],[4,5,6]])

print(sess.run(tf.equal(a,1)))
print(sess.run(tf.where(tf.equal(a,1),a,a1)))
```
输出：
```python
[[0 0 0]
 [0 1 0]
 [1 0 1]
 [1 1 1]
 [2 1 1]]
[[ True False False]
 [False  True  True]]
[[1 2 3]
 [4 1 1]]
```

# 矩阵操作
## （1）tf.cast
```python
tf.cast(
    x,
    dtype,
    name=None
)
```
tf.cast()函数的作用是执行 tensorflow 中张量数据类型转换，比如读入的图片如果是int8类型的，一般在要在训练前把图像的数据格式转换为float32。

- x：待转换的数据（张量）
- dtype：目标数据类型
- name：可选参数，定义操作的名称

测试代码:
```python
import tensorflow as tf
import numpy as np

t1 = tf.Variable([1,2,3,4,5])
t2 = tf.cast(t1,dtype=tf.float32)

with tf.Session() as sess:
    print('t1: {}'.format(t1))
    print('t2: {}'.format(t2))
    sess.run(tf.global_variables_initializer())
    sess.run(t2)
    print(t2.eval())
```
输出：
```python
[1. 2. 3. 4. 5.]
```

## （2）tf.stack
```python
tf.stack(
    values,
    axis=0,
    name='stack'
)
```
将秩为 R 的张量列表堆叠成一个秩为 (R+1) 的张量.

- values：具有相同形状和类型的 Tensor 对象列表.
- axis：一个 int，要一起堆叠的轴，默认为第一维，负值环绕，所以有效范围是[-(R+1), R+1)
- name：此操作的名称(可选)。

测试代码:
```python
import tensorflow as tf
import numpy as np

x = tf.constant([1, 4])
y = tf.constant([2, 5])
z = tf.constant([3, 6])

s0 = tf.stack([x, y, z])  # [[1, 4], [2, 5], [3, 6]] (Pack along first dim.)
s1 = tf.stack([x, y, z], axis=1)  # [[1, 2, 3], [4, 5, 6]]

with tf.Session() as sess:
    print(sess.run(s0))
    print(sess.run(s1))
```
输出：
```python
[[1 4]
 [2 5]
 [3 6]]
[[1 2 3]
 [4 5 6]]
```

## （3）tf.concat
用于对张量进行拼接：
```python
tf.concat(
	[tensor1, tensor2, tensor3,...], 
	axis
)
```

测试代码:
```python
import tensorflow as tf
import numpy as np

x = tf.constant([1, 4])
y = tf.constant([2, 5])
z = tf.constant([3, 6])

s0 = tf.concat([x, y, z], axis=0)  # [[1, 4], [2, 5], [3, 6]] (Pack along first dim.)


x = tf.constant([[1], 
                 [4]])
y = tf.constant([[2], 
                 [5]])
z = tf.constant([[3], 
                 [6]])

s1 = tf.concat([x, y, z], axis=0) 
s2 = tf.concat([x, y, z], axis=1) 

with tf.Session() as sess:
    print(sess.run(s0))
    print(sess.run(s1))
    print(sess.run(s2))

```
输出：
```python
[1 4 2 5 3 6]
[[1]
 [4]
 [2]
 [5]
 [3]
 [6]]
[[1 2 3]
 [4 5 6]]
```

## （3）tf.reduce_max
用于求取某个维度最大值：
```python
tf.reduce_max(
    input_tensor,
    axis=None,
    keep_dims=False,
    name=None,
    reduction_indices=None
)
```
计算一个张量的各个维度上元素的最大值.
按照axis给定的维度减少input_tensor。除非 keep_dims 是true，否则张量的秩将在axis的每个条目中减少1。如果keep_dims为true，则减小的维度将保留为长度1。

- input_tensor：要使用的张量。。
- axis：要减小的尺寸。如果为，None(默认),则减少所有维度.必须在[-rank(input_tensor), rank(input_tensor))范围内。
- keep_dims：如果为true,则保留长度为1的减少维度。
- name：操作的名称(可选)。
- reduction_indices：axis的废弃的名称。

测试代码:
```python
import tensorflow as tf
import numpy as np

a=np.array([[1, 2],
            [5, 3],
            [2, 6]])

b = tf.Variable(a)
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(sess.run(tf.reduce_max(b)))
    print(sess.run(tf.reduce_max(b, axis=1, keepdims=False)))
    print(sess.run(tf.reduce_max(b, axis=1, keepdims=True)))
    print(sess.run(tf.reduce_max(b, axis=0, keepdims=True)))
```
输出：
```python
6
[2 5 6]
[[2]
 [5]
 [6]]
[[5 6]]
```
