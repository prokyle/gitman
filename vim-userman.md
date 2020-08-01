```
"set nocompatible             "去掉讨厌的有关vi一致性模式，避免以前版本的一些bug和局限，在/etc/vimrc里已经定义，这里没有必要
"filetype plugin indent on    "打开文件类型检测功能，在/etc/vimrc里已经定义，这里没有必要

" 以下是关于文件编码的设置
set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1

"hi comment ctermfg=blue      "注释改为浅蓝色
set bg=dark                   "所有背景变成更淡

if has("syntax")              "设置语法高亮
    syntax on
endif

set softtabstop=4             "如果softtabstop=5,tabstop=4,则tab就是一个制表符加一个空格
set tabstop=4                 "tab更换为4个空格
set shiftwidth=0              "表示每一级缩进的长度为4个空格，一般设置成跟 softtabstop 一样的值。set expandtab 时，缩进用空格来表示，而set noexpandtab 则是用制表符表示一个缩进。
"set expandtab                "在Vim设置了 expandtab 后，如果在某些情况下需要输入真正的 Tab ，可以使用组合按键 Ctrl-V<Tab>
if has("autocmd")
    "不应让set expandtab对所有情况都生效，因为Makefile或makefile文件中要求必须使用制表符来分割命令，而如果使用了vim编辑这些文件，制表符被自动换成空格，>则会出现问题。
    autocmd BufRead,BufNewFile *.c, *.cpp, *.h, *.hpp, *.java, *.py set expandtab
endif
set autoindent                "自动缩进，每行的缩进值与上一行相等，使用set noautoindent取消设置。在输入状态用回车键插入一个新行，或者在normal状态用o或者O 插入一个新行时，autoindent 会自动地将当前行的缩进拷贝到新行，也就是"自动对齐”
set cindent                   "按照C 语言的语法，自动地调整缩进的长度，比如，当你输入了半条语句然后回车时，缩进会自动增加一个 TABSTOP 值，当你键入了一个右花括号时，会自动减少一个TABSTOP 值。
set smartindent               "这种缩进模式中，每一行都和前一行有相同的缩进量，同时这种缩进形式能正确的识别出花括号，当遇到右花括号（}），则取消缩进形式。此外还增加了识别C语言关>键字的功能。如果一行是以#开头的，那么这种格式将会被特殊对待而不采用缩进格式。

autocmd InsertEnter * se cul    " 当使用插入编辑时用一条高亮下划线高亮当前行
autocmd InsertLeave * se nocul  " 离开时取消高亮下划线

set showmatch                 " set sm，当你输入右括号，如}])，会自动跳到相匹配的括号处，闪动一下，不影响你继续输入，时间也可以自己设置，如下：
set matchtime=1               "匹配时间，单位是十分之一秒）

set autoread                  "当文件在外部被修改时，自动重新读取

"vim的filttype会把*.py识别为python而不是py，这会其它类型文件“后缀“与”类型”的匹配方式不一致
"autocmd BufNewFile,BufRead *.py set filetype=py
"不同模式之间不可以存在空格，“.[ch], *.cpp, *.java”就会产生错误，必须要写成“*.[ch],*.cpp,*.java”
autocmd BufNewFile *.[ch],*.cpp,*.java,*.py,*.sh exec ":call SetTitle()"
"execute "cmd" 把一个字符串当作Vimscript命令执行

"自定义函数SetTitle()
func SetTitle()
    "vim中表示当前目录和当前文件名的方法
    "%   当前完整的文件名
    "%:h 文件名的头部，即文件目录.例如../path/test.c就会为../path
    "%:t 文件名的尾部.例如../path/test.c就会为test.c
    "%:r 无扩展名的文件名.例如../path/test就会成为test
    "%:e 扩展名
    if &filetype == 'sh' || &filetype == 'python'
        "call setline(1,"###################################")
        "call append(line("."), "# File Name: ".expand("%:t"))
        "call append(line(".")+1, "# Author: prolee")
        "call append(line(".")+2, "# mail: prolee@139.com")
        "call append(line(".")+3, "# Created Time: ".strftime("%c"))
        "call append(line(".")+4, ############################")
        "call append(line(".")+5, "")

        call setline(1,"\# " .expand("%:t") ." Power by Prolee ".strftime("%Y-%m-%d %T", localtime()))
        call append(line("."), "")
    else
        call setline(1, "/*************************")
        call append(line("."), "    > File Name: ".expand("%:t"))
        call append(line(".")+1, "    > Author: prolee")
        call append(line(".")+2, "    > Mail: prolee@139.com ")
        call append(line(".")+3, "    > Created Time: ".strftime("%c"))
        call append(line(".")+4, " ************************/")
        call append(line(".")+5, "")
    endif

    if &filetype == 'cpp'
        call append(line(".")+6, "#include<iostream>")
        call append(line(".")+7, "using namespace std;")
        call append(line(".")+8, "")
    endif

    if &filetype == 'c'
        call append(line(".")+6, "#include<stdio.h>")
        call append(line(".")+7, "")
    endif

    if &filetype == 'sh'
        call append(line(".")+1, "\#!/bin/bash")
        call append(line(".")+2, "")
    endif

    if &filetype == 'python'
        call append(line(".")+1, "\#-*- coding:utf-8 -*-") "“#”号前一定要加“\”转义
        call append(line(".")+2, "")v
    endif

    " normal命令简单地接受一串键值并当作是在normal模式下输入的。
    " 执行下面的命令来映射G键到别的东西：nnoremap G dd，normal G将删除当前行。
    " normal! G 移动光标到文件底部，即使G已经被映射了。
    " 写Vim脚本时，你应该总是使用normal!，永不使用normal。不要信任用户在~/.vimrc中的映射。
    normal G "相当于在normal模式下按G，光标移到当前文件的最后一行
    normal o

endfunc
```





