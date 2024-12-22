---
layout: post
title: "2024 Maary Review: Part2"
date: 2024-12-22 17:00:00 +0800
sitemap:
  lastmod: 
toc: true 
excerpt_separator: <!--more-->
tags:
  - Year Reivew
  - Personal
---

这一部分就是今年用了啥，既有硬件也有软件。  

<!--more-->

## 今年用了啥

### 设备相关

#### Pixel 3 的 Android 14

  在 Pixel 3 上吃到 Android 14 啦！  

  用的是 helluvaOS，但是对这个 ROM 的印象并不好，本来给出的下载链接就有坏链，然后在说明文档中完全不说人话，突出一种纯粹的自嗨。当然这个 ROM 是有好处的：它带上了各种能够给 Pixel 3 用的最新特性，比如即圈即搜。但是最新特性也有代价：它直接给手机模拟成了 Pixel 7。于是就会出现如下的情况  
  > Google Play: 你是 Pixel 7 啊，来这里有个 Google Camera 9.x 的更新快吃  
  > Pixel 3: 只要 gcam 版本 > 8.5 我就会死  
  于是只能禁用更新以及肉眼自动忽略各种应用内外的更新提示。

#### Ubuntu `->` Arch

  我！终于！从 Ubuntu 转移到 Arch 了！  

  此事在 [再见 Ubuntu 👋](2024-06-16-goodbye-ubuntu.md) 中亦有记载。简单来说 Ubuntu 强推 Snap 是赶跑我的最后一根稻草，依靠 BTRFS ~~孤独摇滚文件系统~~ 的子卷特性，在不更换主文件夹的基础上，只更换了根目录实现了到 Arch 的迁移。  

#### X11 `->` Wayland

  我！终于！从 X11(i3wm) 转移到 Wayland(Sway) 了！

  此事在 [Hello, Sway](2024-12-01-Hello-Wayland.md) 中亦有记载。其实就是直接给 wlroots 和 sway 都切换到了 git 版本于是 nvidia 兼容性问题奇迹般地好了。  

#### 早知道，还是原道.jpg

![原道酱可爱捏](/assets/2024-12-31-2024-Maary-Review/image-20.webp){: width="50%" }


#### 沃雷顿红日 500

27.5 英寸车轮、蓝图 A3 变速、油压碟刹（没说哪家的，估计不是很好）、可锁死的前叉，整体上我还是挺满意的。  

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/IMG_20241218_232519_978.webp)|
|本肥宅的赎罪券了。|

买来加了前后的挡泥板，以及后面的货物夹。现在还在绝赞磨合中。  

<div markdown='block' style="width: 50%;">

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/IMG_20241222_132547_394.webp)|
|以及这几天的一次骑行|

</div>

### 应用相关

#### cmd-wrapped: 终端也要年度总结

[cmd-wrapped](https://github.com/YiNNx/cmd-wrapped) 是一个用 Rust 编写的工具，通过分析你的 shell 历史记录来给出一份简要的报告，比如你今年敲的第一条命令、在不同时间段的活跃度，最活跃的时间段等等。

![比如这是我的 2024](/assets/2024-12-31-2024-Maary-Review/image-21.webp)

#### 多邻国

这里准确来说是「今年放弃了啥」。  

<div class="table-wrapper" markdown="block" style="width: 80%;">

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-22.webp)|
|我的连胜断在了这里|

</div>

多邻国的问题在于两个方面，第一个方面是我自己更多地变成了「为了打卡而打卡」，重点变成了完成任务而不是真的去学多少东西；第二个方面则是多邻国的算法变成了某种意义上的「外卖算法」：学的越多，任务越重。于是成了恶性循环，月度任务从完成二十个开始逐渐上升直到印象当中是五十个？而如果你就此放弃，没多长时间它又会掉回去。此外强制的一些不好完成又对我来说没有吸引力的其他「小游戏」任务进一步让我感到无奈。  

于是在课程学得差不多，连胜感觉也可以了之后，卸载跑路了。  

我还是很喜欢多邻国的，而且现在他们推出了音乐课程我可能也会下载回去。  


#### Firefox 干正事了

虽然仍然有各种各样的问题，但是看起来 Firefox 在往好的方向走了。列一些不错或者正在期待的更新内容：  

1. Firefox 把之前 about:config 里藏的部分特性提前放到了 Labs 里，可以在设置里直接开启了。
2. Firefox 承诺将改进侧边栏(config 中可启用)、支持侧边标签页(config 中可启用)和标签分组(config 中可启用，尚未完成)、以及 AI 支持(可在 Labs 中启用)。
3. 切换标签页自动 PIP，好用！
4. Android 版 Firefox 开始适配平板 UI。

顺便还有一个 Inoreader 相关的问题：  
在老版本的 Inoreader 移动端上，页面的渲染存在问题，他们会渲染一个宽度远超过当前屏幕显示范围的页面，导致各种显示错误和滑动问题。于是有了这个 [Inoreader Old Layout Fix](https://addons.mozilla.org/zh-CN/android/addon/inoreader-mobile-fix/) 的插件来修复这个问题。不过好消息是在 Inoreader 新设计的某个 beta 版本之后他们修好了这个问题，可喜可贺。  


#### KDEConnect 自动同步剪贴板

众所周知 Android 10 之后应用在后台读取系统剪贴板的权限受到了限制，于是在不 Root 的情况下 KDEConnect 的同步剪贴板功能就变成了半双向同步：电脑到手机畅通无阻，手机到电脑就需要手动点一下。但其实官方也给了[免 Root 解决方案](https://userbase.kde.org/KDEConnect#Auto-sync_on_Android_10+)，直接给 KDEConnect 一个 `READ_LOGS` 的权限：  

```shell
adb -d shell pm grant org.kde.kdeconnect_tp android.permission.READ_LOGS;
adb -d shell appops set org.kde.kdeconnect_tp SYSTEM_ALERT_WINDOW allow;
adb -d shell am force-stop org.kde.kdeconnect_tp;
```

当然这个方案也有其缺点，一是方法其实有点「脏」，这是官方的描述  

> How does it work?  
>   
> With log reading enabled, KDE Connect watches its own log. If it sees a line indicating that it has been denied access to read the clipboard, it pops up an invisible window to the foreground, grabs the clipboard, and closes the window. 

KDEConnect 在读取到自己访问剪贴板的行为被拒绝的日志之后则在前台开一个透明窗口然后拿剪贴板；  

二是似乎这个权限需要每次重启后都重新授予？但这个我不太确定，也可能不需要？  

#### 本地 LLM

谁能想到你们 AI 真的就突飞猛进呢。  

去年大概也有过类似的感叹，本来觉得跑个 CNN、YOLO 绰绰有余的 3050 现在啥都干不了。  
不过跑个 7B 的模型还马马虎虎。  

折腾本地模型还是靠 Ollama，它对于本地跑 LLM 的帮助太大了，正好体验了一把当时各路的模型，比如 Llama2, Gemma, qwen 等等。比如之前在 [自嗨记录之一](2024-04-22-Wallpaper-Tunnel-3.0.md) 里就试了一下用本地的 llama3:8b 来解释一些概念，感觉还是可以的。  

虽然跨模型之间的比较其实我的使用强度不太能感知到，但是深刻感受到了参数越多越强。所以话说回来，还是 GPT-4o 和 Gemini 1.5 pro 好用啊。  


#### YTM 的播客梦

还是早点醒吧。  

先不提 Spotify 和 YTM 怎么想起来的非要给音乐流媒体塞播客，YTM 的播客体验突出一个想一出是一出。  

1. 好不容易支持了从 RSS 添加播客源，然后发现他们居然不支持 OPML 导入。如果说不做导出功能是可以理解但无耻的行为，不做导入简直就是无法理解的决定。唯一可行的解释就是，YTM 的团队，至少是负责播客的产品经理完全没有用过任何播客客户端。  
2. YTM 的 RSS 添加播客源并不会老老实实按照播客源进行添加，而是会在平台上存在疑似同一个播客时，故作聪明的为你自动替换。于是我就遇到了非常直接的问题：机核的播客可以在 YTM 上搜索到，但是不知道是谁的问题，我这里并不能显示出来任何一集的内容。这个时候选择 RSS 添加，发现给我添加了同一个无法看到内容的频道。

#### 支离破碎的社媒

先开始大家都在用 Twitter，键政的键政、搞涩涩的搞涩涩、发广告的发广告。虽然也有去中心化的平台，比如 Mastodon，但是更多作为小圈子圈地自萌的状态。  
后来马一龙买了 Twitter 开始犯病。这个时候大家开始找替代品，然后混乱开始了。  

我们有：

- 来看看马一龙的 X。虽然难看但是大部分人都在上面更新那只能先用着。
- 老牌去中心化平台 Mastodon，正好我早就注册了账号，用起来。
- Meta 看 Twitter 不顺眼十几年终于憋出来了 Threads，趁你病要你命，反正是 Instagram 账号通用，用起来。
- 号称 Twitter 创始人杰克多西亲自上阵的 BlueSky，感觉不是很靠谱没去凑热闹。（顺便一提似乎杰克多西到底算多大程度上的创始人也不好说，以及现在他已从 BlueSky 跑路了。）

然后在马一龙又一次犯病之后，大家又一次的跑路潮的目的地是：BlueSky？你是说我现在一个 Mastodon 和一个 Threads 账号，但大家又往 BlueSky 跑了？  
于是无奈注册 BlueSky，现在四个平台一个平台一个账户，谁能看得过来啊（全恼

#### Linux 微信 4.0

微信！在 Linux 上！接近全功能！而且不限制系统！  

这么一行文字看过去跟科幻小说一样。  

简单说一下原来微信在 Linux 上的状态：最早的时候所谓的 Linux 微信就是一个网页版的打包。后来虽然出现了更接近 Windows 桌面端体验的版本，但是却限制了仅有 UOS、银河麒麟等个别国产操作系统可用，而且事实上功能依然很多缺失，比如不能自动登陆，小程序缺失等等。现在 Linux 微信 4.0 尽管仍然不能备份手机聊天记录，但是可以自动登录、支持暗色模式、不再限制系统等等，已经非常接近 Windows 端的体验了。  

但是看看人家隔壁 QQ 团队全平台 Electron 的效果，张小龙的母亲还是无法落地。  

#### 第三方 Telegram 客户端

桌面上之前用的朋友打包的打了各种补丁的客户端，比较重要的功能比如屏蔽频道广告、隐藏 Stories 等等。但是朋友维护的也麻烦，他看到 [AyuGram](https://github.com/AyuGram/AyuGramDesktop) 之后直接跑路到这个上了。于是我也跟着看了看，一看发现确实很行，说说最喜欢的俩功能：

- 默认静音发送  

  |:--:|
  |![默认静音！](/assets/2024-12-31-2024-Maary-Review/image-23.webp)|  
  |你们 Tg 既然做了静音发送消息，但是却不能默认静音发送简直不可理喻。|

- 截屏消息  

  |:--:|
  |![比如](/assets/2024-12-31-2024-Maary-Review/image-24.webp)|  
  |可以说是做表情包的利器|

Android 端还是之前就在用的 Nullgram，现在更以全新默认图标呈现。

|:--:|
|![alt text](/assets/2024-12-31-2024-Maary-Review/image-26.webp)|
|头像就是新图标|

顺便一提我也做了一点微小的工作：比如提了希望能点击链接预览中的图片以查看图片而非打开链接的 issue，也提了给新图标对应的主题图标的 PR。  
以及 Nullgram 也支持默认静音发送消息，也是我提的 PR。（如果有关功能出现问题我先滑跪了）

#### 碎碎念

- 流媒体的末路  

简单来说是被 BA 日服 OST 给搞崩心态了，在某一天之后之前正常听的 OST 突然就怎么折腾都无法播放了。  
一通测试之后似乎是必须要日区的 Google 账户 + 日区 IP 才能正常播放，这也太费劲了。不过好在后来找到了国际服的 OST（感觉上编排都不一样了）。  

- WebP 太先进了

之前看到 BlueArchive 5th PV 之后想看看细节另外也是准备当作壁纸备选，就逐帧拆开存本地了。然后发现转换成 PNG 是 12.9G，而转换成无压缩 WebP 则是基本上只要一半空间（但是转换太慢了没有等到转换全部完成），默认选项即带有压缩的 WebP 转换完只要 450MB，而且肉眼上分辨不出来什么区别。

- 通过了 B 站的「硬核会员」答题，靠着 Bard（是的那会还叫 Bard）。

  


[Part 3 在这里](2024-12-31-2024-Maary-Review-Part3.md)。
