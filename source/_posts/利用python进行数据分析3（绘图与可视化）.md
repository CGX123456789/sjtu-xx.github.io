---
title: 利用python进行数据分析3（绘图与可视化）
toc: true
date: 2020-02-17 18:16:27
tags: [pandas,seaborn,python,数据分析]
categories:
- Python
- 第三方库
---

# 六、绘图与可视化
<!--more-->
## 1.matplotlib入门

### （1）图片与子图

```
plt.plot(np.arange(10))
plt.show()
```

plt绘制的图位于Figure对象中

```
fig = plt.figure()
ax1 = fig.add_subplot(2,2,1)  两行两列，第一个位置
ax2 = fig.add_subplot(2,2,2)
ax3 = fig.add_subplot(2,2,3)
此时调用plt.plot()会在最后一张图进行绘制
```

fig.add_subplot返回的是Axes Subplot对象

**图形类型：**

hist，scatter

**子图：**

```
fig,axes = plt.subplots(2,3)
axes为Axes Subplot对象列表
索引方法：axes[0,1]

subplots方法参数：
sharex：所有子图使用相同的x刻度
subplot_kw：传入add_subplot的关键字参数字典，用于生成子图
```

#### 调整子图间距

`plt.subplot_adjust(left=None,bottom=None,right=None,top=None,wspace=None,hspace=None)`

wspace和hspace分别控制的是图片的宽度和高度百分比，用作子图间距。

#### 颜色，标记和线型

1）字符串参数指定：

`plt.plot(x,y,'g--')`绿色，虚线

`plt.plot(x,y,'go--')`绿色，虚线,标记为点

2）参数指定

`plt.plot(x,y,color='g',linestyle='--')`

`plt.plot(x,y,color='g',linestyle='dashed'，marker='o')`

3）折线图的线性插值

`plt.plot(x,y,'k--',drawstyle='step-post')` drawstyle默认为线性插值

### （2）刻度、标签和图例

`xlim,xticks,xticklabels`

使用方法：

1）不带参数

plt.xlim() 返回当前的x轴绘图范围

2)带参数

plt.xlim([0,10]) 设置范围

这些方法对应子图中的get_xlim()和set_xlim()方法

#### 2.1设置标题、轴标签、刻度和刻度标签

```
ax.set_title('Title')
ax.set_xlable('Stages')
ax.set_xticks([0,200,500,750])
ax.set_xticklabels(['one','two','three','four'],rotation=30,fontsize='small')

prop={
	'title':"Title",
	'xlabel':"Stages"
}
ax.set(**prop)
```

#### 2.2 图例

要使用图例，在绘制时要穿入 label参数，

不使用图例，绘制时不要穿入label或传入`label='_nolegend_'`

![image-20190706151130509](image/image-20190706151130509-2397090.png)

### (3)注释与子图加工

文本，箭头：text，arrow，annotate方法

文本：

`ax.text(x,y,'hello world',family='monospace',fontsize=10)`

添加箭头：

```
ax.annotate(label,
						xy=(x, data.asof(x)+75),
						xytext=(x, data.asof(x)+275),
						arrowprops={
								facecolor:'black',
								headwidth:4,
								width:2,
								headlength:4
						},
						horizontalalignment='left',
						verticalalignment='top')
```

其他图形：

matplotlib.pyplot中提供了常见图形的引用，完整的引用在matplotlib.patches中

使用时，先生成patch对象shp，然后调用ax.add_patch将图形添加到子图中

```
rect = plt.Rectangle((0.2,0.75),0.4,0.15,color='k',alpha=0.3)
ax.add_patches(rect)
```

### (4)将图像保存到文件

```
plt.savefig(filename)
plt.savefig(filename,dpi=400,bbox_inches='tight')  bbox_inches修建图片的空白

参数：
facecolor,edgecolor. 子图之外背景的颜色；'w'默认
format 导出图片的格式
bbox_inches. 导出图片中figure所占的百分比
```

### （5）修改配置

```
plt.rc()
rc的第一个参数时想要自定义的组件，如figure，axes，xtick等

使用：
font_options = {
			'family':'monospace',
			'size':'small',
			'weigth':bold
}
plt.rc('font',**font_options)
```

## 2.使用pandas和seaborn绘图

### （1）折线图

默认情况下pandas对象调用plot函数会绘制折线图

```
s.plot()
df.plot() 等价df.plot.line()

Series方法参数：
lable:标签
ax：绘图所用的matplotlib子图对象；如果没有传值，使用当前活动的matplotlib子图
style：'g--'等
kind：可以是area,bar,barh,density,hist,kde,line,pie
logy:在y轴上对数缩放
use_index：使用对象索引刻度标签
rot：刻度标签的旋转
xticks：用于x轴刻度的值
xlim：x轴范围
grid：展示轴的网格

Dataframe方法参数：
subplots：将每一列绘制在单独的子图
sharex：共享x轴的参数
figsize：
title：标题
legend：添加子图图例（默认为True）
sort_columns:按字母顺序绘制各列，默认情况使用已有的列顺序
```

### （2）柱状图

bar(),barh() :垂直/水平柱状图

使用：

`data.plot.bar(ax=axes[0],color='k',alpha=0.7)`

堆积柱状图：

`data.plot.bar(stacked=True)`

![image-20190706155525312](image/image-20190706155525312-2399725.png)

使用seaborn绘制：

```
import seaborn as sns
sns.barplot(x='tip_pct',y='day',data=tips,orient='h')
sns.barplot(x='tip_pct',y='day',hue='time',data=tips,orient='h')  hue参数：将数据按照time进行分类
样式：
sns.set(style='whitegrid')
```

![image-20190706163812460](image/image-20190706163812460-2402292.png)

### （4）直方图和密度图

直方图：`data.plot.hist(bins=50)`分成50列

内核密度估计图：`data.plot.density()` 

同时绘制两者：`sns.distplot(data,bins=100,color='k')`

### (5)散点图和点图

```
散点图
sns.regplot('col1','col2',data=trans_data)

散点图组
sns.pairplot(trans_data,kind='scatter',diag_kind='kde',plot_kws={'alpha':0.2})
```

### （6）特征网格和分类数据

![image-20190706170519024](image/image-20190706170519024-2403919.png)

`sns.factorplot(x='day',y='tip',row='time',col='smoker',data=tips,kind='bar')`

通用的特征网格 sns.FacetGrid






