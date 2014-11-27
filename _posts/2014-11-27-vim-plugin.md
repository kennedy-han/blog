---
layout: post
title: "Vim插件"
description: "vim plugin"
category: Vim
tags: [Vim]
---
{% include JB/setup %}

###折腾vim的总结
从这个项目[maximum-awesome](https://github.com/square/maximum-awesome)派生，项目中使用了很多实用插件，待研究

使用Vundle管理Vim插件很方便

安装后：

* .vimrc.local是本地文件可以用户自定义配置

我的自定义配置

```
set t_Co=256	"Vim使用256色
set tags=/usr/include/tags "ctags插件的索引文件
set laststatus=2	"airline插件设置statusLine的位置
let g:airline_theme='dark'	"airline的主题
```


* .vimrc.bundles.local是本地Bundle插件的管理文件

添加插件时修改.vimrc.bundles.local 添加一行插件名，以下附加我的文件

```
Bundle 'vim-scripts/a.vim'
Bundle 'bling/vim-airline'
```
之后进入vim，使用:BundleInstall来安装插件

同理，删除插件需要在.vimrc.bundles.local删除对应记录，再打开vim执行:BundleClean

####尽量不要修改.vimrc和.vimrc.bundles文件，因为修改后每次更新git pull时会产生冲突，这也就是作者提供.vimrc.local文件的便利（.vimrc会在末尾处source .vimrc.local）

------

a.vim 方便在头文件之间切换 使用`:A`

airline 是修改了statusLine的颜色，美化Vim用

ctags插件方便查看库函数方法，使用`ctrl+]`进入，`ctrl+t`返回

ctrlp插件用于根据文件名查找文件（以SCM为根目录，会自动找.git的文件夹确定顶级目录），需要在一般模式下使用`ctrl+p`（注：插入模式下ctrl+p是补全）

nerdTree 为目录视图，clone项目安装后，使用快捷键`,d`开关

------
###Vim无插件技巧
:ls		查询缓冲区Buffer中的文件

:Buffer 数字	打开对应的文件

gf	找到变量定义的地方