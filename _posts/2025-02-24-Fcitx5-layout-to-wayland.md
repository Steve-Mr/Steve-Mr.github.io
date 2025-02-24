---
layout: post
title: "「不支持由 Fcitx 将键盘布局配置发送给 wayland 混成器」的一个解决思路"
date: 2025-02-24 22:00:00 +0800
sitemap:
  lastmod: 
toc: true 
excerpt_separator: <!--more-->
tags:
  - Linux
  - ArchLinux
  - Sway
  - Wayland
  - Fcitx5
---

> 当前桌面不支持由 Fcitx 将键盘布局配置发送给 wayland 混成器。您仍可以通过将布局作为输入法添加到输入法分组来使用 Fcitx 内部的布局转换。

检查一下输入法分组。

<!--more-->

# 问题

在迁移到 Sway 之后，每次开机都会弹出来 `"Sending keyboard layout configuration to wayland compositor from Fcitx is not yet supported on current desktop. You may still use Fcitx's internal layout conversion by adding layout as input method to the input method group."` 的通知，虽然不影响使用，但总是很烦。  

按着这里的说明看了看输入法列表里是有键盘布局的，于是没了头绪。  

# 思路

昨天终于忍无可忍了，于是直接去搜相关的源代码，然后搜到了这个：  

<iframe frameborder="0" scrolling="no" style="width:100%; height:247px;" allow="clipboard-write" src="https://emgithub.maary.top/iframe.html?target=https%3A%2F%2Fgithub.com%2Ffcitx%2Ffcitx5%2Fblob%2Fa8b2084771a285daf96ff0649834637f73287bc5%2Fpo%2Fzh_CN.po%23L2328-L2335&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

好的，这里指向了使用到这个错误信息的代码段：`src/modules/wayland/waylandmodule.cpp:680`。  

接下来就是直接看这个代码段了：  

<iframe frameborder="0" scrolling="no" style="width:100%; height:604px;" allow="clipboard-write" src="https://emgithub.maary.top/iframe.html?target=https%3A%2F%2Fgithub.com%2Ffcitx%2Ffcitx5%2Fblob%2Fa8b2084771a285daf96ff0649834637f73287bc5%2Fsrc%2Fmodules%2Fwayland%2Fwaylandmodule.cpp%23L672-L696&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

这里它检测到 `groupLayouts` 集合中包含两个或更多的不同布局类型，表示可能存在布局不兼容的问题，于是就会触发这个警告。  

我看了一下确实有个第二分组，由于我其实永不到这个分组于是直接删掉了，然后不管是重启电脑还是重启 Fcitx5 都不再有这个告警了。  

# 但还是有问题

首先这里说「一个解决思路」就是我现在没办法复现这个 bug 了，因此也可能是我触发了其他什么东西导致这个告警不再出现了，因此这里只能算是猜测。  

第二点则是现在仍然遇到 Xwayland 下候选词框随机消失的情况，在写这个的时候连着装了 `voikko nuspell hspell aspell` 几个包，虽然从命令行跑 Fcitx5 没有报错了，但是似乎 Xwayland 下候选词框随机消失仍然存在，这个可能还是等将来找到更具体的原因或者是等 vscode 迁移到 Electron 34 能在 Wayland 模式下支持 v3 输入协议吧。

