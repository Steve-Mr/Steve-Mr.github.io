---
layout: post
title: "2024 Maary Review: Part 3"
date: 2024-12-22 16:55:00 +0800
sitemap:
  lastmod: 
toc: true 
excerpt_separator: <!--more-->
tags:
  - Year Reivew
  - Personal
---

最后有点杂烩了：今年写的东西、玩的东西，以及偶尔看到的可能有点意思的玩意。  

<!--more-->

## 今年写了啥

### Blog

{% assign start_date = "2024-01-01" | date: "%s" %}
{% assign end_date = "2025-01-01" | date: "%s" %}
{% assign post_count_24 = 0 %}

{% for post in site.posts %}
  {% assign post_date = post.date | date: "%s" %}
  {% if post_date >= start_date and post_date <= end_date %}
    {% assign post_count_24 = post_count_24 | plus: 1 %}
  {% endif %}
{% endfor %}

{% assign start_date = "2023-01-01" | date: "%s" %}
{% assign end_date = "2024-01-01" | date: "%s" %}
{% assign post_count_23 = 0 %}

{% for post in site.posts %}
  {% assign post_date = post.date | date: "%s" %}
  {% if post_date >= start_date and post_date <= end_date %}
    {% assign post_count_23 = post_count_23 | plus: 1 %}
  {% endif %}
{% endfor %}

去年还算是刚建起来 Blog 摸摸看，今年则是认真来搞了。一年下来总共写了 {{ post_count_24 }} 篇流水账，作为对比去年总共是 {{ post_count_23 }} 篇。不过更重要的是这次真的好好整了整博客，主要内容就在[该魔改这里主题了](2024-04-24-maary-flavored-minima.md)和[该自定义域名了](2024-04-24-custom-domain!.md)，明年也会继续折腾。  

### 造轮子

|:--:|
|{% github_chart Steve-Mr default %}|
|四五月份这么密集的更新也是有原因的，不过热力图也有欺骗性。|

实际上那段时间我是抱着要把下半年的代码都写完的觉悟在写的(笑)，不过下半年也算开了坑。上半年开的新坑是 [Yet Another Calendar Widget](https://github.com/Steve-Mr/YetAnotherCalendarWidget)，但其实最满意的大概是[给 Wallpaper Tunnel 一通改](2024-04-22-Wallpaper-Tunnel-3.0.md)。下半年开的坑是 [Oblivionis](https://github.com/Steve-Mr/Oblivionis)，一个 Slidebox 的拙劣仿品。这么一年下来算是摸了 Jetpack Compose, Jetpack Glance, Android MVVM，动画等等东西，长进可能不多，看着还挺热闹。  

- [Yet Another Battery Notifier](https://github.com/Steve-Mr/YetAnotherBatteryNotifier)
  - 增加了设置页。可以设置百分比阈值以及夜间静音时间。
  - 增加了微件可以用来显示当前的电流并且一键静音一小时。
- [LiveInPeace](https://github.com/Steve-Mr/LiveInPeace)
  - 进行了常规的 SDK 更新之类的操作。
  - 得益于 Color OS 无亲无后的后台限制，我迫不得已把 Sleep Timer 的功能塞到了里面。
  - 根据 Sleep Timer 增加了和 YABN 的联动：打开 Sleep Timer 则自动启动 YABN 的静音状态。
- [redroid-script](https://github.com/Steve-Mr/redroid-script)
  - 根据 waydroid-script 更新了 ndk 版本
  - 同样根据 waydroid 社区做了给 ndk 打补丁以运行 Blue Archive 的功能
- [Yet Another Calendar Widget](https://github.com/Steve-Mr/YetAnotherCalendarWidget)
  - 差不多是省了两刀还是多少来着。
  - 主要目的是为了展示追的番剧的更新计划。
  - 顺便也塞了个空白微件进去，主要为了实现空白主屏。
- [MaaryBot](https://gist.github.com/Steve-Mr/3d6c03ef1e09594330b174d7e6dc20f9)
  - 从朋友那摸来了链接转换的代码，主要是为了用来转换一些社交平台的链接以提供更好的预览。
- [Wallpaper Tunnel](https://github.com/Steve-Mr/WallpaperTunnel)
  - 更新 Target API 等常规更新。
  - 增加启动页，在实现获取当前壁纸进行编辑功能之外还简化了设置下载的图片为壁纸的流程。
  - 设置专门的编辑器。
    - 增加颜色填充功能
    - 增加超分功能
    - 增加横向图的裁切功能
  - 增加首次启动欢迎页，引导设置必要权限。
  - 优化界面，清理代码。
- [Nullgram](https://github.com/qwq233/Nullgram)
  - 根据 Nullgram 新增的默认图标提了个 themed icon 的 PR。
- [Oblivionis](https://github.com/Steve-Mr/Oblivionis)
  - 上滑删除图片，下滑标记图标不可删除，长按删除按键可以清空当前文件夹（除了被标记图片）。
  - 在回收站可以彻底删除或恢复图片。
- [twitter_to_fxtwitter.js](https://gist.github.com/Steve-Mr/225223060230a2dcd6a0e57efeb97325)
  - 找的各种在 twitter 网页上点分享按钮的时候自动替换成 fxtwitter 的油猴脚本都不管用，倒是一个自动替换 vxtwitter 的正常，于是进行了魔改。

接下来有在考虑要不要试着也做一个 ZeroCam 那样的 Android 程序，但是看起来还挺麻烦的所以暂时观望。 


## 今年玩了啥

### Blue Archive

虽然日常想要退坑但是还没有下定决心，下次一定.jpg  
但也确实从剧情一章不落变成「算了跳过完个任务拉倒了」，当然也和国际服落后日服半年有关，等活动轮到自己的时候早云了个差不多了。  

同时不提哪边的对错，难以想象 Blue Archive 几乎在每个主要市场都出了幺蛾子：  
- 大陆市场因为和雀魂的单向联动造成了所谓 ML 玩家的强烈不满；
- 日韩市场因为部分主创人员集体跳槽，并且有在离职前故意降低作品质量的怀疑中，迅速宣布了与 Blue Archive 各种方面都相似的项目《Project KV》而产生争议，并且后面[迅速滑跪宣布项目终止](https://gnn.gamer.com.tw/detail.php?sn=273508)；
- 港台市场则是经典在国际服周年活动的 Youtube 直播中出现了「国家/地区」争议，把自己架在了横竖都要得罪一拨人的状态。  

但是 BA 的角色真的很香。  

|:--:|
|![礼服 hina！礼服 hina！礼服 hina！](/assets/2024-12-31-2024-Maary-Review/image-27.webp)|
|嘿嘿 hina 嘿嘿|

国际服落后日服半年也不完全是坏事，比如这半年给了自己「未来视」：手里的资源可以根据后面半年的活动进行安排，抽不抽角色一目了然。自然也给退坑造成了困难：  
礼服 hina 完就退坑 -> 乐队甜点部完就退坑 -> 黑子实装完就退坑 -> 妃咲实装完就退坑 -> 偶像玛丽完就退坑(*New!*)

### 死亡搁浅

终于在 DS2 之前打完了死亡搁浅，尽管云了个大概但是自己亲自走完整条线还是很感概。  

|:--:|:--:|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-28.webp)|![alt text](/assets/2024-12-31-2024-Maary-Review/image-29.webp)|![alt text](/assets/2024-12-31-2024-Maary-Review/image-30.webp)|

DS2 预告看起来不错，但是续作的剧情能不能搂住始终是各家都翻过车的问题。现在只能希望不会出现「把我的感动还给我啊！」的剧情吧。

### Balatro

年度游戏提名的含金量，其实还挺想看到小丑牌拿了年度游戏的反应的（笑  

|:--:|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-31.webp)|![alt text](/assets/2024-12-31-2024-Maary-Review/image-32.webp)|

虽然我一直输输输，但是还真赢了一把！  

### 碧蓝航线

这个退坑真的就是沉默成本太大了，而且玩得好好的推什么坑。  
但黄鸡也是一个每个月不找个事情给自己炎上一拨不舒服的主。  

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-33.webp)|
|朋友们这谁顶得住啊|

说实话能坚持碧蓝航线到现在 [AzurLaneAutoScript](https://github.com/LmeSzinc/AzurLaneAutoScript) 帮了大忙，一点启动挂那去睡觉就有小精灵帮你把每日任务全完成的体验也太好了。  

<div markdown='block' style="width: 50%;">

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-37.webp)|
|一些文案放飞自我|

</div>

顺便改了改模拟器试了一下折叠屏的效果，真不错啊。  

|:--:|:--:|:--:|
|![海天！海天！海天！](/assets/2024-12-31-2024-Maary-Review/image-34.webp)|![能代！能代！能代！](/assets/2024-12-31-2024-Maary-Review/image-35.webp)|![安克雷奇！安克雷奇！安克雷奇！](/assets/2024-12-31-2024-Maary-Review/image-36.webp)|


### 动森手游

停止运营了是坏事也是好事。  
虽然明年(2025)十月之后就没有新内容了，但是买断制也避免了各种经典手游不充钱没法玩的限制。可惜我现在的精力却不大能顾的上它了，而且玩法也忘差不多了。  

<div markdown="block" style="width: 50%;">

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/20241221230910.webp)|
|其实玩法还是挺复杂的|

</div>

## 感觉有点意思的东西？

这里主要是视频，但是也有一个项目。  

- [opencall](https://github.com/openrecall/openrecall)：冲着微软 Recall 来的，虽然还没有尝试，但是看起来还行。
- [New Supercomputer Simulation Sheds Light on Moon’s Origin](https://www.youtube.com/watch?v=kRlhlCWplqk)：两年前的视频，对月亮起源的一个模拟。
- [Classical Music Is Just Heavy Metal Before Electricity](https://www.youtube.com/watch?v=Zdol7Q3DMs4)：一些「强而有力」的古典音乐合集。
- [Making Material Design](https://www.youtube.com/watch?v=rrT6v5sOwJg)：现在回看当年 Material Design 的开端，当时的想法还是很有吸引力。
- [Ara: What's next](https://www.youtube.com/watch?v=aWW5mQadZAY)：可能这就是一个「不可能完成的任务」，但是看着现在换个后壳就恨不得满世界嚷嚷「模块化」的手机，希望有一个 Ara 大获成功的世界线。