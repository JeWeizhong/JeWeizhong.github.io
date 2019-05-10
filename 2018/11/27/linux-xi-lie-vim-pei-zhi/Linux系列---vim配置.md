---
title: vimrc文件简单配置
categories: 
  - Linux
tags:
  - vim
  - Linux
date: 2018-11-27
---

```vim
"关闭vi兼容模式
set nocompatible
"设置字符编码
set fileencoding=utf-8
set fileencodings=utf-8,gb2312,gb18030,latin1
set termencoding=utf-8
set encoding=utf-8

"set mouse=a"启用鼠标
" 语法高亮
syntax on

" 检测文件类型
" filetype on
"根据文件类型加载对应的插件, 未安装插件
" filetype plugin on
" filetype indent on " 开启文件类型检查，并且载入与该类型对应的缩进规则,未配置

set ruler " 打开状态栏标尺
set smarttab " 使得按退格键时可以一次删掉4个空格

" 显示行号
set number
" 在第99列显示竖线
set cc=99
" 高亮显示当前行
set cursorline
set showmode " 在底部显示，当前处于命令模式还是插入模式

" 设置各种缩进
set tabstop=4
set softtabstop=4
set shiftwidth=4 " 设定<<和>>命令移动时的宽度为4
set autoindent " 按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致
set smartindent
set cindent
set smartindent " 开启新行时使用智能自动缩进

" 如果行尾有多余的空格（包括Tab键），该配置将让这些空格显示成可见的小方块。
" set listchars=tab:»■,trail:■
" set list

set hlsearch " 搜索时高亮显示被找到的文本
set noerrorbells " 关闭错误信息响铃
set novisualbell " 关闭使用可视响铃代替呼叫
set smartindent " 开启新行时使用智能自动缩进


" Python文件的一般设置，比如不要tab等
autocmd FileType python set tabstop=4 shiftwidth=4 expandtab
autocmd FileType python map <F12> :!python %<CR>
" set expandtab " tab 转换为空格
" set softtabstop=2 " Tab转为多少个空格

```