# Vim 使用整理
> Date： 2016年3月22日21:25

## Vim介绍
> 什么？？ 作为一个程序员，竟然不会vim，好吧，其实也没什么！但是如果你做为一个后端程序员，经常与服务器打交道还不会？那你就太！！！


## Vim 基础及快捷键
> vim 三种操作模式，分别为**编辑模式**、**插入模式**和**命令模式**，当运行vim时首先进入的就是编辑模式。 （完善中。。。）

#### 编辑模式
> 编辑模式主要用途是在被编辑的文件中移动光标的位置。可以进入复制、剪切和粘贴，删除和插入等等一系列操作。
	
1. 光标快速跳转
2. 翻页

#### 插入模式

#### 命令模式

## 准备工作
> 1. 首先进入用户家目录，即/home/AAA/ 
> 2. 查看是否有.vim文件夹，如果没有需要执行<code>mkdir .vim</code>新建目录
> 3. vim 配置文件名称<code>.vimrc</code>

## Vim 常用配置及配置说明
> vim 常用配置介绍

<pre><code>
" Vim 基础配置
set nocompatible " 关闭 vi 兼容模式
syntax on " 自动语法高亮
"colorscheme torte " 设定配色方案
set number " 显示行号
set cursorline " 突出显示当前行
set ruler " 打开状态栏标尺
set shiftwidth=4 " 设定 << 和 >> 命令移动时的宽度为 4
set softtabstop=4 " 使得按退格键时可以一次删掉 4 个空格
set tabstop=4 " 设定 tab 长度为 4
set nobackup " 覆盖文件时不备份
set autochdir " 自动切换当前目录为当前文件所在的目录
filetype plugin indent on " 开启插件
set backupcopy=yes " 设置备份时的行为为覆盖
set ignorecase smartcase " 搜索时忽略大小写，但在有一个或以上大写字母时仍保持对大小写敏感
set nowrapscan " 禁止在搜索到文件两端时重新搜索
set incsearch " 输入搜索内容时就显示搜索结果
set hlsearch " 搜索时高亮显示被找到的文本
set noerrorbells " 关闭错误信息响铃
set novisualbell " 关闭使用可视响铃代替呼叫
set t_vb= " 置空错误铃声的终端代码
" set showmatch " 插入括号时，短暂地跳转到匹配的对应括号
" set matchtime=2 " 短暂跳转到匹配括号的时间
set magic " 设置魔术
set hidden " 允许在有未保存的修改时切换缓冲区，此时的修改由 vim 负责保存
set guioptions-=T " 隐藏工具栏
set guioptions-=m " 隐藏菜单栏
set smartindent " 开启新行时使用智能自动缩进
set backspace=indent,eol,start
</code></pre>

## Vim 常用插件
> vim 插件管理工具及常用插件 （完善中。。。）

**NERDTree**

**设置powerline状态栏**

**Tag List**

**php-doc**

**Bundle插件管理**

**Vundle管理**




## Vim日常小技巧
> 待完善

## 问题说明 
> 问题1： Linux shell切换成zsh后，新建<code>.vimrc</code>后， 设置<code>set nu</code> 后，行号与终端屏幕左侧距离很大？

> 解决方法：试了很多如<code>set nuw=1   set fdc=1</code>均不适用，无奈只好切换成<code>/bin/sh</code>模式，重新新建<code>.vimrc</code>，查看无误后，再次切换回zsh，好吧！问题解决了！

	
