---
title: excel文件读写（python）
toc: true
mathjax: true
date: 2020-07-01 08:40:08
tags:
categories:
- Python
- 第三方库
---

由于后面的项目需要直接处理excel表格（xls，xlsx等），因此，对excel处理的相关工具进行学习。
<!--more-->

对于**csv文件**，由于其本质为文本格式，可以直接进行方便的读写操作

通过python对excel表格文件进行处理有多种方式：pandas，openpyxl，xlrd，xlutils和pyexcel之类的软件包都提供了与excel文件交互的接口。

# 1.pandas与excel
## pandas读取excel
```python
import pandas as pd
file = 'example.xlsx'
xl = pd.ExcelFile(file)
print(xl.sheet_names)
df1 = xl.parse('Sheet1')
```

> **pd.ExcelFile vs pd.read_excel**
> pd.ExcelFile是一个类，而read_excel是一个方法
> 在最新版本的pandas中，read_excel确保它具有一个ExcelFile对象（如果没有则创建一个对象），然后_parse_excel直接调用该方法：
> ```python
>   if not isinstance(io, ExcelFile):
>       io = ExcelFile(io, engine=engine)
>   return io._parse_excel(...)
> ```
> 通过更新的（统一的）参数处理，ExcelFile.parse实际上仅是一条语句：
> `return self._parse_excel(...)`

## pandas写入excel
```python
# Install `XlsxWriter`
!pip install XlsxWriter

import pandas as pd
# Specify a writer
writer = pd.ExcelWriter('example.xlsx', engine='xlsxwriter')

# Write your DataFrame to a file   
# yourData is a dataframe that you are interested in writing as an excel file
yourData.to_excel(writer, 'Sheet1')

# Save the result
writer.save()
```

# 2.openpyxl与excel
openpyxl支持.xlsx，xlsm，xltx，和xltm等格式

## 读取
```python
# Import `load_workbook` module from `openpyxl`
from openpyxl import load_workbook
# Load in the workbook
wb = load_workbook('test.xlsx')
# Get sheet names
print(wb.sheetnames)
```

## 查看activate sheet
```python
# Get a sheet by name
sheet = wb['Sheet1']
# Print the sheet title
print('Sheet Title:',sheet.title)
# Get currently active sheet
anotherSheet = wb.active
# Check `anotherSheet`
print(anotherSheet)
```

## 获取
获取某个单元格的信息
```python
# Retrieve the value of a certain cell
print(sheet['A1'].value)

# Select element 'B3' of your sheet
c = sheet['B3']

# Retrieve the row number of your element
print('Row No.:', c.row)

# Retrieve the column number of your element
print('Column Letter:', c.column)

# Retrieve the coordinates of the cell
print('Coordinates of cell:', c.coordinate)

###################
# 输出
ID
Row No.: 3
Column Letter: 2
Coordinates of cell: B3
```

获取某个位置的单元格
```
print(sheet.cell(row=1, column=2).value)
```

openpyxl有一个`utility`有两个方法`get_column_letter`和`column_index_from_string`。顾名思义，前者返回给定字母的数字/整数，后者返回提供字母的数字作为字符串。
```python
# Import relevant modules from `openpyxl.utils`
from openpyxl.utils import get_column_letter, column_index_from_string
# Return 'A'
print('Column Letter:', get_column_letter(1))
# Return '1'
print('Column Index:', column_index_from_string('A'))

# return
Column Letter: A
Column Index: 1
```

## 切片
```python
for cellObj in sheet['A1':'C3']:
      for cell in cellObj:
              print(cell.coordinate, cell.value)
      print('--- END ---')
```

## sheet属性
```python
# Retrieve the maximum amount of rows
print('Max Rows:', sheet.max_row)

# Retrieve the maximum amount of columns
print('Max Columns:', sheet.max_column)
```

## 与pandas结合使用
可以使用Pandas包中的函数将工作表的值放入DataFrame中，然后使用DataFrame函数来分析和处理数据：
```python
# Import `pandas`
import pandas as pd

# Convert Sheet to DataFrame
df = pd.DataFrame(sheet.values)
```

如果要指定标头和索引，则可以传递标头参数，其中标头和索引的列表为True，但是，由于转换为数据框的工作表已经包含标头，因此不需要添加标头：
```python
from itertools import islice

# Put the sheet values in `data`
data = sheet.values

# Indicate the columns in the sheet values
cols = next(data)[1:]

# Convert your data to a list
data = list(data)

# Read in the data at index 0 for the indices
idx = [r[0] for r in data]

# Slice the data at index 1
data = (islice(r, 1, None) for r in data)

# Make your DataFrame
df = pd.DataFrame(data, index=idx, columns=cols)
print(df)
```

## 写回excel文件
```python
# Import `dataframe_to_rows`
from openpyxl.utils.dataframe import dataframe_to_rows
from openpyxl import *

# Initialize a workbook
wb = Workbook()

# Get the worksheet in the active workbook
ws = wb.active

# Append the rows of the DataFrame to your worksheet
for r in dataframe_to_rows(df, index=True, header=True):
    ws.append(r)
```

# 3.xlrd与excel
## 读取xls和xlxs格式的文件
```python
# Import `xlrd`
import xlrd

# Open a workbook
workbook = xlrd.open_workbook('test.xlsx')

# Loads only current sheets to memory
workbook = xlrd.open_workbook('test.xlsx', on_demand = True)
```

## 获取
```python
# Load a specific sheet by name
worksheet = workbook.sheet_by_name('Sheet1')

# Load a specific sheet by index
worksheet = workbook.sheet_by_index(0)

# Retrieve the value from cell at indices (0,0)
sheet.cell(1, 1).value
print(sheet1.cell(1,0).value)#获取表格里的内容，三种方式

print(sheet1.cell_value(1,0))

print(sheet1.row(1)[0].value)
```

# 4.xlwt与excel
xlwt创建电子表格。
```python
# Import `xlwt`
import xlwt

# Initialize a workbook
book = xlwt.Workbook(encoding="utf-8")

# Add a sheet to the workbook
sheet1 = book.add_sheet("Python Sheet 1")

# Write to the sheet of the workbook
sheet1.write(0, 0, "This is the First Cell of the First Sheet")

# Save the workbook
book.save("spreadsheet.xls")
```

```python
# Initialize a workbook
book = xlwt.Workbook()

# Add a sheet to the workbook
sheet1 = book.add_sheet("Sheet1")

# The data
cols = ["A", "B", "C", "D", "E"]
txt = [0,1,2,3,4]

# Loop over the rows and columns and fill in the values
for num in range(5):
    row = sheet1.row(num)
    for index, col in enumerate(cols):
        value = txt[index] + num
        row.write(index, value)

# Save the result
book.save("test.xls")
```

```python
import xlwt

#设置表格样式
def set_style(name,height,bold=False):
	style = xlwt.XFStyle()
	font = xlwt.Font()
	font.name = name
	font.bold = bold
	font.color_index = 4
	font.height = height
	style.font = font
	return style

#写Excel
def write_excel():
	f = xlwt.Workbook()
	sheet1 = f.add_sheet('学生',cell_overwrite_ok=True)
	row0 = ["姓名","年龄","出生日期","爱好"]
	colum0 = ["张三","李四","恋习Python","小明","小红","无名"]
	#写第一行
	for i in range(0,len(row0)):
		sheet1.write(0,i,row0[i],set_style('Times New Roman',220,True))
	#写第一列
	for i in range(0,len(colum0)):
		sheet1.write(i+1,0,colum0[i],set_style('Times New Roman',220,True))

	sheet1.write(1,3,'2006/12/12')
	sheet1.write_merge(6,6,1,3,'未知')#合并行单元格
	sheet1.write_merge(1,2,3,3,'打游戏')#合并列单元格
	sheet1.write_merge(4,5,3,3,'打篮球')

	f.save('test.xls')

if __name__ == '__main__':
	write_excel()
```

# 5.pyexcel与excel
pyexcel提供用于读取，操作和在写入数据的单个API接口（.csv，.ods，.xls，.xlsx，和.xlsm文件）。使用pyexcel，可以用最少的代码将excel文件中的数据转换为数组或dict格式。

## 数组格式
```python
# Import `pyexcel`
import pyexcel

# Get an array from the data
my_array = pyexcel.get_array(file_name="test.xls")
```
## 字典格式
```python
# Import `OrderedDict` module
from pyexcel._compact import OrderedDict

# Get your data in an ordered dictionary of lists
my_dict = pyexcel.get_dict(file_name="test.xls", name_columns_by_row=0)
```

还可以获得二维数组的字典。简而言之，您可以借助该get_book_dict()功能将所有工作簿表提取到一个字典中。
```python
# Get your data in a dictionary of 2D arrays
book_dict = pyexcel.get_book_dict(file_name="test.xls")
```

## 写入excel文件
```python
# Get the data
data = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Save the array to a file
pyexcel.save_as(array=data, dest_file_name="array_data.xls")


# The data
d2_array_dictionary = {'Sheet 1': [
                                   ['ID', 'AGE', 'SCORE']
                                   [1, 22, 5],
                                   [2, 15, 6],
                                   [3, 28, 9]
                                  ],
                       'Sheet 2': [
                                    ['X', 'Y', 'Z'],
                                    [1, 2, 3],
                                    [4, 5, 6]
                                    [7, 8, 9]
                                  ],
                       'Sheet 3': [
                                    ['M', 'N', 'O', 'P'],
                                    [10, 11, 12, 13],
                                    [14, 15, 16, 17]
                                    [18, 19, 20, 21]
                                   ]}

# Save the data to a file                        
pyexcel.save_book_as(bookdict=d2_array_dictionary, dest_file_name="2d_array_data.xls")
```

# 6. csv库
```python
# import `csv`
import csv

# Read in csv file
for row in csv.reader(open('data.csv'), delimiter=','):
      print(row)

# Write csv file
data = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
outfile = open('data.csv', 'w')
writer = csv.writer(outfile, delimiter=';', quotechar='"')
writer.writerows(data)
outfile.close()
```


# 7. xlsxwriter库
[官方文档](https://xlsxwriter.readthedocs.io/)

xlsxwriter库可以在excel文件中使用excel自带的绘图进行绘制
简单使用：
```python
import xlsxwriter

workbook = xlsxwriter.Workbook('hello.xlsx')
worksheet = workbook.add_worksheet()

worksheet.write('A1', 'Hello world')

workbook.close()
```

- 在XlsxWriter中，行和列都是零索引。工作表中的第一个单元格A1是(0, 0)。

## 设置行高
```python
sheet_handle.set_default_row(30)
sheet_handle.set_row(cur_row, 12)
```

## 设置单元格格式
```python
title_format = workbook_handle.add_format({"font_name": "Arial", "bold": True, "font_size": 20})
sheet_handle.write(cur_row, 0, description_info["title"], title_format)
```

## 添加图表
https://xlsxwriter.readthedocs.io/chart.html
```python
chart = workbook.add_chart({'type': 'column'})
worksheet.insert_chart('A7', chart)
```

**type可用类型**：bar、column、doughnut、line、pie、radar、scatter、stock

### 添加序列
```python
chart.add_series({
    'categories': '=Sheet1!$A$1:$A$5',
    'values':     '=Sheet1!$B$1:$B$5',
    'line':       {'color': 'red'},
})

# Or using a list of values instead of category/value formulas:
#     [sheetname, first_row, first_col, last_row, last_col]
chart.add_series({
    'categories': ['Sheet1', 0, 0, 4, 0],
    'values':     ['Sheet1', 0, 1, 4, 1],
    'line':       {'color': 'red'},
})
```

序列可设置的参数：
- values：这是系列中最重要的属性，并且是每个图表对象的唯一必需选项。此选项将图表与其显示的工作表数据链接起来。可以使用上面第一个示例中所示的公式或使用第二个示例中所示的值列表来设置数据范围。

- categories：这将设置图表类别标签。类别与X轴大致相同。在大多数图表类型中，该categories 属性是可选的，图表仅假设的序列为 1..n。

- name：设置系列的名称。名称显示在编辑栏中。对于非饼图/甜甜圈图，它也会显示在图例中。name属性是可选的，如果未提供，则默认为 。名称也可以是公式，例如或带有工作表名称，行和列的列表，例如。Series 1..n=Sheet1!$A$1['Sheet1', 0, 0]

- line：设置系列线型的属性，例如颜色和宽度。请参阅图表格式：线。

- border：设置系列的边框属性，例如颜色和样式。请参阅图表格式：边框。

- fill：设置系列的实心填充属性，例如颜色。请参阅 图表格式：实心填充。

- - pattern：设置系列的图案填充属性。请参阅 图表格式：模式填充。

- gradient：设置系列的渐变填充属性。请参阅 图表格式：“渐变填充”。

- marker：设置系列标记的属性，例如样式和颜色。请参阅图表系列选项：标记。

- trendline：设置系列趋势线的属性，例如线性，多项式和移动平均线类型。请参阅 图表系列选项：趋势线。

- smooth：设置线系列的平滑属性。

- y_error_bars：设置图表系列的垂直误差范围。请参阅 图表系列选项：误差线。

- x_error_bars：设置图表系列的水平误差范围。请参阅 图表系列选项：误差线。

- data_labels：设置系列的数据标签。请参阅 图表系列选项：数据标签。

- points：设置系列中各个点的属性。请参阅 图表系列选项：点。

- invert_if_negative：将填充颜色反转为负值。通常仅适用于柱状图和条形图。

- overlap：在条形图/柱形图中设置系列之间的重叠。范围是+/-100。默认值为0：

- gap：在条形图/柱形图中设置系列之间的间隔。范围是0到500。默认值是150：

### 设置轴属性
`chart.set_x_axis()`

辅助x轴`chart.set_x2_axis()`

### 合并图表
`chart.combine（）`

### 设置大小

`chart.set_size()`可设置的属性：width、height、x_scale、y_scale、x_offset、y_offset

`offset`也可通过`worksheet.insert_chart('E2', chart, {'x_offset': 25, 'y_offset': 10})`设定

### 设置标题
`chart.set_title({'name': 'Year End Results'})`

### 设置图例
`chart.set_legend({'none': True})`


## notation的转换
```python
from xlsxwriter.utility import xl_rowcol_to_cell

cell = xl_rowcol_to_cell(0, 0)   # A1
cell = xl_rowcol_to_cell(0, 1)   # B1
cell = xl_rowcol_to_cell(1, 0)   # A2

str = xl_rowcol_to_cell(0, 0, col_abs=True)                # $A1
str = xl_rowcol_to_cell(0, 0, row_abs=True)                # A$1
str = xl_rowcol_to_cell(0, 0, row_abs=True, col_abs=True)  # $A$1

column = xl_col_to_name(0, False)  # A
column = xl_col_to_name(0, True)   # $A
column = xl_col_to_name(1, True)   # $B

cell_range = xl_range(0, 0, 9, 0)  # A1:A10
cell_range = xl_range(1, 2, 8, 2)  # C2:C9
cell_range = xl_range(0, 0, 3, 4)  # A1:E4

cell_range = xl_range_abs(0, 0, 9, 0)  # $A$1:$A$10
cell_range = xl_range_abs(1, 2, 8, 2)  # $C$2:$C$9
cell_range = xl_range_abs(0, 0, 3, 4)  # $A$1:$E$4
```