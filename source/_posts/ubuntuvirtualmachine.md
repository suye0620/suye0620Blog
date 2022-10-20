---
title: 在Windows上使用VMware配置一台Ubuntu虚拟机
categories:
  - Article
tags:
  - Linux
excerpt: ''
math: true
date: 2022-10-20 00:04:18
---

<!-- 标题1-#已经被上面使用，故从2级标题开始 -->
## 折腾步骤
1. 汉化和软件源
2. 输入法ibus-拼音
3. 虚拟机重启主机蓝屏问题
4. 使用clash连接外网
5. GitHub的配置和使用
6. VSCode自动补全提示功能
7. miniconda安装
8. pandoc安装
9. 字体修改(中文看着真难受)
10. B站看视频

## 细节

> 我使用的是VMware虚拟机平台，装载的是Ubuntu20.04LTS版本(Gnome)。选择VM是因为Windows自带的Hyper-V太垃圾，不支持外接USB，且其全屏支持异常鸡肋。

### 汉化和软件源

当你在VMware中装载的是常用Linux版本(比如Ubuntu)时，VMware会使用快速安装模式，即由VM控制初始的引导和启动。在这样的情况下，Ubuntu初始语言是英语，不适合我们日常的折腾使用。因此我们需要在**设置**中`区域与语言`中进行汉化。推荐先汉化语言，然后立即注销/重启，再切换时区。注销/重启后，我们再使用命令行或者在**软件更新器**中切换成中国源(我用的Tencent)

### 输入法ibus

我之前折腾过基于Arch的Manjaro发行版，其初始KDE桌面预置fcitx输入法框架，bug挺多的。20年那会儿装fcitx-sougou输入法还会有内存泄漏的问题。现在使用Ubuntu，竟然有一丝窃喜，在**设置**中`区域与语言`中汉化后我们可以直接选择基于ibus的拼音，少了折腾，保护了头发。美中不足的就是输入法初始的字体有点小，需要折腾gnome-tweak进行配置。

### 虚拟机重启主机蓝屏问题

> Vmware Ubuntu虚拟机提示：无法连接虚拟设备 sata0:1,因为主机上没有相对应的设备，解决办法

折腾完虚拟机汉化后，我在VM中关闭了虚拟机，结果被摆了一道。因为VMware虚拟机第一次安装挂载于CD-ROM，第二次启动时就会找不到虚拟系统启动的位置(可能是Win10下的bug)。此时Windows会报错然后蓝屏，解决的方法是**不要点击VM报错的对话框**，而是**在windows任务管理器中关闭VMware**。重启VM，然后[按照此文操作](https://blog.csdn.net/qqLML_ZR/article/details/107236827)。

### 使用clash连接外网

> 我需要使用Github，但是校园网内Github是被墙的，这时我需要使用代理。我的VPN使用的Clash软件。

Clash详细配置参见ikuuu VPN的教程。在Linux下实现Clash的开机自启动我没有搞出来，所以我的替代方案是写了个sh脚本，每次登陆桌面的时候`source`加载一下，倒也不是很麻烦。

```bash
# !/bin/bash
# change dir(这下面要改成自己clash脚本存放的位置)
cd /home/suye0620/Documents/clash/
# start
sudo ./clash -d .
```

有的时候clash自己的默认端口会被其他linux软件占用，这个真是玄学，我不懂哪些软件开发者，脑子有坑会使用7890这样稀有的端口号进行通讯！进而影响哥的科学上网！clash配置完需要注销/重启虚拟机。

### GitHub使用

> Ubuntu居然没有预置git！差评！GNU头号软件难道不配在初始安装包里占有一席之地吗？

远程仓库的配置参见[廖雪峰的git教程](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)。因为挂了梯子，所以要[设置git的全局代理](https://blog.csdn.net/qq_37124515/article/details/114190310)。

```
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy http://127.0.0.1:1080
```

在VSCode中同步GitHub设置可能需要重启几次，反正一开始配置完远程仓库和全局代理后，我的`git push`无法正常使用。但重启了虚拟机好像就好了，谜！

### VSCode的自动补全提示功能

> 在Windows下我日常使用VSCode进行程序编写，所以在Linux依然选择使用VSCode。但是，Ubuntu软件商店中的VSCode居然不支持中文！是的，它被阉割过了。

我发现VSCode的自动补全提示功能在Linux下是正常的，但是在Windows下由于Hot Keys的占用，我们无法用默认的`Ctrl+Space`触发自动补全提示功能。因此，我们可以[更改VSCode的快捷键映射](https://blog.csdn.net/TianJun_Li/article/details/88703188#:~:text=VSCode%E7%9A%84%E4%BB%A3%E7%A0%81%E6%8F%90%E7%A4%BA%E5%BF%AB%E6%8D%B7%E9%94%AE%E6%98%AF,Ctrl%2Bspace%EF%BC%8C%E5%9B%A0%20%E5%92%8Cwindows%E7%B3%BB%E7%BB%9F%E9%BB%98%E8%AE%A4%E8%AE%BE%E7%BD%AE%E5%86%B2%E7%AA%81%EF%BC%8C%E6%97%A0%E6%B3%95%E4%BD%BF%E7%94%A8%E9%9C%80%E8%A6%81%E4%BF%AE%E6%94%B9%EF%BC%88%E4%BF%AE%E6%94%B9%E4%B8%BA%E6%9C%AC%E4%BA%BA%E4%B9%A0%E6%83%AF%E7%9A%84Alt%2B%2F%EF%BC%89%EF%BC%9B)。

### miniconda和pandoc的安装

> 这篇分享是我用Hexo博客框架在虚拟机上写的。Hexo的使用需要pandoc，但是因为apt包管理的依赖的问题，我没有装好。我想到了在Windows下我是用Anaconda装的pandoc，所以照葫芦画瓢，我先安装conda，然后用conda装pandoc不就行了？

其实经常使用conda的话，会知道其实没必要装Anaconda，那么多包其实实用的并不多(说的就是你，Spyder！还有base环境中乱七八糟的包！)我选择使用Miniconda。当然要使用Miniconda带的Python版本，需要重启安装终端(重开一个终端)。

1. [Miniconda下载](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)
2. [Miniconda扫盲和使用](https://blog.csdn.net/qq_42951560/article/details/109152114)：这里面的配置教程是参考的清华大学开源镜像站的说明，最新的配置可到1中的网站去查看
3. [使用conda安装pandoc](https://zhuanlan.zhihu.com/p/258912543)

### Gnome桌面字体修改

> 垃圾Gnome，没有中文字体支持就算了，还没有放修改字体的接口。默认中文字体巨丑，无语

我们需要安装GNOME Tweak Tool来实现桌面字体的改变。但是这里我遇到了问题，apt包管理依然存在依赖的问题。后来我换成aptitude包管理，使用其他的依赖处理方案，成功安装了GNOME Tweak Tool。

1. [aptitude扫盲和使用](https://blog.csdn.net/qq_16759959/article/details/103370681)
2. [GNOME Tweak Tool](https://blog.csdn.net/qq_35395195/article/details/125266461)
3. [微软雅黑字体的下载](https://ztxz.org.cn/weiruan/2609.html)

### B站看视频

>Ubuntu初始情况下居然看不了B站，好像是因为初始安装包中没有解码的东西，离大谱！

[解决方案](https://blog.csdn.net/qq_25298677/article/details/115800994)

```bash
sudo apt-get install ubuntu-restricted-extras
```

其中包含各种基本软件，如 Flash 插件、unrar、gstreamer、mp4、Ubuntu 中的 Chromium 浏览器的编解码器等的软件包



