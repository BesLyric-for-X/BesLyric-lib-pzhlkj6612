<!-- omit in toc -->
# BesLyric-for-X Development Environment (Linux distros)

<!-- omit in toc -->
## 导航

本分支用于 Linux 平台的环境配置。查看其他平台的文档：

- [macOS](../macos/README.md)
- [Windows](../windows/README.md)

<!-- omit in toc -->
## 注意

- 本文档不提供通过编译源码来配置环境的方法；
- 本文档可能不提供有关已结束支持的操作系统和软件的内容。

<!-- omit in toc -->
## 目录

- [0. 有效性参考](#0-有效性参考)
- [1. BesLyric-for-X 项目配置](#1-beslyric-for-x-项目配置)
- [2. 配置开发环境](#2-配置开发环境)
  - [2.0. 基础设施](#20-基础设施)
  - [2.1. Qt 5](#21-qt-5)
  - [2.2. 多媒体](#22-多媒体)
    - [2.2.0. SDL 2](#220-sdl-2)
    - [2.2.1. FFmpeg 4](#221-ffmpeg-4)
- [3. 其他](#3-其他)
- [4. 参考](#4-参考)

## 0. 有效性参考

按照本文档进行配置后，通过测试的操作系统与开发环境的版本组合：

| OS & Kernel | Compiler | Qt | SDL | FFmpeg | TLS Impl. |
|:- |:- |:- |:- |:- |:- |
| <table><tr><td>Ubuntu 18.04.3 LTS &amp; 5.3.0-28-generic</td></tr><tr><td>Ubuntu 18.04.4 LTS &amp; 5.3.0-40-generic</td></tr></table> | gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1) | 5.14.1 | 2.0.8 | ffmpeg version 4.2.2-0york0~18.04 | OpenSSL 1.1.1  11 Sep 2018 |

## 1. BesLyric-for-X 项目配置

对于 Ubuntu ，不需要对项目进行额外的配置。

## 2. 配置开发环境

### 2.0. 基础设施

```console
$ sudo apt install build-essential git
```

### 2.1. Qt 5

从 [Index of /archive/qt](http://download.qt.io/archive/qt/) 下载安装程序。安装时记得勾选当前编译环境对应的预编译组件，例如“Qt / Qt 5.14.1 / Desktop gcc 64-bit”。

### 2.2. 多媒体

#### 2.2.0. SDL 2

安装 SDL 2 ：

```console
$ sudo apt install libsdl2-dev
```

#### 2.2.1. FFmpeg 4

添加 PPA ：

```console
$ sudo add-apt-repository ppa:jonathonf/ffmpeg-4
```

如果 PPA 访问缓慢，可以使用 [ustclug 提供的反向代理](https://lug.ustc.edu.cn/wiki/mirrors/help/revproxy)。有人提供了一个[替换 URL 的正则表达式](https://github.com/ustclug/mirrorrequest/issues/43#issuecomment-280996651)，我根据[通过 http 协议访问出现的问题](https://github.com/ustclug/mirrorrequest/issues/43#issuecomment-280996651)对其进行了一些修改：

```console
$ sudo find /etc/apt/sources.list.d/ -type f -name "*.list" -exec sed -i.bak -r 's#deb(-src)?\s*https?://ppa.launchpad.net#deb\1 https://launchpad.proxy.ustclug.org#ig' {} \;
```

安装 FFmpeg 4 及相关开发库：

```console
$ sudo apt install ffmpeg libavcodec-dev libavdevice-dev libavformat-dev libavfilter-dev libavutil-dev libpostproc-dev libswresample-dev libswscale-dev
```

由于 FFmpeg 等组件依赖一些同名但不同版本的库，所以在链接时可能会出现警告：

```log
/usr/bin/ld: warning: libavcodec.so.57, needed by //usr/lib/x86_64-linux-gnu/libchromaprint.so.1, may conflict with libavcodec.so.58
/usr/bin/ld: warning: libavutil.so.55, needed by //usr/lib/x86_64-linux-gnu/libchromaprint.so.1, may conflict with libavutil.so.56
/usr/bin/ld: warning: libswresample.so.2, needed by //usr/lib/x86_64-linux-gnu/libavcodec.so.57, may conflict with libswresample.so.3
```

目前可忽略这个问题。

## 3. 其他

- 本文档在 VSCode 上使用 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 辅助编辑；
- 本文档中嵌套表格使用 [HTML Table generator - TablesGenerator.com](https://www.tablesgenerator.com/html_tables) 辅助编辑。

## 4. 参考

- https://askubuntu.com/questions/686239/how-do-i-check-the-version-of-ubuntu-i-am-running
- https://linux4one.com/how-to-install-ffmpeg-on-ubuntu-18-04/
