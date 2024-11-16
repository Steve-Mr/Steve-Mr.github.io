---
layout: post
title: "ReVanced CLI 的文档啊……"
date: 2024-11-16 11:30:00 +0800
modified_date:  
toc: true 
excerpt_separator: <!--more-->
tags:
  - ReVanced
  - Android
---

虽然折腾明白了一些 ReVanced CLI 的语法，但还是不太懂，文档还是太简略了。

<!--more-->

## 起因

ReVanced 项目是当年 Vanced 项目的「精神续作」，与 Vanced 直接放出安装包且专注于 Youtube 不同，ReVanced 采取了相比更加「灰色」的模式：我们只是打包工具，用户拿来这工具给什么玩意打了什么包我们当然管不到。因此 ReVanced 更加依赖于社区以提供针对不同应用的拓展包，以及不再提供成品安装包。  

前两天一个依赖于 ReVanced 的 BiliRoamingX 项目出现了一些问题，于是朋友 Fork 了一份 [BiliRoamingM](https://github.com/sakarie9/BiliRoamingM) 跑通了整个流程。但是遇到了一个问题：对于我这种非 Root 用户，直接打包而不修改包名或者版本号的话，会导致 Play 商店认为该程序有更新。于是开始研究怎么修改原流程来用 ReVanced CLI 修改包名或者版本号。  

## 经过

首先这里有一个 [ReVanced CLI patch guide](https://sodawithoutsparkles.github.io/revanced-troubleshooting-guide/06-revanced-cli/) 基本上提供了我们需要的主要内容。  

比如基础命令：`java -jar cli.jar patch -b patches.jar -m integrations.apk -o out.apk input.apk`，但是我们在这里需要的不只是基础命令，为了修改包名/版本号，我们需要增加 patch，于是需要这个命令：`java -jar cli.jar patch -e 'Exclude patch 1' -e 'Exclude patch 2' -i 'Include patch 1' -i 'Include patch 2' ...`。  

这个时候就是下一个问题：语法知道了，具体的 patch 名字叫什么？也就是在这一步问题迷惑了起来。在 [ReVanced 官网补丁页](https://revanced.app/patches) 有这样的补丁列表，根据在 Reddit 搜了半天的结果，其实这里列出的**带有空格**的 patch 名字就是需要在命令行里使用的名字。只能说非常不符合我的使用习惯。  

但是问题到这里还没有结束：我该怎么调用到这个 patch？一开始我以为是 ReVanced CLI 会自动根据 patch 下载，但很遗憾并非如此。具体有什么 Patch 可以添加还是要看你使用的 patches.jar 里有什么。于是需要用到这个命令查看所有的 patches：`java -jar cli.jar list-patches -opv patches.jar`。查看之后发现 BiliRoamingM 里自带了 `Change Version Code` 的 patch，ReVanced 提供的默认 patch 中包含了 `Change Package Name` 的 patch。

1. 改版本号。  
   我还是觉得修改版本号非常的优雅，很大程度也是因为我装完设置好了懒得重装了。但如果这里贸然修改会出问题：推测是客户端和服务器会存在校验，导致其默认设置的最大值会被发现不正常。但好在这里使用的 Play 版的版本号比普通版本落后好多，加个 100 200 的不是问题。  
   更麻烦的来了，我们需要设置一个配置文件来指定配置。首先生成配置文件：`java -jar cli.jar options -o patches.jar`，然后修改 options.json 中的对应内容之后再应用该配置文件：`java -jar cli.jar patch --options=path-to-options-file ...`。  
   那么在 Github Actions 中的具体流程如下：  

   ```yml
   # Check $version & $versionCode
   - name: "Get play Info"
     id: "apk-info-play"
     uses: "8Mi-Tech/get-apk-info-action@master"
     with:
       apkPath: "play.apk"
   # Get play $patchVersion $patchVersionCode
   - name: Get Bilibili-Play Info
     run: |
       echo "Bili_PLAY_VER=${{steps.apk-info-play.outputs.versionNum}}" >> $GITHUB_ENV
       echo "Bili_PLAY_VERCODE=${{steps.apk-info-play.outputs.versionCode}}" >> $GITHUB_ENV

   - name: Modify version code and write to options.json
     run: |
       # 从环境变量中获取 BILI_PLAY_VERCODE 并加 100
       VERSION_CODE=$((BILI_PLAY_VERCODE + 100))
       
       # 创建 options.json 文件并写入数据
       echo '[
         {
           "patchName" : "Change version code",
           "options" : [
             {
               "key" : "versionCode",
               "value" : '"$VERSION_CODE"'
             }
           ]
         }
       ]' > options.json
     env:
       BILI_PLAY_VERCODE: ${{ steps.apk-info-play.outputs.versionCode }}

   - name: Build BiliM-play
     run: |
       java -jar revanced-cli.jar patch --merge integrations.apk --patch-bundle patches.jar --signing-levels 1,2,3 --options=options.json play.apk
   ```
2. 改包名。  
   改包名就还需要一个 ReVanced 官方的 patches.jar，但是其中非常多的 Patch 我们只需要 `Change PackageName` 这一个，可以增加 `--exclusive` 选项再 `-i "Change package name"`。但遗憾的是我还是没弄明白如何两个 patch 一起打，因此用了先打 BiliM patch 再打改包名 patch。  
   Github Actions 流程：  
   
   ```yml
   - name: Build BiliM-play
     run: |
       java -jar revanced-cli.jar patch --merge integrations.apk --patch-bundle patches.jar --signing-levels 1,2,3 play.apk

   - name: Build BiliM-play with Change package name patch
     run: |
       java -jar revanced-cli.jar patch --merge integrations.apk --patch-bundle patches.jar --signing-levels 1,2,3 -i "Change package name" -o play-renamed.apk play.apk  
   ```

## 结果

结果是 ReVanced CLI 的文档太欠缺了，很多东西只能靠猜和在社区中翻记录。  
关于 BiliRoamingM 这点我虽然折腾了个爽但还是有问题：改包名的版本因为 provider 组件的冲突依然不能和原版共存，当然两种方法都成功实现了避免 Play 提示更新的目标。最后朋友决定直接 `apktool` 一把梭改包名，顺便修改 provider 名称实现两个版本共存。之前折腾的这些姑且留个记录。 