---
title: 将vim配制成顺手的python轻量IDE
toc: true
date: 2019-12-10 16:18:00
tags: [vim]
categories:
- CODE工具
---

原链接：https://www.jianshu.com/p/f0513d18742a
<!--more-->
# 基础配置
vim的配置在`~/vimrc`中完成
一些常见的配置
```
"去掉vi的一致性"
set nocompatible
"显示行号"
set number
" 隐藏滚动条"    
set guioptions-=r 
set guioptions-=L
set guioptions-=b
"隐藏顶部标签栏"
set showtabline=0
"设置字体"
set guifont=Monaco:h13         
syntax on   "开启语法高亮"
let g:solarized_termcolors=256  "solarized主题设置在终端下的设置"
set background=dark     "设置背景色"
colorscheme solarized
set nowrap  "设置不折行"
set fileformat=unix "设置以unix的格式保存文件"
set cindent     "设置C样式的缩进格式"
set tabstop=4   "设置table长度"
set shiftwidth=4        "同上"
set showmatch   "显示匹配的括号"
set scrolloff=5     "距离顶部和底部5行"
set laststatus=2    "命令行为两行"
set fenc=utf-8      "文件编码"
set backspace=2
set mouse=a     "启用鼠标"
set selection=exclusive
set selectmode=mouse,key
set matchtime=5
set ignorecase      "忽略大小写"
set incsearch
set hlsearch        "高亮搜索项"
set noexpandtab     "不允许扩展table"
set whichwrap+=<,>,h,l
set autoread
set cursorline      "突出显示当前行"
set cursorcolumn        "突出显示当前列"
```

# 插件安装
## bundle

1. 下载源码
`git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim`
2. 在vim中添加配置
```shell
filetype off
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin '你的插件'
call vundle#end()
filetype plugin indent on  
```
3. 使用
vundle安装插件方法。现在`.vimrc`中添加plugin命令：
```
“这是安装Github上的插件”
Plugin 'Lokaltog/vim-powerline'
```

# YCM的安装和使用
## YCM的安装

1. `brew install cmake`
2. `cd ~/.vim/bundle`
3. `git clone https://github.com/Valloric/YouCompleteMe.git`
4. `cd ~/.vim/bundle/YouCompleteMe`
5. `git submodule update --init --recursive`
6. `/usr/local/Cellar/python/3.7.5/bin/python3.7 ./install.py --all`

## 使得YCM支持第三方库

    由于vim不支持anaconda中的python，所以决定在系统python 环境中安装第三方库，并使得YCM支持安装的第三方库的自动补全。
1. YCM使用的python解释器路径在`~/.vim/bundle/YouCompleteMe/third_party/ycmd/PYTHON_USED_DURING_BUILDING`下，我这里的文件内容为`/usr/local/opt/python/bin/python3.7`
2. 因为不确定系统的第三方库的安装位置，尝试进行安装：
`sudo /usr/local/opt/python/bin/python3.7 -m pip install numpy`
如果numpy已经安装则会出现提示：
`Requirement already satisfied: python-dateutil>=2.6.1 in /usr/local/lib/python3.7/site-packages (from pandas) (2.8.1)`
所以我们就可以发现第三方库的安装路径为`/usr/local/lib/python3.7/site-packages`

3. 为了支持第三方库，在`vim ~/.vim/bundle/YouCompleteMe/.ycm_extra_conf.py`添加：
```python
def PythonSysPath( **kwargs ):
    .......
    sys_path.insert(1,'/usr/local/lib/python3.7/site-packages') # 值为python安装第三方库的路径
    return sys_path
```

