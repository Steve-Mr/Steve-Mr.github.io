---
layout: post
title:  "再见 Ubuntu 👋"
date:   2024-06-19  09:00:00 +0800
toc: true
categories: jekyll 
excerpt_separator: <!--more-->
tags: 
   - Linux
   - Ubuntu
   - ArchLinux
   - BTRFS
---

终于下定决心从 Ubuntu 迁移到 Arch Linux 上了！  
虽然其实要做的事情还有很多。

![Arch!](/assets/2024-06-16-goodbye-ubuntu/1718541975.png){:width=500} 

<!--more-->

# 之前的状态

实际上往回看，我断断续续的十多年 Linux 相关经验全是 Ubuntu 系的：Ubuntu、Kubuntu、Ubuntu Gnome、Elementary OS、Ubuntu Mate。甚至当年最早开始摸 Ubuntu 还是用的所谓 "Wubi" 安装器：像安装一个 exe 一样在 Windows 下安装 Ubuntu，甚至 Ubuntu 使用的文件系统都是 NTFS。此外卸载的时候也是在 Windows 的控制中心卸载。  

| ![alt text](/assets/2024-06-16-goodbye-ubuntu/image.png){:width=500} |
| :-------------------------------------: |
|*Wubi 安装器的界面，图源[官方帮助页面](https://help.ubuntu.com/community/Wubi)*|

一直到现在真正日用 Linux 并且各种折腾，但还是在用 Ubuntu，之前由于要使用 ROS 2 的缘故，停在了 Ubuntu 22.04 版本。前一段新的 LTS 24.04 终于发了，于是花了一下午终于升级了上去。  

# 原因

Ubuntu 是很成熟的发行版了，在数落 Ubuntu 之前还是念点它的好。  

首先是图形化的安装界面，除了手动分区会麻烦一点（但是现在即使分区也有自动安装成双系统的选项，不用手动分区）。  

|![alt text](/assets/2024-06-16-goodbye-ubuntu/image-1.png){: width=300 } ![alt text](/assets/2024-06-16-goodbye-ubuntu/image-2.png){: width=300 } |
| :---: |  
|*24.04 正式启用的基于 Flutter 的安装器。图源 [OMG Ubuntu](https://www.omgubuntu.co.uk/2024/04/ubuntu-24-04-released)*|

然后是对于 Nvidia 的驱动，Ubuntu 有可以说一键式傻瓜化的安装方式，虽然 N 卡驱动犯病不可避，但是就我自己的体验来说基本上没有出现过安装驱动出现问题。  

最后是足够多的发行版和线上的资源，能够给你足够多的选择，遇到的问题通常也已经有人解答过了。

**那为什么我要切换到 Arch 呢？**  

1. Ubuntu 本身的限制  
虽然 Ubuntu 有这样那样的好处，但是其作为一个有 20 年历史的 Debian 分支，难免存在一些历史遗留和上游传下来的问题。  

   - 点版本更新
      Ubuntu 采用的是传统的点版本更新方式，正如你看到的 Ubuntu 24.04 是指在 2024 年 4 月发布的版本。点版本更新类似于 Windows/macOS 的更新模式：每隔一段时间（在 Ubuntu 下基本上是半年）发布一个版本更新，用户在日常更新软件之外需要专门去更新系统。

      相信大多数人都习惯这样的更新模式了，但是在 Linux 环境下这样的更新模式对于**想体验新特性**的用户是有一点问题的。与其他的闭源系统不同，Linux 的内核、桌面环境以及必要的组件大多数情况下都是开源且独立更新的。比如 Ubuntu 23.10 发布的时候使用了 Linux 6.5 和 Gnome 45，那么当 Linux 6.6 / Gnome 46 更新时，Ubuntu 并不会及时更新到这些版本上，而是仅提供 Linux 6.5 和 Gnome 45 的安全更新等内容。直到下一个大版本更新才会更新内核和桌面环境之类关键软件的版本。  

      但是还不只是新特性的问题，点版本更新的系统中的系统的库和工具不够新，导致其在很多时候也**吃不上最新版本的软件**。

      |![alt text](/assets/2024-06-16-goodbye-ubuntu/image-3.png)|
      | :--: |
      |[Fcitx5 的版本状态](https://github.com/fcitx/fcitx5)，即使是最新的 Ubuntu 24.04 上 Fcitx5 的版本也是落后的|  

      当然了问题总是有解决办法的，比如一些开发者会有自己维护的 PPA（软件更新源），或者通过 Flakpak、Snap、AppImage 等方式来直接绕开包管理系统，这样的方式通常能获得最新的软件。但是这里也有一些问题，后面骂 Canonical 的时候会提到。以及最终方式：自己编译，之前用 Ubuntu 真的是没少编译。把 Ubuntu 用成 Gentoo 的样子了。（悲

   - APT

      APT 是 Ubuntu 的默认包管理器，从 Debian 继承下来的。APT 整体上来说还是简明好用的，依赖地狱属于一个你跺你也麻的事情。  

      至于版本问题上面也提到了，还有一个问题是我更新 Ubuntu 24.04 才想起来的：更新系统之后所有的第三方 PPA 都会被禁用，倒是能理解为了能避免安全问题或者是兼容性问题，但是更新完要处理这些 PPA 会很讨厌。


2. Canonical 的倒行逆施

   上面的还是所谓的 Ubuntu 作为一个点版本更新的 Debian 分支的发行版难以避开的问题，那么现在要说的则是 Ubuntu 背后的开发商 Canonical 带来的问题。

   - 隐私问题  

      我其实并不太关心系统的信息收集的问题，但是依然涉及到 Canonical 还是要提一句。在当年 Ubuntu 迁移到 Unity 桌面的时候，Canonical 在搜索引擎中引入了和亚马逊的合作，于是不可避免的引发了对其隐私政策的担忧。  

   - Snap vs APT  

      绕开包管理器使用类似容器之类的打包方式确实是一个解决 Linux 相当碎的包管理器问题的办法。Snap 自身的问题虽然也带来了不少的质疑，但是我其实也不是很关心，只要好用就不是问题，但是如果你以**破坏**原有的包管理器的方式来推广自己的 Snap 就有点问题了。  

      这里说的就是 Firefox，Ubuntu 在 21.10 中将预装的 Firefox 变成了 Snap 版本，而当时的 Snap 版本的 Firefox 问题重重，先不说在容器中带来的和外部软件的关联问题，仅仅是漫长的启动时间就难以忍受，冷启动可以耗 10s 以上（似乎最近的版本变好了，但是我没兴趣测试）。到这里依然可以接受，你预装的不好用，我自己换成 APT 版本就好了嘛。然后发现卸载了 Snap 版本的 Firefox 再 `sudo apt install firefox` 就会发现他最终还是会安装 Snap 版本，他们直接在官方软件源里做了手脚，这种方式真的有点恶心了。  

      更糟糕的部分是，你可以通过安装 Mozilla 官方的 APT 源来安装 deb 版本的 Firefox，但是 Ubuntu 依然会寻找机会偷偷给你更新成 Snap 版本，我在前面一周里遇上了两三次，最终无奈直接用了二进制版本。

   - Ubuntu Pro

      Ubuntu Pro 其实是个不错的服务，能够提供额外五年的安全更新。除了他们的提供方式出了问题：当你在终端中使用 APT 命令的时候会弹出 Ubuntu Pro 的广告，简直是微软行为。这样的处理方式自然招来了不少的批评。

# 迁移

前面骂了这么多 Ubuntu 但其实这里才应该是重头戏的。  

迁移的目标是 Arch Linux，比较热门的滚动更新发行版：几乎不用担心软件的版本是旧的，而是需要担心给系统滚炸了。

|![alt text](../assets/2024-06-16-goodbye-ubuntu/image-4.png){: width=400} |
| :--: |
| 当然有很多系统选择的 Meme |

## 事先准备

虽然我使用 Ubuntu 的过程中还蛮纯 GUI 的，但是多少还是多少积攒了一些知识。比如之前安装 Ubuntu 22.04 的时候想起来用上了 BTRFS，这次给我帮大忙了。

1. 测试

   > Nvidia, Fuck you! --Linus Torvalds

   其实我老早之前摸过一次 Arch，不过感觉要自己去装所有软件有点麻烦。这次虽然下定决心了，但是还是对于 Nvidia 驱动存在各种嘀咕，因此划了个分区先按着 Arch Wiki 教程装了一下。虽然确实是换了发行版很多地方绕了不少弯路，但是最后是整个流程跑通了，也就基本上放心了。

2. 计划

   迁移有一个问题：**重装系统很麻烦。**  

   这里就是 BTRFS 起作用的地方了，我之前在 BTRFS 里设置了两个子卷：@ 和 @home，@ 用于根目录，@home 用于自己的文件夹目录。子卷类似于在 BTRFS 中设置了分区，相对独立。  
   
   于是我计划保存 @home 继续作为主文件夹，只更换根目录。跟朋友讨论了一下决定在安装的时候新建一个 @root 作为新的根文件夹，这样之前 Ubuntu 在根文件夹中的文件也能保存，需要的时候挂载起来还能找回来。

## 安装 Arch
安装 Arch Liunx 还是要参照 [Arch 官方安装文档](https://wiki.archlinux.org/title/Installation_guide)，当然在这里我有前面提到的需求所以和文档有一定的区别。因为要涉及到 Btrfs 因此还参考了这个[教程](https://gist.github.com/mjkstra/96ce7a5689d753e7a6bdd92cdc169bae)  

事实上整体的流程和官方文档无异，主要在于挂载硬盘这部分。  

1. 挂载硬盘
   挂载硬盘是为了接下来能够创建子卷：  
   `mount /dev/nvme1n1p1 /mnt`  
   具体的硬盘/分区名称要看类似 `fdisk -l` 的结果。
2. 创建子卷
   ```
   btrfs subvolume create /mnt/@root
   umount /mnt
   ```  
3. 挂载子卷
   现在需要再次挂载子卷，因为后面会 `arch-chroot` 到 `/mnt` 下所以直接将 `@root` 挂载到这里：  
   `mount -o compress=zstd,subvol=@root /dev/nvme1n1p1 /mnt`  
   然后挂载主目录：
   ```
   mkdir -p /mnt/home
   mount -o compress=zstd,subvol=@home /dev/nvme1n1p1 /mnt/home
   ```
   至于挂载 efi 分区和教程一致。  

## 装好之后

实际上整体安装过程比我印象中的还要简单一些。完成基本系统的安装就是首先装了一整套 gnome 环境然后安装了 greetd 和 i3wm。接下来就是重头戏 Nvidia 了。  

Nvidia 驱动的安装还是参考[官方 Wiki](https://wiki.archlinux.org/title/NVIDIA)，虽然我装的是 `linux` 包但是还是选择了 `nvidia-dkms`。然后就是在 grub 设置 modeset 参数、按照文档尝试设置了硬件加速之类的东西。比起来之前测试的时候完全没有头绪来说顺畅了许多。  

# 但其实事情还多呢

虽然现在已经完成了迁移，但也就只是迁移了。首先还缺少一些软件，比如昨天想起来没有 GIMP，现在也还没装 Steam 或者 Heroic Launcher。还有一些更重要的地方是我现在在用 i3wm——意味着我还在 X11。虽然我也知道 Wayland 现在先进得多，但是要重新处理 Sway/xwayland 以及其他各种潜在兼容问题都让我决定再拖拖，等下次下定决心再来吧。


