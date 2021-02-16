---
title: Matplotlib绘图案例
toc: true
date: 2019-11-27 17:01:00
tags: [python,matplotlib,latex,绘图]
categories:
- Python
- 第三方库
---

# 绘图数据处理
## 绘图时对数据进行三次样条插值：
<!--more-->
```python
import matplotlib.pyplot as plt
from scipy.interpolate import BSpline,make_interp_spline

#....省略数据读取
xnew = np.linspace(np.min(x_data), np.max(x_data), 300)
spl = make_interp_spline(x_data, y_data, k=3)  # type: BSpline
ynew = spl(xnew)

plt.plot(xnew,ynew)
```

# 中文字体支持
windows：
```python
import matplotlib.pyplot as plt
# 设置新罗马字体
plt.rcParams['font.sans-serif']=['SimHei']  #中文
plt.rcParams['axes.unicode_minus']=False  #负号
```
mac:
```python
### 使用mac自带的中文字体，不推荐
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS']

### 使用simhei
# 1. 安装simhei  https://www.fontpalace.com/font-details/SimHei/
# 2. 执行rebuild 
from matplotlib.font_manager import _rebuild
_rebuild() #reload一下

# 3. 使用
plt.rcParams['font.sans-serif']=['SimHei']  #中文
plt.rcParams['axes.unicode_minus']=False  #负号

# 查看可用字体
from matplotlib.font_manager import FontManager
fm = FontManager()
mat_fonts = set(f.name for f in fm.ttflist)
print mat_fonts
```


# 一般折线图的绘制
```python
from pylab import mpl
mpl.rcParams['font.sans-serif'] = ['SimHei'] # 指定默认字体
mpl.rcParams['axes.unicode_minus'] = False # 解决保存图像是负号'-'显示为方块的问题

import matplotlib.pyplot as plt
# 设置新罗马字体
plt.rc('font',family='Times New Roman')
plt.rcParams['font.sans-serif']=['SimHei']  #中文
plt.rcParams['axes.unicode_minus']=False  #负号
# 省略读取部分
fig,ax = plt.subplots()
ax.plot(x_data,y_data,label="线标签")  #添加标签
ax.set_title(r"$\sigma-\epsilon$")   #设置图表名称  latex设置在r‘’中
ax.set_xlabel(r"$\varepsilon$/%")   #设置x轴标签
ax.set_ylabel(r"$\sigma$/MPa")    #设置y轴标签
ax.set_xlim(0,8)   #设置x轴范围
ax.set_xticks([])  #关闭x坐标tick
ax.tick_params(direction='in',colors='black')   #设置tick的参数  
plt.legend()   #显示标签（绘图时的label）
# plt.legend(frameon=False,fontsize=8) #取消边框，设置大小
plt.xticks(x,labels,rotation=70)   #tick显示
# plt.xticks([])  #关闭tick
plt.axis("off")  #关闭坐标轴
plt.savefig(result_name,dpi=600,bbox_inches='tight',format="png")  #保存图片
# 保存tiff时防止文件大小过大
# plt.savefig在保存tiff文件时，调用的是pil中的函数，但是并没有提供对应的参数接口
# 在mpl 3.1.0版本新加入了设置pil.image参数的功能
plt.savefig("color_grain.tiff", dpi=1200, bbox_inches='tight', format="tiff", transparent=True,pil_kwargs={"compression": "tiff_lzw"}) # 当参数错误时会被自动忽略 # 其他压缩算法 https://imageio.readthedocs.io/en/stable/format_tiff-pil.html
plt.show()  #显示图片

plt.cla()   # Clear axis
plt.clf()   # Clear figure
plt.close() # Close a figure window
```

# 直方图的绘制
```python
# 对于dataframe中的数据，直接使用下面的代码
df["column_name"].hist(bins=20)  

# 对于pyplot，使用时存在nan值可能会报错
# 方法1,选择非nan
import numpy as np
import matplotlib.pyplot as plt
data = [1,23,4214,12,4,124,1,24,np.nan]
plt.hist(data[data is not np.nan])
# 方法2，删除nan
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
data = pd.Series([1,23,4214,12,4,124,1,24,np.nan])
plt.hist(data.dropna())
```
# 双y轴图片的绘制
```python
# -*- coding: utf-8 -*-
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import rc
rc('mathtext', default='regular')

time = np.arange(10)
temp = np.random.random(10)*30
Swdown = np.random.random(10)*100-10
Rn = np.random.random(10)*100-10

fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(time, Swdown, '-', label = 'Swdown')
ax.plot(time, Rn, '-', label = 'Rn')
ax2 = ax.twinx()
ax2.plot(time, temp, '-r', label = 'temp')
ax.legend(loc=0)
ax.grid()
ax.set_xlabel("Time (h)")
ax.set_ylabel(r"Radiation ($MJ\,m^{-2}\,d^{-1}$)")
ax2.set_ylabel(r"Temperature ($^\circ$C)")
ax2.set_ylim(0, 35)
ax.set_ylim(-20,100)
ax2.legend(loc=0)
plt.savefig('0.png')
```
> 调整legend框的位置
> `fig.legend(loc=1, bbox_to_anchor=(1,1), bbox_transform=ax.transAxes) # 调整legend框的位置`
> 多个axes中legend的添加
> ```python
> plot1 = ax1.plot() # list
> plot2 = ax2.plot() # list
> lines = plot1+plot2
> ax1.legend(lines,[l.get_label() for l in lines])
> ```

结果：
![](double_y.png)


# 给两色图片添加legend
```python
import numpy as np
import matplotlib.pyplot as plt
import os
import pylab as plot

# 设置legend的label大小以及handle宽度
params = {'legend.fontsize': 12,
          'legend.handlelength': 2}
plot.rcParams.update(params)
from matplotlib.patches import Rectangle

RESULT_DIR = "./result"
if not os.path.exists(RESULT_DIR):
    os.makedirs(RESULT_DIR, exist_ok=True)

for png_name in os.listdir("./data"):
    # 建立两个subplot，第二个来绘制legend
    no_color_img = plt.imread(os.path.join("./data", png_name))
    unique_value = np.unique(no_color_img, return_counts=True)[0]
    print("total_color_count:", len(unique_value))
    value_list = sorted(unique_value.tolist())
    new_color_img = np.zeros_like(no_color_img)
    for index, pvalue in enumerate(value_list):
        new_color_img[no_color_img == pvalue] = index
    print(value_list)

    fig, ax = plt.subplots(1, 2)
    img_ax = ax[0]
    imgs = img_ax.imshow(new_color_img, cmap=plt.get_cmap("winter", len(value_list)))
    img_ax.axis("off")

    # handle的rgb颜色可以通过mac取色器获得
    legend_ax = ax[1]
    if len(value_list) == 2:
        legend_data = [[0, [16, 255, 127], "Ferrite"], [1, [0, 5, 255], "Martensite"]]
    elif len(value_list) == 3:
        legend_data = [[0, [16, 255, 127], "Ferrite"], [1, [0, 5, 255], "Martensite"],[2, [0, 5, 255], "Martensite"]]

    handles = [
        Rectangle((0, 0), 1, 1, color=[v / 255 for v in c]) for k, c, n in legend_data
    ]
    legend_ax.legend(handles, [n for k, c, n in legend_data], mode="expand", ncol=1, frameon=False,
                     loc="center")
    legend_ax.axis("off")
    plt.subplots_adjust(hspace=0, wspace=0)  # plt.tight_layout()
    plt.savefig(os.path.join(RESULT_DIR, "{}-color.tiff".format(png_name)), dpi=1200, bbox_inches='tight',
                format="tiff", transparent=True, pil_kwargs={"compression": "tiff_deflate"})
    plt.show()
```


# 内置的colorbar
[官方网站](https://matplotlib.org/3.1.0/tutorials/colors/colormaps.html)
内置颜色
![colorname](color.png)

# 绘制网格图
```python
import matplotlib.pyplot as plt
import numpy as np
from matplotlib.colors import LinearSegmentedColormap

image_array = np.eye(4) 

fig = plt.figure()
ax = fig.gca()  # Get the current axes, creating one if necessary.
# 自定义colorbar
cmap = LinearSegmentedColormap.from_list('mycmap', ['red', 'blue', 'green'])  # colorbar为过渡渐变
im = ax.imshow(image_array, cmap=cmap, vmin=0, vmax=2)

plt.setp(ax.spines.values(), linewidth=2)  # 设置上下左右边框的宽度
plt.grid(True, color='black', linestyle='-', linewidth=2, axis="both")  #设置网格的属性（颜色和宽度等）
plt.tick_params(direction='in') # 将tick朝内，隐藏
plt.xticks([i + 0.5 for i in range(3)], visible=False)  #设置网格线的位置
plt.yticks([i + 0.5 for i in range(3)], visible=False)
plt.savefig("grid1.png", bbox_inches="tight", dpi=600, transparent=True, format="png") #保存图片
plt.show()
```
执行结果：
![网格图](grid1.png)


# 曲线间的填充
```python
# 对函数和坐标轴之间的区域进行填充
x = np.linspace(0,100,1000)
y = np.sin(x)

plt.fill(x,y,color="g",alpha=0.5)


# 对函数之间的区域进行填充
x = np.linspace(0,100,1000)
y1 = np.sin(x)
y2 = np.cos(x)

plt.fill_between(x,y1,y2,facecolor="g")
plt.fill_between(x,y1,y2,where= y2>=y1,facecolor="g",interpolate=True) #interpolate将可能的空白区域进行填充

```

# 核密度估计图
```python
import seaborn as sns
from scipy.stats import *
sns.kdeplot(x)  #只有1D核密度估计图
sns.kedplot(x,y)  #2D核密度估计图
sns.distplot(x,y) #核密度估计图+直方图
sns.distplot(x,y,kde=False)
sns.distplot(x,y,hist=False)
sns.distplot(x,y,fit=norm) #正态分布
```

# 表格
```python
data = pd.read_csv("11.csv",index_col=0)
plt.clf()
fig, ax = plt.subplots()
fig.patch.set_visible(False)
ax.axis('off')
ax.axis('tight')
ax.table(cellText=data.values, colLabels=data.columns,rowLabels=data.index, loc='center')
fig.tight_layout()
plt.savefig("1.png",dpi=600,bbox_inches="tight",pad_inches=1) #防止保存时超出图片边界
```
