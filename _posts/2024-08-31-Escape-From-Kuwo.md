---
layout: post
title: "一点酷我音乐迁移尝试"
date: 2024-08-31 19:00:00 +0800
toc: true
categories: music 
excerpt_separator: <!--more-->
tags: 
    - Music
---

为了把酷我的歌单下到本地绕了一个超大弯：`在酷我里扒拉数据库 -> 从数据库导出 csv -> 把 csv 导入到 spotify -> 用 spotdl 从 Youtube 下载歌单`，顺带发现了一些整蛊 bug。虽说最后结果也只能说是还行。

<!--more-->

## 在酷我里扒拉数据库

在快进到扒拉数据库之前，其实有一些挣扎：在 Github 上找了两个看起来还算新的项目试图，直接通过歌单链接获取音乐列表。可惜一无所获，最好的情况是获取了十来首的列表，但整个歌单 400+。  

那么歌单链接这条路不通又是怎么转移到扒拉数据库呢？这就要提到 [LocalMusicHelper](https://github.com/Winnie0408/LocalMusicHelper) 这个应用了，尽管我完全理解错了这个应用的作用。它并非是我臆想中的「抓取音乐平台的歌单后以某种格式存储下来」，而是「将音乐平台在本地下载的歌单导入到其他的本地播放器中」，不过这里的误解倒是不影响它给我指出了明路：「别折腾有的没的了，直接扒拉数据库吧！」  

他们文档还给了数据库位置：  
> 数据目录：/data/user/0/cn.kuwo.player/databases
> Patch后的数据目录：酷我音乐/data/databases
> 数据库文件：kwplayer.db  

另外需要让数据库中切实存入歌单的数据，也就是把歌单划拉到底。到这里是非常正常的事情，但是不正常的来了，我在试图将两个歌单分别全选合并到第三个歌单的时候，发现如果把歌单划拉到底，就会出现所谓的「全选」只选了一百首歌的情况。还能这样？
  
Bug 放到一边，总之现在数据库准备好了，不过想必各位已经想起来了：`data` 目录要怎么读呢？  

由于我忘了放在一边 Root 过的像素，于是用了非 Root 的方法：使用 ONPatch 修补酷我音乐客户端以注入`文件提供者`，其作用就是在使用系统的 FilesProvider 的时候酷我的内部数据入口就会展示在侧边栏中，就像下面这里的「错误报告」。

![alt text](/assets/2024-08-31-Escape-From-Kuwo/Screenshot_2024-08-31-18-38-24-01_176161be900e0e29934afca83eb113f8~2.webp)

## 从数据库导出 csv

顺利拿到数据库之后，我还担心了一下会不会有一些加密，结果一想前面提的应用能读那我肯定也能读，不但能读还能直接抄他们的代码：  

> ```
> "KuwoMusic" -> {
>     sourceEng = "KuWoMusic"
>     sourceChn = "酷我音乐"
>     databaseName = "kwplayer.db"
>     songListTableName = "v3_list"
>     songListId = "id"
>     songListName = "showname"
>     musicNum = "musicNum"
>     songListSongInfoTableName = "v3_music"
>     songListSongInfoPlaylistId = "listid"
>     songListSongInfoSongId = "rid"
>     songInfoTableName = "v3_music"
>     sortField = "id"
>     songInfoSongId = "rid"
>     songInfoSongName = "name"
>     songInfoSongArtist = "artist"
>     songInfoSongAlbum = "album"
>     pakageName = "cn.kuwo.player"
> }
> ```  

从这里就能看到包含歌单的表是 `v3_list`，包含歌曲的表是 `v3_music`。顺便一提这是 Sqlite 的数据库，于是装了相关的包用命令行导出 CSV。   

```sql
.headers on 
.mode csv
.output data.csv
select name, artist, album
from v3_music 
where listid=1;
```

## 把 csv 导入到 Spotify 

到这里其实已经非常简单了，现在有好几个转移歌单的服务，除了——大家要吃饭的，免费版各有各的限制。  

我使用了 [TuneMyMusic](https://www.tunemymusic.com/zh-CN)。匹配的效果似乎还行，但是免费版只有五百首的额度，而不是每个歌单限制多少首。  

不过这里虽然有付费计划但并非强制登陆，于是动动脑筋你应该能想到如何重复利用五百首的额度。（Spoiler Alert: 该清理 cookie 了。  

## 用 spotdl 从 Youtube 下载歌单

终于到最后一步了：[spotify-downloader](https://github.com/spotDL/spotify-downloader)。这里其实没什么可说的了，老老实实装包然后下载就好了。  

## 回马枪，小子！

这里是我本来本着能省一点地方就省一点地方的原则用朋友的脚本来将 mp3 转格式到 opus，见[使用 FFmpeg 转换音频为 Opus：避坑指南与实战经验](https://sakari.top/posts/2024/ffmpeg-opus/)。结果发现手机上对 opus 的识别有问题（老设备没想到），于是最后灰溜溜地用 mp3 了。  

回过头来这里从酷我最后绕到 Youtube 来下载具体的歌曲，事实上中间各种没有匹配上/误匹配的情况根本数不过来，只能说聊胜于无了。  

