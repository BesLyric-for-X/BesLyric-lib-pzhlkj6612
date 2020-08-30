<!-- omit in toc -->
# BesLyric-for-X Development Environment (macOS)

<!-- omit in toc -->
## 导航

本分支用于 macOS 平台的环境配置。查看其他平台的文档：

- [Windows](../windows/README.md)
- [Linux distros](../linux/README.md)

<!-- omit in toc -->
## 注意

- 本文档不提供通过编译源码来配置环境的方法；
- 本文档可能不提供有关已结束支持的操作系统和软件的内容。

<!-- omit in toc -->
## 目录

- [0. 有效性参考](#0-有效性参考)
- [1. BesLyric-for-X 项目配置](#1-beslyric-for-x-项目配置)
  - [1.0. 环境变量](#10-环境变量)
- [2. 配置开发环境](#2-配置开发环境)
  - [2.0. 基础设施](#20-基础设施)
    - [2.0.0. Command Line Developer Tools (CLT)](#200-command-line-developer-tools-clt)
    - [2.0.1. Homebrew](#201-homebrew)
  - [2.1. Qt 5](#21-qt-5)
    - [2.1.0. 在线安装程序](#210-在线安装程序)
    - [2.1.1. HomeBrew （不推荐）](#211-homebrew-不推荐)
  - [2.2. 多媒体](#22-多媒体)
    - [2.2.0. SDL 2 & FFmpeg 4](#220-sdl-2--ffmpeg-4)
- [3. 脚注](#3-脚注)
- [4. 参考](#4-参考)
- [5. 其他](#5-其他)

## 0. 有效性参考

按照本文档进行配置后，通过测试的操作系统与开发环境的版本组合：

| OS & Kernel | Compiler | Xcode CLT | Qt | SDL | FFmpeg | SSL Impl. |
|:- |:- |:- |:- |:- |:- |:- |
| Mac OS X 10.15.6 19G73 & Darwin Kernel Version 19.6.0 | Apple clang version 11.0.3 (clang-1103.0.32.62) | 11.5.0.0.1.1588476445 | 5.12.9 | 2.0.12 | ffmpeg version 4.3.1 built with Apple clang version 11.0.3 (clang-1103.0.32.62) | LibreSSL 2.8.3 |
| Mac OS X 10.14.6 18G103 & Darwin Kernel Version 18.7.0 | Apple LLVM version 10.0.1 (clang-1001.0.46.4) | 10.3.0.0.1.1562985497 | 5.12.9 | 2.0.12 | ffmpeg version 4.3.1 built with Apple clang version 11.0.0 (clang-1100.0.33.17) | LibreSSL 2.6.5 |
| Mac OS X 10.13.6 17G65 & Darwin Kernel Version 17.7.0 | Apple LLVM version 10.0.0 (clang-1000.10.44.4) | 10.1.0.0.1.1539992718 | 5.12.9 | 2.0.12 | ffmpeg version 4.3.1 built with Apple LLVM version 10.0.0 (clang-1000.11.45.5) | LibreSSL 2.2.7 |

<a href="#note_2" id="note_2_sup"><sup>[2]</sup></a><a href="#note_3" id="note_3_sup"><sup>[3]</sup></a>

## 1. BesLyric-for-X 项目配置

### 1.0. 环境变量

由于启动 Qt Creaotr 的方式不同（使用 shell 或 Finder ），Qt Creator 持有的`PATH`环境变量可能并不包含 HomeBrew 的默认安装目录（使用`brew --prefix`查看<a href="#note_10" id="note_10_sup"><sup>[10]</sup></a>），进而不能找到`pkg-config`并正确配置第三方库<a href="#note_6" id="note_6_sup"><sup>[6]</sup></a><a href="#note_7" id="note_7_sup"><sup>[7]</sup></a><a href="#note_8" id="note_8_sup"><sup>[8]</sup></a><a href="#note_9" id="note_9_sup"><sup>[9]</sup></a>。

为解决该问题，在 macOS 平台，开发 BesLyric-for-X 可能需要环境变量`B4X_PKG_CONFIG`。

典型的`B4X_PKG_CONFIG`值为`/usr/local/bin/pkg-config`。

不建议将该环境变量配置到操作系统或当前用户中，而是配置到项目内（通过 Qt Creator 左侧列表切换到“ Project ”页面，在“ Build Settings ”-“ Build Environment ”或“ Project Settings ”-“ Environment ”中配置环境变量）。

## 2. 配置开发环境

### 2.0. 基础设施

#### 2.0.0. Command Line Developer Tools (CLT)

Command Line Developer Tools (CLT) 是一个集合，包含大量的开发工具，例如编译器（ Clang ）、构建工具（ Make ）、解析器（ YACC ）、解释器（ Python ）和版本控制系统（ Git 、 SVN ）。

对于 macOS 上的 Qt 开发来说，仅需安装 CLT ，而不需要安装 Xcode 。在国内有以下两种安装方法：

1. 在终端输入`xcode-select --install`在线安装<a href="#note_5" id="note_5_sup"><sup>[5]</sup></a>；
2. 从 [More Software Downloads - Apple Developer](https://developer.apple.com/download/more/) 下载离线安装程序。

#### 2.0.1. Homebrew

[Homebrew](https://brew.sh/) 是一个用于 macOS 的包管理器。有以下两种安装方法：

1. 使用 [cunkai](https://gitee.com/cunkai) 托管在 Gitee 的 [Homebrew 国内安装脚本](https://gitee.com/cunkai/HomebrewCN/)安装<a href="#note_4" id="note_4_sup"><sup>[4]</sup></a>：

```console
$ /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

2. 使用 tuna 提供的 [formula 索引的镜像](https://mirror.tuna.tsinghua.edu.cn/help/homebrew/)和[二进制预编译包的镜像](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew-bottles/)（对于 zsh 用户，记得将`~/.bash_profile`替换为`~/.zshenv`，否则需要每次进入操作系统后都执行一次`source ~/.bash_profile`<a href="#note_1" id="note_1_sup"><sup>[1]</sup></a>）。

### 2.1. Qt 5

#### 2.1.0. 在线安装程序

从 [Index of /archive/qt](http://download.qt.io/archive/qt/) 下载安装程序。安装时记得勾选用于 macOS 的预编译组件，例如“Qt / Qt 5.15.0 / macOS”。

#### 2.1.1. HomeBrew （不推荐）

安装 Qt 和 Qt Creator：

```console
$ brew install qt && \
    brew cask install qt-creator
```

由于需要进行额外的配置，所以**暂时**不推荐。

### 2.2. 多媒体

#### 2.2.0. SDL 2 & FFmpeg 4

安装 SDL 2 、 FFmpeg 4 及相关开发库：

```console
$ brew install sdl2 ffmpeg
```

安装后能在`/usr/local/include/`和`/usr/local/lib/`下找到相关文件（以`libavcodec`为例）：

```console
$ find -L /usr/local/include /usr/local/lib \( -type f -o -type l \) -name "*avcodec*" -exec ls -l {} \;
-rw-r--r--  1 mac  staff  148465 Jul 11 18:39 /usr/local/include/libavcodec/avcodec.h
lrwxr-xr-x  1 mac  admin  53 Aug 23 12:30 /usr/local/lib/libavcodec.58.91.100.dylib -> ../Cellar/ffmpeg/4.3.1/lib/libavcodec.58.91.100.dylib
lrwxr-xr-x  1 mac  admin  53 Aug 23 12:30 /usr/local/lib/pkgconfig/libavcodec.pc -> ../../Cellar/ffmpeg/4.3.1/lib/pkgconfig/libavcodec.pc
lrwxr-xr-x  1 mac  admin  39 Aug 23 12:30 /usr/local/lib/libavcodec.a -> ../Cellar/ffmpeg/4.3.1/lib/libavcodec.a
lrwxr-xr-x  1 mac  admin  43 Aug 23 12:30 /usr/local/lib/libavcodec.dylib -> ../Cellar/ffmpeg/4.3.1/lib/libavcodec.dylib
lrwxr-xr-x  1 mac  admin  46 Aug 23 12:30 /usr/local/lib/libavcodec.58.dylib -> ../Cellar/ffmpeg/4.3.1/lib/libavcodec.58.dylib
```

也能使用`pkg-config`获取相关的构建参数：

```console
$ pkg-config --cflags --libs libavcodec
-I/usr/local/Cellar/ffmpeg/4.3.1/include -L/usr/local/Cellar/ffmpeg/4.3.1/lib -lavcodec
```

## 3. 脚注

1. <a href="#note_1_sup" id="note_1">^</a> *Yes, it's called `~/.zshenv`...*<br>Is there anything in Zsh like bash_profile? - Stack Overflow<br>https://stackoverflow.com/questions/23090390/is-there-anything-in-zsh-like-bash-profile#answer-23091184
2. <a href="#note_2_sup" id="note_2">^</a> *`sw_vers`*<br>How to check what macOS version you have | Digital Citizen<br>https://www.digitalcitizen.life/macos-version
3. <a href="#note_3_sup" id="note_3">^</a> Determine xcode command line tools version - Ask Different<br>https://apple.stackexchange.com/questions/180957/determine-xcode-command-line-tools-version
4. <a href="#note_4_sup" id="note_4">^</a> Homebrew国内如何自动安装（国内地址） - 知乎<br>https://zhuanlan.zhihu.com/p/111014448
5. <a href="#note_5_sup" id="note_5">^</a> How to Install Command Line Tools in Mac OS X (Without Xcode)<br>https://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/
6. <a href="#note_6_sup" id="note_6">^</a> Qt Creator doesn&#x27;t recognize PATH-variable on Mac OSX | Qt Forum<br>https://forum.qt.io/topic/5716/qt-creator-doesn-t-recognize-path-variable-on-mac-osx
7. <a href="#note_7_sup" id="note_7">^</a> lion - How can I have Qt Creator to recognize my environment variables? - Ask Different<br>https://apple.stackexchange.com/questions/54765/how-can-i-have-qt-creator-to-recognize-my-environment-variables
8. <a href="#note_8_sup" id="note_8">^</a> Linux environment variable not recognized by qmake in QtCreator - Stack Overflow<br>https://stackoverflow.com/questions/45368010/linux-environment-variable-not-recognized-by-qmake-in-qtcreator
9. <a href="#note_9_sup" id="note_9">^</a> macos - Adding /usr/local/bin (homebrew) to QtCreator search path for pkg-config on Mac OSX - Stack Overflow<br>https://stackoverflow.com/questions/17129349/adding-usr-local-bin-homebrew-to-qtcreator-search-path-for-pkg-config-on-mac
10. <a href="#note_10_sup" id="note_10">^</a> brew(1) – The Missing Package Manager for macOS — Homebrew Documentation<br>https://docs.brew.sh/Manpage#--prefix-formula

## 4. 参考

- https://doc.qt.io/qt-5/macos.html
- https://www.macissues.com/2014/05/26/what-are-the-command-line-developer-tools-in-os-x/
- https://gist.github.com/shoogle/750a330c851bd1a924dfe1346b0b4a08

## 5. 其他

- 本文档在 VSCode 上使用 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 辅助编辑。
