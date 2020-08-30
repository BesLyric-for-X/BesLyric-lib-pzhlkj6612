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
  - [2.0. Git 与 C++ 开发环境](#20-git-与-c-开发环境)
    - [2.0.0. Ubuntu](#200-ubuntu)
    - [2.0.1. Fedora 32](#201-fedora-32)
    - [2.0.2. Manjaro 20](#202-manjaro-20)
    - [2.0.3. openSUSE 15](#203-opensuse-15)
  - [2.1. Qt 5](#21-qt-5)
    - [2.1.0. 包管理器](#210-包管理器)
      - [2.1.0.0. Ubuntu](#2100-ubuntu)
      - [2.1.0.1. Fedora 32](#2101-fedora-32)
      - [2.1.0.2. Manjaro 20](#2102-manjaro-20)
      - [2.1.0.3. openSUSE 15](#2103-opensuse-15)
    - [2.1.1. 在线安装程序（适用于所有支持的操作系统）](#211-在线安装程序适用于所有支持的操作系统)
  - [2.2. 第三方多媒体库](#22-第三方多媒体库)
    - [2.2.0 SDL 2](#220-sdl-2)
      - [2.2.0.0. Ubuntu](#2200-ubuntu)
      - [2.2.0.1. Fedora 32](#2201-fedora-32)
      - [2.2.0.2. Manjaro 20](#2202-manjaro-20)
      - [2.2.0.3. openSUSE 15](#2203-opensuse-15)
    - [2.2.1. FFmpeg 4](#221-ffmpeg-4)
      - [2.2.1.0. Ubuntu 18.10 （含）以上版本](#2210-ubuntu-1810-含以上版本)
      - [2.2.1.1. Fedora 32](#2211-fedora-32)
      - [2.2.1.2. Manjaro 20](#2212-manjaro-20)
      - [2.2.1.3. openSUSE 15](#2213-opensuse-15)
      - [2.2.1.4. Ubuntu 18.04 （含）以下版本](#2214-ubuntu-1804-含以下版本)
- [3. 异常处理](#3-异常处理)
  - [3.0. 为 Qt 5.15 安装 xcb 库的相关组件](#30-为-qt-515-安装-xcb-库的相关组件)
- [4. 脚注](#4-脚注)
- [5. 参考](#5-参考)
- [6. 其他](#6-其他)

## 0. 有效性参考

按照本文档进行配置后，通过测试的操作系统与开发环境的版本组合：

| OS & Kernel | Compiler | Qt | SDL | FFmpeg | TLS Impl. |
|:- |:- |:- |:- |:- |:- |
| Ubuntu 20.04 LTS &amp; 5.4.0-40-generic | 9.3.0 (Ubuntu 9.3.0-10ubuntu2) | <table><tr><td>5.12.8 (apt)</td></tr><tr><td>5.12.9</td></tr><tr><td>5.15.0</td></tr></table> | 2.0.10 | 4.2.2-1ubuntu1 | OpenSSL 1.1.1f  31 Mar 2020 |
| Fedora release 32 (Thirty Two) &amp; 5.7.16-200.fc32.x86_64 | 10.2.1 20200723 (Red Hat 10.2.1-1) (GCC) | <table><tr><td>5.12.9</td></tr><tr><td>5.13.2 (dnf)</td></tr></table> | 2.0.12 | 4.2.4 | OpenSSL 1.1.1g FIPS  21 Apr 2020 |
| Manjaro Linux xfce 20.0.3 &amp; 5.6.19-2-MANJARO | 10.1.0 (GCC) | <table><tr><td>5.15.0</td></tr><tr><td>5.15.0 (pacman)</td></tr></table> | 2.0.12 | n4.2.4 | OpenSSL 1.1.1g  21 Apr 2020 |
| openSUSE Leap 15.2 &amp; 5.3.18-lp152.33-default | 7.5.0 (SUSE Linux) | <table><tr><td>5.12.7 (zypper)</td></tr><tr><td>5.15.0</td></tr></table> | 2.0.8 | 4.2.1-lp152.1.4 | OpenSSL 1.1.1d  10 Sep 2019 |
| Ubuntu 18.04.4 LTS &amp; 5.3.0-62-generic | 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04) | <table><tr><td>5.12.9</td></tr><tr><td>5.14.2</td></tr><tr><td>5.15.0</td></tr></table> | 2.0.8 | 4.3-2~18.04.york0 | OpenSSL 1.1.1  11 Sep 2018 |
| Ubuntu 16.04.6 LTS &amp; 4.15.0-107-generic | 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.12) | <table><tr><td>5.12.9</td></tr><tr><td>5.15.0</td></tr></table> | 2.0.4 | 4.3-2~16.04.york1 | OpenSSL 1.1.1g  21 Apr 2020 (Built from source) |

## 1. BesLyric-for-X 项目配置

不需要对项目进行额外的配置。

## 2. 配置开发环境

### 2.0. Git 与 C++ 开发环境

#### 2.0.0. Ubuntu

```console
# apt update && \
    apt install build-essential pkg-config git
```

#### 2.0.1. Fedora 32

```console
# dnf check-update && \
    dnf groupinstall \
      "Development Tools" \
      "C Development Tools and Libraries"
```

如有需要，可以启用FastestMirror<a href="#note_8" id="note_8_sup"><sup>[8]</sup></a><a href="#note_9" id="note_9_sup"><sup>[9]</sup></a>使`dnf`进行测试以选择最低时延的镜像服务器。

#### 2.0.2. Manjaro 20

```console
# pacman --sync --refresh && \
    pacman --sync base-devel gdb git
```

#### 2.0.3. openSUSE 15

```console
# zypper refresh && \
    zypper install --type pattern devel_C_C++ && \
    zypper install pkg-config git
```

### 2.1. Qt 5

#### 2.1.0. 包管理器

##### 2.1.0.0. Ubuntu

```console
# apt install git qt5-default qtcreator
```

##### 2.1.0.1. Fedora 32

```console
# dnf install qt5-devel qt-creator
```

##### 2.1.0.2. Manjaro 20

```console
# pacman --sync qt5-base qtcreator
```

##### 2.1.0.3. openSUSE 15

```console
# zypper install --type pattern devel_qt5
```

#### 2.1.1. 在线安装程序（适用于所有支持的操作系统）

- 从 [Index of /official_releases/online_installers](http://download.qt.io/official_releases/online_installers/) 下载在线安装程序；
- 安装时记得勾选当前编译环境对应的预构建组件（ Pre-built Components ），例如“ Qt / Qt 5.15.0 / Desktop gcc 64-bit ”。

### 2.2. 第三方多媒体库

#### 2.2.0 SDL 2

##### 2.2.0.0. Ubuntu

```console
# apt install libsdl2-dev
```

##### 2.2.0.1. Fedora 32

```console
# dnf install SDL2-devel
```

##### 2.2.0.2. Manjaro 20

```console
# pacman --sync sdl2
```

##### 2.2.0.3. openSUSE 15

```console
# zypper install libSDL2-devel
```

#### 2.2.1. FFmpeg 4

##### 2.2.1.0. Ubuntu 18.10 （含）以上版本

```console
# apt install \
    ffmpeg \
    libavcodec-dev \
    libavdevice-dev \
    libavfilter-dev \
    libavformat-dev \
    libavutil-dev \
    libpostproc-dev \
    libswresample-dev \
    libswscale-dev
```

##### 2.2.1.1. Fedora 32

<a href="#note_10" id="note_10_sup"><sup>[10]</sup></a>

```console
# dnf install \
    https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm && \
    dnf install ffmpeg-devel
```

##### 2.2.1.2. Manjaro 20

```console
# pacman --sync ffmpeg
```

##### 2.2.1.3. openSUSE 15

```console
# zypper install \
    ffmpeg-4-libavcodec-devel \
    ffmpeg-4-libavdevice-devel \
    ffmpeg-4-libavfilter-devel \
    ffmpeg-4-libavformat-devel \
    ffmpeg-4-libavutil-devel \
    ffmpeg-4-libpostproc-devel \
    ffmpeg-4-libswresample-devel \
    ffmpeg-4-libswscale-devel
```

##### 2.2.1.4. Ubuntu 18.04 （含）以下版本

<a href="#note_1" id="note_1_sup"><sup>[1]</sup></a>添加 PPA ：

```console
# add-apt-repository ppa:jonathonf/ffmpeg-4
```

如果 PPA 访问缓慢，可以使用 [ustclug 提供的反向代理](https://lug.ustc.edu.cn/wiki/mirrors/help/revproxy)。有人提供了一个[替换 URL 的正则表达式](https://github.com/ustclug/mirrorrequest/issues/43#issuecomment-280996651)，我根据[通过 http 协议访问出现的问题](https://github.com/ustclug/mirrorrequest/issues/43#issuecomment-280996651)对其进行了一些修改：

```console
# find /etc/apt/sources.list.d/ -type f -name "*.list" -exec \
    sed -i.bak -r 's#deb(-src)?\s*https?://ppa.launchpad.net#deb\1 https://launchpad.proxy.ustclug.org#ig' {} \;
```

安装：

```console
# apt install \
    ffmpeg \
    libavcodec-dev \
    libavdevice-dev \
    libavformat-dev \
    libavfilter-dev \
    libavutil-dev \
    libpostproc-dev \
    libswresample-dev \
    libswscale-dev
```

## 3. 异常处理

### 3.0. 为 Qt 5.15 安装 xcb 库的相关组件

<a href="#note_2" id="note_2_sup"><sup>[2]</sup></a><a href="#note_3" id="note_3_sup"><sup>[3]</sup></a><a href="#note_4" id="note_4_sup"><sup>[4]</sup></a><a href="#note_5" id="note_5_sup"><sup>[5]</sup></a><a href="#note_6" id="note_6_sup"><sup>[6]</sup></a><a href="#note_7" id="note_7_sup"><sup>[7]</sup></a>

运行程序时出现错误：

```console
$ ./binary
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.

Available platform plugins are: eglfs, linuxfb, minimal, minimalegl, offscreen, vnc, wayland-egl, wayland, wayland-xcomposite-egl, wayland-xcomposite-glx, xcb.

Aborted (core dumped)
```

设置环境变量`QT_DEBUG_PLUGINS=1`再次运行程序：

```console
$ QT_DEBUG_PLUGINS=1 ./binary
QFactoryLoader::QFactoryLoader() checking directory path "/the/path/to/Qt/5.15.0/gcc_64/plugins/platforms" ...

...

Got keys from plugin meta data ("xcb")

...

Cannot load library /the/path/to/Qt/5.15.0/gcc_64/plugins/platforms/libqxcb.so: (libxcb-xinerama.so.0: cannot open shared object file: No such file or directory)
QLibraryPrivate::loadPlugin failed on "/the/path/to/Qt/5.15.0/gcc_64/plugins/platforms/libqxcb.so" : "Cannot load library /the/path/to/Qt/5.15.0/gcc_64/plugins/platforms/libqxcb.so: (libxcb-xinerama.so.0: cannot open shared object file: No such file or directory)"

...

Aborted (core dumped)

```

系统中缺少 libxcb-xinerama ，搜索：

```console
$ apt search libxcb-xinerama
Sorting... Done
Full Text Search... Done
libxcb-xinerama0/focal 1.14-2 amd64
  X C Binding, xinerama extension

libxcb-xinerama0-dev/focal 1.14-2 amd64
  X C Binding, xinerama extension, development files

```

安装：

```console
# apt install libxcb-xinerama0
```

如果缺少其它的库，也按照上述方法进行安装。

## 4. 脚注

1. <a href="#note_1_sup" id="note_1">^</a> How to Install FFmpeg on Ubuntu 18.04 - Linux4one<br>https://linux4one.com/how-to-install-ffmpeg-on-ubuntu-18-04/
2. <a href="#note_2_sup" id="note_2">^</a> qt.qpa.plugin: Could not load the Qt platform plugin &quot;xcb&quot; in &quot;&quot; even though it was found. | Qt Forum<br>https://forum.qt.io/topic/93247/qt-qpa-plugin-could-not-load-the-qt-platform-plugin-xcb-in-even-though-it-was-found
3. <a href="#note_3_sup" id="note_3">^</a> Qt Creator + Ubuntu 20.04 | Qt Forum<br>https://forum.qt.io/topic/116299/qt-creator-ubuntu-20-04
4. <a href="#note_4_sup" id="note_4">^</a> &quot;Failed to load platform plugin &quot;xcb&quot; &quot; while launching qt5 app on linux without qt installed - Stack Overflow<br>https://stackoverflow.com/questions/17106315/failed-to-load-platform-plugin-xcb-while-launching-qt5-app-on-linux-without
5. <a href="#note_5_sup" id="note_5">^</a> “Failed to load platform plugin ”xcb“ ” while launching qt5 app on linux without qt installed - Ask Ubuntu<br>https://askubuntu.com/questions/308128/failed-to-load-platform-plugin-xcb-while-launching-qt5-app-on-linux-without
6. <a href="#note_6_sup" id="note_6">^</a> Identify pyqt5 515 ci issue by j9ac9k · Pull Request #1221 · pyqtgraph/pyqtgraph<br>https://github.com/pyqtgraph/pyqtgraph/pull/1221
7. <a href="#note_7_sup" id="note_7">^</a> Re: [Interest] Qt 5.15 for Linux/X11 : "-qt-xcb" no longer	supported?<br>https://www.mail-archive.com/interest@qt-project.org/msg34052.html
8. <a href="#note_8_sup" id="note_8">^</a> PackageManagement/Yum/FastestMirror - CentOS Wiki<br>https://wiki.centos.org/PackageManagement/Yum/FastestMirror
9. <a href="#note_9_sup" id="note_9">^</a> Dnf fastest mirror on Fedora/CentOS/RHEL &#x2d; Darryl Dias<br>https://darryldias.me/2020/how-to-setup-fastest-mirror-in-dnf/
10. <a href="#note_10_sup" id="note_10">^</a> Enabling the RPM Fusion repositories :: Fedora Docs Site<br>https://docs.fedoraproject.org/en-US/quick-docs/setup_rpmfusion/

## 5. 参考

- https://doc.qt.io/qt-5/linux.html

## 6. 其他

- 本文档在 VSCode 上使用 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 辅助编辑；
- 本文档中嵌套表格使用 [HTML Table generator - TablesGenerator.com](https://www.tablesgenerator.com/html_tables) 辅助编辑。
