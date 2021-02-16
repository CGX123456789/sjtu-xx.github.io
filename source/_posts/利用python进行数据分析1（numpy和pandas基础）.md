---
title: 利用python进行数据分析1（numpy和pandas基础）
toc: true
date: 2020-02-17 18:09:40
tags: [numpy,pandas,python,数据分析]
categories:
- Python
- 第三方库
---

ipynb案例：推荐使用<https://github.com/sjtu-xx/scientific-packages-for-python/>
# 一、numpy
<!--more-->
## 1.ndarray

### （1）生成ndarray

```python
np.array （将输入转换为ndarray，自动复制）
np.asarray (输入转换为ndarray，已经为ndarray不再复制)
np.arange  np.arange(32).reshape((8,4)
np.ones
np.ones_like
np.zeros
np.zeros_like
np.empty
np.empty_like
np.full
np.full_like
np.eye,np.identity (单位矩阵)
np.random.randn(6,3) 	生成正态分布的6*3的数组
```

### （2）数据类型和大小

```
设定
a= np.array([1,2,3],dtype=np.int32)
查看
a.dtype
更改
a.astype()

a.shape 数组的尺寸
a.ndim 数组的维度

> means ‘big-endian’
```

删除：np.delete(a,[0,3]) 	删除索引处的值

插入：np.append(ndarray, elements, axis)\\np.insert(ndarray, index, elements, axis)



### （3）数组算数

1.相同尺寸的数组操作：对应元素操作

```python
arr + arr
arr - arr
arr * arr
```

2.带有标量计算的操作：将参数传递给每个元素

```python
1/arr
arr**2
```

3.同尺寸数组比较：产生同尺寸布尔数组

```python
arr2 > arr1
```

### （4）索引和切片

**索引返回的是对象的视图（自己认为是返回对象的引用）**

```python
索引与赋值：
arr[5:8] = 12

arr[::2] 从0开始，步长为2
索引数组的变化会体现在原数组上
arr_slice = arr[5:8]
arr_slice[1] = 2

复制：
arr_copy = arr[5:8].copy()

二维索引(两种方法等价)：
arr[2][1]
arr[2,1]

arr[:2] 前两行
arr[1,:2] 	第一行前两列

布尔索引
data == 1 	返回与data同尺寸的布尔数组
data[data==1]   
类似的有 
取反操作： ~(data==1)   data!=1 
或操作： (data==1)|(data==2)
与操作： (data==1)&(data==2)
比较： data<0

data[name == 'Bob'] 若name是一维数组，且大小与data数组轴索引长度一致，则返回True对应的行

神奇索引
arr[[4,3,1,2]] 将依次返回对应的行
arr[[4,2,3],[1,2,3]] 	将返回(4，1)，(2，2)，(3，3)对应的值

查找：
arr.searchsorted(0.5) 在已经排序的数组中查找0.5
```

### （5）数组的转置和换轴

```python
arr.T 转置
arr.transpose(1,0,2) （0，1，2）轴替换为（1，0，2）轴
arr.swapaxes(1,2) 交换1，2轴，返回数组的视图，没有对原数组进行复制
```

## 2.通用函数：快速的逐元素数组函数

### （1）一元ufunc

1. `abs、fabs`计算整数、浮点数或复数的绝对值。对于非复数值，可以使用更快的fabs。

2. `log10，log2，log1p`，其底数分别为，10,2；以及log（1+ P)

3. `singn` 计算个元素正负号，1（正数），0（零），-1（负数）

4. `ceil`返回大于等于该值的最小整数。

5. `floor` 返回小于等于该值的最大整数。

6. `rint`四舍五入，保留dtype。

7. `modf`将数组的小数和整数部分一两个独立数组的形式返回。

8. `isnan`返回布尔型数组，表示哪些值是NaN（非数字）。

9. `isfinite/isinf`返回布尔型数组，表示哪些元素是有穷的（非inf，非NaN）或那些是无穷的。

10. `cos,cosh,sin,sinh,tan,tanh`普通型和双曲型三角函数。

11. `arccos,arccosh,arcsin,arcsinh,asrtan,arctanh`反三角函数。

12. `logical_not`计算个元素的not x的真值，相当于-arr。

### （2）二元ufunc

1.  `add`   元素对应相加。

2.  `subtract`   第一个数组元素减去第二个的元素。

3.  `multiply` 数组元素相乘。

4.  `divide,floor_divide`    除法，或向下整除（丢弃余数）。

5.  `power`第一个数组中的元素A，第二个数组中的元素B，返回A的B次方。

6.  `maximum,fmax`返回两个数组中较大值组成的数组。fmax忽略NaN。

7.  `minimum,fmin`同上。

8.  `mod`求模，即求余数。

9.  `copysign`第二个数组的符号，复制给第一个数组。

10.  `greater,greater_equal,less,less_equal,equal,not_euqal`元素级比较运算，最终产生布尔型数组，相当于>,>=,<,<=,==,!=。

11.  `logical_and,logical_or,logical_xor`元素级真值运算，相当于&，|，^。

## 3.使用数组进行面向数组编程

### （1）将条件逻辑作为数组操作

```python
np.where(cond,xarr,yarr)  等价于 x if condition else y, cond布尔数组，xarr，yarr同大小数组
np.where(a>0,2,-2) 大于0填2，否则-2
```

### （2）数学和统计方法

```python 
均值
np.mean(arr)
arr.mean()
arr.mean(axis=1) 指定轴的操作

sum 求和
mean 平均
std，var  标准差和方差
min，max 最值
argmin，argmax 最值的位置
cumsum 累加和，可以对指定的轴进行累加
cumprod 累乘，可以对指定的轴进行累乘
```

### （3）布尔数组的方法

```python
a = np.array([True,False])
a.sum()  计算True的个数
a.any()   True，any可以接受axis参数
a.all()   False
```

### （4）排序

```python
arr.sort()
arr.sort(1) 对维度1的数据进行排序
```

### （5）唯一值与其他集合逻辑

```python
np.unique(arr) 计算唯一值，并排序
intersect1d(x,y) 计算交集并排序
union1d(x,y)  计算并集并排序
in1d(x,y) 计算在x中元素是否在y中，返回布尔数组
setdiff1d(x,y) 计算在x但不在y中的元素
setxor1d(x,y)  在x,y但不在交集中的元素
```

## 4.使用数组进行文件的输入和输出

```python
np.save('test.npy',arr)
np.load('test.npy')
np.savez('test.npz',a=arr1,b=arr2)
```

## 5. 线性代数

```python
点乘：
np.dot(x,y)
x.dot(y)
x @ y
```

**np.linalg中的函数**

| 函数  | 描述                       |
| ----- | -------------------------- |
| diag  | 将对角元素作为一维数组返回 |
| dot   | 点乘                       |
| trace | 对角元素和                 |
| det   | 行列式                     |
| eig   | 特征值和特征向量           |
| inv   | 逆矩阵                     |
| pinv  | Moore-Penrose伪逆          |
| qr    | QR分解                     |
| svd   | 计算奇异值分解（SVD）      |
| solve | 求解Ax=b，其中A是方阵      |
| lstsq | 计算Ax=b的最小二乘解       |

## 6.伪随机数的生成

np.random的数据生成函数公用了一个随机数种子 即，np.random.seed(1234)

可以生成一个自定义种子的随机数生成器，rng = np.random.RandomState(1234)   rng.randn(10)

np.random下的部分函数

| 函数        | 描述                             |
| ----------- | -------------------------------- |
| seed        | 随机数种子                       |
| permutation | 返回一个序列的随机排列           |
| shuffle     | 随机排列一个序列                 |
| rand        | 从均匀分布中抽取样本             |
| randint     | 随机整数                         |
| randn       | 从均值0方差1的正态分布中抽取样本 |
| binomial    | 从二项分布中抽取样本             |
| normal      | 从正态分布中抽取样本             |
| beta        | 从beta分布中抽取样本             |
| chisquare   | 从卡方分布中抽取样本             |
| gamma       | 从伽马分布中。。。。             |
| uniform     | 从（0，1）中抽取样本             |

# 二、pandas

## 1.pandas中的数据结构

### （1）Series

```python
obj = pd.Series([1,2,3,4])
obj.values 返回值
obj.index 	返回索引
obj.add(item,fill_value=0)

obj = pd.Series([1,2,3,4],index=['a','b','x','e'])
obj['a']	可以使用标签索引

obj = pd.Series({'a':12,'b':1})
字典参数时，obj将按照value从大到小进行排列
obj = pd.Series({'a':12,'b':1}，index=["a","b"])
这样可以按照index中的键进行排序

Series可以使用numpy中的函数

obj.isnull() 返回布尔Series
obj.notnull()

Series对象和其索引都有name属性
obj.name
obj.index.name 

obj.index = ['b','a'] 	可以改变元素顺序

```

### （2）DataFrame

```python
构造：
data = {'a':[1,312,3,4],'n':[23,123,4,32]}
frame = pd.DataFrame(data)
指定列顺序
frame = pd.DataFrame(data,columns=['n','a'])
指定index
frame = pd.DataFrame(data,columns=['n','a'],index=['asd','asdf','asdfasdf'])

frame.head() 前五行
frame.tail()

返回名为a的列:
frame.a 
frame['a'] 

行的选取：
frame.loc['asd']

返回所有的列名
frame.column

删除列
del frame['a']

转置
frame.T

返回数据
frame.values 以二维数组ndarray的方式返回
```

注意：选取列/行返回的是列的索引。

#### （3）索引对象

```python
obj.index 索引对象不可变，用户不可修改
pd.Index(['a','b'])  创建索引对象
```

**索引对象的方法**

| 方法         | 描述                                   |
| ------------ | -------------------------------------- |
| append       |                                        |
| difference   | 差集                                   |
| intersection | 交集                                   |
| union        | 并集                                   |
| isin         | 表示每一个值是否在传值容器中的布尔数组 |
| Delete       | 将位置i处的元素删除                    |
| delete       |                                        |
| insert       | 在位置i处插入元素                      |
| is_monotonic | 如果索引递增返回True                   |
| is_unique    | 如果索引序列唯一返回True               |
| unique       | 计算索引的唯一值序列                   |

## 2.基本功能

### （1）重建索引

```python
行重建索引
obj.reindex(['a','b','x']) 不存在的索引会引入缺省值
列重建索引
obj.reindex(column=['a','b','c'])
查看
obj.loc[['a','b','c'],['aw','qwer']]  行，列
```

### （2）轴向上删除数据

```python
data.drop('a')
data.drop('a',axis=1)
data.drop(['a','b'],axis='column')
```

注意：凡是会对原数组作出修改并返回一个新数组的，往往都有一个 inplace可选参数。如果手动设定为True（默认为False），那么原数组直接就被替换。也就是说，采用inplace=True之后，原数组名（如2和3情况所示）对应的内存值直接改变；

而采用inplace=False之后，原数组名对应的内存值并不改变，需要将新的结果赋给一个新的数组或者覆盖原数组的内存位置（如1情况所示）。

### （3）索引，选择，过滤

注意：标签索引包含尾部，整数索引不包含尾部

索引

```python
Series
obj = pd.Series([0,1,12,34]，index=['a','b','c'])
obj[2:3] 不包含尾部
obj['b':'c']包含尾部

DataFrame
data['a'] 使用单个值或序列索引列
data[['a','b']]
data[:2] 选择前两行
data[data['three']>5]选择three大于5的行
```

选择

```python
轴标签loc,整数标签iloc
data.loc[['a','b','c'],['as','ear','ear']]
支持切片

选择单个标签
df.at['a','b']
df.iat[1,2]

get_value,set_value 根据标签设置单个值
```

### （4）算数和数据对齐

两个Series或DataFrame运算时，不重叠的部分会产生缺省值NaN

r开头的方法有fill_value关键字参数

df1.radd(df2,fill_value=0)

### （5）DataFrame和Series数据之间的操作

默认情况下，DataFrame和Series的数学操作会将Series的索引和DataFrame的列进行匹配并广播到各行。

如果要在列上广播，使用df.sub(series,axis="index")

### （6）函数应用和映射

将函数应用到某一行或某一列的数组上。

```python
f = lambda x:x.max()-x.min()
frame.apply(f)  对每一列调用一次，有axis参数

也可以返回序列
def f(series):
  return pd.Series([series.max(),series.min()],index=['max','min'])
```

### （7）排序和排名

```python
obj.sort_index(axis=0,ascending=False)  将对应的数据也进行排序，默认是升序
obj.sort_values(by=['a','b'],axis=0)  按照a,b,两列进行排序
obj.rank()  返回排名值，对并列的取平均
obj.rank(method='first')  返回排名，当时对并列的不取平均
```

rank的方法

| 方法    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| average | 默认，平均值                                                 |
| min     | 最小值                                                       |
| max     | 最大值                                                       |
| first   | 按照在数据中出现的次序进行排序                               |
| dense   | 类似于method=‘min’，但组间排名总是加一，而不是一个组中的相等元素的数量。 |

### （8）含有重复标签的轴索引

```
obj.index.is_unique 	判断是否存在重复
```

## 3.描述性统计的概述与计算

skipna参数默认为True，

描述性统计和汇总统计

| 方法           | 描述                                                     |
| -------------- | -------------------------------------------------------- |
| count          | 非NA值的个数                                             |
| describe       | 计算Series或DataFrame各列的汇总统计集合                  |
| min，max       | 计算最小值，最大值                                       |
| argmin，argmax | 计算最大值，最小值的索引位置                             |
| idxmin，idxmax | 计算最大值，最小值的索引                                 |
| quantile       | 计算样本从0-1之间的分位数                                |
| sum            |                                                          |
| mean           |                                                          |
| median         |                                                          |
| mad            | 平均值的平均绝对偏差                                     |
| prod           | 所有值的积                                               |
| var            | 方差                                                     |
| std            | 标准差                                                   |
| skew           | 样本偏度值（第三时刻）                                   |
| kurt           | 样本峰度值（第四时刻）                                   |
| cumsum         | 累加                                                     |
| cummin，cummax | 累计的最小，最大                                         |
| cumprod        | 累乘                                                     |
| diff           | 计算第一个算数差值<br />将数据作某种移动后与原数据的差值 |
| pct_change     | 计算百分比改变，相比前一项                               |

### （1）相关性和协方差

```python
df['col1'].corr(df1['col2']) 	计算两个Series中重叠的，非NA的，按照索引对齐的值的相关性。
df['col1'].cov(df1['col2'])

返回所有列之间相互的相关性
df1.corr()
df1.cov()

返回所有列和IBM列的相关性
df1.corrwith(df1.IBM)
```

### （2）唯一值，计数和成员属性检查

```python
Series方法
obj.unique() 返回唯一值
obj.value_count() 数据计数

pd.value_count(obj.values,sort=False)

obj.isin(['a','b']) 	成员属性检查
```

### 4.索引

```
df.reindex(columns='xx',level='ess')
```

### 5.map

```
df['name'].map(func) 对name列的对象执行func操作
```


