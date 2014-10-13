---
layout: post
title: "Ubuntu14.04安装笔记"
description: "linux"
category: linux
tags: [linux] 
---


### Ubuntu14.04安装笔记
-----
    最近在实验室电脑上装上了xp与ubuntu双系统，折腾了两天，终于把vim什么的都配置好了，中间遇到了不少问题，就记录下来分享和备忘：
---
主要遇到的有：
> xp硬盘安装ubuntu的注意事项
>有关Grub引导的几个问题
>配置vim中插件YouCompleteme中遇到的几个问题，编译libclang

---
####xp硬盘安装ubuntu

这部分网上教程比较多，主要就是在c盘装上grub4dos进行引导，最终进入ubuntu的liveCD界面进行安装。
具体的教程可以看[这里](http://blog.csdn.net/ylchou/article/details/7525435)

根据这个教程，我倒是成功的安装上了ubuntun14.04,但是比较悲剧的是无法自动引导进入xp系统了，只能自己做了个u盘引导，才能进入xp，实在悲催，找了半天也没有找到解决方法。
不过又重新复习了很多计算机操作系统的知识，做个简单记录：
- MBR(Master Boot Record)主要启动记录区：用来安装开机管理程序的地方 占有前446byte，后64byte用来存储硬盘分割记录。



开机启动流程：
>1 BIOS 进行自检程序。
>2 没有故障后，转交给MBR中第一个扇区的开机管理程序
>3 boot loader 可以读取核心档案(系统)执行的软件
 >> boot loader 提供以下项目
  * 提供选项（进入不同的系统，也是多重引导的主要内容）
  * 载入系统
  * 转交其他loader
 

我的电脑现在就是无法在进入第3步要读入windows系统时，无法启动，不知是什么原因，最近比较忙，先凑活用，等到后期了解了后在深入研究。

这里主要参考了
[1] [wiki](http://zh.wikipedia.org/wiki/%E4%B8%BB%E5%BC%95%E5%AF%BC%E8%AE%B0%E5%BD%95)
[2]鸟哥的私房菜
后续有时间了会继续研究研究这个~

-------
#### vim插件安装 之vundle（插件管理工具）与代码补全工具YouCompleteMe
vim是linux上鼎鼎大名的书写工具，为了更好的适应linux系统，肯定善用vim，为了能更好的利用vim工具，按照教程装上了一些vim插件，历经忐忑，也在此记录：
首先先记下几个vim练习的网站，没事的时候试试：
[简明vim练级攻略](http://coolshell.cn/articles/5426.html)
[vim大冒险](http://vim-adventures.com/)

-----
首先解释下 .vimrc 这个就是vim配置文件，在系统的根目录里有个总配置，这个文件保存着全局的配置，当然轻易的去改这个文件是极不好的，因此我们可以根据自己的需要在自己的目录下修改。.vimrc，这样不容易出问题。

- vim插件管理器之vundle
这个插件相当好用，简而言之就是一款插件管理器，省去了自己的大量的操作，还可以及时的备份自己的vimrc，总是相当好用，安装过程也及其简单
这个插件的地址[GitHub](https://github.com/gmarik/Vundle.vim)
根据其readme进行安装，就不会有错啦，非常简单。
- 代码补全插件 YouCompleteMe
在这之前试了很多补全插件，感觉都是在瞎猜，这个插件非常厉害，支持的特性很多，就是安装的时候很蛋疼；当然相应的安装教程非常多，对于64位的ubuntu应当是比较容易安装，因为这个插件需要libclang，但是clang的官网只给出了64位的预编译版，因此对于32位的系统就要自己编译了……

首先这个网上教程比较多，我就把我参考的列出来，然后记下几个要注意的地方
[教程](http://www.it165.net/os/html/201311/6756.html)
注意：由于网上给出的教程都比较早，在最新版本的ycm中有几个文件的地址发生了改动：
在编译ycm时注意将：
- cmake -G "Unix Makefiles"  ~/.vim/bundle/YouCompleteMe/cpp -DEXTERNAL_LIBCLANG_PATH=~/ycm_temp/llvmsrc/lib/libclang.so
- 改为
- cmake -G "Unix Makefiles"  ~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp -DEXTERNAL_LIBCLANG_PATH=~/your/lib/path/libclang.so
- 然后其相应的config文件也发生了改变
- 其地址为[Github](https://github.com/Valloric/YouCompleteMe)
最后要是需要32位的libclang，可以邮件向我索取~我备份了一份

