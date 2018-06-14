---
layout: post
title: 把时间浪费在美好的事物上 - 写给程序员的Mac OS X入门指南
date: 2015-12-31 14:02:50.000000000 +09:00
tags: [Mac]
---

> 即将跨入2016年，这也将是我大学里最后一个新年，仔细回想这一年里对我个人来说做出的改变确实不少。断断续续重装了不知道不知道多少次Windows，Linux发行版换来换去总是觉得不那么满意，终于在今年三月份买了人生中第一台Mac。暑假之后开始了人生里第一次真正意义上的实习、第一款App Store上架应用、全面转向了JavaScript全栈开发
![image](http://7xort8.com1.z0.glb.clouddn.com/blog_mac-01.jpg)

## 为什么是Mac
> 要是到现在的移动端、服务器端跑的大多数都是\*nix系统，熟悉\*nix系统显然是很有必要的，Ubuntu对新手来说也是个不错的选择，但是我想Mac或许会是个更好的选择。

### 软硬件层面
- 首先Mac OS X的底层是Unix，而且是真正的Unix（符合标准，有授权，合法的可以用Unix商标），强大的命令行
- 高效快捷（菜单栏置顶，多桌面空间，大量而且全局一致的快捷键）
- 异常好用的触控板（再也不用担心鼠标手的问题了）
- Retina屏幕（代码看着清晰锐利）
- 超长续航（意味着你可以在咖啡馆编程一整天不用充电）
- 高性能SSD
- 不用担心各种驱动问题
- 杰出的工业设计

### 社区层面
- 如果你是Swift／Objective-C程序员，Mac应该是标配好吧
- 如果你是Ruby on Rails程序员，包括DHH在内的绝大多数ror开发者首推的开发环境就是Mac
- 对于Web前端开发者来说，Mac的Retina屏的杀伤力想必不小吧
- 对于广大Python、PHP程序员来说, Mac可是自带apache、php、python


> 话说Linus用的也是Mac，虽然他的Mac上装的并不是OS X

![Linus](http://7xort8.com1.z0.glb.clouddn.com/blog_linus-01.jpg)


## Mac的快速上手指南
### 常用快捷键
> 需要注意的地方是Mac OS X遵循的是Unix哲学，所以对文件不能用剪切操作,只能移动文件,对应的快捷键是 `Command` + `Option` + `V`（快捷键一览图片来源于网络）

![short cut](http://7xort8.com1.z0.glb.clouddn.com/blog_shortcut.jpg)



### 开发者常用快捷键
|功能             |快捷键                       |
|:--------------:|:--------------------------:|
|开发者工具（浏览器）| `Command` + `Option` + `I` |
|将光标移动到行首   | `Ctrl` + `A`               |
|将光标移动到行尾   | `Ctrl` + `E`               |
|清除屏幕          | `Ctrl` + `L`               |
|搜索以前使用命令   | `Ctrl` + `R`               |
|清除当前行        | `Ctrl` + `U`               |
|清除至当前行尾     | `Ctrl` + `K`               |
|单词为单位移动     | `Option` + `方向键`         |
|切换选项卡(向左)   | `Command` + `Shift` + `[`  |
|切换选项卡(向右)   | `Command` + `Shift` + `]`  |


## 常用软件清单
> 不要装全局Flash，可以考虑用Chrome浏览器（Chrome自带flash播放器）
不推荐装360或杀毒类应用，实在是有洁癖的话可以考虑购买Clean My Mac

### 命令行工具
- Xcode的Command Line Tools，直接在terminal中输入命令`xcode-select --install`安装
  - git, wget等工具开箱即用
  - Mac下c语言编译器用的是clang、C++编译器用clang++，调试器用的lldb，以上三个装完Xcode的command line tools后就安装完成，分别用来替代gcc、g++、gdb
- [Oh-My-Zsh](http://ohmyz.sh/)：使用 `zsh：chsh -s /bin/zsh`，将默认的shell切换成zsh（zsh是OS X自带的不需要另行安装），接着安装Oh-My-Zsh:
  - 自动安装：
  ```
  wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
  ```
  - 手动安装：
  ```
  git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
  cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
  ```
- [Home Brew](http://brew.sh)：Mac下最好用的包管理工具
- brew-cask：用来装GUI程序的包管理工具，直接用brew安装
- LaunchRocket： 图形化的Service管理工具，可以直接用brew-cask命令安装 `brew tap jimbojsb/launchrocket`
`brew cask install launchrocket`

### 其它常用软件
- 解压缩：the Unarchiver（App Store直接安装）
- 视频播放器：MPlayer X, VLC, [mpv](https://mpv.io/)
- MarkDown编辑器：Mou
- 终端：iTerm2（终端快速分屏的时代来临）
- Android手机数据传输：Android File Transfer


## 拓展阅读

[Macintosh演化史](http://macshuo.com/?p=728)

[Mac OS X背后的故事](http://www.douban.com/group/topic/23658084/)

[Why do most professional programmers prefer Macs?](https://www.quora.com/Why-do-most-professional-programmers-prefer-Macs)

[为什么我认为每个程序员都应该用Mac OS X？](http://tiny4.org/blog/2010/02/why-programmers-should-use-mac-os-x/)
