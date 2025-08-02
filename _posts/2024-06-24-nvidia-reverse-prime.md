---
layout: post
title:  "设置了一下 Reverse PRIME"
date:   2024-06-25 09:00:00 +0800
toc: true
categories: linux 
excerpt_separator: <!--more-->
tags: 
  - Linux
  - Nvidia
  - X11
image: /assets/2024-06-24-nvidia-reverse-prime/image.webp
---

## 起因

虽然我的笔记本只有一个 3050，但是我还是觉得它至少能完成硬件加速的任务——于是用 `prime-run` 跑 genymotion。性能确实变好了，只是在使用外接屏幕的我似乎感受到了一定的随机卡顿/掉帧的问题。  

搜了一圈发现也没什么很有效的方案，只能合理怀疑是 PRIME 的机制再加上 X11 一些落后机制造成随机卡顿？

考虑到现在懒得切换到 Wayland，于是只能从 PRIME 上下手了：折腾一下 Reverse PRIME 吧。

<!--more-->

## Reverse PRIME 是什么

最通俗（和扭曲）的理解大概就是好好利用 N 卡的性能。  

ArchWiki 是这么说的：  

>If the second GPU has outputs that are not accessible by the primary GPU, you can use Reverse PRIME to make use of them. This will involve using the primary GPU to render the images, and then pass them off to the second GPU. 

## 启用 Reverse PRIME

先说好这里肯定不是最佳实践。其次参照了 Nvidia 的[文档](https://us.download.nvidia.com/XFree86/Linux-x86_64/550.54.14/README/randr14.html) 的 To use the NVIDIA driver as an RandR 1.4 output sink provider 部分。  

1. `/etc/X11/xorg.conf`

    还是先直接上配置：
    ```
    Section "ServerLayout"
            Identifier "layout"
            Screen 0 "amdgpu"
            Inactive "nvidia"
            Option "AllowNVIDIAGPUScreens"
    EndSection

    Section "Device"
            Identifier "nvidia"
            Driver "nvidia"
    EndSection

    Section "Screen"
            Identifier "nvidia"
            Device "nvidia"
    EndSection

    Section "Device"
            Identifier "amdgpu"
            Driver "modesetting"
            BusID "PCI:6:0:0"
    EndSection

    Section "Screen"
            Identifier "amdgpu"
            Device "amdgpu"
    EndSection
    ```  

    这个配置首先是用 `sudo nvidia-xconfig` 生成然后修改的，添加了
    ```
    Section "Device"
            Identifier "amdgpu"
            Driver "modesetting"
            BusID "PCI:6:0:0"
    EndSection

    Section "Screen"
            Identifier "amdgpu"
            Device "amdgpu"
    EndSection
    ```
    这里的 BusID 需要自己查看本地的情况，比如使用 `lspci | grep VGA`。只要 Driver 是 modesetting 就好了，名字反而是随意了。  

2. `~/.xinitrc`

    设置好上面的部分已经完成主要工作了，接下来需要设定的是启用一些相关的设置，还是先看配置

    ```sh
    #!/bin/sh

    # /etc/X11/xinit/xinitrc
    #
    # global xinitrc file, used by all X sessions started by xinit (startx)

    xrandr --setprovideroutputsource NVIDIA-0 modesetting
    xrandr --auto
    xrandr --output eDP-1-1 --primary

    # invoke global X session script
    #. /etc/X11/Xsession
    dbus-update-activation-environment --systemd DBUS_SESSION_BUS_ADDRESS DISPLAY XAUTHORITY
    xrdb ~/.Xresources
    exec i3
    ```  

    我使用 xinitrc 来启动 i3，于是就在这里启动 i3 之前先设置了这几个命令

    ```sh
    xrandr --setprovideroutputsource NVIDIA-0 modesetting
    xrandr --auto
    xrandr --output eDP-1-1 --primary
    ```

    首先是设置 Nvidia 为 output sink，然后是设置笔记本的屏幕为主显示器。  

主要的设置就是上面两个，顺带还有一个额外效果：我的 Firefox 硬件加速正常了。这部分的设置除了 ArchWiki 还可以参考 nvidia-vaapi-driver 的 [README](https://github.com/elFarto/nvidia-vaapi-driver/#firefox)。

|![alt text](/assets/2024-06-24-nvidia-reverse-prime/image.webp)|
|:--:|
|现在终于用上 Nvidia 的硬件加速了|