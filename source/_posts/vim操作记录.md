---
title: vim操作记录
toc: true
date: 2019-12-09 10:26:22
tags: [vim]
categories: 
- CODE工具
---

通过命令行输入`vimtutor`打开教程

<!--more-->

## 命令解释
```
  Many commands that change text are made from an operator and a motion.
  The format for a delete command with the  d  delete operator is as follows:

        d   motion

  Where:
    d      - is the delete operator.
    motion - is what the operator will operate on (listed below).

  A short list of motions:
    w - until the start of the next word, EXCLUDING its first character.
    e - to the end of the current word, INCLUDING the last character.
    $ - to the end of the line, INCLUDING the last character.

  Thus typing  de  will delete from the cursor to the end of the word.

                     Lesson 2.4: USING A COUNT FOR A MOTION


   ** Typing a number before a motion repeats it that many times. **

  1. Move the cursor to the start of the line marked ---> below.

  2. Type  2w  to move the cursor two words forward.

  3. Type  3e  to move the cursor to the end of the third word forward.

  4. Type  0  (zero) to move to the start of the line.
```

## 光标移动
             ^
             k              Hint:  The h key is at the left and moves left.
       < h       l >               The l key is at the right and moves right.
             j                     The j key looks like a down arrow.
             v

## 退出
```
<ESC>
:q   # 退出
:wq  # 退出并保存
```

## 删除
```
x # 删除单个字符
dw # 删除从当前字母开始到结束的单词
d$ # 删除从当前字母到行末的内容
ndd  # 删除n行
```

## 插入
```
a # 在后面添加
i # 在前面插入
p # 粘贴
```

## 撤销
```
u # 撤销一步
U # 返回当前行的初始状态
ctrl+R #撤销undo
```

## 光标位置
```
nG # 移动到某一行
G # 移动到末尾
gg # 移动到开头
Ctrl-G 显示当前位置
```

## 查找
```
/word 查找word
查找后按n可以继续查找下一个，N向相反的方向查找
在相反方向查找使用？代替/
CTRL-o  返回到上一个位置
CTRL-i  进入到下一个位置

光标指向（，[,{按下%可以跳转到),],}位置

替换
:s/old/new <ENTER> # 替换第一个old出现的位置
:s/old/new/g  # 替换当前行global出现的位置
:#,#s/old/new/g  # 替换#行和#行之间的old
:%s/old/new/g  #替换当前文件所有出现的old
:%s/old/new/gc #查找所有出现的位置，不确定是否替换c confirmation
```

## 执行外部命令
```
:! COMMAND 可以执行命令
```

## 选择行
```
按住v 进行选择
选择行后，按下:, 然后 w <FILENAME> 保存选中的部分
```

## 插入文件
```
:r FILENAME 将文件内容插入到下方
:r !ls 将ls执行结果插入到文件下方 
```

## 插入
```
o 在当前行下方新建行，进入插入模式
O 在当前行下方新建行，进入插入模式
a 在后方插入
```

## 复制粘贴
```
y 复制yank
p 粘贴paste
```

## 查找设置
```
:set ic  # ignore case
:set noic # 关闭
:set hlsearch # 高亮
```

## 多行缩紧
```
方法1: v 进入视图模式后，选择多行，然后按>
方法2: 输入:21,324>  将21到324行进行缩进
```

## 复制文件中的某些行
```
:5,10 w>> filename   将本文件中的某些行复制到filename中
:5,10 w! filename  将文件中的某些行复制到filename中，覆盖原来的内容

"+100yy   复制100行到系统的剪切板（+寄存器）
```

## 替换`^M`
```
vim下 :%s/^M//g 或者 :1,$s/^M//g 均可
补充一点：
^M是使用 "CTRL-V CTRL-M" 而不是字面上的 ^M
```

## 修改括号中的内容
```
ci" 修改引号中的内容
di" 删除引号中的内容
```
