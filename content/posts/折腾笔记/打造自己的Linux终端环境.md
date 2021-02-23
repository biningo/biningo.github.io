---
title: 打造自己的终端环境
date: 2021-02-04
categories: [折腾笔记]
tags: [linux,安装,折腾,zsh,tmux,vim]
---

## 配置zsh

1. 安装`zsh`

```bash
apt install zsh
```

2. 安装`oh-my-zsh`

```bash
#https://github.com/ohmyzsh/ohmyzsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

运行上面的sh之后，就会在家目录下配置`.zshrc`文件，当然也可以手动`clone`仓库代码到，然后按照`oh-my-zsh`模板文件进行配置`.zshrc`

3. 配置`.zshrc`

该文件的作用和`.bashrc`作用一样，为了不让之前`.bashrc`的命令失效，可以在`.zshrc`中`source`进来，下面展示主要配置

```bash
#配置终端主题，不然会很丑 主题路径在.oh-my-zsh/themes 可以修改主题的样式
ZSH_THEME="robbyrussell" 

#配置zsh插件 有些系统自带的插件则直接配置即可
#有些外部插件则需要下载(clone)到 .oh-my-zsh/custom/plugins路径下  然后需要在下面写上名字即可
plugins=(
    git
    zsh-syntax-highlighting #shell命令高亮插件
    colored-man-pages #系统自带
    zsh-autosuggestions #命令提示插件
 )
```

​    

## 配置tmux

1. **安装tmux**

```bash
apt install tmux
```

2. **修改配置文件**

`tmux`的配置文件在`~/.tmux.conf`

个人觉得tmux默认的快捷键不符合自己，那么可以修改，下面展示我的配置文件

```bash
#设置快捷键前缀
set -g prefix C-a #修改快捷键前缀 我习惯与ctrl+a
unbind C-b # 解绑默认的 ctrl+b 前缀快捷键
bind C-a send-prefix # 绑定Ctrl+a为新的指令前缀
 
#绑定方向键
bind -r k select-pane -U # 绑定k为↑
bind -r j select-pane -D # 绑定j为↓
bind -r h select-pane -L # 绑定h为←
bind -r l select-pane -R # 绑定l为→
 
#切换切割pane按键 分割窗口要用到
unbind '"' #解绑默认的键
# 垂直方向新增面板，默认进入当前目录 
bind ] splitw -v -c '#{pane_current_path}' 
unbind %
# 水平方向新增面板，默认进入当前目录
bind [ splitw -h -c '#{pane_current_path}' 
```

tmux的基本命令这里分享一个链接，可以速查一些相关的命令: https://gist.github.com/ryerh/14b7c24dfd623ef8edc7

​    

## 配置vim

1. 安装vim8

```
apt install vim
```

2. **安装插件管理器**`vim-plug`

vim插件管理器比较出名的有两个：**vim-plug、Vundle**，后者比较老了，`vim-plug`则比较轻量级，并且可以**按需加载**插件，可以加快vim启动的时间，所以我选择`vim-plug`来管理的我vim插件

首先安装`vim-plug`

```bash
cd ~/.vim/autoload/
wget https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

然后需要在`.vimrc`中配置，下面只展示插件部分（参考了https://vimjc.com/vim-plug.html）：

```bash
call plug#begin('~/.vim/plugged') #指定插件下载路径
Plug 'junegunn/vim-easy-align' #https://github.com/junegunn/vim-easy-align
Plug 'https://github.com/junegunn/vim-github-dashboard.git' #也可以指定全名
Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets' #用 | 将两个vim插件写在同一行配置中
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' } #使用 按需加载，表明只有在 NERDTreeToggle 命令被调用时, 对应的插件才会被加载
Plug 'tpope/vim-fireplace', { 'for': 'clojure' } #使用 按需加载，表明只有编辑 clojure 类型的文件时该插件才会被打开
Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' } #显式指定使用YCM-Generator插件的 stable 分支
Plug 'nsf/gocode', { 'tag': 'v.20150303', 'rtp': 'vim' } #指定插件所用git标签，rtp描述了包含vim插件的子目录
Plug '~/my-prototype-plugin' #指定了本地插件的路径
call plug#end()
```

`vim-plug`的主要命令有：

```bash
PlugInstall #安装插件
PlugUpdate #安装或更新vim插件
PlugClean #卸装插件 只需要将配置删除 然后执行此命令即可删除插件 手动删除也可以
PlugUpgrade	#更新vim-plug自身
PlugStatus	#查看vim插件的状态
PlugDiff	#对比上次PlugUpdate的更改
PlugSnapshot #
```

3. `.vimrc`配置文件

下面展示一下我的`.vim`配置文件：

```bash
set number #设置行号
set nowrap
set nobackup
set cursorcolumn
hi CursorColumn cterm=NONE ctermbg=240 ctermfg=green guibg=NONE guifg=NONE
#-------tab键长度修改为2个空格------
set ts=2 
set sw=2 
#----------------
set relativenumber #显示相对行号
set hlsearch
set noerrorbells
set autoindent 
 
inoremap ' ''<ESC>i
inoremap " ""<ESC>i
inoremap ( ()<ESC>i
inoremap [ []<ESC>i
inoremap { {<CR>}<ESC>O
 
 
call plug#begin('~/.vim/plugged')
 
Plug '~/.vim/plugged/YouCompleteMe'
 call plug#end()

```

​    

## 参考

- https://vimjc.com/vim-plug.html