<!-- omit in toc -->
# BesLyric-for-X Development Environment (Windows x64 MSVC)

<!-- omit in toc -->
## 导航

本分支用于 64 位 Windows 平台的 MSVC 工具链的 BesLyric-for-X 开发环境配置。查看其他平台的文档：

- [Linux distros](../linux/README.md)
- [macOS](../macos/README.md)

<!-- omit in toc -->
## 注意

- 本文档不提供通过编译源码来配置环境的方法；
- 本文档可能不提供有关已结束支持的操作系统<a href="#note_2" id="note_2_sup"><sup>[2]</sup></a>和软件<a href="#note_3" id="note_3_sup"><sup>[3]</sup></a><a href="#note_4" id="note_4_sup"><sup>[4]</sup></a>的内容。

<!-- omit in toc -->
## 目录

- [0. 有效性参考](#0-有效性参考)
- [1. BesLyric-for-X 项目配置](#1-beslyric-for-x-项目配置)
  - [1.0. 环境变量](#10-环境变量)
- [2. 配置开发环境](#2-配置开发环境)
  - [2.0. C++ 开发环境](#20-c-开发环境)
    - [2.0.0. MSVC 工具集 & Windows SDK](#200-msvc-工具集--windows-sdk)
    - [2.0.1. CDB 调试器](#201-cdb-调试器)
  - [2.1. Qt 5](#21-qt-5)
    - [2.1.0. 安装 Qt 5](#210-安装-qt-5)
    - [2.1.1. 为 Qt 5.15 （不含）以下版本配置用于 MSVC 2019 的 Kits （构建套件）](#211-为-qt-515-不含以下版本配置用于-msvc-2019-的-kits-构建套件)
  - [2.2. 第三方库](#22-第三方库)
    - [2.2.0. 多媒体](#220-多媒体)
      - [2.2.0.0. SDL 2](#2200-sdl-2)
      - [2.2.0.1. FFmpeg 4](#2201-ffmpeg-4)
    - [2.2.1. 网络](#221-网络)
      - [2.2.1.0. OpenSSL](#2210-openssl)
- [3. 脚注](#3-脚注)
- [4. 参考](#4-参考)
- [5. 其他](#5-其他)

## 0. 有效性参考

按照本文档进行配置后，通过测试的操作系统与开发环境的版本组合：

| OS &<br>ReleaseID &<br>Kernel | %VCToolsVersion% | Windows SDK | Qt | SDL | FFmpeg | TLS Impl. |
|:- |:- |:- |:- |:- |:- |:- |
| <table><tr><td>Microsoft Windows 10 Pro &amp;<br>2004 &amp;<br>Microsoft Windows [Version 10.0.19041.329]</td></tr></table> | 14.26.28801 | 10.0.18362.1 | 5.15.0 | 2.0.12 | ffmpeg version 4.3.1 built with gcc 10.2.1 (GCC) 20200726 | OpenSSL 1.1.1g  21 Apr 2020 |
| <table><tr><td>Microsoft Windows 10 Pro &amp;<br>2004 &amp;<br>Microsoft Windows [Version 10.0.19041.329]</td></tr></table> | 14.16.27023 | 10.0.17763.0 | 5.12.9 | 2.0.12 | ffmpeg version 4.3.1 built with gcc 10.2.1 (GCC) 20200726 | OpenSSL 1.1.1g  21 Apr 2020 |
| <table><tr><td>Microsoft Windows 10 Pro &amp;<br>1909 &amp;<br>Microsoft Windows [Version 10.0.18363.592]</td></tr><tr><td>Microsoft Windows 10 Pro &amp;<br>1903 &amp;<br>Microsoft Windows [Version 10.0.18362.592]</td></tr><tr><td>Microsoft Windows 10 Enterprise LTSC &amp;<br>1809 &amp;<br>Microsoft Windows [Version 10.0.17763.973]<a href="#note_1" id="note_1_sup"><sup>[1]</sup></a></td></tr></table> | 14.24.28314 | 10.0.18362.1 | 5.14.1 | 2.0.10 | ffmpeg version 4.2.2 built with gcc 9.2.1 (GCC) 20200122 | OpenSSL 1.1.1d  10 Sep 2019 |

<a href="#note_26" id="note_26_sup">[26]</a><a href="#note_27" id="note_27_sup">[27]</a>

## 1. BesLyric-for-X 项目配置

### 1.0. 环境变量

BesLyric-for-X 需要一些第三方库，在 Windows 平台，开发 BesLyric-for-X 需要环境变量`B4X_DEP_PATH`（ macOS 也使用该环境变量）。

典型的`B4X_DEP_PATH`值为`C:\b4x-lib`。 BesLyric-for-X 中的 qmake 指令向`$$INCLUDEPATH`变量添加`${B4X_DEP_PATH}/include`，使 Qt 和 MSVC 能够找到第三方库的头文件；同时，向`$$LIB`添加`-L${B4X_DEP_PATH}/lib`，使 Qt 和 MSVC 能够找到第三方库的库文件。

不建议将该环境变量配置到操作系统或当前用户中，而是配置到项目内（ 过 Qt Creator 左侧列表切换到“ Project ”页面，在“ Build Settings ”-“ Build Environment ”中配置环境变量）。

注意：即使`B4X_DEP_PATH`指向的文件夹的路径包含空格，也不需要在路径两端加上引号。

## 2. 配置开发环境

### 2.0. C++ 开发环境

#### 2.0.0. MSVC 工具集 & Windows SDK

MSVC 工具集包含 C/C++ 编译器（`cl.exe`）、资源编译器（`rc.exe`）、链接器（`link.exe`）、构建工具（`nmake.exe`）等程序。

Windows SDK 包含 Windows 开发所需要的文档、头文件（例如`Windows.h`和`stdio.h`）、库文件（例如`ncrypt.lib`和`libucrt.lib`）、调试器（例如 CDB ）等组件。

MSVC 、 Visual Studio 与 Windows SDK 的版本*对应关系*<sup>（版本不对应也能用，但是**过低**版本 Visual Studio 不能发现高版本 Windows SDK <a href="#note_20" id="note_20_sup">[20]</a>）</sup>如下表<a href="#note_9" id="note_9_sup"><sup>[9]</sup></a>：

| MSVC | Visual Studio | Windows SDK |
|:- |:- |:- |
| v140 | <table><tr><td>Visual Studio 2015</td></tr><tr><td>Visual Studio 2015 Update 1 &amp; 2</td></tr><tr><td>Visual Studio 2015 Update 3</td></tr></table> | <table><tr><td>10.0.10240.0</td></tr><tr><td>10.0.10586.212</td></tr><tr><td>10.0.14393.795</td></tr></table> |
| v141 | <table><tr><td>Visual Studio 2017 ver.15.1</td></tr><tr><td>Visual Studio 2017 ver.15.4</td></tr><tr><td>Visual Studio 2017 ver.15.7</td></tr><tr><td>Visual Studio 2017 ver.15.8</td></tr></table> | <table><tr><td>10.0.15063.0</td></tr><tr><td>10.0.16299.15</td></tr><tr><td>10.0.17134.0</td></tr><tr><td>10.0.17763.0</td></tr></table> |
| v142 | <table><tr><td>Visual Studio 2019</td></tr></table> | <table><tr><td>10.0.18362.0</td></tr><tr><td>10.0.19041.0</td></tr></table> |

推荐使用 [Visual Studio Installer](https://visualstudio.microsoft.com/) 安装 MSVC 工具集和 Windows SDK 。

对于 Qt 开发来说，在 Visual Studio Installer 中，勾选“ Desktop development with C++ ”工作负载后安装即可。

#### 2.0.1. CDB 调试器

CDB 与 WinDbg 同为 Windows 上的调试器<a href="#note_8" id="note_8_sup"><sup>[8]</sup></a>， Qt Creator 需要使用它调试程序。

通过 Visual Studio Installer 安装 Windows SDK 后，在“应用和功能”列表中找到“ Windows Software Development Kit - Windows 10.N ”，选择它并单击“修改”，弹出“ Windows Software Development Kit - Windows 10.N ”窗口。在“ Maintain your Windows Software Development Kit - Windows 10.N features ”页面选择“ Change ”，下一步后，在“ Select the features you want to change ”页面勾选“ Debugging Tools for Windows ”并单击“ Change ”以安装 CDB 。

### 2.1. Qt 5

#### 2.1.0. 安装 Qt 5

- 从 [Index of /official_releases/online_installers](http://download.qt.io/official_releases/online_installers/) 下载在线安装程序；
- 安装时记得勾选当前编译环境对应的预构建组件（ Pre-built Components ），例如“ Qt / Qt 5.15.0 / MSVC 2019 64-bit ”；
- 同时记得勾选调试器的支持组件，例如“ Qt / Developer and Designer Tools / Qt Creator 4.12.3 CDB Debugger Support ”;
- 如果之前已经安装了 CDB ，记得**取消勾选**默认勾选的调试器，例如“ Qt / Developer and Designer Tools / Debugging Tools for Windows ”

#### 2.1.1. 为 Qt 5.15 （不含）以下版本配置用于 MSVC 2019 的 Kits （构建套件）

<details>
    <summary>Qt 5.15 已能自动探测到 MSVC 2019 ，请忽略本节。</summary>

<br>

由于低于 5.15 版本的 Qt 不能自动探测到 MSVC 2019 ，所以需要手动配置构建套件。

由于 MSVC 2017 与 2019 是二进制兼容的<a href="#note_5" id="note_5_sup"><sup>[5]</sup></a>，所以“ MSVC 2017 ”版本的 Qt 组件可用于 MSVC 2019<a href="#note_10" id="note_10_sup"><sup>[10]</sup></a> 。

- 单击菜单栏“ Tools ”-“ Options... ”打开“ Options - Qt Creator ”窗口，单击左侧列表中的“ Kits ”切换到构建套件上下文并切换到“ Kits ”选项卡；
- 列表中有自动探测到的构建套件，例如“ Desktop Qt 5.14.1 MSVC2017 64bit ”，而且其头部有黄色叹号，提示“ Warning: No compile set in kit. ”；
- 单击该构建套件，为其选择对应平台（“x86”与“amd64”）的 C 和 C++ 的编译器。注意，“amd64_x86”和“x86_amd64”是交叉编译（“{开发平台}_{目标平台}”）的选项，可以忽略；
- 检查该构建套件的调试器配置，为其选择对应平台的 CDB ；
- 完成上述配置后，该 Kit 头部依然有黄色叹号，提示“ The compiler "..." (...) may not produce code compatible with the Qt version "..." (...). ”，这是正常现象，可以忽略。

</details>

<br>

### 2.2. 第三方库

#### 2.2.0. 多媒体

##### 2.2.0.0. SDL 2

> SDL（英语：Simple DirectMedia Layer）是一套开放源代码的跨平台多媒体开发库，使用C语言写成。<a href="#note_18" id="note_18_sup"><sup>[18]</sup></a>

- 下载用于 Windows 的， devel 版本的，用于 Visual C++ 的 [SDL 2](https://www.libsdl.org/download-2.0.php) ；
- 对于头文件，从压缩包中提取`\SDL2-2.N.N\include\`下的所有`.h`文件到`%B4X_DEP_PATH%\include\SDL2\`下；
- 对于静态库文件，从压缩包中提取`\SDL2-2.N.N\lib\x64\`下的`SDL2.lib`和`SDL2main.lib`文件到`%B4X_DEP_PATH%\lib\`下；
- 对于动态库文件，从压缩包中提取`\SDL2-2.N.N\lib\x64\`下的`SDL2.dll`文件到`%B4X_DEP_PATH%\bin\`下。

<details>
    <summary>完整的 SDL 2 库文件的文件夹结构如下（点击展开）：</summary>

<br>

```cmd
> tree /A /F %B4X_DEP_PATH%
%B4X_DEP_PATH%
+---include
|   \---SDL2
|           begin_code.h
|           close_code.h
|           SDL.h
|           SDL_assert.h
|           SDL_atomic.h
|           SDL_audio.h
|           SDL_bits.h
|           SDL_blendmode.h
|           SDL_clipboard.h
|           SDL_config.h
|           SDL_config.h.cmake
|           SDL_config.h.in
|           SDL_config_android.h
|           SDL_config_iphoneos.h
|           SDL_config_macosx.h
|           SDL_config_macosx.h.orig
|           SDL_config_minimal.h
|           SDL_config_pandora.h
|           SDL_config_psp.h
|           SDL_config_windows.h
|           SDL_config_winrt.h
|           SDL_config_wiz.h
|           SDL_copying.h
|           SDL_cpuinfo.h
|           SDL_egl.h
|           SDL_endian.h
|           SDL_error.h
|           SDL_events.h
|           SDL_filesystem.h
|           SDL_gamecontroller.h
|           SDL_gesture.h
|           SDL_haptic.h
|           SDL_hints.h
|           SDL_joystick.h
|           SDL_keyboard.h
|           SDL_keycode.h
|           SDL_loadso.h
|           SDL_log.h
|           SDL_main.h
|           SDL_messagebox.h
|           SDL_mouse.h
|           SDL_mutex.h
|           SDL_name.h
|           SDL_opengl.h
|           SDL_opengles.h
|           SDL_opengles2.h
|           SDL_opengles2_gl2.h
|           SDL_opengles2_gl2ext.h
|           SDL_opengles2_gl2platform.h
|           SDL_opengles2_khrplatform.h
|           SDL_opengl_glext.h
|           SDL_pixels.h
|           SDL_platform.h
|           SDL_power.h
|           SDL_quit.h
|           SDL_rect.h
|           SDL_render.h
|           SDL_revision.h
|           SDL_rwops.h
|           SDL_scancode.h
|           SDL_sensor.h
|           SDL_shape.h
|           SDL_stdinc.h
|           SDL_surface.h
|           SDL_system.h
|           SDL_syswm.h
|           SDL_test.h
|           SDL_test_assert.h
|           SDL_test_common.h
|           SDL_test_compare.h
|           SDL_test_crc32.h
|           SDL_test_font.h
|           SDL_test_fuzzer.h
|           SDL_test_harness.h
|           SDL_test_images.h
|           SDL_test_log.h
|           SDL_test_md5.h
|           SDL_test_memory.h
|           SDL_test_random.h
|           SDL_thread.h
|           SDL_timer.h
|           SDL_touch.h
|           SDL_types.h
|           SDL_version.h
|           SDL_video.h
|           SDL_vulkan.h
|
+---dll
|       SDL2.dll
|
\---lib
        SDL2.lib
        SDL2main.lib
```

</details>

<br>

##### 2.2.0.1. FFmpeg 4

> FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。<a href="#note_19" id="note_19_sup"><sup>[19]</sup></a>

- 下载用于 64 位 Windows 的， Shared 和 Dev 版本的 [FFmpeg](https://ffmpeg.zeranoe.com/builds/) ；
- 对于头文件，从 Dev 版本的压缩包中提取`\ffmpeg-4.N-winNN-dev\include\`下的所有文件夹以及其中的文件到`%B4X_DEP_PATH%\include\`下；
- 对于静态库文件，从 Dev 版本的压缩包中提取`\ffmpeg-4.N-wi64-dev\lib\`下的所有`.lib`文件到`%B4X_DEP_PATH%\lib\`下；
- 对于动态库文件，从 Shared 版本的压缩包中提取`\ffmpeg-4.N-win64-shared\bin\`下的所有`.dll`文件到`%B4X_DEP_PATH%\bin\`下。

<details>
    <summary>完整的 FFmpeg 4 库文件的文件夹结构如下（点击展开）：</summary>

<br>

```cmd
> tree /A /F %B4X_DEP_PATH%
%B4X_DEP_PATH%
+---include
|   +---libavcodec
|   |       ac3_parser.h
|   |       adts_parser.h
|   |       avcodec.h
|   |       avdct.h
|   |       avfft.h
|   |       d3d11va.h
|   |       dirac.h
|   |       dv_profile.h
|   |       dxva2.h
|   |       jni.h
|   |       mediacodec.h
|   |       qsv.h
|   |       vaapi.h
|   |       vdpau.h
|   |       version.h
|   |       videotoolbox.h
|   |       vorbis_parser.h
|   |       xvmc.h
|   |
|   +---libavdevice
|   |       avdevice.h
|   |       version.h
|   |
|   +---libavfilter
|   |       avfilter.h
|   |       buffersink.h
|   |       buffersrc.h
|   |       version.h
|   |
|   +---libavformat
|   |       avformat.h
|   |       avio.h
|   |       version.h
|   |
|   +---libavutil
|   |       adler32.h
|   |       aes.h
|   |       aes_ctr.h
|   |       attributes.h
|   |       audio_fifo.h
|   |       avassert.h
|   |       avconfig.h
|   |       avstring.h
|   |       avutil.h
|   |       base64.h
|   |       blowfish.h
|   |       bprint.h
|   |       bswap.h
|   |       buffer.h
|   |       camellia.h
|   |       cast5.h
|   |       channel_layout.h
|   |       common.h
|   |       cpu.h
|   |       crc.h
|   |       des.h
|   |       dict.h
|   |       display.h
|   |       downmix_info.h
|   |       encryption_info.h
|   |       error.h
|   |       eval.h
|   |       ffversion.h
|   |       fifo.h
|   |       file.h
|   |       frame.h
|   |       hash.h
|   |       hdr_dynamic_metadata.h
|   |       hmac.h
|   |       hwcontext.h
|   |       hwcontext_cuda.h
|   |       hwcontext_d3d11va.h
|   |       hwcontext_drm.h
|   |       hwcontext_dxva2.h
|   |       hwcontext_mediacodec.h
|   |       hwcontext_qsv.h
|   |       hwcontext_vaapi.h
|   |       hwcontext_vdpau.h
|   |       hwcontext_videotoolbox.h
|   |       imgutils.h
|   |       intfloat.h
|   |       intreadwrite.h
|   |       lfg.h
|   |       log.h
|   |       lzo.h
|   |       macros.h
|   |       mastering_display_metadata.h
|   |       mathematics.h
|   |       md5.h
|   |       mem.h
|   |       motion_vector.h
|   |       murmur3.h
|   |       opt.h
|   |       parseutils.h
|   |       pixdesc.h
|   |       pixelutils.h
|   |       pixfmt.h
|   |       random_seed.h
|   |       rational.h
|   |       rc4.h
|   |       replaygain.h
|   |       ripemd.h
|   |       samplefmt.h
|   |       sha.h
|   |       sha512.h
|   |       spherical.h
|   |       stereo3d.h
|   |       tea.h
|   |       threadmessage.h
|   |       time.h
|   |       timecode.h
|   |       timestamp.h
|   |       tree.h
|   |       twofish.h
|   |       tx.h
|   |       version.h
|   |       xtea.h
|   |
|   +---libpostproc
|   |       postprocess.h
|   |       version.h
|   |
|   +---libswresample
|   |       swresample.h
|   |       version.h
|   |
|   \---libswscale
|           swscale.h
|           version.h
|
+---dll
|       avcodec-58.dll
|       avdevice-58.dll
|       avfilter-7.dll
|       avformat-58.dll
|       avutil-56.dll
|       postproc-55.dll
|       swresample-3.dll
|       swscale-5.dll
|
\---lib
        avcodec.lib
        avdevice.lib
        avfilter.lib
        avformat.lib
        avutil.lib
        postproc.lib
        swresample.lib
        swscale.lib
```

</details>

#### 2.2.1. 网络

##### 2.2.1.0. OpenSSL

> 在计算机网络上，OpenSSL是一个开放源代码的软件库包，应用程序可以使用这个包来进行安全通信，避免窃听，同时确认另一端连线者的身份。这个包广泛被应用在互联网的网页服务器上。<a href="#note_21" id="note_21_sup"><sup>[21]</sup></a>

*一些程序是在运行时加载 OpenSSL 库*<sup>（是只要包含`QT += network`便在启动时加载，还是仅在进行 HTTPS 访问时？）</sup>，因此并不能通过 [lucasg/Dependencies](https://github.com/lucasg/Dependencies) 等类似工具在程序的导入表中找到相关的信息，但这些程序（例如 BesLyric-for-X ）的确需要它们。

Qt 5 使用的 TLS 实现默认为 OpenSSL ，无法使用 Windows 的 [Schannel SSP](https://docs.microsoft.com/windows-server/security/tls/tls-ssl-schannel-ssp-overview) <a href="#note_23" id="note_23_sup"><sup>[23]</sup></a><a href="#note_24" id="note_24_sup"><sup>[24]</sup></a><a href="#note_25" id="note_25_sup"><sup>[25]</sup></a>。

由于某些国家和地区的法律对密码学的限制<a href="#note_14" id="note_14_sup"><sup>[14]</sup></a><a href="#note_22" id="note_22_sup"><sup>[22]</sup></a>， Qt 不能（通过`windeployqt`等途径）将 OpenSSL 一并交付<a href="#note_13" id="note_13_sup"><sup>[13]</sup></a>，所以需要开发者自行编译，或者从第三方获得 OpenSSL 库的二进制文件，并将其添加到输出文件夹下<a href="#note_16" id="note_16_sup"><sup>[16]</sup></a>。

<details>
    <summary>Qt for Windows 与 OpenSSL 的版本兼容关系如下表（点击展开）：</summary>

<br>

注意：出现“更高版本” Qt 使用更低版本 OpenSSL ，是因为同时有多个分支的 Qt 在进行开发。

| Qt for Windows | OpenSSL |
|:- |:- |
| ~5.2 | Unknown |
| [5.3.0](https://wiki.qt.io/Qt_5.3_Tools_and_Versions#Qt5.3.0_Tools_.26_Versions) | 1.0.1g |
| [5.4.0](https://wiki.qt.io/Qt_5.4_Tools_and_Versions#Software.27s_used_for_making_Qt_5.4.0_release_packages) | 1.0.1j |
| [5.5](https://wiki.qt.io/Qt_5.5_Tools_and_Versions#Software_configurations_with_Qt_5.5) | 1.0.2
| [5.6.0](https://wiki.qt.io/Qt_5.6_Tools_and_Versions#Software_configurations_with_Qt_5.6) LTS for Windows 7 X86 and Windows 8.1 X64 | 1.0.1e<a href="#note_15" id="note_15_sup"><sup>[15]</sup></a> |
| [5.6.0](https://wiki.qt.io/Qt_5.6_Tools_and_Versions#Software_configurations_with_Qt_5.6) LTS for other Windows | 1.0.2d |
| [5.6.1](https://wiki.qt.io/Qt_5.6_Tools_and_Versions#Software_configurations_with_Qt_5.6) LTS | 1.0.2g |
| [5.8](https://wiki.qt.io/Qt_5.8_Tools_and_Versions#Software_configurations_with_Qt_5.8) | 1.0.2h |
| [5.9.1](https://wiki.qt.io/Qt_5.9_Tools_and_Versions#Software_configurations_for_Qt_5.9.1) LTS for Windows 10 X64 | 1.0.2j |
| [5.9.2](https://wiki.qt.io/Qt_5.9_Tools_and_Versions#Software_configurations_for_Qt_5.9.2) LTS for Windows 8.1 X64 | 1.0.2j |
| [5.9.3](https://wiki.qt.io/Qt_5.9_Tools_and_Versions#Software_configurations_for_Qt_5.9.3) LTS for Windows 7 X86 and Windows 8.1 X86 | 1.0.2j |
| [5.9.6](https://wiki.qt.io/Qt_5.9_Tools_and_Versions#Software_configurations_for_Qt_5.9.6) LTS | 1.0.2j |
| [5.10.0](https://wiki.qt.io/Qt_5.10_Tools_and_Versions#Software_configurations_for_Qt_5.10.0) for Windows 10 X86 | 1.0.2h |
| [5.11.3](https://wiki.qt.io/Qt_5.11_Tools_and_Versions#Software_configurations_for_Qt_5.11.3) | 1.0.2p |
| [5.12.9](https://wiki.qt.io/Qt_5.12_Tools_and_Versions#Software_configurations_for_Qt_5.12.9) LTS | 1.1.1d |
| [5.13.0](https://wiki.qt.io/Qt_5.13_Tools_and_Versions#Software_configurations_for_Qt_5.13.0) | 1.1.1b |
| [5.14](https://wiki.qt.io/Qt_5.14_Tools_and_Versions) | 1.1.1b |
| [5.15](https://wiki.qt.io/Qt_5.15_Tools_and_Versions) LTS | 1.1.1d |

</details>

<br>

[Binaries - OpenSSLWiki](https://wiki.openssl.org/index.php/Binaries) 上有一个表格，包含一些提供预编译的 OpenSSL 库文件的**第三方**网站的链接。为了避免 OpenSSL 库本身的依赖所造成的问题（例如 [BesLyric-for-X#27](https://github.com/BesLyric-for-X/BesLyric-for-X/issues/27) ），同时结合库文件的获取难易程度，经过对比，最终选择了用于 ICS 项目的 OpenSSL 库文件。

**注意：使用这些 OpenSSL 衍生产品，后果自负。这些产品尚未经过 OpenSSL 项目的评估或测试。**<a href="#note_11" id="note_11_sup"><sup>[11]</sup></a>

<br>

OpenSSL 的 1.0 与 1.1 版本并非二进制兼容<a href="#note_12" id="note_12_sup"><sup>[12]</sup></a>，本项目仅使用 OpenSSL 1.1 。

- 下载用于 64 位 Windows 的 [OpenSSL](http://wiki.overbyte.eu/wiki/index.php/ICS_Download#Download_OpenSSL_Binaries_.28required_for_SSL-enabled_components.29) ；
- 对于动态库文件，从压缩包中提取`libcrypto-1_1-x64.dll`和`libssl-1_1-x64.dll`文件到`%B4X_DEP_PATH%\win64\bin\`下。

<details>
    <summary>完整的 OpenSSL 1.1 库文件的文件夹结构如下（点击展开）：</summary>

<br>

```cmd
> tree /A /F %B4X_DEP_PATH%
%B4X_DEP_PATH%
\---bin
       libcrypto-1_1-x64.dll
       libssl-1_1-x64.dll
```

</details>

<br>

<details>
    <summary>使用 <a href="https://github.com/lucasg/Dependencies">lucasg/Dependencies</a> 查看依赖（点击展开）：</summary>

<br>

```cmd
> Dependencies_x64_Release_22298477> .\Dependencies.exe -imports %B4X_DEP_PATH%\dll\libcrypto-1_1-x64.dll
[-] Import listing for file : %B4X_DEP_PATH%\dll\libcrypto-1_1-x64.dll
Import from module WS2_32.dll :
         ...
Import from module ADVAPI32.dll :
         ...
Import from module USER32.dll :
         ...
Import from module bcrypt.dll :
         ...
Import from module KERNEL32.dll :
         ...
[-] Import listing done

> Dependencies_x64_Release_22298477> .\Dependencies.exe -imports %B4X_DEP_PATH%\dll\libssl-1_1-x64.dll
[-] Import listing for file : %B4X_DEP_PATH%\dll\libssl-1_1-x64.dll
Import from module libcrypto-1_1-x64.dll :
         ...
Import from module KERNEL32.dll :
         ...
[-] Import listing done
```

</details>

<br>

## 3. 脚注

1. <a href="#note_1_sup" id="note_1">^</a> 在 Windows 10 1809 上进行调试时输出大量`Invalid parameter passed to C runtime function.`的问题。<br>https://blog.csdn.net/u014132751/article/details/91876385<br>https://bugreports.qt.io/browse/QTBUG-70917<br>https://developercommunity.visualstudio.com/content/problem/363323/getadaptersaddresses-invalid-parameter-passed-to-c.html<br>https://blog.csdn.net/u014132751/article/details/91876385
2. <a href="#note_2_sup" id="note_2">^</a> Windows 10 的生命周期。<br>https://docs.microsoft.com/windows/release-information/
3. <a href="#note_3_sup" id="note_3">^</a> Visual Studio 的生命周期。<br>https://docs.microsoft.com/visualstudio/releases/2019/servicing#support-for-older-versions-of-visual-studio
4. <a href="#note_4_sup" id="note_4">^</a> Qt 5 的生命周期。<br>https://en.wikipedia.org/wiki/Qt_version_history#Qt_5
5. <a href="#note_5_sup" id="note_5">^</a> C++ 二进制兼容性 (2015-2019)。<br>https://docs.microsoft.com/cpp/porting/binary-compat-2015-2017
6. <a href="#note_6_sup" id="note_6">^</a> Qt Visual Studio Tools - Visual Studio Marketplace<br>https://marketplace.visualstudio.com/items?itemName=TheQtCompany.QtVisualStudioTools2019
7. <a href="#note_7_sup" id="note_7">^</a> 使用 Visual Studio Installer 进行命令行安装。<br>https://docs.microsoft.com/visualstudio/install/use-command-line-parameters-to-install-visual-studio<br>https://docs.microsoft.com/visualstudio/install/workload-component-id-vs-community
8. <a href="#note_8_sup" id="note_8">^</a> *The debugger that comes with Debugging Tools for Windows goes by the name [WinDbg](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/debugger-download-tools), short for Windows Debugger, and generally pronounced win-debug or win-dee-bee-gee. It’s part of a suite of lightweight debuggers, along with `ntsd` (short for NT Symbolic Debugger) and `cdb` (Console Debugger), which are all based on the same debugging engine, creatively named `DbgEng`...*<br>How do you pronounce WinDbg? | The Old New Thing<br>https://devblogs.microsoft.com/oldnewthing/20200128-00/?p=103371
9. <a href="#note_9_sup" id="note_9">^</a> Microsoft Windows SDK - Wikipedia<br>https://en.wikipedia.org/wiki/Microsoft_Windows_SDK
10. <a href="#note_10_sup" id="note_10">^</a> Qt Tools and Versions &amp; MSVC 2019 | Qt Forum<br>https://forum.qt.io/topic/104123/qt-tools-and-versions-msvc-2019
11. <a href="#note_11_sup" id="note_11">^</a> *Use these OpenSSL derived products at your own risk; these products have not been evaluated or tested by the OpenSSL project.*<br>Binaries - OpenSSLWiki<br>https://wiki.openssl.org/index.php/Binaries
12. <a href="#note_12_sup" id="note_12">^</a> *...Unfortunately OpenSSL 1.1 is binary incompatible with 1.0, so users need to switch to the new one and repackage their applications...*<br>Qt 5.12.4 Released with support for OpenSSL 1.1.1<br>https://www.qt.io/blog/2019/06/17/qt-5-12-4-released-support-openssl-1-1-1
13. <a href="#note_13_sup" id="note_13">^</a> *...It's not handled because by default, OpenSSL is dlopened and Qt doesn't ship OpenSSL because of international laws about cryptographic software...*<br>How to properly include openssl for a windows deployment when using mingw? | Qt Forum<br>https://forum.qt.io/topic/86420/how-to-properly-include-openssl-for-a-windows-deployment-when-using-mingw/2<br>*By default Qt is build to dlopen the OpenSSL dlls. This allows Qt to be build to support cryptographie while letting the developer handle all related paperwork for its application to be distributed (different countries have different rules regarding cryptographically enabled software).*<br>Windows and OpenSSL Woes | Qt Forum<br>https://forum.qt.io/topic/91255/windows-and-openssl-woes/12
14. <a href="#note_14_sup" id="note_14">^</a> Legal Restrictions on Cryptography - Web Security, Privacy &amp; Commerce, 2nd Edition [Book]<br>https://www.oreilly.com/library/view/web-security-privacy/0596000456/ch04s04.html
15. <a href="#note_15_sup" id="note_15">^</a> *The affected versions of OpenSSL are OpenSSL 1.0.1 through 1.0.1f (inclusive). Subsequent versions (1.0.1g and later) and previous versions (1.0.0 branch and older) are not vulnerable...*<br>Affected OpenSSL installations # Heartbleed - Wikipedia<br>https://en.wikipedia.org/wiki/Heartbleed#Affected_OpenSSL_installations
16. <a href="#note_16_sup" id="note_16">^</a> *The application may require additional 3rd-party libraries (for example, database libraries), which are not taken into account by windeployqt...If Qt was configured to link against ICU or OpenSSL, the respective DLL's need to be added to the release folder, too.*<br>Qt for Windows - Deployment | Qt 5.14<br>https://doc.qt.io/qt-5/windows-deployment.html#creating-the-application-package
17. <a href="#note_17_sup" id="note_17">^</a> git - 维基百科，自由的百科全书<br>https://zh.wikipedia.org/wiki/Git
18. <a href="#note_18_sup" id="note_18">^</a> SDL - 维基百科，自由的百科全书<br>https://zh.wikipedia.org/wiki/SDL
19. <a href="#note_19_sup" id="note_19">^</a> FFmpeg首页、文档和下载 - 多媒体处理工具 - OSCHINA<br>https://www.oschina.net/p/ffmpeg
20. <a href="#note_20_sup" id="note_20">^</a> *Windows 10 development targeting Windows 10, version 2004 (or later) requires Visual Studio 2017 (or later). This SDK will not be discovered by previous versions of Visual Studio.*<br>Windows 10 SDK - Windows app development<br>https://developer.microsoft.com/windows/downloads/windows-10-sdk/
21. <a href="#note_21_sup" id="note_21">^</a> OpenSSL - 维基百科，自由的百科全书<br>https://zh.wikipedia.org/wiki/OpenSSL
22. <a href="#note_22_sup" id="note_22">^</a> OpenSSL # Qt 5.15.0 Known Issues - Qt Wiki<br>https://wiki.qt.io/Qt_5.15.0_Known_Issues#OpenSSL
23. <a href="#note_23_sup" id="note_23">^</a> [QTBUG-62637] [Windows]: Add support for using Secure Channel for SSL sockets - Qt Bug Tracker<br>https://bugreports.qt.io/browse/QTBUG-62637
24. <a href="#note_24_sup" id="note_24">^</a> *Windows: Secure Channel support for SSL socket (QTBUG-62637)*<br>New Features in Qt 5.13 - Qt Wiki<br>https://wiki.qt.io/New_Features_in_Qt_5.13
25. <a href="#note_25_sup" id="note_25">^</a> [QTBUG-82876] [Windows]: Switch default builds to use Secure Channel for SSL sockets - Qt Bug Tracker<br>https://bugreports.qt.io/browse/QTBUG-82876
26. <a href="#note_26_sup" id="note_26">^</a> How to Find Your Windows 10 Build Number, Version, Edition and Bitness<br>https://www.winhelponline.com/blog/find-windows-10-build-version-edition-bit/
27. <a href="#note_27_sup" id="note_27">^</a> Windows: Get Windows Feature Version/Release ID<br>https://michlstechblog.info/blog/windows-get-windows-feature-version-release-id/

## 4. 参考

- https://doc.qt.io/qt-5/windows.html
- https://stackoverflow.com/questions/47773289/debugging-in-qtcreator-using-msvc2017-compiler

## 5. 其他

- 本文档在 VSCode 上使用 [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) 辅助编辑；
- 本文档中嵌套表格使用 [HTML Table generator - TablesGenerator.com](https://www.tablesgenerator.com/html_tables) 辅助编辑。
