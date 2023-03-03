---
title: Neovim + Ubuntu快速搭建教程
date: 2021-04-30 20:00:00
tags: [ubuntu,neovim]
categories: 干货
---


# Neovim + Ubuntu快速搭建教程

先说下目的吧，因为自己经常需要在GPU服务器上跑DL模型，大多数时候是通过本地debug之后的代码上传到服务器来跑。这样最大的问题就是效率太低，每次出错都要把代码拉下来修改。其次呢，因为是通过终端来操作大部分使用的是vim编辑器。说实话，写了这么多年的代码了vim用的是相当的不熟练。所以想学一学vim并且想要找到一个好的插件，能够满足我的一下需求：
- [x] 方便文件的操作（增删改查）
- [x] 支持python自动补全
- [ ] 可以debug
- [x] 以上操作均是通过终端实现

正好在b站看到大佬TheCW出得Neovim教程发现vim效率确实高，于是花了几天自己动手搭建了一次。中间多多少少遇到了一点坑，总结一下为了以后配置更快点吧。

<!--more-->

## Installation
- [ ] Ubuntu 18.04 LST
- [ ] Neovim 0.5.0+

### 安装Neovim
这里遇到了一点坑，就是一开始使用命令
```
sudo apt-get install neovim
```
无奈可能是apt版本太低，导致安装的Neovim版本为0.2.x。GooGle到一篇文章推荐使用ppa安装，命令如下：
```
sudo add-apt-repository ppa:neovim-ppa/unstable
sudo apt update
sudo apt install -y neovim
```
第一行指定为unstable可下载最新的版本.

### 安装Nodejs
这里是踩的第二个坑，由于需要配置许多插件，其中coc.nvim在启动nvim时老弹出错误，搞了半天才发现是Nodejs版本太低。
需要12.12.x以上，但是我这台服务器已经装了6.x的版本，一开始在配环境变量上耽误了许多时间，但还是没搞定。最后采用了另外一种方法，通过nvm安装。这里介绍一下，nvm是针对nodejs的专门软件，可以很方便的安装卸载各个版本。这里的命令如下：
```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```
其中 v0.38.0为nvm的版本，可通过这里查看[Lastest nvm version](https://github.com/nvm-sh/nvm/releases)

安装好nvm之后可安装nodejs了，这里安装的版本为v14.17.3
```
nvm install <version-number>
nvm use <version-number>
```

### 下载vim-plug插件
github 自己搜吧

### 设置nvim的配置文件+插件
与vim不同的是，nvim的配置文件在～/.config/nvim/init.vim，vim在～/.vim/.vimrc
首先，进入.config文件夹并clone我放在GitHub上的配置文件
```
cd ~/.config/
git clone https://github.com/wudi1183456665/nvim.git
cd nvim
mkdir plugged tmp
```
### 启动nvim及安装插件
首次启动时可通过终端输入nvim启动，强迫症的我习惯了之前的vim启动，这里可以修改zsh的启动文件
打开
vim ~/.zshrc

添加
alias vim='nvim'

插件的安装也很容易，主要就是网速太慢
进入nvim，输入:PlugInstall 应该会自动安装，安装完成后就可以愉快的使用了。



