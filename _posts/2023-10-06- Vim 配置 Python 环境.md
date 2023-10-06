---
title: Vim 配置 Python 环境
categories: [note]
comments: true
---

# Vim 配置 Python 环境

## 目的

这篇博客是为了记录如何在 Ubuntu 下给 Vim 配置 Python 环境，让使用 Vim 编写 Python 代码更高效。

通过安装相应插件后，实现了以下功能：

-   补全 Python 代码：`Vim-EasyComplete`；
-   格式化 Python 代码：`vim-autoformat`；
-   以不同的颜色展示不同层次的括号：`Rainbow Parentheses Improved`。

## 安装插件管理器

在安装上述 **插件** 前需要先安装 **插件管理器**，然后通过 **插件管理器** 安装 **插件**。

需要安装的 **插件管理器** 有两个：

-   `vim-plug`；
-   `Vundle`。

### 安装 vim-plug

1.   下载 `plug.vim` 并放入 `autoload` 文件夹内；
2.   在 `~/.vimrc` 中进行相关配置。

由于国内访问 `vim-plug Github` 仓库过慢，所以这里使用 `Gitee` 镜像下载 `plug.vim`。

-   `vim-plug Github` 仓库：`https://github.com/junegunn/vim-plug`；
-   `vim-plug Gitee` 仓库：`https://gitee.com/lxyoucan/vim-plug`。

**步骤1**：

```shell
# 使用 git 本地下载 vim-plug 仓库
git clone https://gitee.com/lxyoucan/vim-plug.git

# 进入下载好的 vim-plug 仓库
cd vim-plug/

# 创建vim插件目录
mkdir -p ~/.vim/autoload

# 将 plug.vim 放置目标文件夹：autoload
cp plug.vim ~/.vim/autoload/

```

**步骤2** 将放到 **插件安装** 部分一并操作。

### 安装 Vundle

安装 `Vundle` 要比安装 `vim-plug` 简单的多。

可以选用以下任意两条命令进行安装：

```
# 命令 1
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

# 命令 2
git clone https://gitee.com/mirrors/vundle-vim.git ~/.vim/bundle/Vundle.vim

```

考虑到国内访问 `Github` 的难处，此处更建议使用第 2 条命令。第二条命令同样是采用的 `Gitee` 镜像用来替代访问 `Github` 仓库。

-   `Vundle Github` 仓库：`https://github.com/VundleVim/Vundle.vim`；
-   `Vundle Gitee` 仓库：https://gitee.com/mirrors/vundle-vim。

## 安装插件及相关配置

**插件** 通过 **插件管理器** 安装。

### 配置 vim-plug 及安装 Vim-EasyComplete

```shell
# 创建并打开 vim 用户配置文件
vim ~/.vimrc

```

```Zimbu
" 将下列内容写入 vimrc 后保存并退出
" --------------------------------
call plug#begin('~/.vim/plugged')
Plug 'jayli/vim-easycomplete'
Plug 'SirVer/ultisnips'
call plug#end()
" --------------------------------

```

```Zimbu
" 再次打开 vimrc，并在 vim 中输入下列命令。
" 由于国内访问 Github 的问题，所以该命令有很大的可能会失败。
" 目前能想到的解决办法就是多试几次，直到该命令成功运行。
:PlugInstall

" 上述命令执行成功后，将下列内容写入 vimrc

" ----------------------------------------
" 用于去除 easycomplete 的 LSP Warning
let g:easycomplete_diagnostics_enable = 0
let g:easycomplete_lsp_checking = 0
" ----------------------------------------

" 在 vim 中输入下列命令配置 Python
" 执行该命令时可能因为缺少依赖而报错，
" 根据提示使用 sudo apt-get 补全缺失的依赖。
:InstallLspServer py

```

### 配置 Vundle 及安装 vim-autoformat 和 Rainbow Parentheses Improved

```
" 将下列内容写入 vimrc

" --------------------------------------------------------------------------------
set nocompatible              " 去除VI一致性,必须
filetype off                  " 必须

" 设置包括vundle和初始化相关的runtime path
set rtp+=~/.vim/bundle/Vundle.vim

call vundle#begin()
" 另一种选择, 指定一个vundle安装插件的路径
"call vundle#begin('~/some/path/here')

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

" 安装 vim-autoformat
Plugin 'vim-autoformat/vim-autoformat'

" 安装 Rainbow Parentheses Improved
Bundle 'luochen1990/rainbow'
let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle

" 你的所有插件需要在下面这行之前
call vundle#end()            " 必须
filetype plugin indent on    " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
" --------------------------------------------------------------------------------

" 运行下列命令完成配置和安装插件
" 此处注意该命令与上一命令 :PlugInstall 的区别！
:PluginInstall
```

```shell
# 执行下列命令用于配置 vim-autoformat
sudo apt-get install python3-autopep8
```

## 参考资料

---

1.   终端文本编辑神器--Vim命令详解。如何配置Vim以及Vim插件？ - 雨月空间站[EB/OL]. [2023-10-06]. [https://www.mintimate.cn/2021/08/25/vim/#Vim%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6](https://www.mintimate.cn/2021/08/25/vim/#Vim%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6).
2.   lxyoucan/vim-plug[EB/OL]//Gitee. [2023-10-06]. [https://gitee.com/lxyoucan/vim-plug](https://gitee.com/lxyoucan/vim-plug).
3.   CHOI J. junegunn/vim-plug[CP/OL]. (2023-10-06)[2023-10-06]. [https://github.com/junegunn/vim-plug](https://github.com/junegunn/vim-plug).
4.   VundleVim/Vundle.vim[CP/OL]. VundleVim, 2023[2023-10-06]. [https://github.com/VundleVim/Vundle.vim](https://github.com/VundleVim/Vundle.vim).
5.   Gitee 极速下载/Vundle[EB/OL]//Gitee. [2023-10-06]. [https://gitee.com/mirrors/Vundle](https://gitee.com/mirrors/Vundle).
6.   CHEN L. Rainbow Parentheses Improved[CP/OL]. (2023-10-04)[2023-10-06]. [https://github.com/luochen1990/rainbow](https://github.com/luochen1990/rainbow).
7.   vim-plug-mirror/rainbow[EB/OL]//Gitee. [2023-10-06]. [https://gitee.com/vim-plug-mirror/rainbow](https://gitee.com/vim-plug-mirror/rainbow).
8.   jayli/vim-easycomplete: 杭州市余杭区最好用的 VIM/NVIM 代码补全插件[EB/OL]. [2023-10-06]. [https://github.com/jayli/vim-easycomplete](https://github.com/jayli/vim-easycomplete).
9.   vim-autoformat[CP/OL]. vim-autoformat, 2023[2023-10-06]. [https://github.com/vim-autoformat/vim-autoformat](https://github.com/vim-autoformat/vim-autoformat).
10.   Linux Vim代码格式化/美化插件vim-autoformat安装 - 刘昭廷 - 博客园[EB/OL]. [2023-10-06]. [https://www.cnblogs.com/liuzhaoting/articles/13794745.html](https://www.cnblogs.com/liuzhaoting/articles/13794745.html).

