---
layout: post
title:  "提升 QEMU 后端 Genymotion 模拟器存储空间"
date:   2024-08-30 09:00:00 +0800
toc: true
categories: android 
excerpt_separator: <!--more-->
---

以下内容整理自：[https://www.reddit.com/r/linux4noobs/comments/bj7ezk/comment/kapd7c7/](https://www.reddit.com/r/linux4noobs/comments/bj7ezk/comment/kapd7c7/)

<!--more-->

## 问题

Genymotion 是 Linux 平台上为数不多的 Android 模拟器之一，虽然已经足够满足我对于模拟器功能的需求，但是缺少一个关键功能：修改模拟器内存容量。默认的 32G 空间在我最小安装完碧蓝航线之后就所剩无几，更别提下载全量包了。

## 解决方法步骤

### 1. `qemu-img` 扩容虚拟磁盘  
首先确定 Genymotion 中的模拟器名称，这里假定为 `Nexus7`。其次需要安装有关的 Qemu 工具，这里安装 Genymotion 过程中就应该已经满足，运行命令时提示找不到命令请参考对应发行版的软件源自行安装。  

1. 进入模拟器文件夹  
模拟器的文件通常位于主目录下的 .Genymobile 文件夹下：  
`cd ~/.Genymobile/Genymotion/deployed/[Device]`  
这里 `[Device]` 即模拟器名称，根据实际情况填写，如 `cd ~/.GenymobileGenymotion/deployed/Nexus7`。  
2. 查看当前虚拟磁盘信息  
`qemu-img info data.qcow2`  
此时应该显示磁盘的大小，用于和修改后对照。
3. 修改磁盘空间大小  
`qemu-img resize data.qcow2 64G`  
其中 `64G` 为目标的磁盘大小，完成后可再次查看虚拟磁盘信息进行比对。  

### 2. `adb shell` 扩容 `/data`  
在完成虚拟磁盘的扩容后，还需要让模拟器中的 Android 系统能够正常识别新增的空间。这里假定宿主系统中已经有相关的 ADB 工具，如果尚未安装 ADB 请参考 Google 开发者文档或有关发行版软件源自行安装。  

1. 启动模拟器
2. 在宿主机中打开终端  
使用 `adb devices` 查看模拟器可被正常识别  
3. 进入模拟器中 Android 系统的 Shell  
终端中输入 `adb shell`  
5. 查看 Android 分区信息
`df -h`  
记录 `/data` 分区对应的文件系统名称，如 `/dev/block/vdb5`  
6. 扩展 `/data` 分区  
根据步骤 5 中记录的文件系统名称修改并运行以下命令：
`resize2fs /dev/block/vdb5` 
7. 重启模拟器

## 其他

Mac 用户修改时文件位置可能有不同，请参考相关[评论](https://www.reddit.com/r/linux4noobs/comments/bj7ezk/comment/levw4hq/)。使用 VirtualBox 后端的 Genymotion 模拟器扩展容量参考引用的 Reddit 评论对应原帖。