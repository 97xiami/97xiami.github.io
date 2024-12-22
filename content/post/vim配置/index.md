---
title: vim配置
slug: vim配置
date: 2024-12-22 15:57:00+0800
categories:
    - 教程
tags:
    - vim配置
---

```.vimrc
"语法检测
syntax enable
"历史容量
set history=100
"检测文件类型
filetype on
"不同文件类型不同缩进格式
filetype plugin indent on
"自动载入
set autoread
"启动不显示援助
set shortmess+=c
"取消备份
set nobackup
"关闭交换文件
set noswapfile
"突出显示当前列
set cursorcolumn
"突出显示当前行
set cursorline
"启用鼠标
set mouse=a
"显示行列号
set ruler
"显示正在输入的命令
set showcmd
"显示当前模式
set showmode
"光标距顶/底部行数
set scrolloff=3
"显示行号
set number
"自动换行
set wrap
"符号换行
set linebreak
"高亮搜索文本
set hlsearch
"实时搜索
set incsearch
"忽略大小写
set ignorecase
"一个大写
set smartcase
"智能缩进
set smartindent
"自动缩进
set autoindent
"tab宽度
set tabstop=2
"tab转为空格
set expandtab
"缩进宽度
set shiftwidth=2
"文件编码
set encoding=utf-8
"启用256色
set termguicolors
"显示状态栏
set laststatus=2
"括号配对
set showmatch
"保留撤销历史
set undofile
"撤销历史文件位置
set undodir=/home
"命令补全
set wildmenu
"显示所有命令，再次tab切换
set wildmode=longest:full,full
"不兼容vi
set nocompatible
"智能补全
set completeopt=longest,menu
"backspace键无法删除
set backspace=indent,eol,start
```
